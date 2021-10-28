# 一：XML介绍

### 1.1 XML简介

- XML 指`可扩展标记语言`（EXtensible Markup Language）
- XML 是一种`标记语言`，很类似 HTML
- XML 的设计宗旨是`传输数据`，而非显示数据
- XML 标签没有被预定义。您需要`自行定义标签`。
- XML 被设计为具有`自我描述性`。
- XML 是 `W3C 的推荐标准`

### 1.2 XML树结构

XML 文档形成了一种树结构，它从“根部”开始，然后扩展到“枝叶”。

- XML自我描述性

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```

第一行是 XML 声明。它定义 XML 的版本 (1.0) 和所使用的编码 (ISO-8859-1 = Latin-1/西欧字符集)。

后面是XML描述的内容，包含根节点node，子节点to和from等，每个节点都有结束标识</>。

- XML树结构

```xml
<root>
  <child>
    <subchild>.....</subchild>
  </child>
</root>
```

XML文档必须包含根元素。该元素是所有其他元素的父元素，XML文档中的元素形成了一棵文档树，这棵树从根部开始，并扩展到树的最底端，所有元素均可拥有子元素。

### 1.3 XML语法

- 所有 XML 元素都须有关闭标签

```xml
<p>This is a paragraph</p>
<p>This is another paragraph</p>  
```

> XML 声明没有关闭标签，声明不属于XML本身的组成部分。它不是 XML 元素，也不需要关闭标签。

- XML 标签对大小写敏感

XML元素使用XML标签进行定义，XML标签对大小写敏感。在XML中，标签\<Letter>与标签\<letter>是不同的，必须使用相同的大小写来编写打开标签和关闭标签：

```xml
<Message>这是错误的</message>
<message>这是正确的</message> 
```

- XML 必须正确地嵌套

在 XML 中，所有元素都必须彼此正确地嵌套：

```xml
<b><i>This text is bold and italic</i></b>
```

- XML 文档必须有根元素

XML 文档必须有一个元素是所有其他元素的父元素。该元素称为根元素。

```xml
<root>
  <child>
    <subchild>.....</subchild>
  </child>
</root>
```

- XML 的属性值须加引号

与 HTML 类似，XML 也可拥有属性（名称/值的对），在 XML 中，XML 的属性值须加引号。

```xml
<!-- 错误示例 -->
<note date=08/08/2008>
<to>George</to>
<from>John</from>
</note> 
<!-- 正确示例 -->
<note date="08/08/2008">
<to>George</to>
<from>John</from>
</note> 
```

- 实体引用

在 XML 中，一些字符拥有特殊的意义。如果把字符 "<" 放在 XML 元素中，会发生错误，这是因为解析器会把它当作新元素的开始。特殊字符需要转义：

```xml
<!-- 错误示例 -->
<message>if salary < 1000 then</message>
<!-- 正确示例 -->
<message>if salary &lt; 1000 then</message> 
```

在 XML 中，有 5 个预定义的实体引用：

| &lt;   | <    | 小于   |
| ------ | ---- | ------ |
| &gt;   | >    | 大于   |
| &amp;  | &    | 和号   |
| &apos; | '    | 单引号 |
| &quot; | "    | 引号   |

> 在 XML 中，只有字符 "<" 和 "&" 确实是非法的。大于号是合法的，但是用实体引用来代替是一个好习惯。

- XML 中的注释

```xml
<!-- This is a comment --> 
```

- 在 XML 中，空格会被保留

HTML 会把多个连续的空格字符裁减（合并）为一个，在 XML 中，文档中的空格不会被删节。

```txt
HTML:	Hello           my name is David.
输出:	Hello my name is David.
```

- XML 以 LF 存储换行

在 Windows 应用程序中，换行通常以一对字符来存储：回车符 (CR) 和换行符 (LF)。这对字符与打字机设置新行的动作有相似之处。在 Unix 应用程序中，新行以 LF 字符存储。而 Macintosh 应用程序使用 CR 来存储新行。

### 1.4 XML元素

- 什么是XML元素？

XML 元素指的是从（且包括）开始标签直到（且包括）结束标签的部分。

元素可包含其他元素、文本或者两者的混合物。元素也可以拥有属性。

```xml
<bookstore>
<book category="CHILDREN">
  <title>Harry Potter</title> 
  <author>J K. Rowling</author> 
  <year>2005</year> 
  <price>29.99</price> 
