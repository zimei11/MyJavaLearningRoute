# Vue3+BootStrape学习

## 组件的调用

被调用的组件里使用`<slot></slot>`

调用的组件直接用结构出来的标签包裹

### 子拿父数据

> `reactive`定义对象，不可重新赋值

**父** 先申明并return出数据。数据为对象用reactive存，里面有数据用户id,用户全名，用户名，用户姓，用户的粉丝数，该用户是否被关注。

![image-20220709125556812](https://img.zimei.xyz/202207091255858.png)

**父** 绑定属性

绑定`user`属性

> `v-bind:`或`:`绑定属性
>
> `v-bind`简写成`:`

![image-20220709125739711](https://img.zimei.xyz/202207091257745.png)

**子** props：存储父组件传递给子组件的数据

![image-20220709125929059](https://img.zimei.xyz/202207091259091.png)

### 父拿子数据

> `v-if`是否

判断当前用户是否被关注，**子**添加`v-if`

![image-20220709130952565](https://img.zimei.xyz/202207091309603.png)

**子** 先申明并return出数据。这里数据为关注，没关注。

> context.emit()：触发父组件绑定的函数
>
> setup中要传入`context`

![image-20220709134724129](https://img.zimei.xyz/202207091347172.png)

**子** 绑定事件

绑定`follow`事件

> `v-on:click`或``@click`属性：绑定事件
>
> `v-on`简写为`@`

![image-20220709132331900](https://img.zimei.xyz/202207091323952.png)

**父** 定义相关函数

定义follow，unfollow函数并且把他们返回

![image-20220709134328813](https://img.zimei.xyz/202207091343851.png)

![image-20220709133508686](https://img.zimei.xyz/202207091335722.png)

**父** 事件触发

触发 父 `follow`绑定的函数

![image-20220709132828725](https://img.zimei.xyz/202207091328761.png)

## 动态计算

> `computed`：动态计算某个数据
>
> 需要 导入 `computed`

![image-20220709125025740](https://img.zimei.xyz/202207091250839.png)

需要`setup()`并且因为不支持`this`所以先传值props，再用computed动态计算。

## 小结

> 摘抄自acwing Web框架课

### script部分

`export default`对象的属性：

*   `name`：组件的名称
*   `components`：存储`<template>`中用到的所有组件
*   `props`：存储父组件传递给子组件的数据
*   `watch()`：当某个数据发生变化时触发
*   `computed`：动态计算某个数据
*   `setup(props, context)`：初始化变量、函数
    *   `ref`定义变量，可以用`.value`属性重新赋值
    *   `reactive`定义对象，不可重新赋值
    *   `props`存储父组件传递过来的数据
    *   `context.emit()`：触发父组件绑定的函数

* * *

### template部分

*   `<slot></slot>`：存放父组件传过来的`children`。
*   `v-on:click`或`@click`属性：绑定事件
*   `v-if`、`v-else`、`v-else-if`属性：判断
*   `v-for`属性：循环，`:key`循环的每个元素需要有唯一的`key`
*   `v-bind:`或`:`：绑定属性

* * *

### style部分

*   `<style>`标签添加`scope`属性后，不同组件间的css不会相互影响。

* * *

### 第三方组件

*   `view-router`包：实现路由功能。
*   `vuex`：存储全局状态，全局唯一。
    *   `state`: 存储所有数据，可以用`modules`属性划分成若干模块
    *   `getters`：根据`state`中的值计算新的值
    *   `mutations`：所有对`state`的修改操作都需要定义在这里，不支持异步，可以通过`$store.commit()`触发
    *   `actions`：定义对`state`的复杂修改操作，支持异步，可以通过`$store.dispatch()`触发。注意不能直接修改`state`，只能通过`mutations`修改`state`。
    *   `modules`：定义`state`的子模块