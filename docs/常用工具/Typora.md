# 一：Typora基本使用

### 1.1 Typora介绍

Typora是一款免费的MarkDown编辑器，支持所有MarkDown语法，并且支持导出pdf、html等，非常适合用来总结、记笔记。

### 1.2 Typora常用语法

- 标题：Typora支持六级标题，通过`#`后面加标题名称即可。
- 排序列表：Typora支持无序列表和有序列表，无序列表通过中划线`-`，有序列表通过数字`1/2/3`等。
- 代码块：Typora支持插入代码块，通过三个反引号实现。
- 加粗：通过文字前后各加两个星号`*`实现。
- 删除线：通过文字前后各加两个波浪号`~~`实现。
- 超链接：通过两个中括号外加两个小括号实现，`[文件内容](链接内容)`。
- 水平分割线：三个或三个以上中划线`-`或星号`*`实现。
- 表格：虽然MarkDown语法也支持插入表格，但推荐鼠标右键选择插入表格，方便控制行和列。

# 二：Typora替换主题



### 2.1 Typora如何配置主题

1. 菜单栏里的主题选型里可以修改Typora主题，系统内置了几种。
2. 菜单栏的`文件-偏好设置-外观-获取主题 `可以跳转Typora的主题仓库，里面可以选择下载喜欢的主题。
3. 下载完成后，将下载的内容拷贝到`文件-偏好设置-外观-打开主题文件夹`目录下，重启Typora即可选择添加的主题。

> Typora的不同主题下载的出来的内容不同，一般是一个文件夹和一个css文件，部分主题只有一个css文件。

### 2.2 Typora推荐主题

- 官方主题仓库：https://theme.typora.io/
- Drake：https://theme.typora.io/theme/Drake/
- Fluent：https://theme.typora.io/theme/Fluent/
- Vue：https://theme.typora.io/theme/Vue/

- FloraDark：https://github.com/HeXueZhi/typora-theme-FloraDark
- Gitbook：https://theme.typora.io/theme/Gitbook/
- Ursine：https://theme.typora.io/theme/Ursine/
- Cobalt：https://theme.typora.io/theme/cobalt/
- Inside：https://theme.typora.io/theme/Inside/

> 推荐Drake/Fluent/Vue，Drake的字体偏小，可通过`偏好设置-外观设置`进行调整。
>
> 以上主题的百度网盘链接如下：
>
> 链接：https://pan.baidu.com/s/1ujw9_yKFP09GiQRS9opwzg 
> 提取码：3ks0 
> 复制这段内容后打开百度网盘手机App，操作更方便哦

# 三：Typora配置图床

### 3.1 Typora图床介绍

Typora默认的上传图片方式是存储本地，因此当md笔记远程访问时图片就会不可见，为了解决这个问题，可以将md文件里的图片上传到图床，实现远程可访问。

> 注意：Typora在0.9.86版本之后才可以将图片上传图床，在`帮助 - 关于/检查更新`菜单里可以查看当前版本和升级版本。

### 3.2 Typora配置图床

- 图床选择

Typora支持默认图床PicGo，但由于在国内访问速度很慢，因此采用第三方图床，常见第三方图床有：

1. 七牛云：支持对象存储服务，每个月10G免费额度，注册赠送临时域名一个月，可以自己购买域名进行备案绑定。
2. 微博图床

>  Typora在0.9.96版本之后支持第三方图床，此处以七牛云图床为例。

七牛云注册完成后，根据指导创建对象存储空间，绑定自定义域名。

- Typora配置

1. 打开`文件 - 偏好设置`菜单，选择图像选项，配置如下，然后点击`下载或更新`

<img src="http://rocks526.top/lzx/image-20210320114558415.png" />

2. 下载完成后，点击打开配置文件，是一个JSON文件，修改配置如下：

```json
{
	"picBed": {
		"uploader": "qiniu",
		"qiniu": {
			"accessKey": "秘钥",
			"secretKey": "密匙",
			"bucket": "空间名称",
			"url": "域名",
			"area": "地区的编号，华南是z2",
			"options": "",
			"path": "上传图片前缀"
		}
	},
	"picgoPlugins": {}
}
```

3. 点击验证图片上传，出现SUCCESS即成功