</book>
<book category="WEB">
  <title>Learning XML</title> 
  <author>Erik T. Ray</author> 
  <year>2003</year> 
  <price>39.95</price> 
</book>
</bookstore>
```

在上例中，\<bookstore> 和 \<book> 都拥有元素内容，因为它们包含了其他元素。\<author> 只有文本内容，因为它仅包含文本。

在上例中，只有 \<book> 元素拥有属性 (category="CHILDREN")。

- XML 命名规则

XML 元素必须遵循以下命名规则：

1. 名称可以含字母、数字以及其他的字符
2. 名称不能以数字或者标点符号开始
3. 名称不能以字符 “xml”（或者 XML、Xml）开始
4. 名称不能包含空格

可使用任何名称，没有保留的字词。

- 最佳命名习惯

1. 使名称具有描述性。使用下划线的名称很不错。
2. 名称应当比较简短，比如：<book_title>，而不是：<the_title_of_the_book>。
3. 避免 "-" 字符。如果按照这样的方式进行命名："first-name"，一些软件会认为只需要提取第一个单词。
4. 避免 "." 字符。如果按照这样的方式进行命名："first.name"，一些软件会认为 "name" 是对象 "first" 的属性。
5. 避免 ":" 字符。冒号会被转换为命名空间来使用。
6. 非英语的字母比如 éòá 也是合法的 XML 元素名，不过需要留意当软件开发商不支持这些字符时可能出现的问题。

- XML 元素是可扩展的

XML 元素是可扩展，以携带更多的信息。

原始数据如下：

```xml
<note>
<to>George</to>
<from>John</from>
<body>Don't forget the meeting!</body>
</note>
```

应用程序，可将 \<to>、\<from> 以及 \<body> 元素提取出来，并产生以下的输出：

```txt
MESSAGE
To: George
From: John

Don't forget the meeting!
```

之后这个 XML 文档进行扩展，又添加了一些额外的信息：

```xml
<note>
<date>2008-08-08</date>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```

由于整体的层级结构不变，因此之前的程序依旧可以正常提取数据。

### 1.5 XML属性

XML元素可以在开始标签中包含属性，类似 HTML。属性 (Attribute) 提供关于元素的额外（附加）信息。

- 示例

属性通常提供不属于数据组成部分的信息。在下面的例子中，文件类型与数据无关，但是对需要处理这个元素的软件来说却很重要：

```xml
<file type="gif">computer.gif</file>
```

- XML属性必须加引号

属性值必须被引号包围，不过单引号和双引号均可使用：

```xml
<person sex="female">
<person sex='female'>
```

如果属性值本身包含双引号，那么有必要使用单引号：

```xml
<gangster name='George "Shotgun" Ziegler'>
```

或者可以使用实体引用：

```xml
<gangster name="George &quot;Shotgun&quot; Ziegler">
```

- XML 元素 vs 属性

```xml
<person sex="female">
  <firstname>Anna</firstname>
  <lastname>Smith</lastname>
</person> 

<person>
  <sex>female</sex>
  <firstname>Anna</firstname>
  <lastname>Smith</lastname>
</person> 
```

在第一个例子中，sex 是一个属性。在第二个例子中，sex 则是一个子元素。两个例子均可提供相同的信息。

XML属性存在的一些问题：

1. 属性无法包含多重的值（元素可以）
2. 属性无法描述树结构（元素可以）
3. 属性不易扩展（为未来的变化）
4. 属性难以阅读和维护

因此尽量使用元素来描述数据。而仅仅使用属性来提供与数据无关的信息。

- 针对元数据的 XML 属性

有时候会向元素分配 ID 引用。这些 ID 索引可用于标识 XML 元素，它起作用的方式与 HTML 中 ID 属性是一样的。示例如下：

```xml
<messages>
  <note id="501">
    <to>George</to>
    <from>John</from>
    <heading>Reminder</heading>
    <body>Don't forget the meeting!</body>
  </note>
  <note id="502">
    <to>John</to>
    <from>George</from>
    <heading>Re: Reminder</heading>
    <body>I will not</body>
  </note> 
