# 一：CSS介绍

### 1.1 css介绍

- css全称`层叠样式表 (Cascading Style Sheets)`
- css可以用来为网页创建样式表，通过样式表可以对网页进行装饰
- 所谓层叠，可以将整个网页想象成是一层 一层的结构，层次高的将会覆盖层次低的

### 1.2 基本语法

CSS的样式表由一个一个的样式构成，一个样式又由`选择器`和`声明块`构成。语法如下：

- 选择器 {样式名:样式值；样式名:样式值 ; }
- 示例：– p {color:red ; font-size:12px;}

### 1.3 css分类

css根据编写位置可以分为三种：

- 行内样式

可以直接将样式写到标签内部的style属性中，这种样式不用填写选择器，直接编写声明即可

```css
<p style="color: red;font-size: 30px"></p>
```

这种方式编写简单，定位准确。但是由于直接将css代码写到了html标签的内部，导致结构与表现耦合，同时导致样式不能够复用，所以这种方式使用很少。

- 内部样式表

可以直接将样式写到\<style>标签中。

```css
<style>
	p{color:red; font-size: 30px;}
</style>
```

这样使css独立于html代码，而且可以同时为多个元素设置样式，这是我们使用的比较多的一种方式。

但是这种方式，样式只能在一个页面中使用， 不能在多个页面中重复使用。

- 外部样式表

可以将所有的样式保存到一个外部的css文件中，然后通过标签将样式表引入到文件中。

```css
<link rel="stylesheet" type="text/css"
href="style.css">
```

这种方式将样式表放入到了页面的外部， 可以在多个页面中引入，同时浏览器加载 文件时可以使用缓存，这是我们开发中使用的最多的方式。

# 二：选择器

之前介绍过，css由`选择器`和`声明块`构成，选择器的作用是告诉声明块的作用加在哪一块html文本。

css提供多种选择器供我们使用，包括如下：

### 2.1 元素选择器

元素选择器也叫标签选择器，可以根据标签的名字来从页面中选取指定的元素，示例如下：

```css
p { color:red; font-size: 30px; }
```

### 2.2 类选择器

类选择器，可以根据元素的class属性值选取元素。

```css
.className { color:red; font-size: 30px;  }
```

### 2.3 ID选择器

ID选择器，可以根据元素的id属性值选取元素。

```css
#id { color:red; font-size: 30px; }
```

> 和class属性不同，id属性是不能重复的。

### 2.4 复合选择器

复合选择器，可以同时使用多个选择器， 这样可以选择同时满足多个选择器的元素。

```css
选择器1选择器2{}
例如:
div.box1{ color:red; font-size: 30px;}选中页面中具有box1这个class的div元素。
```

> 可以理解为交集选择器，多个选择器的交集

### 2.5 群组选择器

群组选择器，可以同时使用多个选择器， 多个选择器将被同时应用指定的样式。

```css
选择器1,选择器2,选择器3 { }
例如:
p,.hello,#box{ color:red; font-size: 30px;}会同时选中页面中p元素，class为hello的元素，id为box的元素。
```

> 可以理解为并集选择器，多个选择器的并集

### 2.6 通用选择器

通用选择器，可以同时选中页面中的所有元素。

```css
*{ color:red; font-size: 30px;}
```

> 通配选择器的性能比较差，尽量避免使用

### 2.7 后代选择器

后代选择器可以根据标签的关系，为处在元素内部的代元素设置样式。

```css
语法：祖先元素 后代元素{}
例如: div span { color:red; font-size: 30px;}
```

### 2.8 子元素选择器

选中指定元素的指定子元素

```css
语法：父元素 > 子元素 {}
例如: div > span {}
```

### 2.9 选择器注意事项

#### 2.9.1 选择器的继承

- 就像父亲的财产会遗传给儿子一样，在CSS中祖先元素的样式 同样也会被子元素继承。
- 继承是指应用在一个标签上的那些CSS样式会同时被应用到其内嵌标签上。
- 比如为父元素设置了字体颜色，子元素也会应用上相同的颜色。
- 并不是所有的样式都会继承，比如：背景相关的，边框相关的，定位相关的

#### 2.9.2 选择器的权重

在页面中使用CSS选择器选中元素时，经常都是一个元素同时被多个 选择器选中。如果两个选择器设置的样式不一致就好产生冲突，具体应用的样式则和选择器的权重有关。

不同的选择器有不同的权重值：

