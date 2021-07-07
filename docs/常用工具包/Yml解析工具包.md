# 一：Yaml介绍

### 1.1 Yaml简介

YAML 语言的设计目标，就是方便人类读写。它实质上是一种通用的数据串行化格式。

YAML 主要用来写配置文件，相比JSON的分隔符式结构化描述，YAML的缩进符描述结构显得更加简洁和强大。

### 1.2 Yaml基础语法

- yaml文件以`---` 标识每个片段的开始

```yaml
%YAML 1.2
--- 
YAML: YAML Language
```

基本语法规则如下：

- 大小写敏感
- 使用缩进表示层级关系
- 缩进时不允许使用Tab键，只允许使用空格。
- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可

> `#` 表示注释，从这个字符一直到行尾，都会被解析器忽略。

YAML 支持的数据结构有三种：

- 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
- 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
- 纯量（scalars）：单个的、不可再分的值

### 1.3 对象

对象的一组键值对，使用冒号结构表示。

```yaml
person:
	name: zhangsan
	age: 18
	sex: 男
```

Yaml 也允许另一种写法，将所有键值对写成一个行内对象。

```yaml
person: {name: zhangsan, age: 18, sex: 男}
```

### 1.4 数组

一组连词线开头的行，构成一个数组。

```yaml
animal:
	- cat
	- dog
	- tiger
```

数据结构的子成员是一个数组，则可以在该项下面缩进一个空格。

```yaml
animal:
	- 
		- wightCat
		- redCat
		- blackCat
	- dog
	- tiger
```

数组也可以采用行内表示法。

```yaml
animal: [cat, dog]
```

> 数组和对象可以组成复合结构，使用方式和JSON类似。

### 1.5 纯量

纯量是最基本的、不可再分的值。

- 数值直接以字面量的形式表示。

```yaml
number: 12.30
```

- 布尔值用`true`和`false`表示。

```yaml
isSet: true
```

- `null`用`~`表示。

```yaml
parent: ~ 
```

- 时间采用 ISO8601 格式。

```yaml
createTime: 2001-12-14t21:59:43.10-05:00 
date: 1976-07-31
```

- YAML 允许使用两个感叹号，强制转换数据类型。

```yaml
str: !!str 123
str2: !!str true
```

### 1.6 字符串

- 字符串是最常见，也是最复杂的一种数据类型。字符串默认不使用引号表示。

```yaml
str: 这是一行字符串
```

- 如果字符串之中包含空格或特殊字符，需要放在引号之中。

```yaml
str: '内容： 字符串'
```

- 单引号和双引号都可以使用，双引号不会对特殊字符转义。

```yaml
s1: '内容\n字符串'
s2: "内容\n字符串"
```

- 单引号之中如果还有单引号，必须连续使用两个单引号转义。

```yaml
str: 'boy name is ''lucy'''
```

- 字符串可以写成多行，从第二行开始，必须有一个单空格缩进。换行符会被转为空格。

```yaml
str: 这是一段
  多行
  字符串
```

- 多行字符串可以使用`|`保留换行符，也可以使用`>`折叠换行。

```yaml
this: |
  Foo
  Bar
that: >
  Foo
  Bar
  
  ## 相等于 { this: 'Foo\nBar\n', that: 'Foo Bar\n' }
```

- `+`表示保留文字块末尾的换行，`-`表示删除字符串末尾的换行。

```yaml
s1: |
  Foo

s2: |+
  Foo


s3: |-
  Foo
  
  ## 相等于 { s1: 'Foo\n', s2: 'Foo\n\n\n', s3: 'Foo' }
```

- 字符串之中可以插入 HTML 标记。

```yaml
message: |

  <p style="color: red">
    段落
  </p>
```

### 1.7 引用

锚点`&`和别名`*`，可以用来引用。

```yaml
defaults: &defaults
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  <<: *defaults

test:
  database: myapp_test
  <<: *defaults
```

相等于：

```yaml
defaults:
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  adapter:  postgres
  host:     localhost

test:
  database: myapp_test
  adapter:  postgres
  host:     localhost
```

`&`用来建立锚点（`defaults`），`<<`表示合并到当前数据，`*`用来引用锚点。

# 二：Yml解析

### 2.1 yml解析工具

Java解析yml文件的工具较多，常用的是snakeyaml。

```xml
        <dependency>
            <groupId>org.yaml</groupId>
            <artifactId>snakeyaml</artifactId>
            <version>1.17</version>
        </dependency>
```

snakeyaml的常用api如下：

- load：加载yaml内容，解析成Object对象
- loadAs：加载yaml内容，解析成指定Bean或者LinkedHashMap
- loadAll：加载多个yaml片段，返回迭代器（多个LinkedHashMap）