</messages>
```

上面的 ID 仅仅是一个标识符，用于标识不同的便签。它并不是便签数据的组成部分。

因此，元数据（有关数据的数据）应当存储为属性，而数据本身应当存储为元素。

### 1.6 XML验证

拥有正确语法的 XML 被称为“形式良好”的 XML。通过 DTD 验证的 XML 是“合法”的 XML。

- 形式良好的 XML 文档

“形式良好”（Well Formed）的 XML 文档会遵守前几章介绍过的 XML 语法规则：

1. XML 文档必须有根元素
2. XML 文档必须有关闭标签
3. XML 标签对大小写敏感
4. XML 元素必须被正确的嵌套
5. XML 属性必须加引号

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```

- 验证 XML 文档

合法的 XML 文档是“形式良好”的 XML 文档，同样遵守文档类型定义 (DTD) 的语法规则：

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE note SYSTEM "Note.dtd">
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>  
```

在上例中，DOCTYPE 声明是对外部 DTD 文件的引用。下面的段落展示了这个文件的内容。

- XML DTD

DTD 的作用是定义 XML 文档的结构。它使用一系列合法的元素来定义文档结构：

```xml
<!DOCTYPE note [
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to      (#PCDATA)>
  <!ELEMENT from    (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body    (#PCDATA)>
]> 
```

> DTD详细信息参考：https://www.w3school.com.cn/dtd/index.asp

- XML Schema

W3C 支持一种基于 XML 的 DTD 代替者，它名为 XML Schema：

```xml
<xs:element name="note">

<xs:complexType>
  <xs:sequence>
    <xs:element name="to"      type="xs:string"/>
    <xs:element name="from"    type="xs:string"/>
    <xs:element name="heading" type="xs:string"/>
    <xs:element name="body"    type="xs:string"/>
  </xs:sequence>
</xs:complexType>

</xs:element> 
```

> XML Schema详细信息参考：https://www.w3school.com.cn/schema/index.asp

- XML验证器

XML提供了一个通用的文档格式验证器，避免程序处理格式错误的文档导致的异常。

不同语言有不同的实现API，在线验证地址：https://www.w3school.com.cn/xml/xml_validator.asp

# 二：Java解析XML

XML解析器提供方法来访问或修改XML文档中的数据，Java提供了多种选择来解析XML文档，以下是各种类型解析器其通常用于解析XML文档：

- DOM解析器：通过加载该文件的全部内容进行解析，并构建完整的文档树结构。
- SAX解析器：基于事件的的解析器，流式处理。
- JDOM解析器：和DOM方式类似，优化了内存占用和性能，提供更简洁的API。
- StAX解析器：和SAX方式类似，提供更简洁和优化的API。
- XPath解析器：基于XPath表达式解析。
- DOM4J解析器：一个Java开源库，支持DOM，SAX，XPath和XSLT多种方式，解析大型XML文档时具有极低的内存占用。

# 三：DOM方式

### 3.1 DOM方式介绍

- DOM介绍

文档对象模型是万维网联盟(W3C)的官方推荐。它定义了一个接口，使程序能够访问更新样式、结构和XML文档的内容。支持DOM实现该接口的XML解析器。

当使用DOM 解析器解析一个XML文档，会得到一个树形结构，其中包含的所有文档的元素。 DOM提供了多种可用于检查文档的内容和结构的函数。

- 适用场景

在以下几种情况时，应该使用DOM解析器：

1. 需要知道整个文档的结构
2. 需要将文档的部分周围(例如，可能需要某些元素进行排序)
3. 需要使用的文件中的信息超过一次

- 优点

DOM是用于处理文档结构的通用接口。它的一个设计目标是Java代码编写一个DOM兼容的解析器，运行在任何其他的DOM兼容的解析器不会有变化。

> Java提供的默认DOM解析器是javax包的解析器，但DOM解析都是面向接口编程，都是W3C定义的接口，因此换成其他DOM解析器也是ok的，代码不用变化。

- DOM标准接口

DOM定义了几个Java接口，这里是最常见的接口：

1. 节点 ： DOM的基本数据类型。
2. 元素 ： 要处理的对象绝大多数是元素。
3. Attr ： 代表元素的属性。
4. 文本 ： 元素或Attr的实际内容。
5. 文档 ： 代表整个XML文档。文档对象是通常被称为DOM树。

- DOM常用方法

使用DOM，有经常用到的几种方法：

1. Document.getDocumentElement()：返回文档的根元素。
2. Node.getFirstChild()：返回给定节点的第一个子节点。
3. Node.getLastChild()：返回给定节点的最后一个子节点。
4. Node.getNextSibling()：这些方法返回一个特定节点的下一个兄弟节点。
5. Node.getPreviousSibling()：这些方法返回一个特定节点的前一个兄弟节点。
6. Node.getAttribute(attrName)：对于给定的节点，则返回所请求的名字的属性。

### 3.2 XML解析

- 步骤

1. 导入XML相关的包
2. 创建DocumentBuilder
3. 从文件或流创建一个文档
4. 提取根元素
5. 检查属性
6. 检查子元素

- 示例XML

```xml
<?xml version="1.0"?>
<users>
    <user id="1">
        <name>lizhaoxuan</name>
        <age>23</age>
        <sex>男</sex>
    </user>
    <user id="2">
        <name>zhangsan</name>
        <age>22</age>
        <sex>男</sex>
    </user>
</users>
```

- 解析代码

```java
package com.lizhaoxuan.dom;

import lombok.SneakyThrows;
import org.junit.jupiter.api.Test;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;

/**
 * DOM方式
 * @author lizhaoxuan
 * @date 2021/10/27
 */
public class DomTest {

    @SneakyThrows
    @Test
    public void parseXml(){
        // 创建文档构造器
        DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
        DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder();
        // 读取文档
        Document document = documentBuilder.parse("E:\\source-code\\Java\\xml\\src\\main\\resources\\user.xml");
        // 格式化
        document.normalize();
        // 获取根节点
        Element root = document.getDocumentElement();
        // 获取所有用户信息
        NodeList childNodes = root.getChildNodes();
        System.out.println("根节点:" + root.getTagName() + ",用户个数:" + childNodes.getLength() + "!\n");
        for (int i=0;i<childNodes.getLength();i++){
            Node node = childNodes.item(i);
            if (node.getNodeType() == Node.ELEMENT_NODE){
                Element user = (Element) node;
                System.out.println("-------------------------------------");
                System.out.println("ID:" + user.getAttribute("id"));
                System.out.println("名称:" + user.getElementsByTagName("name").item(0).getTextContent());
                System.out.println("年龄:" + user.getElementsByTagName("age").item(0).getTextContent());
                System.out.println("性别:" + user.getElementsByTagName("sex").item(0).getTextContent());
                System.out.println("-------------------------------------");
            }
        }
    }

}
```

![image-20211027103851824](http://rocks526.top/lzx/image-20211027103851824.png)

> 子节点个数为5是因为包含了空格换行之类的数据。

### 3.3 XML修改

- 示例XML

```xml
<?xml version="1.0"?>
<users>
    <user id="1">
        <name>lizhaoxuan</name>
        <age>23</age>
        <sex>男</sex>
    </user>
    <user id="2">
        <name>zhangsan</name>
        <age>22</age>
        <sex>男</sex>
    </user>
</users>
```

- 修改代码

```java
package com.lizhaoxuan.dom;

import lombok.SneakyThrows;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;
import org.w3c.dom.*;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;
import java.io.File;

/**
 * DOM方式
 * @author lizhaoxuan
 * @date 2021/10/27
 */
public class DMOTest {

    private static DocumentBuilder documentBuilder;
    private static Transformer transformer;

    @SneakyThrows
    @Test
    @BeforeAll
    public static void init(){
        // 创建文档构造器
        DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
        documentBuilder = documentBuilderFactory.newDocumentBuilder();
        // 保存文件转换器
        TransformerFactory transformerFactory = TransformerFactory.newInstance();
        transformer = transformerFactory.newTransformer();
    }

    /**
     * 修改XML文件
     */
    @SneakyThrows
    @Test
    public void updateXml(){
        // 读取文档
        Document document = documentBuilder.parse("E:\\source-code\\Java\\xml\\src\\main\\resources\\user.xml");
        // 获取根节点
        Element root = document.getDocumentElement();
        // 获取所有用户信息
        NodeList childNodes = root.getChildNodes();
        for (int i=0;i<childNodes.getLength();i++) {
            Node node = childNodes.item(i);
            if (node.getNodeType() == Node.ELEMENT_NODE){
                Element user = (Element) node;
                // 移除id字段
                user.removeAttribute("id");
                // 将sex修改为枚举
                Node sex = user.getElementsByTagName("sex").item(0);
                sex.setTextContent(sex.getTextContent().equals("男") ? "1" : "0");
                // 添加角色名称
                Element role = document.createElement("role");
                role.setTextContent("Role" + i);
                user.appendChild(role);
                Text blank = document.createTextNode("\n");
                user.appendChild(blank);
            }
        }
        // 保存 ==> 覆盖原文件则为更新
        transformer.transform(new DOMSource(document),new StreamResult(new File("E:\\source-code\\Java\\xml\\src\\main\\resources\\user.xml")));
        // 测试输出新文档
        transformer.transform(new DOMSource(document),new StreamResult(System.out));
    }

}

```

![image-20211027145120166](http://rocks526.top/lzx/image-20211027145120166.png)

### 3.3 XML生成

```java
package com.lizhaoxuan.dom;

import lombok.SneakyThrows;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;
import org.w3c.dom.*;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;
import java.io.File;

/**
 * DOM方式
 * @author lizhaoxuan
 * @date 2021/10/27
 */
public class DMOTest {

    private static DocumentBuilder documentBuilder;
    private static Transformer transformer;

    @SneakyThrows
    @Test
    @BeforeAll
    public static void init(){
        // 创建文档构造器
        DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
        documentBuilder = documentBuilderFactory.newDocumentBuilder();
        // 保存文件转换器
        TransformerFactory transformerFactory = TransformerFactory.newInstance();
        transformer = transformerFactory.newTransformer();
    }

    /**
     * 创建XML文档
     */
    @SneakyThrows
    @Test
    public void createXml(){
        Document document = documentBuilder.newDocument();
        // 创建根节点
        Element users = document.createElement("users");
        document.appendChild(users);
        // 创建子节点
        for (int i=0;i<3;i++){
            // 用户
            Element user = document.createElement("user");
            user.setAttribute("id",String.valueOf(i));
            // 名称
            Element name = document.createElement("name");
            name.setTextContent("张三" + i);
            user.appendChild(name);
            // 年龄
            Element age = document.createElement("age");
            age.setTextContent("2" + i);
            user.appendChild(age);
            // 性别
            Element sex = document.createElement("sex");
            sex.setTextContent(i%2==0?"男":"女");
            user.appendChild(sex);
            users.appendChild(user);
        }
        // 保存
        // 保存 ==> 覆盖原文件则为更新
        transformer.transform(new DOMSource(document),new StreamResult(new File("E:\\source-code\\Java\\xml\\src\\main\\resources\\user2.xml")));
        // 测试输出新文档
        transformer.transform(new DOMSource(document),new StreamResult(System.out));
    }

}
```

![image-20211027150501432](http://rocks526.top/lzx/image-20211027150501432.png)

> 生成的XML是被压缩的，如果需要格式化，可以在里面加如空行等。

# 四：SAX方式

### 4.1 SAX方式介绍

- SAX简介

SAX是`基于事件的XML文档的解析器`。不像DOM解析器，SAX解析器不需要解析整个文档树结构。

SAX顺序扫描整个XML文档，从根节点开始，根节点的结束标签为结束，将扫描到的元素、节点、属性封装成事件通知应用程序，调用应用程序提前注册好的回调函数。

- SAX适用场景

1. XML文件非常大时，DOM方式需要将整个文件加载进内存，还原文档树，非常耗费内存，SAX方式流式处理只需要少量内存
2. 只需要提取XML文件的某一些内容，不需要读取全量数据的时候
3. XML文件的嵌套层级比较浅的时候

- SAX缺点

1. 流时处理只能顺序，无法随机选择一个节点，而且一个节点处理后无法再次处理

- ContentHandler接口

此接口是SAX解析器用来通知事件的接口，里面包含一系列的回调函数：

1.  startDocument：开始解析文件
2.  endDocument：文件解析结束
3.  startElement：开始处理一个元素
4.  endElement：处理一个元素结束
5.  characters：出现字符数据
6.  ignorableWhitespace：出现空白行
7.  processingInstruction：出现处理指令
8.  setDocumentLocator：提供可用于识别文档中的位置的定位器
9.  skippedEntity：遇到未声明的实体引用
10.  startPrefixMapping：遇到新的命名空间的映射定义
11.  endPrefixMapping：命名空间定义结束

- 属性接口

这种接口指定用于处理连接到一个元素的属性的方法。

1. getLength：返回属性的数目
2. getQName：获取名称
3. getValue：根据索引获取值
4. getValue：根据名称获取值

### 4.2 XML解析

```java
package com.lizhaoxuan.sax;

import lombok.extern.slf4j.Slf4j;
import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;
import java.util.HashMap;
import java.util.List;
import java.util.Locale;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * user.xml处理器
 */
@Slf4j
public class UserHandler extends DefaultHandler {

    private static Map<String, Boolean> tagNameMaps;

    static {
        tagNameMaps = new HashMap<>(4);
        tagNameMaps.put("name",false);
        tagNameMaps.put("age",false);
        tagNameMaps.put("sex",false);
    }

    @Override
    public void startDocument() throws SAXException {
        log.info("start parse document!");
    }

    @Override
    public void endDocument() throws SAXException {
        log.info("parse document end!");
    }

    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        if (qName.equals("user")){
            log.info("----------------------------------------");
            log.info("ID:{}", attributes.getValue("id"));
        }
        if (tagNameMaps.containsKey(qName)){
            tagNameMaps.put(qName,true);
        }
    }

    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        if (qName.equals("user")){
            log.info("----------------------------------------");
        }
    }

    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        // 遇到字符，是一个标签内容结束的时候 ==> 根据map的value判断当前是哪个标签的内容,正常只有一个
        List<Map.Entry<String, Boolean>> collect =
                tagNameMaps.entrySet().stream().filter(Map.Entry::getValue).collect(Collectors.toList());
        collect.forEach(entry -> {
            log.info("{}:{}", entry.getKey().toUpperCase(Locale.ROOT), new String(ch,start,length));
            tagNameMaps.put(entry.getKey(),false);
        });
    }
}
```

```java
package com.lizhaoxuan.sax;

