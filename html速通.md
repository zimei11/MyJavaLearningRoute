# `HTML`速通

> 学习后端的过程中不可避免的需要了解一些前端知识，本博文用于速查HTML各种标签，仅供参考。
>
> 文章基于[青空の霞光的个人空间_哔哩哔哩_bilibili](https://space.bilibili.com/37737161)整理

## 基本语法

### 基本结构

* **头部**：一般包含页面的标题、页面的图标、还有页面的一些设置，也可以在这里导入css、js等内容。
* **主体**：整个页面所有需要显示的内容全部在主体编写。

```html
<!DOCTYPE html>
<html lang="en">
  <!-- 头部 -->
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
  <!-- 主体 -->
<body>

</body>
</html>
```

### 网站图标

将任意图标放入到项目目录中，并命名为icon.png，在HTML**头部**添加以下内容：

```html
<link rel="icon" href="icon.png" type="image/x-icon" />
```

### 显示图片

**主体**中添加图片

```html
<img width="300" src="image.xxx" alt="当图片加载失败时，显示的文本">
```

### `div`标签来分割页面

```html
<body>
    <div>我是第一块</div>
    <div>我是第二块</div>
</body>
```

### `span`按元素划分空间

```html
<body>
    <div>
        <span>我是第一块第一个部分</span>
        <span>我是第一块第二个部分</span>
    </div>
    <div>我是第二块</div>
</body>
```

### `p`段落标签文章分段

```html
<body>
    <p>
        这是第一段
        你好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
        你好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
    </p>
    <p>
        这是第二段
        你好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
        你好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
    </p>
</body>
```

### 使用转义字符显示特殊字符

![点击查看源网页](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.51wendang.com%2Fpic%2F208288d7561926f359c6be84%2F1-352-jpg_6_0_______-356-0-0-356.jpg&refer=http%3A%2F%2Fwww.51wendang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1639877607&t=bcc1fcfe8bae53e90c365a4fd8c00a1c)

**注意：**多个连续的空格字符只能被识别为一个，如果需要连续多个必须使用转义字符，同时也不会识别换行，换行只会变成一个空格，需要换行必须使用`br`标签。

## HTML常用标签

前面我们已经了解了HTML的基本语法规范，那么现在我们就来看看，有哪些常用的标签吧，首先是换行和分割线：

### `br` 换行与`hr` 分割线

```html
<body>
    <div>
        我是一段文字<br>我是第二段文字
    </div>
    <hr>
    <div>我是底部文字</div>
</body>
```

### 标题

```html
<body>
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
<p>我是正文内容，真不错。</p>
</body>
```

### 超链接指向其他网站

```html
<a href="https://www.bilibili.com">点击访问小破站</a>
```

### 指定页面锚点进行滚动

```html
<body>
<a href="#test">跳转锚点</a>
<img src="image.jpeg" width="500">
<img src="image.jpeg" width="500">
<img src="image.jpeg" width="500">
<img src="image.jpeg" width="500">
<div id="test">我是锚点</div>
<img src="image.jpeg" width="500">
<img src="image.jpeg" width="500">
<img src="image.jpeg" width="500">
</body>
```

每个元素都可以有一个id属性，我们只需要给元素添加一个id属性，就使用a标签可以跳转到一个指定锚点。

### 列表

无序列表`ul`

```html
<ul>
    <li>一号选项</li>
    <li>二号选项</li>
    <li>三号选项</li>
    <li>四号选项</li>
    <li>五号选项</li>
</ul>
```

有序列表`ol`

```html
<ol>
    <li>一号选项</li>
    <li>二号选项</li>
    <li>三号选项</li>
    <li>四号选项</li>
    <li>五号选项</li>
</ol>
```

### 表格

```html
<table>
    <thead>
    <tr>
        <th>学号</th>
        <th>姓名</th>
        <th>性别</th>
        <th>年级</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>0001</td>
        <td>小明</td>
        <td>男</td>
        <td>2019</td>
    </tr>
    <tr>
        <td>0002</td>
        <td>小红</td>
        <td>女</td>
        <td>2020</td>
    </tr>
    </tbody>
</table>
```

虽然这样生成了一个表格，但是这个表格并没有分割线，并且格式也不符合我们想要的样式，那么如何才能修改这些基础属性的样式呢，我们就需要聊聊CSS了。

## `HTML`表单

表单就像其名字一样，用户在页面中填写了对应的内容，点击按钮就可以提交到后台，比如登陆界面，就可以使用表单来实现：

### 创建输入框

```html
<label>
    我是输入框
    <input type="text">
</label>
```

对于一个输入框，我们一般会将其包括在一个`lable`标签中，它和span效果一样，但是我们点击前面文字也能快速获取输入框焦点。

```html
<body>
<div>登陆我们的网站</div>
<hr>
<div>
    <label>
        账号：
        <input type="text">
    </label>
</div>
<div>
    <label>
        密码：
        <input type="password">
    </label>
</div>
</body>
```

输入框可以有很多类型，我们来试试看password，现在输入内容就不会直接展示原文了。

### 创建按钮

有以下几种方式，在学习JavaWeb时，我们更推荐第二种方式，我们后面进行登陆操作需要配合表单使用：

```html
<button>登陆</button>
<input type="submit" value="登陆">
<input type="button" value="登陆">
```

### 实现输入多行文本

```html
<label>
    这是我们的文本框<br>
    <textarea placeholder="文本内容..." cols="10" rows="10"></textarea>
</label>
```

我们还可以指定默认的行数和列数，拖动左下角可以自定义文本框的大小。

### 添加勾选框

多选框：

```html
<label>
    <input type="checkbox">
    我同意本网站的隐私政策
</label>
```

单选框：

```html
<label>
    <input type="radio" name="role">
    学生
</label>
<label>
    <input type="radio" name="role">
    教师
</label>
```

这里需要使用name属性进行分组，同一个组内的选项只能选择一个。

### 下拉列表

```html
<label>
    登陆身份：
    <select>
        <option>学生</option>
        <option>教师</option>
    </select>
</label>
```

默认选取的是第一个选项，我们可以通过`selected`属性来决定默认使用的是哪个选项。

## 测试

### 基本语法演示

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <!-- 网站标题 -->
    <title>你好</title>

    <!-- 网站图标 -->
    <link rel="icon" href="icon.png" type="image/x-icon" />
</head>

<body>
    Hello World<br>

    <!-- 跳转到页面某个元素位置，与id属性配合使用 -->
    <a href="#test">跳转到图片</a>

    <!-- 换行 -->
    <br>

    <!-- <div>分块 <span>分部分 -->
    <div>
        <span>我是第一块的第一部分</span>
        <span>我是第一块的第二部分</span>
    </div>
    <div>
        我是第二块
    </div>

    <div>
        <!-- <p>标签 定义段落 -->
        <p>
            这是第一段
            你好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
            你好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
        </p>
        <p>
            这是第二段
            我好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
            我好，你看这个彬彬啊，才喝几杯就醉了，真是逊啊。
        </p>
    </div>
    <div>
        <!-- 转义字符 -->
        &lt;
        t&nbsp;&nbsp;&nbsp;&nbsp;test<br>
        <!-- 点击跳转其他网页 -->
        <a href="https://www.cnblogs.com/yasmi/articles/4884396.html">博客园转义字符相关参考文章</a>
    </div>

    <div>
        <h1>一级标题</h1>
        <h2>二级标题</h2>
    </div>

    <!-- 插入图片，id：HTML元素标签的标志，可以用来唯一表示该元素的标识符-->
    <img id="test" width="300" src="http://img.zimei.fun/202206291737738.png" alt="加载失败">

    <!-- 分隔线 -->
    <hr>
    <div>我是底部文字</div>

    <div>
        <!-- 无序列表 -->
        <ul>
            <li>一号选项</li>
            <li>二号选项</li>
            <li>三号选项</li>
        </ul>
        <!-- 有序列表 -->
        <ol>
            <li>一号选项</li>
            <li>二号选项</li>
            <li>三号选项</li>
        </ol>
    </div>

    <div>
        <!-- 表格 -->
        <table border>
            <thead>
                <tr>
                    <th>学号</th>
                    <th>姓名</th>
                    <th>性别</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>0001</td>
                    <td>小红</td>
                    <td>女</td>
                </tr>
                <tr>
                    <td>0002</td>
                    <td>小芳</td>
                    <td>女</td>
                </tr>
            </tbody>
        </table>
    </div>
</body>

</html>
```

### 登录界面演示

简易的登陆页面：

```html
<body>
    <h1>登陆我们的网站</h1>
    <form>
        <div>
            <label>
                账号：
                <input type="text" placeholder="Username...">
            </label>
        </div>
        <div>
            <label>
                密码：
                <input type="password" placeholder="Password...">
            </label>
        </div>
        <br>
        <a href="https://www.baidu.com">忘记密码</a>
        <br>
        <br>
        <div>
            <input type="submit" value="登陆">
        </div>
    </form>
</body>
```

表单一般使用`form`标签将其囊括。

![1](http://img.zimei.fun/202206291803972.png)

------

当然，HTML的元素远不止我们所提到的这些，有关更多HTML元素的内容，可以自行了解。🎈🎈🎈