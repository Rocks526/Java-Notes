# 一：SQL介绍

### 1.1 数据库介绍

数据库这个术语的用法很多，一个通俗的说法是：`数据库是一个以某种有组织的方式存储的数据集合`。理解数据库的一种最简单的办法是将其想象为一个文件柜。此文件柜是`一个存放数据的物理位置，不管数据是什么以及如何组织的`。

### 1.2 表的介绍

在你将资料放入自己的文件柜时，并不是随便将它们扔进某个抽屉就完事了，而是在文件柜中创建文件，然后将相关的资料放入特定的文件中。

`在数据库领域中，这种文件称为表。表是一种结构化的文件，可用来存储某种特定类型的数据`。表可以保存顾客清单、产品目录，或者其他信息清单。

这里关键的一点在于，`存储在表中的数据是一种类型的数据或一个清单`。决不应该将顾客的清单与订单的清单存储在同一个数据库表中。这样做将使以后的检索和访问很困难。应该创建两个表，每个清单一个表。

`数据库中的每个表都有一个名字，用来标识自己`，此名字是唯一的。

### 1.3 列的介绍

`表由列组成，列中存储着表中某部分的信息。列是表中的一个字段，所有表都是由一个或多个列组成的`。

理解列的最好办法是将数据库表想象为一个网格。网格中每一列存储着一条特定的信息。例如，在顾客表中，一个列存储着顾客编号，另一个列存储着顾客名，而地址、城市以及邮政编码全都存储在各自的列中。

正确地将数据分解为多个列极为重要，通过把它分解开，才有可能利用特定的列对数据进行排序和过滤。

`数据库中每个列都有相应的数据类型。数据类型定义列可以存储的数据种类`。例如，如果列中存储的为数字（或许是订单中的物品数），则相应的数据类型应该为数值类型。如果列中存储的是日期、文本、注释、金额等，则应该用恰当的数据类型规定出来。

数据类型限制可存储在列中的数据种类（例如，防止在数值字段中录入字符值）。数据类型还帮助正确地排序数据，并在优化磁盘使用方面起重要的作用。因此，在创建表时必须对数据类型给予特别的关注。

### 1.4 行的介绍

`表中的数据是按行存储的，所保存的每个记录存储在自己的行内`。

如果将表想象为网格，网格中垂直的列为表列，水平行为表行。例如，顾客表可以每行存储一个顾客。表中的行数为记录的总数。

### 1.5 主键的介绍

`表中每一行都应该有可以唯一标识自己的一列（或一组列）`。

一个顾客表可以使用顾客编号列，而订单表可以使用订单ID，雇员表可以使用雇员ID或雇员社会保险号。

`唯一标识表中每行的这个列（或这组列）称为主键`。主键用来表示一个特定的行。没有主键，更新或删除表中特定行很困难，因为没有安全的方法保证只涉及相关的行。

表中的任何列都可以作为主键，只要它满足以下条件：

- 任意两行都不具有相同的主键值
- 每个行都必须具有一个主键值（主键列不允许NULL值）

`主键通常定义在表的一列上，但这并不是必需的，也可以一起使用多个列作为主键`。在使用多列作为主键时，上述条件必须应用到构成主键的所有列，所有列值的组合必须是唯一的。

### 1.6 SQL介绍

`SQL是结构化查询语言（Structured QueryLanguage）的缩写`。SQL是一种专门用来与数据库通信的语言。

SQL具有如下特点：

- 声明式语言：只需要告诉DBMS要做什么，具体怎么做由DBMS决定
- 面向集合：游标是对面向集合的一种补充，实现面向过程的方式
- 半衰期长：诞生于1974年，主要遵循SQL92和SQL99标准
- 通用性强：除了RDBMS之外，还有一些涉及数据处理的工具都支持SQL
- 学习简单，语义化明显

SQL的分类：

- DDL(Data Definition Language)：数据定义语言，用来定义数据库对象，如创建、修改、删除数据库，数据库表，列等
- DCL(Data Control Language)：数据控制语言，用来定义和控制用户访问权限和数据库安全级别
- DML(Data Manipulation Language)：数据操作语言，用来操作数据库相关的记录，如删除，创建，更新数据表中的记录
- DQL(Data Query Language)：数据查询语言，用来查询数据库记录信息，也是SQL中的重点

SQL语言是大小写不敏感的，但推荐采用以下方式：

- 表名，表别名，字段名，字段别名等采用小写
- SQL保留字，函数名，绑定变量等采用大写

### 1.7 环境说明

Mysql 5.7版本，数据表结构如下：