- 内联样式：权重是 1000
- id选择器：权重是 100
- 类、属性、伪类选择器：权重是 10
- 元素选择器：权重是 1
- 通配符：权重是0

计算权重需要将一个样式的全部选择器相加，权重最高的样式会被应用。

#### 2.9.3 选择器的性能

- 浏览器在解析一组选择器时，是从后边往前一个一个的解析的
- 如果选择器编写过于长的话，浏览器解析起来性能会比较差，所以在编写选择器时，越短越好。
- *通配选择器，性能也比较差，要避免使用通配选择器

# 三：声明块

### 3.1 声明块介绍

介绍完选择器后，接下来介绍声明块：

- 每一个CSS声明都是一个样式，实际上就是一个名值对的结构
- 名和值之间使用:连接
- :左边是样式的名字
- :右边是样式的值
- 每一个声明以;结尾
- 例如：color:red;

css提供的样式非常多，下面会介绍一些常用的样式，在介绍样式之前，需要先介绍下样式的值可选用的一些基本单位。

### 3.2 基本单位

#### 3.2.1 长度单位

样式Value的长度单位有以下三种：

- px：像素

像素就是构成一个图片的最小的单位，我们的屏幕就是由一个一个像素点构成。

一个像素指的就是一个像素点。

在不同的显示器中，一个像素的大小是不同的，越清晰的屏幕像素越小。

- %

可以将一个元素的样式值设置为一个百分比的值，这样浏览器将会根据父元素的值去计算出相应的值。

当父元素的值改变时，子元素的值会按照一定比例一起改变，经常用于自适应的页面。

- em

em会相对于当前元素的字体大小来计算，1em = 1font-size。

em经常用于设置文字相关的一些样式，因为当文字大小发生改变时，em会随之改变。

#### 3.2.2 颜色单位

- 颜色单词

直接使用英文单词来表示颜色，例如：red green blue orange

- RGB值

所谓RGB值就是通过红、绿、蓝三元色的不同组合来搭配出各种不同的颜色，语法如下：

1. rgb(红色,绿色,蓝色)
2. 这三个值需要一个0-255之间的值
3. 也可以使用百分数来设置RGB值，需要0%-100%之间的值

- 十六进制RGB值

也是一种RGB值的表示方式，不同的是它使用的是16进制数字来表示而不是10进制，语法如下：

1. #红色绿色蓝色
2. 这里的颜色需要一个00-ff之间的值
3. 例如：#ff0000
4. 如果颜色的是两位两位重复的，可以进行简写，比如 #aabbcc 可以写成 #abc

# 四：常用样式

css常用样式分为文本样式和背景样式两大类。

### 4.1 文本样式

#### 4.1 常用字体标签

- \<em>：em标签用于表示一段内容中的着重点
- \<strong>：strong标签用于表示一个内容的重要性

> 通常em显示为斜体，而strong显示为粗体。

- \<i>：i标签会使文字变成斜体。
- \<b>：b标签会使文字变成粗体

> 这两个标签和em和strong类似，但是这两 个标签没有语义。

- \<small>：small标签表示细则一类的旁注，通常包括免责声明、注意事项、法律限制、版权信息等。

> 浏览器在显示small标签时会显示一个比父元素小的字号。

- \<cite>：使用cite标签可以指明对某内容的引用或参考。
- 有序列表：使用ol和li来创建一个有序列表

```css
<ol>
    <li>列表项1</li>
    <li>列表项2</li>
    <li>列表项3</li>
</ol>
```

- 无序列表：使用ul和li来创建一个无序列表

```css
<ul>
    <li>列表项1</li>
    <li>列表项2</li>
    <li>列表项3</li>
</ul>
```

- 定义列表：使用dl、dd、dt来创建一个定义列表

```css
<dl>
    <dt>定义项1</dt>
        <dd>定义描述1</dd>
    <dt>定义项2</dt>
        <dd>定义描述2</dd>
    <dt>定义项3</dt>
        <dd>定义描述3</dd>
</dl>
```

#### 4.2 字体样式

- color：字体的颜色
- font-size：字体的大小，浏览器中默认的字体大小一般都是16px，而我们开发时一般会统一为12px
- font-family：设置文字的字体
- font-style：设置斜体
- font-weight：设置文字的加粗
- font-variant：小型大写字母
- font：文字的简写属性，可以同时设置所有的字体相关的样式。例如：font: [加粗 斜体 小大字母] 大小[/行高] 字体

#### 4.3 文本样式

- line-height：行高，文本默认都是在行高中垂直居中的，通过line-height可以修改行高

