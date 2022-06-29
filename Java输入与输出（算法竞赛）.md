# Java输入与输出（算法竞赛）

> 笔记基于AcWing Spring Boot 框架课整理

## 大规模数据

使用下面的方式进行输入输出效率较高，输入输出规模较大时使用。注意需要抛异常。

```java
package com.lxs;

import java.io.*;

public class Main
{
    public static void main(String[] args) throws IOException//输入输出都需要抛异常
    {
        //输入 直白翻译：new缓冲读（new输入流读（系统输入））
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        String str=bufferedReader.readLine();

        //输出 直白翻译：new缓冲写（new输出流读（系统输出））
        BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(System.out));
        bufferedWriter.write(str);
        bufferedWriter.flush();//需要刷新缓冲区
    }
}
```

## `String`类

### 格式化字符串

类似于C++中的sprintf。用于输出指定位数小数，输出特定格式字符串等。

```java
package com.lxs;

public class Main
{
    public static void main(String[] args)
    {
        //处理小数位数
        System.out.printf("%.2f\n",3.14159);//输出3.14，保留两位小数

        //格式化字符串
        String s=String.format("括号中间有个数字:[%d]",3);
        System.out.println(s);//结果为：括号中间有个数字:[3]
    }
}
```

### 访问`String`中的字符

```Java
String str="Hello World";
for (int i = 0; i < str.length(); i++)
{
    System.out.print(str.charAt(i)+" ");
    //结果为：H e l l o   W o r l d 
}
```

常用API：

* `split(String regex)`：分割字符串

  ```java
  String [] str="hello world".split(" ");//字符串数组存放由" "划分的每个字符串 
  for(String x:str)
  {
      System.out.println(x);//hello '换行' world
  }
  ```

* `indexOf(char c)`、`indexOf(String str)`：查找，找不到返回-1

* `equals()`：判断两个字符串是否相等，注意不能直接用`==`

* `compareTo()`：判断两个字符串的字典序大小，负数表示小于，0表示相等，正数表示大于

* `startsWith()`：判断是否以某个前缀开头

* `endsWith()`：判断是否以某个后缀结尾

* `trim()`：去掉首位的空白字符

* `toLowerCase()`：全部用小写字符 `toUpperCase()`：全部用大写字符

* `replace(char oldChar, char newChar)`：替换字符

* `replace(String oldRegex, String newRegex)`：替换字符串

* `substring(int beginIndex, int endIndex)`：返回`[beginIndex, endIndex)`中的子串

* * *

## `StringBuilder`、`StringBuffer`

`String`不能被修改，如果打算修改字符串，可以使用`StringBuilder`和`StringBuffer`。

`StringBuffer`线程安全，速度较慢；`StringBuilder`线程不安全，速度较快。

```java
StringBuffer str=new StringBuffer("Hello");
str.append("World");//附加字符串
System.out.println(str);//HelloWorld

str.setCharAt(0, (char) (str.charAt(0)+1));//将原来字符串第一个字符加1
System.out.println(str);//IelloWorld（H加1变成I）
```

常用API：

*   `reverse()`：翻转字符串