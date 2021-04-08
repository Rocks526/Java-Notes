# <a id="cj">一：IDEA常用插件</a>

### 1.1 推荐插件

- Codota

> 根据代码上下文信息，自动推断接下来要输入的代码并自动补全。
>
> 对Jdk、Spring等常用的类有API推荐，自动感知使用最多的API。

- Alibaba Java Coding Guidelines

>  阿里Java编码规范

- RestfulToolkit

> 全局搜索请求URL，根据RestURL定位代码
>
> 快捷键：Alt + 反斜杠

- Maven Helper

>  Maven依赖分析，生成依赖树

- Lombok

> 利用Java的编译时注解动态修改编译结果，自动生成get/set/builder/构造器等

- Free Mybatis Plugin / MybatisCodeHelperPro

> 关联接口和XML文件，相互跳转

- Translation

> 翻译插件

- Rainbow Brackets

> 彩色括号

- SonarLinter

> Sonar代码扫描

### 1.2 其他插件

- Alibaba Cloud Toolkit

> 快速部署项目到服务器

- Docker

>  IDEA对Docker的支持，自动识别Dockerfile文件，自动补全等

- lombok

>  自动生成set/get/toString/Construct等

- leetcode editor

> Leetcode

- GenerateAllSetter

> 一键调用对象的所有set方法,适合批量赋值的场景
>
> 快捷键Alt + Enter

- GsonFormat

> 根据JSON生成实体
>
> 快捷键Alt + S

- Properties to YAML Converter

> properties配置文件一键转YAML

- CodeGlance

> 代码编辑器迷你视图

- Solarized Themes

> 修改主题

- Power Mode II

> 增加打字效果

- Backgroud Image Plus+

> 修改背景图

- Scala

 > IDEA开发Scala插件

# <a id="ts">二：IDEA其他问题</a>

[IDEA调试](https://www.cnblogs.com/yjd_hycf_space/p/7483471.html)

[IDEA调试](https://blog.csdn.net/baidu_38634017/article/details/86484620)

[SpringBoot远程Debug](https://blog.csdn.net/weixin_42740530/article/details/89524509)

[IDEA修改快捷键为Eclipse之后Console的搜索功能丢失问题](https://blog.csdn.net/zuoyixiao/article/details/53516252)

[IDEA自动生成返回值全部带有final修饰问题](https://blog.csdn.net/weixin_45636595/article/details/102892836)

[IDEA解决一个类无法启动多个窗口的问题](https://blog.csdn.net/sinat_41905822/article/details/97813057)

- 解决IDEA打开File目录时屏幕卡死问题

IDEA会为每个项目创建一个缓存，清除掉对应的缓存即可重新初始化项目。

> 由于没找到IDEA缓存具体存放位置，因此将整个IDEA配置删除，IDEA恢复。但之前的配置和插件也都被删除。
C:\Users\lizhaoxuan\.IntelliJIdea2019.1\config