import lombok.extern.slf4j.Slf4j;
import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;
import java.util.HashMap;
import java.util.List;
import java.util.Locale;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * user.xml处理器
 */
@Slf4j
public class UserHandler extends DefaultHandler {

    private static Map<String, Boolean> tagNameMaps;

    static {
        tagNameMaps = new HashMap<>(4);
        tagNameMaps.put("name",false);
        tagNameMaps.put("age",false);
        tagNameMaps.put("sex",false);
    }

    @Override
    public void startDocument() throws SAXException {
        log.info("start parse document!");
    }

    @Override
    public void endDocument() throws SAXException {
        log.info("parse document end!");
    }

    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        if (qName.equals("user")){
            log.info("----------------------------------------");
            log.info("ID:{}", attributes.getValue("id"));
        }
        if (tagNameMaps.containsKey(qName)){
            tagNameMaps.put(qName,true);
        }
    }

    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        if (qName.equals("user")){
            log.info("----------------------------------------");
        }
    }

    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        // 遇到字符，是一个标签内容结束的时候 ==> 根据map的value判断当前是哪个标签的内容,正常只有一个
        List<Map.Entry<String, Boolean>> collect =
                tagNameMaps.entrySet().stream().filter(Map.Entry::getValue).collect(Collectors.toList());
        collect.forEach(entry -> {
            log.info("{}:{}", entry.getKey().toUpperCase(Locale.ROOT), new String(ch,start,length));
            tagNameMaps.put(entry.getKey(),false);
        });
    }
}
```

![image-20211027161348084](http://rocks526.top/lzx/image-20211027161348084.png)

### 4.3 XML修改

```java
package com.lizhaoxuan.dom;