```sql

-- ----------------------------
-- 图书表
-- ----------------------------
DROP TABLE IF EXISTS `book`;
CREATE TABLE `book`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `name` varchar(64)  NOT NULL COMMENT '名称',
  `publisher` varchar(255)  NOT NULL COMMENT '出版社',
  `publish_time` date NOT NULL COMMENT '出版时间',
  `price` double NOT NULL COMMENT '价格',
  `isbn` varchar(64)  NOT NULL COMMENT '图书编号',
  `introduce` TEXT(65535)  NULL DEFAULT NULL COMMENT '图书介绍',
  `author` bigint NOT NULL COMMENT '作者',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '更新时间'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书作者表
-- ----------------------------
DROP TABLE IF EXISTS `book_author`;
CREATE TABLE `book_author`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `name` varchar(64)  NOT NULL COMMENT '名称',
  `sex` int NOT NULL COMMENT '性别',
  `country` varchar(64)  NULL DEFAULT NULL COMMENT '国家',
  `birthday` date NOT NULL COMMENT '生日',
  `tel` varchar(64)  NOT NULL COMMENT '手机号',
  `introduce` TEXT(65535)  NULL DEFAULT NULL COMMENT '介绍',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '更新时间'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书评论表
-- ----------------------------
DROP TABLE IF EXISTS `book_comment`;
CREATE TABLE `book_comment`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `score` int NOT NULL COMMENT '评分',
  `content` TEXT(65535)  NULL DEFAULT NULL COMMENT '评价内容',
  `user` varchar(64) NULL DEFAULT NULL COMMENT '评论人',
  `create_time` datetime NOT NULL COMMENT '评论时间',
  `update_time` datetime NOT NULL COMMENT '更新时间'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书和评论关联表
-- ----------------------------
DROP TABLE IF EXISTS `book_comment_relation`;
CREATE TABLE `book_comment_relation`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `book_id` bigint NOT NULL COMMENT '图书ID',
  `book_comment` bigint NOT NULL COMMENT '图书评论ID'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书标签表
-- ----------------------------
DROP TABLE IF EXISTS `book_tag`;
CREATE TABLE `book_tag`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `name` varchar(64)  NOT NULL COMMENT '标签名称',
  `sort` int NOT NULL COMMENT '标签排序',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '更新时间'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书标签关联表
-- ----------------------------
DROP TABLE IF EXISTS `book_tag_relation`;
CREATE TABLE `book_tag_relation`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `book_id` bigint NOT NULL COMMENT '图书ID',
  `tag_id` bigint NOT NULL COMMENT '标签ID'
) ENGINE = InnoDB;

-- 预置数据
INSERT INTO `book`.`book`(`name`, `publisher`, `publish_time`, `price`, `isbn`, `introduce`, `author`, `create_time`, `update_time`) VALUES ('理解海德格尔', '译林出版社', '2022-02-09', 89, '9787544788397', '《理解海德格尔》是对海德格尔复杂艰涩的全集作品的全新解读。本书论述清晰，严谨地植根于海德格尔的全部著作，论证了其思想的严格统一，提出了三个主要论点：海德格尔的工作从始至终都是现象学的；“存在”意指事物在人类关切的世界中的意义显现；使这种可理知性得以可能的是人的实存论结构，即“开抛”或本有的“澄明之境”。\r\n\r\n希恩为过去半个世纪主导海德格尔研究的经典范式提供了一种令人信服的替代方案，同时，他对海德格尔的关键术语进行了有价值的重新翻译。这部重要的著作开辟了海德格尔研究的新道路，不仅将激发海德格尔研究内部的对话，而且将引起与现象学传统之外的哲学家以及神学、文学批评、存在主义精神病学领域的学者的对话。', 1, '2022-03-01 16:17:50', '2022-03-28 16:17:52');
INSERT INTO `book`.`book`(`name`, `publisher`, `publish_time`, `price`, `isbn`, `introduce`, `author`, `create_time`, `update_time`) VALUES ('高性能Mysql', '电子工业出版社', '2013-05-15', 128.8, '9787121198854', '本书是MySQL领域的经典之作，拥有广泛的影响力。第3版更新了大量的内容，不但涵盖了*MySQL 5.5版本的新特性，也讲述了关于固态盘、高可扩展性设计和云计算环境下的数据库相关的新内容，原有的基准测试和性能优化部分也做了大量的扩展和补充。全书共分为16章和6个附录，内容涵盖MySQL架构和历史，基准测试和性能剖析，数据库软硬件性能优化，复制、备份和恢复，高可用与高可扩展性，以及云端的MySQL和MySQL相关工具等方面的内容。每一章都是相对独立的主题，读者可以有选择性地单独阅读。 本书不但适合数据库管理员（DBA）阅读，也适合开发人员参考学习。不管是数据库新手还是专家，相信都能从本书有所收获。', 2, '2022-03-23 16:46:12', '2022-03-28 16:46:15');
INSERT INTO `book`.`book`(`name`, `publisher`, `publish_time`, `price`, `isbn`, `introduce`, `author`, `create_time`, `update_time`) VALUES ('深入理解Java虚拟机：JVM高级特性与实践（第3版）', '电子工业出版社', '2019-06-14', 89.9, '9787111641247', '这是一部从工作原理和工程实践两个维度深入剖析JVM的著作，是计算机领域公认的经典，繁体版在台湾也颇受欢迎。\r\n自2011年上市以来，前两个版本累计印刷36次，销量超过30万册，两家主要网络书店的评论近90000条，内容上近乎零差评，是原创计算机图书领域不可逾越的丰碑。\r\n第3版在第2版的基础上做了重大修订，内容更丰富、实战性更强：根据新版JDK对内容进行了全方位的修订和升级，围绕新技术和生产实践新增逾10万字，包含近50%的全新内容，并对第2版中含糊、瑕疵和错误内容进行了修正。\r\n全书一共13章，分为五大部分：\r\n第壹部分（第1章）走近Java\r\n系统介绍了Java的技术体系、发展历程、虚拟机家族，以及动手编译JDK，了解这部分内容能对学习JVM提供良好的指引。\r\n第二部分（第2~5章）自动内存管理\r\n详细讲解了Java的内存区域与内存溢出、垃圾收集器与内存分配策略、虚拟机性能监控与故障排除等与自动内存管理相关的内容，以及10余个经典的性能优化案例和优化方法；', 3, '2022-03-28 16:56:05', '2022-03-28 16:56:07');

INSERT INTO `book`.`book_author`(`name`, `sex`, `country`, `birthday`, `tel`, `introduce`, `create_time`, `update_time`) VALUES ('托马斯·希恩', 0, '美国', '1974-03-28', '13328009762', '美国哲学家，斯坦福大学宗教研究教授、芝加哥洛约拉大学哲学系荣休教授。研究方向为当代欧洲哲学及其与宗教问题的关系，尤其关注海德格尔和罗马天主教。主要作品有《海德格尔：人和思想家》（1981）、《埃德蒙·胡塞尔：心理学和超越论现象学以及与海德格尔的交会》（1997）、《成为海德格尔》（2007）及《理解海德格尔：范式的转变》（2015）等。', '2022-03-28 16:13:18', '2022-03-28 16:13:20');
INSERT INTO `book`.`book_author`(`name`, `sex`, `country`, `birthday`, `tel`, `introduce`, `create_time`, `update_time`) VALUES ('Baron Schwartz', 0, '美国', '1983-03-28', '17890821772', 'Baron Schwartz是一位软件工程师，居住在弗吉尼亚州的Charlottesville，网络常用名是Xaprb，这是按照QWERTY键盘的顺序在Dvorak键盘上打出来的名字。在不忙于解决有趣的编程挑战时，Baron会和他的妻子Lynn以及小狗Carbon一起享受闲暇的时光。他有一个软件工程方面的博客，地址是http://www.xaprb.com/blog/\r\nBaron Schwartz目前是Percona公司的首席性能架构师。他创造了很多的工具和技术，使得MySQL更加易用和可靠。Peter Zaitsev是Percona公司的CEO和联合创始人。他是一位数据库内核、计算机硬件和应用扩展性方面的专家。他曾经负责管理MySQL的高性能小组，一直到2006年。adim Tkachenko是Percona公司的CTO和联合创始人。他目前负责带领Percona公司的开发小组，开发的产品包括Percona Server、Percona XtraDB集群，以及Percona Xtrabackup。', '2022-03-02 16:40:21', '2022-03-28 16:40:25');
INSERT INTO `book`.`book_author`(`name`, `sex`, `country`, `birthday`, `tel`, `introduce`, `create_time`, `update_time`) VALUES ('周志明', 0, '中国', '1984-01-24', '1899203928', '资深Java技术专家、机器学习技术专家和企业级开发技术专家，现任远光软件研究院院长。\r\n\r\n开源技术的积极倡导者和推动者，对计算机科学相关的多个领域都有深刻的见解，尤其是人工智能、Java技术和敏捷开发等，对虚拟机技术有非常深入的研究。\r\n\r\n撰写了《深入理解Java虚拟机》《深入理解OSGi》《智慧的疆界》等多本著作，翻译了《Java虚拟机规范》等著作。其中《深入理解Java虚拟机》已累计印刷逾40次，总销超过30万册，成为原创计算机专业图书领域难以逾越的丰碑。', '2022-03-10 16:57:38', '2022-03-28 16:57:40');

INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (5, '暂时自觉地止步于第七章，每每看到在哲学研究中对待词源学细致的梳理便可发觉翻译一部哲学著作的不易，Dasein何以被翻译作此在，这种对于普通哲学读者看似容易的事对于哲学研究者和翻译家来说却充满了困难。现阶段能考虑的就是将海德格尔的现象学中存在何以可能的考察浅显地置于艺术创作和文化批评的角度下进行运用。', '异步', '2022-03-28 16:30:45', '2022-03-28 16:30:48');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (5, '剥开层层躯壳，发现那个内核——原来是空的，因为剥开的过程中，意义已经产生了；如果有那个内核，那么一定是死亡。', '苍生', '2022-03-23 16:31:17', '2022-03-28 16:31:21');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (4, '「一面让死亡悬临，一面为世界赋义。」写得很好同时又很艰深，等回头翻完《存在与时间》再来读一遍。。。', '聂北秦', '2022-03-18 16:31:49', '2022-03-18 16:31:53');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (3, '海德格尔关于科技的“战后”反思并不是其哲学生涯的画蛇添足，具体而言，它既不意味着海德格尔的哲学向现代世界妥协，也不应仅被视为社会文化方面的某种独白。实际上，它是海德格尔的后期思想所曾达到的顶峰，甚至为探究他的前期思想打开了一扇窗 。1953年，当他提到“行星科技与现代人类的相遇”之时，他似乎曾将亲身经历映入脑海。现代科技给他对于德国西南简单宁静的田园生活的浪漫憧憬笼上了一层阴影。他曾亲历“一战”，目睹了科技造成的毁灭性灾难，潜伏其后的思想动因更是令他忧心忡忡：在未来的蓝图中，人类意欲经由科技的更新换代主宰世界。在由理性席卷一切的现代化浪潮中，海德格尔终其一生关注的东西迅速在视野中隐退：人的不可计算性与隐秘的有限性，此乃人的内核，它们优先于理性、科学和技术，比这些事物更加深刻......', '大林木', '2022-03-09 16:32:22', '2022-03-09 16:32:25');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (5, '读一本好书的最高境界应该是读着读着，忽然恍然大悟，把以前的知识点以及做过的东西串起来。仰慕这本书很久了，终于入手了一本。本书介绍MySQL的驱动语言SQL的详细内容与使用方法，是MySQL领域的经典之作，拥有广泛的影响力。第3 版更新了大量的内容，不但涵盖了最新MySQL5.5版本的新特性，也讲述了关于固态盘、高可扩展性设计和云计算环境下的数据库相关的新内容，原有的基准测试和性能优化部分也做了大量的扩展和补充。全书共分为16 章和6 个附录，内容涵盖MySQL架构和历史，基准测试和性能剖析，数据库软硬件性能优化，复制、备份和恢复，高可用与高可扩展性，以及云端的MySQL和MySQL相关工具等方面的内容。本书全面而且相当实用，涵盖了面向初级数据库管理员和程序员的基本信息，深入浅出，从基础开始，循序渐进地带领读者详细分析和实践每一个步骤，使读者从没有多少SQL编程和数据设计经验的新手，完整讲解了数据库开发和使用中SQL语言的使用技巧，迅速成长为数据库开发和设计领域的专家。本书适合程序员、Web开发者、DBA或数据库用户等参考。', '张物', '2015-06-12 16:48:45', '2022-03-17 16:48:54');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (5, '这本高性能MySQL可以说是很经典的一本书了，内容上来说属于中高级的内容，很多内容点介绍的很详细，我建议在读的时候抓大内容不要太拘泥于细节，先有一个宏观的概念之后再深入到一些点中去研究，有些点可能书中限于篇幅并没有展开去讲解，这种点需要自己去查一些资料，我这里可以推荐一下，比如姜承尧的深入理解Innodb存储引擎，还有阿里的何登成的博客。\r\n这本书中对索引和事务还有查询优化方案都讲的很不错的，也比较详细了，我建议在看这部分内容的时候可以先看脉络，然后看几个点，不懂的地方可以多回来翻翻书查查，厚度确实很大，读个四五遍不一定能全部融会贯通，只要经常拿来翻翻看看相信慢慢的就会对其有一个比较深刻的印象了。到时候在仔细的钻研一些知识点结合其他的书和文章研究的话可能也会比较顺利，这本书可以当作字典的性能来看。\r\n当然如果你想在数据库方面钻的比较深，建议你多读几遍，每一遍扫清几个自己没有理解透彻的点，其实你会发现当你每一次读的时候肯定会有很多不懂的点，因为一个是内容太多，读起来容易忘记，另一个是有些点可能由于翻译的问题造成文字展现不出来原作者的本意，这样你可以去找一下英文版读读相关的地方，看看是不是这个意思，不过本书内容很丰富，很多人反映的是翻译的并不是很好，读一次两次的不一定能发现，当你读的多几次的话会发现里面有的地方有些突兀没法理解。\r\n找工作的朋友可以多读读SQL性能优化的部分还有索引和事务的部分，这些应该属于经常考的部分，不过，对于后面像复制还有操作系统文件等这些内容需要结合自己的公司的业务来学习，书上会讲很多方案或者是小的技巧，但是很容易忘记，结合业务实际的操练操练，或者没有办法操练的话可以自己搭建环境来测试下，到底这种方式有没有不好的地方呢？这个往往有时候书中讲很多有点，各种好，但是真正用上的话并不一定就是好的每个公司可能业务场景不一样，各自有各自场景下的更好的方案。\r\n这本书翻译有些地方可能存在不足的地方，读者自己去进行甄别。本书总体而言是很不错的，至少能够帮助掌握了SQL一些基本的使用的人员来进行提升，如果基本的使用还没有完全入门的话，不建议读这本书，有些东西只是知道原理却还不会用有点像是纸上谈兵了。', '陈晨', '2019-06-15 16:49:17', '2022-03-03 16:49:25');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (3, '刚买的。准备慢慢看，后端程序员必备数据库优化的书籍。', '陈畅', '2022-03-15 16:49:42', '2022-03-28 16:49:46');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (4, '不错，书是新书，内容大概浏览了一下，对于开发者还是有很大帮助的，知识讲的很全面。', '张武', '2022-03-10 16:50:01', '2022-03-28 16:50:04');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (2, '图片超过4M就不能上传。。这本书我没有发言权，没有看过，全凭知乎和销量买的。python下周要学数据库了，特买一本来预习', '陈政', '2018-06-09 16:50:23', '2022-03-28 16:50:31');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (3, '这书评分挺高的，大概翻了一下，挺有意思，值得一看', '张淼', '2020-04-25 16:59:06', '2022-03-28 16:59:14');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (3, '书的质量还可以，网上推荐的挺多的，开始学习吧', '张珊珊', '2021-07-18 16:59:30', '2022-03-28 16:59:37');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (4, '收到了第三版新书，深入理解J**A虚拟机，有助于我们代码调优，即将找实习，希望通过对此书的学习助我找到心意的工作哈', '米思思', '2022-03-17 16:59:53', '2022-03-28 16:59:56');
INSERT INTO `book`.`book_comment`(`score`, `content`, `user`, `create_time`, `update_time`) VALUES (5, '这个书特别的经典，特别的好，应该人手一本，Java必备', '张武', '2022-03-12 17:00:13', '2022-03-28 17:00:16');

INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (1, 1);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (1, 2);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (1, 3);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (1, 4);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (2, 5);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (2, 6);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (2, 7);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (2, 8);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (2, 9);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (3, 10);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (3, 11);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (3, 12);
INSERT INTO `book`.`book_comment_relation`(`book_id`, `book_comment`) VALUES (3, 13);

INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('文学', 1, '2022-03-28 16:25:09', '2022-03-28 16:25:11');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('小说', 10, '2022-03-28 16:25:26', '2022-03-28 16:25:29');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('历史', 5, '2022-03-28 16:25:38', '2022-03-28 16:25:40');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('社会纪实', 20, '2022-03-28 16:25:59', '2022-03-28 16:26:01');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('科学新知', 8, '2022-03-28 16:26:17', '2022-03-28 16:26:19');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('艺术设计', 30, '2022-03-28 16:26:38', '2022-03-28 16:26:41');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('商业经管', 2, '2022-03-28 16:27:00', '2022-03-28 16:27:02');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('绘画漫本', 50, '2022-03-28 16:27:24', '2022-03-28 16:27:26');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('计算机', 3, '2022-03-10 16:37:19', '2022-03-28 16:37:23');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('Java', 4, '2022-03-11 16:37:46', '2022-03-28 16:37:49');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('Spring', 6, '2022-03-24 16:38:00', '2022-03-28 16:38:03');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('SpringCloud', 8, '2022-03-17 16:38:13', '2022-03-28 16:38:17');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('微服务', 9, '2022-03-16 16:38:25', '2022-03-23 16:38:28');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('Go', 50, '2022-03-12 16:38:44', '2022-03-28 16:38:47');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('操作系统', 88, '2022-03-10 16:38:58', '2022-03-24 16:39:01');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('数据库', 66, '2022-03-17 16:47:16', '2022-03-28 16:47:19');
INSERT INTO `book`.`book_tag`(`name`, `sort`, `create_time`, `update_time`) VALUES ('Mysql', 45, '2022-03-17 16:47:29', '2022-03-28 16:47:33');

INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (1, 1);
INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (1, 4);
INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (1, 5);
INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (2, 16);
INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (2, 17);
INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (2, 9);
INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (3, 15);
INSERT INTO `book`.`book_tag_relation`(`book_id`, `tag_id`) VALUES (3, 10);
```

