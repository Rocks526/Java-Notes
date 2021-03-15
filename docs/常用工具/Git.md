# 一：Git介绍





# 二：Git常用命令







# 三：Github项目

### 3.1 Github项目推荐

Github的`Explore -> Trending`里记录了最近一段时间的热门项目，可以根据语言筛选。

### 3.2 HelloGitHub项目

`HelloGitHub`一个入门级的Github项目推荐库，每周更新一次，推荐一些有意思的开源项目。

### 3.3 GitHub高级搜索

> GitHub搜索支持根据名称/语言/star/fork等各种指标过滤，需要符合GitHub的搜索语法，具体如下：

#### 3.3.1 GitHub常用词含义

- **watch**：会持续收到该项目的动态
- **fork**：复制某个项目到自己的Github仓库中
- **star**：可以理解为点赞
- **clone**：将项目下载至本地
- **follow**：关注你感兴趣的作者，会收到他们的动态

#### 3.3.2 awesome加强搜索

Github对一些热门项目做了分类/标签，通过`awesome`关键字就可以进行搜索，例如：

- 搜索优秀的redis相关的项目，包括框架、教程等：awesome redis

#### 3.3.3 in关键字

通过`in`关键字可以结合一些指标对项目进行过滤，语法为：**xxx关键词 in:name 或 description 或 readme**

- xxx in:name 项目名包含xxx的
- xxx in:description 项目描述包含xxx的
- xxx in:readme项目的readme文件中包含xxx的
- 搜索项目名或readme中包含秒杀的项目：seckill in:name,readme

#### 3.3.4 start/fork等指标过滤

- xxx关键词 stars 通配符 (:> 或者 :>=)

- 区间范围数字：数字1..数字2

- 查找stars数大于等于5000的springboot项目: springboot stars :>=5000

- 查找forks数大于500的springcloud项目: springcloud forks:>500

- 查找fork在100到200之间并且stars数在80到100之间的springboot项目：springboot forks:100..200 starts:80..100

# 四：Github加速方式

### 4.1 配置域名-IP映射

> 访问网站的时候，需要先通过DNS服务器解析出IP，然后发送请求，可以通过hosts文件配置域名和IP的映射关系，减去DNS解析这一步。

- 打开`ipaddress.com`网站，查看如下两个域名对应的IP：
  - `github.com`
  - `github.global.ssl.fastly.net`
- 更新hosts文件，添加域名和IP的映射

```txt
140.82.113.4 github.com
199.232.69.194 github.global.ssl.fastly.net
```