import com.lizhaoxuan.sax.UserHandler;
import com.lizhaoxuan.sax.UserModifyHandler;
import lombok.SneakyThrows;
import org.apache.commons.io.FileUtils;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;
import java.io.File;

/**
 * SAX方式
 * @author lizhaoxuan
 * @date 2021/10/27
 */
public class SAXTest {

    private static SAXParser saxParser;

    @Test
    @SneakyThrows
    @BeforeAll
    public static void init(){
        // 获取SAX解析器
        SAXParserFactory saxParserFactory = SAXParserFactory.newInstance();
        saxParser = saxParserFactory.newSAXParser();
    }

    @SneakyThrows
    @Test
    public void parseUpdate(){
        // 文件
        File file = new File("E:\\source-code\\Java\\xml\\src\\main\\resources\\user.xml");
        // 处理器
        UserModifyHandler userHandler = new UserModifyHandler();
        // 解析
        saxParser.parse(file, userHandler);
        // 获取内容，写入新的文件
        File file2 = new File("E:\\source-code\\Java\\xml\\src\\main\\resources\\user3.xml");
        FileUtils.writeLines(file2,UserModifyHandler.getContent());
        // 测试输出
        UserModifyHandler.getContent().forEach(System.out::println);
    }


}
```

```java
package com.lizhaoxuan.sax;