> 行间距 = 行高 - 字体大小

- text-transform：设置文本的大小写
- text-decoration：设置文本修饰
- text-align：设置文本对齐
- text-indent：设置首行缩进，它需要一个长度单位，如果是正值则首行向右移动，如果是负值则向左移动
- letter-spacing：字符间距
- word-spacing：单词间距

### 4.2 背景样式

- background-color：背景颜色
- background-image：背景图片，需要一个url地址作为参数
- background-repeat：设置背景图片重复方式，可选值如下
  - repeat：默认值，背景图片会平铺显示，沿x轴和y轴双方向重复
  - no-repeat：背景图片不重复
  - repeat-x：背景图片沿水平方向重复
  - repeat-y：背景图片沿垂直方向重复
- background-position：设置背景图片的位置

设置方式一：可以直接通过几个位置的关键字来设置图片的位置，例如：top，left，right，bottom，center。

可以通过以上关键字两两组合的形式，将背景图片设置到元素的任意位置，如果仅仅指定一个值，则第二个值默认是center。

设置方式二：可以直接指定两个值，来设置背景图片的偏移量

例如：background-position : x轴偏移量  y轴偏移量;

x轴偏移量，用来指定图片的水平位置，如果指定一个正值，则图片向右移动，如果指定一个负值，则图片向左移动。

y轴偏移量，用来指定图片的垂直位置，如果指定一个正值，则图片向下移动，如果指定一个负值，则图片向上移动。

- background-attachment：用来设置背景是否随页面滚动，可选值：
  - scroll：默认值，背景图片会随页面一起滚动
  - fixed：背景图片不随页面滚动，会固定在页面的指定位置，一般这种背景都会设置给body
- background：背景的简写属性，可以通过它来设置所有的背景相关的样式
- opacity：用来设置背景的不透明度，可选0-1
  - 0表示完全透明
  - 1表示完全不透明
  - 0.5半透明

# 五：盒子模型

### 5.1 元素之间的关系

- 父元素：直接包含子元素的的元素叫做父元素
- 子元素：直接被父元素包含的元素叫做子元素
- 祖先元素：直接或间接包含后代元素的元素叫做祖先元素，父元素也是祖先元素
- 后代元素：直接或间接被祖先元素包含的元素叫后代元素，子元素也是后代元素
- 兄弟元素：拥有相同父元素的元素叫做兄弟元素

### 5.2 块元素和内联元素

#### 5.2.1 块元素

- 块元素会独占页面中的一行，无论他的内容的多少
- 一般使用块元素对页面进行布局
- 常见的块元素：div，p，h1~h6

#### 5.2.2 内联元素

- 内联元素只占用自身的大小，不会独占一行
- 内联元素也叫行内元素（inline）
- 一般内联元素都是用来为文本来设置效果
- 常见的内联：span，a，img

#### 5.2.3 包裹规则

- 一般都是使用块元素去包裹内联元素，而不会使用内联去包裹块元素
- a元素可以包含任意元素，除了a本身
- p元素不能包含任何块元素

### 5.3 盒子模型

#### 5.3.1 盒子模型介绍

- CSS处理网页时，它认为每个元素都包含在一个不可见的盒子里。
- 如果把所有的元素都想象成盒子，那么我们对网页的布局就相 当于是摆放盒子，只需要将相应的盒子摆放到网页中相应的位置即可完成网页的布局。

#### 5.3.2 盒子模型组成部分

一个盒子我们会分成几个部分：

- 内容区(content)
- 内边距(padding)
- 边框(border)
- 外边距(margin)

