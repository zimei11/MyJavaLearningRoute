# `WebSocket`实现游戏匹配系统

[toc]

> 博客基于`AcWing SpringBoot`框架课（6.1实现微服务：匹配系统），本文重点分析后端开发部分。
>
> 本文章参考了很多博客，在文章末尾都有标注。在此特意说明，并表示感谢(●'◡'●)。

## 前置知识

### 概念

> #### `WebSocket`
>
> **`WebSocket`**是一种在单个[TCP](https://baike.baidu.com/item/TCP)连接上进行[全双工](https://baike.baidu.com/item/全双工)通信的协议。 `WebSocket`通信协议于2011年被[IETF](https://baike.baidu.com/item/IETF)定为标准RFC 6455，并由RFC7936补充规范。`WebSocket API`也被[W3C](https://baike.baidu.com/item/W3C)定为标准。
>
> `WebSocket`使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在`WebSocket API`中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。
>
> --百度百科

> #### 全双工通信
>
> 全双工通信允许数据同时在两个方向上传输，即有两个信道，因此允许同时进行双向传输。
> 全双工通信是两个单工通信方式的结合，要求收发双方都有独立的接收和发送能力。
> 全双工通信效率高，控制简单，但造价高。
>
> 计算机之间的通信是全双工方式。一般的电话、手机也是全双工的系统,因为在讲话时可以听到对方的声音。

## 为什么需要`WebSocket`

#### `WebSocket`特点

了解计算机网络协议的人，应该都知道：HTTP 协议是一种无状态的、无连接的、单向的应用层协议。

它采用了请求/响应模型。通信请求只能由客户端发起，服务端对请求做出应答处理。

这种通信模型有一个弊端：HTTP 协议无法实现服务器主动向客户端发起消息。



这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。大多数 Web 应用程序将通过频繁的异步 JavaScript 和 XML（AJAX）请求实现长轮询。轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）。



因此，工程师们一直在思考，有没有更好的方法。WebSocket 就是这样发明的。

WebSocket 连接允许客户端和服务器之间进行全双工通信，以便任一方都可以通过建立的连接将数据推送到另一端。

WebSocket 只需要建立一次连接，就可以一直保持连接状态。这相比于轮询方式的不停建立连接显然效率要大大提高。

#### 匹配功能实现的需要

##### 游戏匹配需求分析

匹配系统（Matching System）是另外一个服务（进程）。后端（Server）接收到用户的请求之后，发送给匹配系统。

两个用户用户匹配成一组后，将结果返回给前端（用户端）。见下图：

![image-20220812184427513](https://img.zimei.fun/202208121844570.png)

特点：

1. 匹配时间未知
2. 匹配成功后后端结果需要立即返回给前端

`http`一问一答式（问完立即返回结果），但是本模块需要发送一个请求，等未知的时间返回结果（可多结果）。所以需要新的协议来处理请求。



##### 防作弊与生成相同游戏地图

之前地图生成与蛇的移动、蛇的判定都在用户端本地执行。虽然这样是高效的，并且极大的减轻后台服务器压力，但是用户可以伪造数据实现作弊。

用户端本地判断如吃鸡等各种fps游戏，在本地操作非常频繁，后端处理压力过大。所以可以直接在前端判断，但是容易造成外挂横飞。

而本游戏是回合制游戏通信量比较少，完全放在后端处理是没有问题的。因此本系统服务器端判断完之后再把结果返还给前端、前端只是用来花动画的，不做任何判定逻辑。

以后设计时应该尽可能在游戏体验在用户作弊之间做一个权衡。

 

整个游戏数据传输流程，如下图： 

![1](https://cdn.acwing.com/media/article/image/2022/08/10/113094_2a675db018-1.png)

------

## `WebSocket`实现方式

其中，每一个建立的WebSocket连接都会在后端维护起来（通过WebSocket类的方式，即创建一个个WebSocket类实例，来存储）。此时WebSoket就是多线程。

> 前后端通信使用JSON会更加方便
>
> 本项目使用fastjson
>
> 两个作用
>
> - 提供服务器端、安卓客户端两种解析工具，性能表现较好。
>
> - 提供了 toJSONString () 和 parseObject () 方法来将 Java 对象与 JSON 相互转换。
>
>   toJSONString方法即可将对象转换成 JSON 字符串，
>
>   parseObject 方法将 JSON 字符串转换成对象.

### 创建，关闭连接，收发数据

借助WebSocket类，实现建立与关闭连接，从前端接受信息，见下面代码：

```java
@Component
@ServerEndpoint("/websocket/{token}")  // 注意不要以'/'结尾
public class WebSocketServer {
    @OnOpen
    public void onOpen(Session session, @PathParam("token") String token) {
        // 建立连接
    }
    @OnClose
    public void onClose() {
        // 关闭链接
    }

    @OnMessage
    public void onMessage(String message, Session session) {
        // 从Client接收消息
    }

    @OnError
    public void onError(Session session, Throwable error) {
        error.printStackTrace();
    }
}
```
后端向前端发数据需要用到WebSocket的API：`Session`

每个连接其实是使用Session来维护的

```java
private Session session = null;

@OnOpen
public void onOpen(Session session, @PathParam("token") String token) throws IOException {
    // 建立连接
    this.session = session;
}
public void sendMessage(String message) {//异步通信加锁
    synchronized (this.session) {
        try {
            this.session.getBasicRemote().sendText(message);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### 维护单个连接信息

我们需要知道每个连接是哪位用户发起的。创建局部变量User。

### 维护所有连接信息

我们需要能够根据用户的id，分析出用户对应连接的具体信息。需要创建静态变量（线程安全`ConcurrentHashMap`，一种Map）来存储id=>对应连接 这个映射。

当接收到前端发来的匹配请求后，我们需要将前端发送请求用户的信息加入到匹配池中（线程安全`CopyOnWriteArraySet`,一种Set）。以此来存储匹配池中用户的信息。

### 开始对局

接受到前端发来的匹配请求后，执行`startMatching();`函数来新建匹配。

```java
private void startMatching() {
    matchPool.add(this.user);//加入匹配池

    while (matchPool.size()>=2){
        Iterator<User> it=matchPool.iterator();
        User a=it.next(),b=it.next();
        matchPool.remove(a);
        matchPool.remove(b);

        Game game=new Game(13,14,20);
        game.createMap();

        JSONObject respA=new JSONObject();
        respA.put("event","start-matching");
        respA.put("opponent_username",b.getUsername());
        respA.put("opponent_photo",b.getPhoto());
        respA.put("gamemap",game.getG());
        users.get(a.getId()).sendMessage(respA.toJSONString());

        JSONObject respB=new JSONObject();
        respB.put("event","start-matching");
        respB.put("opponent_username",a.getUsername());
        respB.put("opponent_photo",a.getPhoto());
        respB.put("gamemap",game.getG());
        users.get(b.getId()).sendMessage(respB.toJSONString());

    }
}
```

执行`stopMatching()`函数来关闭匹配

```java
private void stopMatching() {
    matchPool.remove(this.user);//取出匹配池
}
```

具体代码见：[xuecan yan / kob · GitLab](https://git.acwing.com/yxc/kob/-/blob/0175704aeda1a2e51f909c6d8c63917065c81046/backend/src/main/java/com/kob/backend/consumer/WebSocketServer.java)

## 加载游戏地图

地图原本是在用户端本地生成，这是不合理的。（作弊，

我们需要将地图存在服务器端，并将生成的地图返回给前端。

具体实现就是把 之前前端写的代码，用java复现一遍。



最后在将生成的地图发送给前端

```java
respA.put("gamemap",game.getG());
users.get(a.getId()).sendMessage(respA.toJSONString());
```

代码见：[xuecan yan / kob · GitLab](https://git.acwing.com/yxc/kob/-/blob/0175704aeda1a2e51f909c6d8c63917065c81046/backend/src/main/java/com/kob/backend/consumer/utils/Game.java)

## 总结

大功告成o(〃＾▽＾〃)o

前端部分同理，不再介绍~~（偷懒）~~。

## 参考文章

[WebSocket 详解教程 - 静默虚空 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jingmoxukong/p/7755643.html)

[工程课 SpringBoot-6.1. 上课笔记 - AcWing一万小时定律 ,](https://www.acwing.com/solution/content/131500/)