import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;

import java.util.ArrayList;
import java.util.List;

/**
 * user.xml修改器
 * 每个user删除id属性，添加role子标签
 */
public class UserModifyHandler extends DefaultHandler {

    /**
     * 保存解析出来的内容
     */
    private static List<String> content = new ArrayList<>(1000);

    public static List<String> getContent() {
        return content;
    }

    @Override
    public void startDocument() throws SAXException {
        content.add("<?xml version=\"1.0\" encoding=\""+ "UTF-8" + "\"?>");
    }

    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        StringBuilder value = new StringBuilder();
        value.append('<');
        value.append(qName);
        if (attributes != null) {
            int numberAttributes = attributes.getLength();
            for (int loopIndex = 0; loopIndex < numberAttributes;
                 loopIndex++){
                if (attributes.getQName(loopIndex).equals("id")){
                    // id属性跳过
                    continue;
                }
                value.append(' ');
                value.append(attributes.getQName(loopIndex));
                value.append("=\"");
                value.append(attributes.getValue(loopIndex));
                value.append('"');
            }
        }
        value.append('>');
        content.add(value.toString());
    }

    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        String value = "";
        value += "</";
        value += qName;
        content.add(value+">");

        if (qName.equals("sex")) {
            // 添加role标签
            startElement("", "role", "role", null);
            characters("测试角色".toCharArray(), 0, "测试角色".length());
            endElement("", "role", "role");
        }

    }

    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        String characterData = (new String(ch, start, length)).trim();
        if(!characterData.contains("\n") && characterData.length() > 0) {
            content.add(characterData);
        }
    }

}
```

SAX方式的XML修改，需要流式的将整个文件读取出来，读取过程中修改数据，然后保存新文件。

# 五：XPath方式

### 5.1 XPath方式介绍

`XPath简介：`

- XPath 是一门在 XML 文档中查找信息的语言。
- XPath 可用来在 XML 文档中对元素和属性进行遍历。
- XPath 是 W3C XSLT 标准的主要元素，并且 XQuery 和 XPointer 都构建于 XPath 表达之上。

`XPath特点：`

-  XPath提供了强大的路径表达式选择的节点或在XML文档中的节点列表
- XPath提供了丰富的标准函数库操纵字符串值，数值，日期
- XPath是在XSLT标准的主要元素之一

在 XPath 中，有七种类型的节点：元素、属性、文本、命名空间、处理指令、注释以及文档（根）节点。XML 文档是被作为节点树来对待的。树的根被称为文档节点或者根节点。

### 5.2 XPath语法

- 选取节点

XPath 使用路径表达式在 XML 文档中选取节点。节点是通过沿着路径或者 step 来选取的。

| 表达式   | 描述                                                       |
| :------- | :--------------------------------------------------------- |
| nodename | 选取此节点的所有子节点。                                   |
| /        | 从根节点选取。                                             |
| //       | 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。 |
| .        | 选取当前节点。                                             |
| ..       | 选取当前节点的父节点。                                     |
| @        | 选取属性。                                                 |

示例：

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<bookstore>

<book>
  <title lang="eng">Harry Potter</title>
  <price>29.99</price>
</book>

<book>
  <title lang="eng">Learning XML</title>
  <price>39.95</price>
</book>

</bookstore>
```

