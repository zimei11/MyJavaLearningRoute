# KOB项目实现QQ第三方授权登录

我实现好的**网站地址**：  
[robs.zimei](https://www.zimei.fun/)

参考文章：[工程课 Django-6.2. 上课笔记（实现qq一键登录） - AcWing](https://www.acwing.com/file_system/file/content/whole/index/content/3795676/)

### 1、前往qq互联进行开发者资料审核

[https://connect.qq.com/devuser.html#/create/1/](https://connect.qq.com/devuser.html#/create/1/)  
选择个人接入后填入个人信息

注：手持照片得用后置摄像头拍摄，前置摄像头有镜像功能，手指不能遮挡证件信息，一定要拍清楚，否则会被驳回！

* * *

### 2、创建应用

1.在应用管理界面点击创建应用，选择创建网站应用。  
2.网站名称一栏填写备案的网站名称

![](https://img.zimei.fun/202209161631752.png)

3.网站回调地址填写处理qq返回的请求路径

就是对应acwing一键登录中的接收code,  
如我acwing接收code的路由是：

```html
https://www.zimei.fun/user/account/qq/receive_code
```

在这就填写：

```
https://www.zimei.fun/user/account/qq/receive_code
```

注：此处末尾**不能**加 /

![image-20220916163634598](https://img.zimei.fun/202209161636642.png)

4.填写提供方和备案号

可前往查询： [https://icp.chinaz.com/](https://icp.chinaz.com/)  
![](https://img.zimei.fun/202209161631048.png)

5.点击创建应用等待审核

* * *

### 3、实现代码

官方教程： [https://wiki.connect.qq.com/%e4%bd%bf%e7%94%a8authorization\_code%e8%8e%b7%e5%8f%96access\_token](https://wiki.connect.qq.com/%e4%bd%bf%e7%94%a8authorization_code%e8%8e%b7%e5%8f%96access_token)

点击操作中的查看，可以看到APP ID 和 APP Key

![image-20220916163937236](https://img.zimei.fun/202209161639287.png)

#### 1.实现前端代码

##### view  向后端发送GET请求

```
src/views/user/account/UserAccountQQReceiveCodeView.vue
```

```ABAP
<template>
<div></div>
</template>

<script>
import router from "@/router/index";
import {useStore} from "vuex";
import {useRoute} from "vue-router";
import $ from 'jquery'
export default {
  name: "UserAccountQQReceiveCodeView",
  setup() {
    const myRoute = useRoute();
    const store = useStore();
    $.ajax({
      url: "https://www.zimei.fun/api/user/account/qq/receive_code",
      type: "GET",
      data: {
        code: myRoute.query.code,
        state: myRoute.query.state,
      },
      success: resp => {
        if (resp.result === "success") {
          localStorage.setItem("jwt_token", resp.jwt_token);
          store.commit("updateToken", resp.jwt_token);
          router.push({name: "home"});
          store.commit("updatePullingInfo", false);
        } else {
          router.push({name: "user_account_login"});
        }
      }
    })
  }
}
</script>

<style scoped>

</style>
```

##### view 登录界面QQ授权登录图标

上传qq的图标到本地静态资源文件夹，或者使用图床。 
图标下载链接 [https://game.ilotus.top/static/image/settings/qq\_logo.png](https://game.ilotus.top/static/image/settings/qq_logo.png)  
官方资源库 [https://wiki.connect.qq.com/%e8%a7%86%e8%a7%89%e7%b4%a0%e6%9d%90%e4%b8%8b%e8%bd%bd](https://wiki.connect.qq.com/%e8%a7%86%e8%a7%89%e7%b4%a0%e6%9d%90%e4%b8%8b%e8%bd%bd)

参考代码

```vue
<div @click="qq_login">
    <img height="30"
        style="cursor: pointer;"
        src="图床链接"
    />
</div>
```

```js
const qq_login = () => {
  $.ajax({
    url: "https://www.zimei.fun/api/user/account/qq/apply_code/",
    type: "GET",
    success: resp => {
      if (resp.result === "success") {
        window.location.replace(resp.apply_code_url);
      }
    }
  })
}
```

##### router 修改路由

```
src/router/index.js
```

```js
//仅供参考，我这里使用的路由懒加载写法
{
    path: "/user/account/qq/receive_code",
    name: "user_account_qq_receive_code",
    component: ()=>import('@/views/user/account/UserAccountQQReceiveCodeView'),
    meta:{
    requestAuth : false
    }
},
```

#### 2.实现后端代码

##### Service接口

```
src/main/java/com/lxs/backend/service/user/account/qq/QQWebService.java
```

```java
package com.lxs.backend.service.user.account.qq;

import com.alibaba.fastjson.JSONObject;

public interface QQWebService {
    JSONObject applyCode();
    JSONObject receiveCode(String code,String state);
}
```

##### Serviece实现

这里我重构了数据库，acwing的openid和qq的openid不应该存在一起，数据库user表**增加**属性**openid_qq**。注意项目全局搜索用到User的地方，全部都要更新openid_qq，和添加openid同理。

appId appSecret换成自己的

```
src/main/java/com/lxs/backend/service/impl/user/account/qq/QQWebServiceImpl.java
```

```java
package com.lxs.backend.service.impl.user.account.qq;

import com.alibaba.fastjson.JSONObject;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.lxs.backend.mapper.UserMapper;
import com.lxs.backend.pojo.User;
import com.lxs.backend.service.impl.user.account.acwing.utils.HttpClientUtil;
import com.lxs.backend.service.user.account.qq.QQWebService;
import com.lxs.backend.utils.JwtUtil;
import org.apache.http.NameValuePair;
import org.apache.http.message.BasicNameValuePair;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.time.Duration;
import java.util.LinkedList;
import java.util.List;
import java.util.Random;

@Service
public class QQWebServiceImpl implements QQWebService {
    private static final String appId = "你的ID";
    private static final String appSecret = "你的密匙";
    private static final String redirectUri = "https://www.zimei.fun/user/account/qq/receive_code";
    private static final String applyAccessTokenUrl = "https://graph.qq.com/oauth2.0/token";
    private static final String getUserInfoUrl = "https://graph.qq.com/user/get_user_info";
    private static final String getUserOpenIDUrl="https://graph.qq.com/oauth2.0/me";
    private final static Random random = new Random();

    @Autowired
    private UserMapper userMapper;

    @Autowired
    private RedisTemplate<String, String> redisTemplate;
    @Override
    public JSONObject applyCode() {
        JSONObject resp = new JSONObject();
        String encodeUrl = "";
        try {
            encodeUrl = URLEncoder.encode(redirectUri, "UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            resp.put("result", "failed");
            return resp;
        }

        //随机字符串，防止csrf攻击
        StringBuilder state = new StringBuilder();
        for (int i = 0; i < 10; i++) {
            state.append((char) (random.nextInt(10) + '0'));
        }
        //存到redis里，有效期设置为10分钟
        resp.put("result", "success");
        redisTemplate.opsForValue().set(state.toString(), "true");
        redisTemplate.expire(state.toString(), Duration.ofMinutes(10));

        String applyCodeUrl = "https://graph.qq.com/oauth2.0/authorize"
                + "?response_type="+"code"
                + "&client_id=" + appId
                + "&redirect_uri=" + encodeUrl
                + "&state=" + state
                ;
        resp.put("apply_code_url", applyCodeUrl);

        return resp;
    }

    @Override
    public JSONObject receiveCode(String code, String state) {
        JSONObject resp = new JSONObject();
        resp.put("result", "failed");
        if (code == null || state == null) return resp;
        if (Boolean.FALSE.equals(redisTemplate.hasKey(state))) return resp;
        redisTemplate.delete(state);
//        获取access_token
        List<NameValuePair> nameValuePairs = new LinkedList<>();
        nameValuePairs.add(new BasicNameValuePair("grant_type", "authorization_code"));
        nameValuePairs.add(new BasicNameValuePair("client_id", appId));
        nameValuePairs.add(new BasicNameValuePair("client_secret", appSecret));
        nameValuePairs.add(new BasicNameValuePair("code", code));
        nameValuePairs.add(new BasicNameValuePair("redirect_uri", redirectUri));
        nameValuePairs.add(new BasicNameValuePair("fmt", "json"));

        String getString = HttpClientUtil.get(applyAccessTokenUrl, nameValuePairs);
        if (getString == null) return resp;
        JSONObject getResp = JSONObject.parseObject(getString);
        String accessToken = getResp.getString("access_token");

//        获取open_id
        nameValuePairs=new LinkedList<>();
        nameValuePairs.add(new BasicNameValuePair("access_token",accessToken));
        nameValuePairs.add(new BasicNameValuePair("fmt", "json"));

        getString=HttpClientUtil.get(getUserOpenIDUrl,nameValuePairs);
        if(getString==null) return resp;
        getResp = JSONObject.parseObject(getString);
        String openId=getResp.getString("openid");

        if (accessToken == null || openId == null) return resp;

        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("openid_qq", openId);
        List<User> users = userMapper.selectList(queryWrapper);

//        用户已经授权，自动登录
        if (!users.isEmpty()) {
            User user = users.get(0);
            //生成JWT
            String jwt = JwtUtil.createJWT(user.getId().toString());

            resp.put("result", "success");
            resp.put("jwt_token", jwt);
            return resp;
        }

//        新用户授权，获取用户信息
        nameValuePairs = new LinkedList<>();
        nameValuePairs.add(new BasicNameValuePair("access_token", accessToken));
        nameValuePairs.add(new BasicNameValuePair("openid", openId));
        nameValuePairs.add(new BasicNameValuePair("oauth_consumer_key", appId));
        getString = HttpClientUtil.get(getUserInfoUrl, nameValuePairs);
        if (getString == null) return resp;

        getResp = JSONObject.parseObject(getString);
        String username = getResp.getString("nickname");
        String photo = getResp.getString("figureurl_1");//50*50的头像

        if (username == null || photo == null) return resp;

        for (int i = 0; i < 100; i++) {
            QueryWrapper<User> usernameQueryWrapper = new QueryWrapper<>();
            usernameQueryWrapper.eq("username", username);
            if (userMapper.selectList(usernameQueryWrapper).isEmpty()) break;
            username += (char) (random.nextInt(10) + '0');
            if (i == 99) return resp;
        }
        User user = new User(
                null,
                username,
                null,
                photo,
                1500,
                null,
                openId
        );
        userMapper.insert(user);
        //生成JWT
        String jwt = JwtUtil.createJWT(user.getId().toString());
        resp.put("result", "success");
        resp.put("jwt_token", jwt);
        return resp;
    }
}

```

##### Controller 实现

```
src/main/java/com/lxs/backend/controller/user/account/qq/QQWebController.java
```

```java
package com.lxs.backend.controller.user.account.qq;

import com.alibaba.fastjson.JSONObject;
import com.lxs.backend.service.user.account.qq.QQWebService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

@RestController
public class QQWebController {
    @Autowired
    private QQWebService qqWebService;

    @GetMapping("/api/user/account/qq/apply_code/")
    JSONObject applyCode(){
        return qqWebService.applyCode();
    }

    @GetMapping("/api/user/account/qq/receive_code")
    public JSONObject receiveCode(@RequestParam Map<String,String> data){
        String code=data.get("code");
        String state=data.get("state");
        return qqWebService.receiveCode(code,state);
    }
}

```

##### 放行Security

```
src/main/java/com/lxs/backend/config/SecurityConfig.java
```

```java
  .antMatchers(
                        "/api/user/account/token/",
                        "/api/user/account/register/",
                        "/api/user/account/acwing/web/receive_code/",
                        "/api/user/account/acwing/web/apply_code/",
                        "/api/user/account/acwing/acapp/apply_code/",
                        "/api/user/account/acwing/acapp/receive_code/",
                        "/api/user/account/qq/apply_code/",
                        "/api/user/account/qq/receive_code"
                ).permitAll()
```

