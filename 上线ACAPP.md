# 上线ACAPP

我的源码[zimei11github](https://github.com/zimei11/KingOfBots/tree/afb46c93beec2991f4df272613a8fecf8c0d6b92)（只有上节课Web版，acapp element-plus版正在测试，botstrap版见y总仓库[xuecan yan / kob · GitLab](https://git.acwing.com/yxc/kob/-/tree/3e707445a8e7161c0b24ecd746c6e2f038ee743a)）

网页版体验地址：www.zimei.fun

## 本节课任务

1. Nginx配置跨域

   不管用自己域名还是y总提供的域名都会有跨域问题，因为acwing网址是`www.acwing.com`，而y总提供的域名是 `appxxx.acapp.acwing.com.cn`，两者域名不一致。

2. 修复Bootstrap污染acwing样式问题

   Bootstrap作为第三方UI库，无法将其css作用域限制在局部。最重要的与acwing官网样式冲突，还容易影响到其他acapp样式。所以需要删除Bootstrap的所有引用，重构前端。

3. 重构项目前端

   自己写前端请注意，路由需要更改。因为acapp打开时，地址不应该发生变化，所以需要重构路由。然后再重写所有css样式。

   前端不想写的同学，请直接拉取y总本节课地址仓库[xuecan yan / kob · GitLab](https://git.acwing.com/yxc/kob/-/tree/3e707445a8e7161c0b24ecd746c6e2f038ee743a)，安装依赖后，改下token与网址就可以直接用了。打包后注意修改js文件，为上线acapp做准备。

4. 关于Element-Plus组件库

   实测后，直观上与acwing样式并不冲突，但是y总禁用了acapp的滚动条（鼠标滚轮），导致页面太长的话需要自己加滚轮，或者重新设计页面布局

   

## 致谢

非常感谢@一万小时定律 帮我指出并修复，游览器插件导致未能正确获取websocket，的bug。

非常感谢@小熊熊 帮我提供前端滚轮，以及全屏的解决方案。

😘😘😘

## 效果图

![1 (2)](https://img.zimei.fun/202209011416800.png)

![image-20220901134113781](https://img.zimei.fun/202209011341932.png)