| bookstore       | 选取 bookstore 元素的所有子节点。                            |
| --------------- | ------------------------------------------------------------ |
| /bookstore      | 选取根元素 bookstore。注释：假如路径起始于正斜杠( / )，则此路径始终代表到某元素的绝对路径！ |
| bookstore/book  | 选取属于 bookstore 的子元素的所有 book 元素。                |
| //book          | 选取所有 book 子元素，而不管它们在文档中的位置。             |
| bookstore//book | 选择属于 bookstore 元素的后代的所有 book 元素，而不管它们位于 bookstore 之下的什么位置。 |
| //@lang         | 选取名为 lang 的所有属性。                                   |

- 谓语

谓语用来查找某个特定的节点或者包含某个指定的值的节点。谓语被嵌在方括号中。

| 路径表达式                         | 结果                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| /bookstore/book[1]                 | 选取属于 bookstore 子元素的第一个 book 元素。                |
| /bookstore/book[last()]            | 选取属于 bookstore 子元素的最后一个 book 元素。              |
| /bookstore/book[last()-1]          | 选取属于 bookstore 子元素的倒数第二个 book 元素。            |
| /bookstore/book[position()<3]      | 选取最前面的两个属于 bookstore 元素的子元素的 book 元素。    |
| //title[@lang]                     | 选取所有拥有名为 lang 的属性的 title 元素。                  |
| //title[@lang='eng']               | 选取所有 title 元素，且这些元素拥有值为 eng 的 lang 属性。   |
| /bookstore/book[price>35.00]       | 选取 bookstore 元素的所有 book 元素，且其中的 price 元素的值须大于 35.00。 |
| /bookstore/book[price>35.00]/title | 选取 bookstore 元素中的 book 元素的所有 title 元素，且其中的 price 元素的值须大于 35.00。 |