> snakeyaml官网文档：https://bitbucket.org/asomov/snakeyaml/wiki/Documentation

### 2.2 Yml封装

```java
package com.qax.ngsoc.datareportsdk.utils;

import com.alibaba.fastjson.JSON;
import com.qax.ngsoc.datareportsdk.po.InputComponent;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.yaml.snakeyaml.Yaml;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.util.Collections;
import java.util.List;
import java.util.Map;
import java.util.Objects;
import static com.qax.ngsoc.datareportsdk.common.Constant.*;

/**
 * @author lizhaoxuan
 * @Description Yml解析器
 * @date 2021/07/06
 */
@Slf4j
public class YmlParser {

    private Map<String, Object> properties;

    private YmlParser(){}

    /**
     * 初始化
     * @param filePath  解析的yml文件位置
     * @return
     */
    public static YmlParser init(String filePath){
        log.info("YmlParser start init , filePath:{}",filePath);
        YmlParser ymlParser = new YmlParser();
        Yaml yaml = new Yaml();
        if (StringUtils.isBlank(filePath)){
            throw new IllegalArgumentException("yml file path must not be empty!");
        }
        File file = new File(filePath);
        if (!file.exists()){
            throw new IllegalArgumentException("yml file is not exists!");
        }
        if (!file.isFile()){
            throw new IllegalArgumentException("yml file maybe directory?");
        }
        try {
             FileInputStream fileInputStream = new FileInputStream(file);
            ymlParser.properties = yaml.loadAs(fileInputStream, Map.class);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        log.info("YmlParser init success!");
        return ymlParser;
    }

    public <T> T parse(Class<T> clazz){
        return JSON.parseObject(JSON.toJSONString(properties),clazz);
    }

    public <T> T parseOrDefault(Class<T> clazz, T defaultValue){
        try {
            T result = parse(clazz);
            return Objects.nonNull(result) ? result : defaultValue;
        }catch (Exception e){
            return defaultValue;
        }
    }

    public <T> T parse(String targetPath, Class<T> clazz){
        if (StringUtils.isBlank(targetPath)){
            throw new IllegalArgumentException(String.format("parse yml failed : targetPath(%s) is empty!", targetPath));
        }
        String[] split = targetPath.split(SEPARATOR_PERIOD);
        Object value = properties;
        for (String key : split){
            value = ((Map<String, Object>) value).get(key);
            if (Objects.isNull(value) || !(value instanceof Map)){
                throw new IllegalArgumentException(String.format("parse yml failed : targetPath(%s) is invalid!", targetPath));
            }
        }
        return JSON.parseObject(JSON.toJSONString(value),clazz);
    }

    public <T> T parseOrDefault(String targetPath, Class<T> clazz, T defaultValue){
        try {
            T result = parse(targetPath, clazz);
            return Objects.nonNull(result) ? result : defaultValue;
        }catch (Exception e){
            return defaultValue;
        }
    }

    public <T> List<T> parseArray(Class<T> clazz){
        return JSON.parseArray(JSON.toJSONString(properties),clazz);
    }

    public <T> List<T> parseArrayOrDefault(Class<T> clazz, List<T> defaultArray){
        try {
            List<T> result = parseArray(clazz);
            return Objects.nonNull(result) ? result : defaultArray;
        }catch (Exception e){
            return defaultArray;
        }
    }

    public <T> List<T> parseArray(String targetPath, Class<T> clazz){
        if (StringUtils.isBlank(targetPath)){
            throw new IllegalArgumentException(String.format("parse yml failed : targetPath(%s) is empty!", targetPath));
        }
        String[] split = targetPath.split(SEPARATOR_PERIOD);
        Object value = properties;
        for (String key : split){
            value = ((Map<String, Object>) value).get(key);
            if (Objects.isNull(value) || !(value instanceof Map)){
                throw new IllegalArgumentException(String.format("parse yml failed : targetPath(%s) is invalid!", targetPath));
            }
        }
        return JSON.parseArray(JSON.toJSONString(value),clazz);
    }

    public <T> List<T> parseArrayOrDefault(String targetPath, Class<T> clazz, List<T> defaultArray){
        try {
            List<T> result = parseArray(targetPath, clazz);
            return Objects.nonNull(result) ? result : defaultArray;
        }catch (Exception e){
            return defaultArray;
        }
    }

    public static void main(String[] args) {
        YmlParser ymlParser = YmlParser.init("data-report.yml");
        List<InputComponent> inputComponents = ymlParser.parseArray("data-report.input", InputComponent.class);
        System.out.println(inputComponents);
    }

}
```