![image-20210223201246801](http://rocks526.top/lzx/image-20210223201246801.png)

##### 5.3.2.1 内容区

- 内容区指的是盒子中放置内容的区域，也就是元素中的文本内容，子元素都是存在于内容区中的。
- 如果没有为元素设置内边距和边框，则内容区大小默认和盒子大小是一致的。
- 通过`width`和`height`两个属性可以设置内容区的大小。
- `width`和`height`属性只适用于块元素。

##### 5.3.2.2 内边距

- 顾名思义，内边距指的就是元素内容区与边框以内的空间

- 默认情况下width和height不包含padding的大小。
- 使用padding属性来设置元素的内边距。
- 例如：`padding:10px 20px 30px 40px`这样会设置元素的上、右、下、左四个方向的内边距。

> 同时在css中还提供了padding-top、padding-right、paddingright、padding-bottom分别用来指定四个方向的内边距。

##### 5.3.2.3 边框

- 可以在元素周围创建边框，边框是元素可见框的最外部。
- 可以使用border属性来设置盒子的边框：`border:1px red solid;`分别指定了边框的宽度、颜色和样式。

- 也可以使用border-top/left/right/bottom分别指定上右下左四个方向的边框。
- 和padding一样，默认width和height并不包括边框的宽度。

- 边框可以设置多种样式：
  - none（没有边框）
  - dotted（点线）
  - dashed（虚线）
  - solid（实线）
  - double（双线）
  - groove（槽线）
  - ridge（脊线）
  - inset（凹边）
  - outset（凸边）

##### 5.3.2.4 外边距

- 外边距是元素边框与周围元素相距的空间。
- 使用margin属性可以设置外边距。
- 用法和padding类似，同样也提供了四个方向的 margin-top/right/bottom/left。
- 当将左右外边距设置为auto时，浏览器会将左右外边距设置为相等，所以这行代码margin:0 auto可 以使元素居中。
- 我们不能为行内元素设置width、height、 margin-top和margin-bottom。

#### 5.3.3 盒子模型相关的样式

- 我们可以通过修改display来修改元素的性质：
  - block：设置元素为块元素
  - inline：设置元素为行内元素
  - inline-block：设置元素为行内块元素
  - none：隐藏元素（元素将在页面中完全消失）
- visibility属性主要用于元素是否可见，和display不同，使用visibility隐藏一个元 素，隐藏后其在文档中所占的位置会依然 保持，不会被其他元素覆盖。
  - visible：可见的
  - hidden：隐藏的
- 当相关标签里面的内容超出了样式的宽度和高度是，就会发生一些奇怪的事情，浏 览器会让内容溢出盒子，可以通过overflow来控制内容溢出的情况。
  - visible：默认值
  - scroll：添加滚动条
  - auto：根据需要添加滚动条
  - hidden：隐藏超出盒子的内容

# 六：页面布局

页面布局方式有`浮动`和`定位`两大类，在介绍这两种布局方式前，需要先介绍下文档流。

### 6.1 文档流

- 文档流指的是网页中的一个位置
- 文档流是网页的基础，是网页的最底层，所有的元素默认都是在文档流中排列
- 元素在文档流中默认自左向右，自上向下排列（和我们的书写习惯一致）
- 块元素在文档流中自上向下排列
- 块元素在文档流中宽度默认是父元素的100%
- 块元素在文档流中高度默认被内容撑开
- 内联元素在文档流中自左向右排列，如果一行中不足以容下所有的内联元素，则换到下一行，继续自左至右排列
- 内联元素在文档流中宽度和高度默认都被内容撑开

### 6.2 浮动

#### 6.2.1 浮动介绍

- 所谓浮动指的是使元素脱离原来的文本流，在父元素中浮动起来。
- 浮动使用float属性，可选值：
  - none：不浮动
  - left：向左浮动
  - right：向右浮动
- clear属性可以用于清除元素周围的浮动对元素的影响，也就是元素不会因为上方出现了浮动元素而改变位置，可选值：
  - left：忽略左侧浮动
  - right：忽略右侧浮动
  - both：忽略全部浮动
  - none：不忽略浮动，默认值

#### 6.2.2 浮动特点

- 元素浮动以后会完全脱离文档流
- 浮动以后元素会一直向父元素的最上方移动，直到遇到父元素的边框或者其他的浮动元素，会停止移动
- 如果浮动元素的上边是一个块元素，则浮动元素不会覆盖块元素
- 浮动元素不会超过他上边的浮动的兄弟元素，最多一边齐
- 浮动元素不会覆盖文字，文字会自动环绕在浮动元素的周围，可以通过浮动来实现文字环绕的效果

- 元素浮动以后，会使其完全脱离文档流
- 块元素脱离文档流以后，不会独占一行，宽度和高度都被内容撑开
- 内联元素脱离文档流以后会变成块元素

#### 6.2.3 高度塌陷

父元素在文档流中高度默认是被子元素撑开的，当子元素脱离文档流以后，将无法撑起父元素的高度，也就会导致父元素的高度塌陷。

父元素的高度一旦塌陷所有元素的位置将会上移，导致整个页面的布局混乱。

解决高度塌陷有三种方式：

- 开启父元素的BFC或hasLayout
- 在塌陷的父元素的最后添加一个空白的div，然后对该div进行清除浮动
- 使用after伪类，向父元素后添加一个块元素，并对其清除浮动(推荐)

### 6.3 定位

#### 6.3.1 定位介绍

使用position来设置元素的定位，可选：

- static：默认值，元素没有开启定位
- relative：开启元素的相对定位
- absolute：开启元素的绝对定位
- fixed：开启元素的固定定位

#### 6.3.2 相对定位

- 开启元素的相对定位后，如果不设置偏移量元素不会发生任何变化
- 相对定位元素相对于其自身在文档流中的位置来定位
- 相对定位的元素不会脱离文档流
- 相对定位不会改变元素的性质，块元素还是块元素，内联元素还是内联元素
- 相对定位的元素会提升一个层级

#### 6.3.3 绝对定位

- 元素设置绝对定位以后，如果不设置偏移量，元素的位置不会发生变化
- 绝对定位的元素是相对于距离他最近的开启了定位的祖先元素进行定位，如果所有的祖先元素都没开启定位，则相对于浏览器窗口进行定位
- 绝对定位的元素会完全脱离文档流
- 绝对定位会改变元素的性质。内联变块，块的高度和宽度都被内容撑开，并且不独占一行
- 绝对定位会使元素提升一个层级

#### 6.3.4 固定定位

- 固定定位是一种特殊的绝对定位，它的特点大部分都和绝对定位一样
- 不同的是，固定定位的元素永远都是相对于浏览器窗口进行定位的。并且他不会随滚动条滚动
- IE6不支持固定定位

#### 6.3.5 层级

- 定位元素 > 浮动元素 > 文档流中的元素
- 当元素开启了定位以后，可以通过z-index来设置元素的层级
- z-index值越高元素越优先显示
- 如果z-index值一样，或者都没有z-index则优先显示下边的元素
- 父元素永远不会盖住子元素

#### 6.3.6 偏移量

- 当元素开启了定位以后，可以通过偏移量来设置元素的位置
- left：元素距离定位位置的左侧距离
- top：元素距离定位位置的上边距离
- right：元素距离定位位置的右侧距离
- bottom：元素距离定位位置的底部距离
- 一般情况下，只使用两个值即可定义一个元素的位置

# 七：表格和表单

### 7.1 表格

#### 7.1.1  表格介绍

- 在Web的历史中，HTML的表格发挥了极大的作用。最初创建表格就是为了以表格的形式显示数据，后来表格 变成了一个极受欢迎的布局工具
- 但是有了CSS以后，CSS在布局网页方面实际上会更出色，所以现在我们使用表格的作用只有一个，就是用来 表示格式化的数据
- HTML中的表格可以很复杂，但是通常情况下我们不需要创建过于复杂的表格

- 使用table标签创建一个表格，tr表示表格中的一行，tr中可以编写一个或多个th或td，th表示表头，td表示表格中的一个单元格

> caption表示表格的标题，thead表示表格的头部，tbody表示表格的主体，tfoot表示表格的底部

#### 7.1.2 合并单元格

- 合并单元格指将两个或两个以上的单元格合并为一个单元格。
- 合并单元格可以通过在th或td中设置属性来完成。
  - 横向合并：colspan
  - 纵向合并：rowspan

#### 7.1.3 表格的样式

- 之前学习的很多属性都可以用来设置表格的样式
  - 比如color可以用来设置文本的颜色
  - padding可以设置内容和表格边框的距离
- text-align：设置文本的水平对齐。
- vertical-align：设置文本的垂直对齐。
  - 可选值：top、baseline、middle、bottom
- border-spacing：边框间距
- border-collapse：合并边框
  - collapse：合并边框
  - separate：不合并边框

### 7.2 表单

#### 7.2.1 表单介绍

- 网页中的表单是用来向服务器提交信息的
- 表单可以将用户填写的信息提交的服务器

![image-20210224114246410](http://rocks526.top/lzx/image-20210224114246410.png)

#### 7.2.2 表单项

- input是我们使用的最多的表单项，它可以根据不同的type属性呈现不同的状态：
  - text：文本框
  - password：密码框
  - submit：提交按钮
  - radio：单选按钮
  - checkbox：多选框
  - reset ：重置按钮

- select：用于创建一个下拉列表

- option：表示下拉列表中的列表项
- optgroup：用于为列表项分组
- textarea：用来创建一个文本域，实际效果和 文本框类似，只是可以输入多行数据
  - cols：文本域的列数
  - rows：文本域的行数
- fieldset：用来为表单项进行分组
- legend：用于指定每组的名字
- label：用来为表单项定义描述文字