- 通配符

XPath 通配符可用来选取未知的 XML 元素。

| 通配符 | 描述                 |
| :----- | :------------------- |
| *      | 匹配任何元素节点。   |
| @*     | 匹配任何属性节点。   |
| node() | 匹配任何类型的节点。 |

| /bookstore/* | 选取 bookstore 元素的所有子元素。 |
| ------------ | --------------------------------- |
| //*          | 选取文档中的所有元素。            |
| //title[@*]  | 选取所有带有属性的 title 元素。   |

- 运算符

通过在路径表达式中使用“|”运算符，可以选取若干个路径。

| 路径表达式                       | 结果                                                         |
| :------------------------------- | :----------------------------------------------------------- |
| //book/title \| //book/price     | 选取 book 元素的所有 title 和 price 元素。                   |
| //title \| //price               | 选取文档中的所有 title 和 price 元素。                       |
| /bookstore/book/title \| //price | 选取属于 bookstore 元素的 book 元素的所有 title 元素，以及文档中所有的 price 元素。 |

### 5.2 XML解析

```java
package com.lizhaoxuan.dom;

import lombok.SneakyThrows;
import org.junit.jupiter.api.Test;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.xpath.XPath;
import javax.xml.xpath.XPathConstants;
import javax.xml.xpath.XPathFactory;
import java.io.File;

/**
 * XPath测试
 * @author lizhaoxuan
 * @date 2021/10/27
 */
public class XPathTest {

    @SneakyThrows
    @Test
    public void parseXml(){
        // 获取文档
        DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
        DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder();
        Document document = documentBuilder.parse(new File("E:\\source-code\\Java\\xml\\src\\main\\resources\\user.xml"));
        // 获取XPath解析器
        XPathFactory xPathFactory = XPathFactory.newInstance();
        XPath xPath = xPathFactory.newXPath();
        // 提取所有用户信息
        NodeList nodeList = (NodeList) xPath.compile("/users/user").evaluate(document, XPathConstants.NODESET);
        System.out.println("用户个数:" + nodeList.getLength() + "!\n");
        for (int i=0;i<nodeList.getLength();i++){
            Node node = nodeList.item(i);
            if (node.getNodeType() == Node.ELEMENT_NODE){
                Element user = (Element) node;
                System.out.println("-------------------------------------");
                System.out.println("ID:" + user.getAttribute("id"));
                System.out.println("名称:" + user.getElementsByTagName("name").item(0).getTextContent());
                System.out.println("年龄:" + user.getElementsByTagName("age").item(0).getTextContent());
                System.out.println("性别:" + user.getElementsByTagName("sex").item(0).getTextContent());
                System.out.println("-------------------------------------");
            }
        }
    }

}
```

![image-20211027202453845](http://rocks526.top/lzx/image-20211027202453845.png)

# 六：DMO4J方式







