# 二：检索数据

### 2.1 select语句

SQL语句是由简单的英语单词构成的，这些单词称为关键字，每个SQL语句都是由一个或多个关键字构成的。最经常使用的SQL语句就是SELECT语句了，它的用途是从一个或多个表中检索信息。

### 2.2 检索单列

- 需求：查询所有图书的名称

- 语句

```sql
select name from book;
```

- 输出

![image-20220328174902654](http://rocks526.top/lzx/image-20220328174902654.png)

### 2.3 检索多列

要想从一个表中检索多个列，同样需要使用SELECT语句，唯一的不同是必须在SELECT关键字后给出多个列名，列名之间以逗号分隔。

- 需求：查询所有图书的名称、出版社、价格

- 语句

```sql
select name,publisher,price from book;
```

- 输出

![image-20220328174803179](http://rocks526.top/lzx/image-20220328174803179.png)

### 2.4 检索所有列

除了指定所需的列外，SELECT语句还可以通过使用星号（*）通配符来实现检索所有列，当然手动列出所有列名也是可以实现相同的效果。

- 需求：查询图书的所有信息

- 语句

```sql
select * from book;
```

- 输出

![image-20220328174956937](http://rocks526.top/lzx/image-20220328174956937.png)

- 注意：除非真的需要所有列，否则不建议使用select *，检索过多的列会降低查询的性能。

### 2.5 检索不同列

SELECT会返回所有匹配的行，如果想要进行去重，只拿到数据不同的行，则`需要DISTINCT关键字`。

- 需求：查询所有图书的出版社类型
- 语句

```sql
select DISTINCT publisher from book;
```

- 输出

![image-20220328175055488](http://rocks526.top/lzx/image-20220328175055488.png)

### 2.6 限制返回结果

SELECT语句会返回所有匹配的行，当我们使用分页查询时，需要使用`LIMIT`子句。

- 需求：每页展示两条图书信息，展示第一页
- 语句

```sql
select * from book LIMIT 2 OFFSET 0 ;
```

- 输出

![image-20220328175217810](http://rocks526.top/lzx/image-20220328175217810.png)

### 2.7 使用完全限定名

上面使用的SQL例子都只通过列名引用列，SQL也可能使用完全限定的名字来引用列，或者表的别名引用列，也支持输出的列进行重命名。

- 需求：查询所有图书的信息，并将name字段重命名为book_name
- 语句

```sql
select name as book_name, publisher, publish_time, price, isbn, introduce, author, create_time, update_time from book;
```

- 输出

![image-20220328175416133](http://rocks526.top/lzx/image-20220328175416133.png)

# 三：排序数据

### 3.1 order by语句

上面介绍了select语句来查询数据，但返回的数据都是没有固定顺序的，都是按照mysql底层存储的顺序返回，想要使返回的顺序进行排序，需要使用order by关键字。

### 3.2 指定列排序

- 需求：查询所有图书信息，根据价格升序排序
- 语句

```sql
select * from book order by price;
```

- 输出

![image-20220328175458702](http://rocks526.top/lzx/image-20220328175458702.png)

### 3.3 指定多列排序

在部分场景中可能存在多个排序字段，例如订单需要根据价格排序，价格相同的按照生成时间排序。这种场景可以通过order by指定多个列名进行排序。

- 需求：查询所有图书信息，根据价格升序排序，价格一致的根据名称升序
- 语句

```sql
select * from book order by price,name;
```

- 输出

![image-20220328175547057](http://rocks526.top/lzx/image-20220328175547057.png)

### 3.4 指定排序方向

在上面的排序中，都没有指定排序方向，`默认都是按照升序排序`。当需要使用降序排序时，需要手动指定方向。

- 需求：查询所有图书信息，根据价格降序排列，价格相同的根据名称升序排列
- 语句

```sql
select * from book order by price desc,name asc;
```

- 输出

![image-20220328175753786](http://rocks526.top/lzx/image-20220328175753786.png)

# 四：过滤数据

### 4.1 where关键字

数据库表一般包含大量的数据，很少需要检索表中所有行，通常只会根据特定条件提取需要的行，这时候就需要根据`WHERE`子句中指定的搜索条件进行过滤。

- 需求：查询价格大于100的所有图书名称、出版社、介绍等信息

- 语句

```sql
select name,publisher,publish_time,price,introduce from book where price > 100;
```

- 输出

![image-20220328180307148](http://rocks526.top/lzx/image-20220328180307148.png)

### 4.2 匹配操作符

where子句支持多种操作连接符，可以实现比较复杂的匹配逻辑，具体支持的操作符如下：

| 操作符            | 说明               |
| ----------------- | ------------------ |
| =                 | 等于               |
| <>                | 不等于             |
| !=                | 不等于             |
| <                 | 小于               |
| >                 | 大于               |
| <=                | 小于等于           |
| \>=               | 大于等于           |
| between...and.... | 两者之间，包含两端 |

#### 4.2.1 不等于匹配

- 需求：查询出版社不是'电子工业出版社'的所有图书
- 语句

```sql
select * from book where publisher != '电子工业出版社';
```

- 输出

![image-20220328193444335](http://rocks526.top/lzx/image-20220328193444335.png)

#### 4.2.2 大于匹配

- 需求：查询价格大于100元的所有图书信息
- 语句

```sql
select * from book where price > 100;
```

- 输出

![image-20220328193939550](http://rocks526.top/lzx/image-20220328193939550.png)

#### 4.2.3 小于匹配

- 需求：查询价格小于等于100元的所有图书信息
- 语句

```sql
select * from book where price <= 100;
```

- 输出

![image-20220328194017447](http://rocks526.top/lzx/image-20220328194017447.png)

#### 4.2.4 区间匹配

- 需求：查询价格在50到89.9元之间的所有图书信息
- 语句

```sql
select * from book where price BETWEEN 50 AND 89.9;
```

- 输出

![image-20220328194122373](http://rocks526.top/lzx/image-20220328194122373.png)

#### 4.2.5 空值匹配

- 需求：查询出版社信息为空的所有图书信息

- 语句

```sql
select * from book where publisher is NULL;
```

- 输出

![image-20220328194255611](http://rocks526.top/lzx/image-20220328194255611.png)

### 4.3 逻辑操作符

SQL除了支持匹配操作符之外，还支持逻辑操作符，可以实现编程语言类似的and、or、not操作，支持的逻辑操作符如下：

| 操作符 | 说明                               |
| ------ | ---------------------------------- |
| AND    | 并且的关系，两边的条件都需要满足   |
| OR     | 或者的关系，这边的条件满足其一即可 |
| NOT    | 取反的关系                         |

#### 4.3.1 AND操作符

- 需求：查询出版时间在2020年以前，并且价格大于90的图书
- 语句

```sql
select * from book where publish_time < '2020-01-01' and price > 90;
```

- 输出

![image-20220328195420224](http://rocks526.top/lzx/image-20220328195420224.png)

#### 4.3.2 OR操作符

- 需求：查询出版时间在2020年以前，或者价格大于90的图书
- 语句

```sql
select * from book where publish_time < '2020-01-01' or price > 90;
```

- 输出

![image-20220328195553776](http://rocks526.top/lzx/image-20220328195553776.png)

#### 4.3.3 NOT操作

- 需求：查询价格不大于90元的图书
- 语句

```sql
select * from book where not price > 90;
select * from book where price <= 90;
```

- 输出

![image-20220328195718914](http://rocks526.top/lzx/image-20220328195718914.png)

#### 4.3.4 操作符优先级

当存在比较复杂的条件时，可以通过小括号进行多条件组合，小括号的优先级最高。

- 需求：查询价格小于90元并且出版时间在2020年之前，或者价格大于100元并且是'电子工业出版社'出版的图书
- 语句

```sql
select * from book where (price < 90 and publish_time < '2020-01-01') or (price > 100 and publisher = '电子工业出版社');
```

- 输出

![image-20220328195947033](http://rocks526.top/lzx/image-20220328195947033.png)

### 4.4 通配符

前面都是针对已知的值进行匹配，当要对比的值未知时，需要通配符帮助匹配。类似于查询出版社名称里包含'工业'的图书信息这种需求。

Mysql针对通配符的使用需要`LIKE`关键字，支持的通配符有两种：

- %：匹配任意长度的字符
- _：匹配单个字符

#### 4.4.1 %通配符

- 需求：查询名称包含'理解'两个字的所有图书信息
- 语句

```sql
select * from book where name like '%理解%';
```

- 实现

![image-20220329103615956](http://rocks526.top/lzx/image-20220329103615956.png)

- 需求：查询名称以'理解'两个字开头的所有图书信息
- 语句

```sql
select * from book where name like '理解%';
```

- 实现

![image-20220329103705083](http://rocks526.top/lzx/image-20220329103705083.png)

#### 4.4.2 _通配符

- 需求：查询出版社为'某某工业出版社'的图书信息
- 语句

```sql
select * from book where publisher like '__工业出版社'
```

- 实现

![image-20220329103910878](http://rocks526.top/lzx/image-20220329103910878.png)

### 4.5 正则表达式

SQL支持各种匹配符之外，还支持正则表达式，通过`REGEXP`进行匹配

- 需求：查询以'理解'开头的所有图书信息

- 语句

```sql
select * from book where name REGEXP '^理解.*$';
```

- 实现

![image-20220329110828950](http://rocks526.top/lzx/image-20220329110828950.png)

- 需求：查询包含'理解'两个字的所有图书信息

- 语句

```sql
select * from book where name REGEXP '^.*理解.*$';
```

- 实现

![image-20220329110748680](http://rocks526.top/lzx/image-20220329110748680.png)

### 4.6 创建计算字段

#### 4.6.1 字段拼接

SQL支持查询时进行字段拼接，将多个字段的值拼接在一起。

- 需求：查询图书名称和价格信息，拼接在一起展示
- 语句

```sql
select CONCAT(name," [",price,"]") as book_name from book;
```

- 实现

![image-20220329113117439](http://rocks526.top/lzx/image-20220329113117439.png)

#### 4.6.2 算术运算

SQL支持查询字段时，基于原字段进行二次计算，支持常用的算术运算符。

- 需求：查询图书价格信息，并将每本书的价格增加10元
- 语句

```sql
select name, price, price + 10 as new_price from book;
```

- 实现

![image-20220329113357681](http://rocks526.top/lzx/image-20220329113357681.png)

### 4.7 数据处理函数

SQL除了支持创建计算字段外，还支持各种数据处理函数，可以基于原数据进行加工处理，由于`不同数据库的函数支持不一致`，因此不在此处介绍，在介绍具体的数据库时进行介绍。

# 五：汇总数据

### 5.1 聚合函数

有时候存在聚合数据的场景，例如查询总数、平均值、最大值、最小值等，SQL提供了一些聚合函数，可以将多行数据聚合成一行，具体如下：

| 函数      | 说明             |
| --------- | ---------------- |
| AVG（）   | 返回某列的平均值 |
| COUNT（） | 返回某列的行数   |
| MAX（）   | 返回某列的最大值 |
| MIN（）   | 返回某列的最小值 |
| SUM（）   | 返回某列得值之和 |

注：不同数据库对函数的支持可能也不一样，但大部分都支持这些基础的聚合函数。

#### 5.1.1 AVG函数

- 需求：查询'电子工业出版社'的所有图书的平均价格
- 语句

```sql
select avg(price) from book where publisher = '电子工业出版社';
```

- 实现

![image-20220329161124267](http://rocks526.top/lzx/image-20220329161124267.png)

#### 5.1.2 COUNT函数

- 需求：查询图书的总数
- 语句

```sql
select count(*) as book_total_publisher from book;
```

- 实现

![image-20220329161741251](http://rocks526.top/lzx/image-20220329161741251.png)

#### 5.1.3 MAX函数

- 需求：查询价格最高的图书价格
- 语句

```sql
select max(price) as book_max_price from book;
```

- 实现

![image-20220329161927926](http://rocks526.top/lzx/image-20220329161927926.png)

#### 5.1.4 MIN函数

- 需求：查询价格最低的图书价格
- 语句

```sql
select min(price) as book_min_price from book;
```

- 实现

![image-20220329162118825](http://rocks526.top/lzx/image-20220329162118825.png)

#### 5.1.5 SUM函数

- 需求：查询所有图书的价格总和
- 语句

```sql
select sum(price) as total_price from book;
```

- 实现

![image-20220329162341185](http://rocks526.top/lzx/image-20220329162341185.png)

### 5.2 聚合不同值

默认聚合函数会计算匹配到的所有行，可以通过`DISTINCT`关键字可以改变聚合逻辑，只聚合不同值的行。

- 需求：查询所有图书的出版社厂家数
- 语句

```sql
select count(DISTINCT publisher) as publisher_count from book;
```

- 实现

![image-20220329162803456](http://rocks526.top/lzx/image-20220329162803456.png)

### 5.3 组合聚合函数

以上的示例都只使用了单个聚合函数，实际上SQL支持同时使用多个聚合函数。

- 需求：查询所有图书的最高价格、最低价格、平均价格
- 语句

```sql
select max(price) as max_price, min(price) as min_price, avg(price) as avg_price from book;
```

- 实现

![image-20220329163047734](http://rocks526.top/lzx/image-20220329163047734.png)

# 六：分组数据

### 6.1 分组

SQL除了提供过滤、聚合之外，还支持分组的功能，通过`GROUP BY`关键字实现。

- 需求：查询各个出版社出版的图书数量
- 语句

```sql
select publisher, count(*) as publisher_book_count from book group by publisher;
```

- 实现

![image-20220329164334726](http://rocks526.top/lzx/image-20220329164334726.png)

- 需求：查询各个出版社各个作者出版的图书数量
- 语句

```sql
select publisher,author, count(*) as publisher_and_author_book_count from book group by publisher,author;
```

- 实现

![image-20220329204332469](http://rocks526.top/lzx/image-20220329204332469.png)

### 6.2 分组过滤

SQL对数据进行分组后，支持对组级别进行过滤，通过`HAVING`关键字实现。

- 需求：查询出版社，出版图书数量大于1的出版社名称和出版图书数量
- 语句

```sql
select publisher, count(*) as publisher_book_count from book group by publisher having publisher_book_count > 1;
```

- 实现

![image-20220329164458867](http://rocks526.top/lzx/image-20220329164458867.png)

# 七：子查询

### 7.1 子查询作为过滤条件

当存在连环查询时，可以通过子查询实现一个SQL语句完成查询。

例如查询作者名称为'周志明'的所有图书信息，由于作者信息在book_author表里，需要先查book_author表，过滤出作者id，在通过作者id去查图书信息。如果没有子查询，需要通过两个SELECT语句完成，通过子查询，只需要一个SELECT语句即可实现。

- 需求：查询'周志明'编写的所有图书信息
- 语句

```sql
select * from book where author = (select id from book_author where name = '周志明');
```

- 实现

![image-20220329170701307](http://rocks526.top/lzx/image-20220329170701307.png)

- 需求：查询所有作者是美国的图书信息
- 语句

```sql
select * from book where author in (select id from book_author where country = '美国');
```

- 实现

![image-20220329170857324](http://rocks526.top/lzx/image-20220329170857324.png)

### 7.2 子查询作为计算字段

在上面的例子中，子查询是作为过滤条件的，还存在某种场景，子查询需要作为查询结果的列出现。

- 需求：查询所有图书的名称、作者名称、出版社、出版时间等信息
- 语句

```sql
select name, (select name from book_author where id = author) as author, publisher, publish_time, price, introduce from book;
```

- 实现

![image-20220329171424871](http://rocks526.top/lzx/image-20220329171424871.png)

由于作者信息在另外一张表中，每行的作者信息需要通过子查询进行富化。

### 7.3 子查询注意事项

- 子查询在SQL层面没有限制嵌套层数，但尽量少做嵌套，会降低性能
- 子查询作为计算字段时，会对每行记录进行反复查询，比较消耗性能
- 子查询会涉及多个表，可能存在同名的列，要避免歧义性，可以通过表名.列名进行指定

# 八：联结表

### 8.1 表关系



### 8.2 交叉联结





### 8.3 内联结





### 8.4 自联结





### 8.5 外联结







- 需求：
- 语句

```sql

```

- 实现







# 九：查询顺序

### 9.1 查询关键字顺序

SQL查询方式众多，不同关键字之间存在先后顺序，整体次序如下：

```sql
select .... from .... where .... group by .... having .... order by .... limit .....
```

- 需求：查询价格小于200，并且根据作者和名称分组，过滤作者ID大于1的数据，取前5条
- 语句

```sql
select name,author from book where price < 200 group by author,name having author > 1 limit 5 offset 0;
```

- 实现

![image-20220329204057609](http://rocks526.top/lzx/image-20220329204057609.png)

### 9.2 查询解析顺序















# 十：DDL

`DDL(Data Definition Language)`是数据定义语言，主要用来操作数据库、表等元数据。

### 10.1 创建数据库

- 语法

```sql
create database 数据库名;
create database 数据库名 character set 字符集;
```

- 示例

```sql
-- 查询数据库支持的所有字符集
show character set;
-- 修改数据库默认字符集
set character_set_server='utf8';
-- 创建数据库，并制定字符集
create database db_test CHARACTER set utf8;
```

### 10.2 查看数据库

- 语法

```sql
-- 查看当前所有的数据库
show databases;
-- 查看某个数据库的定义信息
show create database 数据库名;
```

- 示例

```sql
show create database db_test;
```

### 10.3 删除数据库

- 语法

```sql
drop database 数据库名称;
```

- 示例

```sql
drop database db_test;
```

### 10.4 其他数据库操作

```sql
-- 切换当前使用的数据库
use 数据库名;
-- 查看当前使用的数据库
select database();
```

### 10.5 创建表

- 语法

```sql
create table 表名(
字段名 类型(长度) 约束,
字段名 类型(长度) 约束
);
```

- 字段约束分类

```markdown
- 主键约束：primary key
- 唯一约束：unique
- 非空约束：not null
主键约束 = 唯一约束 + 非空约束
```

- 示例

```sql
create table tbl_user(
	id bigint primary key AUTO_INCREMENT COMMENT '用户ID',
        name varchar(64) not null COMMENT '用户名称',
        birthday date not null COMMENT '用户生日',
        idCard varchar(32) unique not null COMMENT '用户身份证',
        create_time datetime not null COMMENT '创建时间',
        update_time datetime not null COMMENT '更新时间'
);
```

### 10.6 查看表

- 语法

```sql
-- 查看数据库所有的表
show tables;
-- 查看表结构
desc 表名;
```

- 示例

```sql
desc tbl_user;
```

![image-20220329210711494](http://rocks526.top/lzx/image-20220329210711494.png)

### 10.7 删除表

- 语法

```sql
drop table 表名;
```

- 示例

```sql
drop table tbl_user;
```

### 10.8 修改表

- 语法

```sql
-- 修改表添加列.
alter table 表名 add 列名 类型(长度) 约束; 
-- 修改表修改列的类型长度及约束.
alter table 表名 modify 列名 类型(长度) 约束; 
 -- 修改表修改列名.
alter table 表名 change 旧列名 新列名 类型(长度) 约束;
-- 修改表删除列.
alter table 表名 drop 列名; 
-- 修改表名
rename table 表名 to 新表名;
-- 修改表的字符集
alter table 表名 character set 字符集; 
```

- 示例

```sql
-- 添加删除标识列，已有数据会自动补int的默认值0
alter table tbl_user add delete_flag int not null; 
-- 修改列的类型
alter table tbl_user modify delete_flag bigint not null; 
-- 修改列的类型和名称
alter table tbl_user change delete_flag deleled int not null;
-- 删除列
alter table tbl_user drop deleled; 
-- 修改表名
rename table tbl_user to tbl_account;
-- 修改表的字符集
alter table tbl_account character set 'gbk'; 
```

# 十一：DML

`DML(Data Manipulation Language)`是数据操作语言，主要用来操作数据表里的记录，如删除，插入，更新等。

### 11.1 插入数据

- 语法

```sql
-- 向表中插入某些列
insert into 表 (列名1,列名2,列名3..) values (值1,值2,值3..); 
--向表中插入所有列
insert into 表 values (值1,值2,值3..); 
-- 向表中插入多行
insert into 表 values (值1,值2,值3..),(值1,值2,值3..),(值1,值2,值3..),(值1,值2,值3..)......; 
insert into 表 (列名1,列名2,列名3..) values (值1,值2,值3..),(值1,值2,值3..),(值1,值2,值3..),(值1,值2,值3..).....; 
-- 向表中插入查询结果
insert into 表 (列名1,列名2,列名3..) values select (列名1,列名2,列名3..) from 表
insert into 表 values select * from 表
```

- 注意事项

1. 插入不指定列名时，如果某个列是自增的，可以将值设置为NULL，数据库会自动获取
2. 列名数与values后面的值的个数相等
3. 列的顺序与插入的值得顺序一致
4. 列名的类型与插入的值要一致
5. 插入值的时候不能超过最大长度
6. 值如果是字符串或者日期需要加单引号

- 示例

```sql
insert into tbl_user (name,birthday,idCard,create_time,update_time) values ('lzx','1998-05-26','xxxxxxxxxxx','2022-03-29 21:10:01', '2022-03-29 21:10:01');
insert into tbl_user values ('lzx','1998-05-26','xxxxxxxxxxx','2022-03-29 21:10:01', '2022-03-29 21:10:01');
```





### 11.2 更新数据





### 11.3 删除数据











# 十二：DCL







# 十三：视图





# 十四：存储过程



# 十五：游标



# 十六：触发器









