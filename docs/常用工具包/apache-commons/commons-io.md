# 一：Commons IO介绍

Java原生的IO分为字符流/字节流/输入流/输出流四大类，还有File/Files等相关文件操作类，对于IO操作较为繁琐。

Commons IO 是一个工具库，用来帮助开发IO功能， 它包括6个主要部分：

- Utils：提供的很多工具类，包括IO操作，文件操作等
- Input：输入，InputStream 和 Reader 包装
- Output：输出，OutputStream 和 Writer 包装
- Filters：过滤器，多种文件过滤器实现(定义了 IOFileFilter接口，同时继承了 FileFilter 和 FilenameFilter 接口)
- Comparators： 比较器，用于文件比较的多种java.util.Comparatot实现
- File Monitor：文件监控

# 二：Commons IO常用API

### 2.1 引入Maven依赖

```xml
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>
```

### 2.2 常用工具类API

commons io包提供的第一部分工具类主要包括以下几个：

- IOUtils：用于处理读，写和拷贝，这些方法基于 `InputStream`, `OutputStream`, `Reader` 和 `Writer`工作.

- FileUtils：用于读，写，拷贝和比较文件，它们基于`File`对象工作
- FilenameUtils：针对文件名进行操作，旨在保持Linux和Windows下的一致

> 除了上面三个常用的之外，还有FileSystemUtils/EndianUtils/SwappedDataInputStream等。

#### 2.2.1 IOUtils

```java
package com.rocks.commons.io;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.io.IOUtils;

import java.io.*;
import java.util.List;

/**
 * IOUtils示例
 * @author lizhaoxuan
 */
@Slf4j
public class IoUtilDemo {

    @SneakyThrows
    public static void main(String[] args)  {
        String encoding = "UTF-8";
        File sourceFile = new File("D:\\system\\target\\demo2.txt");
        FileInputStream sourceFileInputStream = new FileInputStream(sourceFile);
        File targetFile = new File("D:\\system\\target\\demo3.txt");
        FileOutputStream targetFileOutputStream = new FileOutputStream(targetFile);

        // 由于toString读取一次 导致toByteArray会读取不到数据
        // 从文本文件中读出内容
        String content = IOUtils.toString(sourceFileInputStream, encoding);
        byte[] bytes = IOUtils.toByteArray(sourceFileInputStream);
        System.out.println(content);
        System.out.println(new String(bytes,encoding));
        // 从文本文件中分行读取内容
        List<String> lines = IOUtils.readLines(sourceFileInputStream, encoding);
        System.out.println(lines);

        // 文件拷贝
        IOUtils.copy(sourceFileInputStream,targetFileOutputStream);
        // 大文件拷贝  例如2G以上等  会将缓冲区设置大
        IOUtils.copyLarge(sourceFileInputStream,targetFileOutputStream);

        // 从字符串获取输入流 可指定编码
        InputStream inputStream = IOUtils.toInputStream("File Content", encoding);
        // 获取带缓冲区的输入流 默认缓冲区1024
        InputStream bufferedInputStream = IOUtils.toBufferedInputStream(inputStream, 2048);

        // 从流中读取数据到缓冲区
        byte[] buffer = new byte[1024];
        IOUtils.read(inputStream, buffer);
        // 将缓冲区数据写入流中
        IOUtils.write("Hello",targetFileOutputStream,encoding);
        
        // 两个流的内容比较
        boolean b = IOUtils.contentEquals(sourceFileInputStream, inputStream);
        // 忽略换行符
        boolean b2= IOUtils.contentEqualsIgnoreEOL(new InputStreamReader(sourceFileInputStream), new InputStreamReader(inputStream));

        // 让流跳过指定长度
        IOUtils.skip(sourceFileInputStream,8);
    }

}
```

#### 2.2.2 FileUtils

```java
package com.rocks.commons.io;

import org.apache.commons.io.FileUtils;
import org.apache.commons.io.filefilter.FileFileFilter;
import org.apache.commons.io.filefilter.IOFileFilter;

import java.io.*;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.Arrays;
import java.util.Collection;
import java.util.Date;

/**
 * FileUtils示例
 * @author lizhaoxuan
 */
public class FileUtilsDemo {

    private static final String ZIP_PREFIX = ".zip";

    private static final String ENCODING = "UTF-8";

    public static void main(String[] args) throws IOException {

        // 复制文件夹(文件夹里的文件一块复制) 第三个参数可以用来过滤文件 可选
        FileUtils.copyDirectory(new File("D:\\system\\target"), new File("D:\\system\\target2"), new FileFilter() {
            @Override
            public boolean accept(File pathname) {
                // 过滤掉文件夹里的zip文件
                if (pathname.getName().endsWith(ZIP_PREFIX)){
                    return false;
                }
                return true;
            }
        });
        // 以目录的形式将文件夹复制到另一个文件夹里 作为一个子目录
        FileUtils.copyDirectoryToDirectory(new File("D:\\system\\target"), new File("D:\\system\\target2"));

        // 复制文件
        FileUtils.copyFile(new File("D:\\system\\target\\demo2.txt"), new File("D:\\system\\target\\demo2_bak.txt"));
        // 将文件复制到输出流(可以当做上传/下载等)
        FileUtils.copyFile(new File("D:\\system\\target\\demo2.txt"), new FileOutputStream(new File("D:\\system\\target\\demo2_bak2.txt")));
        // 将文件拷贝到指定目录
        FileUtils.copyFileToDirectory(new File("D:\\system\\target\\demo2.txt"),new File("D:\\system"));
        // 将输入流的内容拷贝到指定文件
        FileUtils.copyInputStreamToFile(new FileInputStream("D:\\system\\target\\demo2.txt"),new File("D:\\system\\target\\demo2_bak3.txt"));
        // 将URL的内容拷贝到指定文件(可以当做下载)
        FileUtils.copyURLToFile(new URL("http://rocks526.top/lzx/image-20200907173628246.png"),new File("D:\\system\\target\\download.png"));

        // 字符串写入文件 设置编码并且设置追加还是覆盖
        FileUtils.writeStringToFile(new File("D:\\system\\target\\demo2.txt"),"\nWrite By FileUtils!",ENCODING,true);
        // 写入字节数组  没有设置append则默认会覆盖原文件
        FileUtils.writeByteArrayToFile(new File("D:\\system\\target\\demo2.txt"),"\nWrite By FileUtils2!".getBytes(StandardCharsets.UTF_8));
        // 将集合内容写入文件 设置每个元素的结尾为 \n 追加模式
        FileUtils.writeLines(new File("D:\\system\\target\\demo2.txt"), Arrays.asList("line1", "line2", "line3"),"\n",true);

        // 移动文件
        FileUtils.moveFile(new File("D:\\system\\target\\demo2.txt"),new File("D:\\system\\demo2.txt"));
        // 移动文件夹
        FileUtils.moveDirectory(new File("D:\\system\\target"),new File("D:\\system\\target2"));
        // 移动文件到文件夹  第三个参数代表文件夹不存在是否创建
        FileUtils.moveFileToDirectory(new File("D:\\system\\demo2.txt"),new File("D:\\system\\target3"),true);
        // 移动文件或目录到指定的文件夹里
        FileUtils.moveToDirectory(new File("D:\\system\\target2"),new File("D:\\system\\target3"),false);

        // 删除文件夹  包括里面的所有文件
        FileUtils.deleteDirectory(new File("D:\\system\\target3"));
        // 清空文件夹里的所有内容
        FileUtils.cleanDirectory(new File("D:\\system\\target2"));

        // 递归创建文件夹
        FileUtils.forceMkdir(new File("D:\\system\\target3\\target3\\demo"));

        // 测试文件修改时间
        Date date = new Date();
        boolean fileNewer = FileUtils.isFileNewer(new File("D:\\system\\target"), date);
        System.out.println(fileNewer);
        FileUtils.writeLines(new File("D:\\system\\target\\demo.txt"),Arrays.asList("first line ", "second line"),"\n",true);
        boolean fileNewer2 = FileUtils.isFileNewer(new File("D:\\system\\target"), date);
        System.out.println(fileNewer2);
        // 对比两个文件修改时间
        boolean fileNewer1 = FileUtils.isFileNewer(new File("D:\\system\\target\\demo.txt"), new File("D:\\system\\target"));
        System.out.println(fileNewer1);

        // 遍历文件或文件夹  第三个参数是是否递归遍历子文件夹  第二个参数是限制后缀
        Collection<File> files = FileUtils.listFiles(new File("D:\\system"), new String[]{"txt", "zip", "md"}, true);
        files.stream().forEach(System.out::println);
        // 后面两个参数可以设置文件过滤器和文件夹过滤器 自定义过滤条件
        Collection<File> files1 = FileUtils.listFiles(new File("D:\\system"),null,null);
        System.out.println(files1);

        // 判断文件是否是符号连接
        boolean symlink = FileUtils.isSymlink(new File("D:\\software\\RedisDesktopManager\\Another Redis Desktop Manager.exe"));
        System.out.println(symlink);
        // 判断文件夹内是否包含某个文件或者文件夹
        boolean b = FileUtils.directoryContains(new File("D:\\system"), new File("D:\\system\\target\\demo.txt"));
        System.out.println(b);
        // 获取文件或文件夹的大小
        long size = FileUtils.sizeOf(new File("D:\\system\\lin-cms-vue"));
        System.out.println(size);
        // 创建文件
        FileUtils.touch(new File("D:\\system\\demo.txt"));
        // 比较两个文件内容是否相同
        boolean equals = FileUtils.contentEquals(new File("D:\\system\\demo.txt"), new File("D:\\system\\demo2.txt"));
        System.out.println(equals);
    }

}
```

#### 2.2.3 FilenameUtils

```java
package com.rocks.commons.io;

import org.apache.commons.io.FilenameUtils;

import java.util.Arrays;

/**
 * FilenameUtils示例
 * @author lizhaoxuan
 */
public class FilenameUtilsDemo {

    private static final String fullName = "D:\\system\\target\\demo1.txt";

    public static void main(String[] args) {

        // 合并目录和文件名为全路径
        String filePath = FilenameUtils.concat("D:\\system\\target", "demo1.txt");
        System.out.println(filePath);

        // 获取文件名
        String name = FilenameUtils.getName(filePath);
        System.out.println(name);

        // 获取文件名 去除目录和后缀后的
        String baseName = FilenameUtils.getBaseName(filePath);
        System.out.println(baseName);

        // 获取文件后缀
        String extension = FilenameUtils.getExtension(filePath);
        System.out.println(extension);

        // 获取文件的目录
        String fullPath = FilenameUtils.getFullPath(filePath);
        System.out.println(fullPath);

        // 转换路径分隔符为当前系统的
        String filePathNew = FilenameUtils.separatorsToSystem(filePath);
        System.out.println(filePathNew);

        // 判断文件路径是否相同
        boolean equals = FilenameUtils.equals(filePath, fullName);
        System.out.println(equals);
        // 判断文件路径是否相同 格式化
        boolean b = FilenameUtils.equalsNormalized(filePath, fullPath);
        System.out.println(b);

        // 判断文件扩展名是否在指定的集合里
        boolean extension1 = FilenameUtils.isExtension(filePath, Arrays.asList("zip", "md", "java"));
        System.out.println(extension1);
    }

}
```

### 2.3 输入常用API

在`org.apache.commons.io.input`包下有许多InputStrem类的实现，便于我们使用，例如TeeInputStream。

TeeInputStream将InputStream以及OutputStream作为参数传入其中，自动实现将输入流的数据读取到输出流中。而且，通过传入第三个参数，一个boolean类型参数，可以在数据读取完毕之后自动关闭输入流和输出流。

```java
package com.rocks.commons.io.input;

import lombok.SneakyThrows;
import org.apache.commons.io.input.TeeInputStream;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.nio.charset.StandardCharsets;

/**
 *  TeeInputStream示例
 * @author lizhaoxuan
 */
public class TeeInputStreamDemo {

    @SneakyThrows
    public static void main(String[] args)  {
        String msg = "Output Data By TeeInputStream!";
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        // TeeInputStream可以自动将输入流的内容转给输出流 第三个参数设置为true代表读取完后自动关闭流
        TeeInputStream teeInputStream = new TeeInputStream(new ByteArrayInputStream(msg.getBytes(StandardCharsets.UTF_8)),byteArrayOutputStream,true);
        teeInputStream.read(new byte[msg.length()]);
        System.out.println(byteArrayOutputStream.toString());
    }

}
```

### 2.4 输出常用API

与`org.apache.commons.io.input`包中的类相似， `org.apache.commons.io.output`包中同样有OutputStream类的实现，他们可以在多种情况下使用。

例如：TeeOutputStream，它可以将输出流进行分流，换句话说我们可以用一个输入流将数据分别读入到两个不同的输出流。

```java
package com.rocks.commons.io.output;

import lombok.SneakyThrows;
import org.apache.commons.io.input.TeeInputStream;
import org.apache.commons.io.output.TeeOutputStream;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.nio.charset.StandardCharsets;

/**
 *  TeeOutputStream示例
 * @author lizhaoxuan
 */
public class TeeOutputStreamDemo {

    @SneakyThrows
    public static void main(String[] args) {
        String msg = "Output Data By TeeInputStream!";
        ByteArrayOutputStream byteArrayOutputStream1 = new ByteArrayOutputStream();
        ByteArrayOutputStream byteArrayOutputStream2 = new ByteArrayOutputStream();
        TeeOutputStream teeOutputStream = new TeeOutputStream(byteArrayOutputStream1, byteArrayOutputStream2);
        TeeInputStream teeInputStream = new TeeInputStream(new ByteArrayInputStream(msg.getBytes(StandardCharsets.UTF_8)),teeOutputStream);
        teeInputStream.read(new byte[msg.length()]);
        System.out.println(byteArrayOutputStream1.toString());
        System.out.println(byteArrayOutputStream2.toString());
    }

}
```

### 2.5 过滤器常用API

过滤器可以以组合的方式使用并且它的用途非常多样。

它可以轻松的区分不同的文件并且找到满足我们条件的文件。

我们可以组合不同的过滤器来执行文件的逻辑比较并且精确的获取我们所需要文件，而无需使用冗余的字符串比较来寻找我们的文件。

基本功能过滤器有以下：

- 根据类型过滤
  - FileFileFilter ：文件过滤器
  - DirectoryFilter ： 目录过滤器
- 根据文件名称过滤
  - PrefixFileFilter ：前缀过滤器
  - SuffixFileFilter ：后缀过滤器
  - NameFileFilter ：文件名称过滤器
  - WildcardFileFilter ：通配符过滤器
  - RegexFileFilter ：正则过滤器
- 根据时间过滤
  - AgeFileFilter ： 最后修改时间过滤器
  - MagicNumberFileFileter ：魔数过滤器
- 根据大小过滤
  - EmptyFileFilter ： 空文件/目录过滤器
  - SizeFileFilter ：文件/目录大小过滤器
- 根据文件属性过滤
  - HiddenFileFilter ：隐藏文件过滤器
  - CanReadFileFilter ：可读文件过滤器
  - CanWriteFileFilter ： 可写文件过滤器

逻辑关系过滤器有以下：

- AndFileFilter ：基于AND逻辑运算组合多个过滤器
- OrFileFilter ：基于OR逻辑运算组合多个过滤器
- NotFileFilter ：基于NOT逻辑运算组合多个过滤器
- TrueFileFilter ：不进行过滤
- FalseFileFilter ：过滤所有文件和目录

还有一个工具类FileFilterUtils，提供一些工厂方法用于生成各类文件过滤器，提供一些静态方法用于对指定的File集合进行过滤：

- FileFilterUtils.ageFileFilter(Date cutoffDate)
- FileFilterUtils.and(IOFileFilter... filters)
- FileFilterUtils.asFileFilter(FileFilter filter)
- FileFilterUtils.directoryFileFilter()
- FileFilterUtils.falseFileFilter()
- FileFilterUtils.fileFileFilter()
- FileFilterUtils.filter(IOFileFilter filter, File... files)
- FileFilterUtils.filterList(IOFileFilter filter, File... files)
- FileFilterUtils.filterSet(IOFileFilter filter, File... files)
- FileFilterUtils.or(IOFileFilter... filters)
- FileFilterUtils.prefixFileFilter(String prefix)
- FileFilterUtils.sizeFileFilter(long threshold)
- FileFilterUtils.suffixFileFilter(String suffix)
- FileFilterUtils.trueFileFilter()

```java
package com.rocks.commons.io.filter;

import org.apache.commons.io.FileUtils;
import org.apache.commons.io.filefilter.*;
import java.io.File;
import java.util.Arrays;
import java.util.Collection;

/**
 *  过滤器代码示例
 * @author lizhaoxuan
 */
public class FilterDemo {

    private static final String dirPath = "D:\\system";

    private static final String[] acceptFileNames = {"README.md","demo.txt"};

    private static final String[] suffixFileNames = {"md","txt","vue"};

    public static void main(String[] args) {

        dirList();
        FileUtilsList();
    }

    /**
     * 使用FileUtils的递归遍历目录下所有文件
     * 由于FileUtils没提供支持过滤器和递归遍历的api 因此只能先根据后缀匹配 拿到所有文件 然后steam过滤
     */
    private static void FileUtilsList(){

        Collection<File> files = FileUtils.listFiles(new File(dirPath), null, true);

        // 文件名过滤器  所有过滤器都允许传入多个匹配参数 后续只以一个示例
        files.stream().filter(new NameFileFilter(acceptFileNames)::accept).forEach(System.out::println);
        System.out.println("==============================================");

        // 通配符过滤器
        files.stream().filter(new WildcardFileFilter("*demo*")::accept).forEach(System.out::println);
        System.out.println("==============================================");

        // 前缀过滤器
        files.stream().filter(new PrefixFileFilter("README")::accept).forEach(System.out::println);
        System.out.println("==============================================");

        // 后缀过滤器
        files.stream().filter(new SuffixFileFilter(suffixFileNames)::accept).forEach(System.out::println);
        System.out.println("==============================================");

        // 逻辑或过滤器
        files.stream().filter(new OrFileFilter(new NameFileFilter(acceptFileNames),new SuffixFileFilter(suffixFileNames))::accept).forEach(System.out::println);
        System.out.println("==============================================");

        // 逻辑与过滤器
        files.stream().filter(new AndFileFilter(new NameFileFilter(acceptFileNames),new SuffixFileFilter(suffixFileNames))::accept).forEach(System.out::println);
        System.out.println("==============================================");
    }

    /**
     * 使用File.list遍历 只能遍历一层目录 不可递归
     */
    private static void dirList(){
        File dir = new File(dirPath);

        // 文件名过滤器  所有过滤器都允许传入多个匹配参数 后续只以一个示例
        String[] list = dir.list(new NameFileFilter(acceptFileNames));
        Arrays.stream(list).forEach(System.out::println);

        // 通配符过滤器
        String[] list1 = dir.list(new WildcardFileFilter("*demo*"));
        Arrays.stream(list1).forEach(System.out::println);

        // 前缀过滤器
        String[] list2 = dir.list(new PrefixFileFilter("REAMDE"));
        Arrays.stream(list2).forEach(System.out::println);

        // 后缀过滤器
        String[] list3 = dir.list(new SuffixFileFilter(suffixFileNames));
        Arrays.stream(list3).forEach(System.out::println);

        // 逻辑或过滤器
        String[] list4 = dir.list(new OrFileFilter(new NameFileFilter(acceptFileNames),new SuffixFileFilter(suffixFileNames)));
        Arrays.stream(list4).forEach(System.out::println);

        // 逻辑与过滤器
        String[] list5 = dir.list(new AndFileFilter(new NameFileFilter(acceptFileNames),new SuffixFileFilter(suffixFileNames)));
        Arrays.stream(list5).forEach(System.out::println);
    }

}
```

### 2.6 比较器常用API

`使用org.apache.commons.io.comparator` 包下的类可以让你轻松的对文件或目录进行比较或者排序。你只需提供一个文件列表，选择不同的类就可以实现不同方式的文件比较。

比较常用的有以下几种比较器：

-  NameFileComparator ：根据文件名称比较
-  SizeFileComparator ：根据文件大小比较
-  LastModifiedFileComparator ：根据修改时间比较

```java
package com.rocks.commons.io.comparator;

import org.apache.commons.io.FileUtils;
import org.apache.commons.io.IOCase;
import org.apache.commons.io.comparator.LastModifiedFileComparator;
import org.apache.commons.io.comparator.NameFileComparator;
import org.apache.commons.io.comparator.SizeFileComparator;
import java.io.File;
import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

/**
 * 比较器示例代码
 * @author lizhaoxuan
 */
public class ComparatorDemo {

    private static final String dirPath = "D:\\system\\target";

    public static void main(String[] args) {

        Collection<File> filesTmp = FileUtils.listFiles(new File(dirPath), null, false);
        List<File> files = filesTmp.stream().collect(Collectors.toList());

        // 按文件名称排序 设置大小写不敏感
        NameFileComparator nameFileComparator = new NameFileComparator(IOCase.INSENSITIVE);
        List<File> sort = nameFileComparator.sort(files);
        System.out.println(sort);

        // 按照文件大小排序
        SizeFileComparator sizeFileComparator = new SizeFileComparator();
        List<File> sort1 = sizeFileComparator.sort(files);
        System.out.println(sort1);

        // 按最后修改时间排序
        LastModifiedFileComparator lastModifiedFileComparator = new LastModifiedFileComparator();
        List<File> sort2 = lastModifiedFileComparator.sort(files);
        System.out.println(sort2);

    }

}
```

### 2.7 监控器常用API

`org.apache.commons.io.monitor`包下的类包含的方法可以获取文件的指定信息，不过更重要的是，它可以创建处理器（handler）来跟踪指定文件或目录的变化并且可以在文件或目录发生变化的时候进行一些操作。

实现主要是由文件监控类FileAlterationMonitor中的线程按指定的间隔不停的扫描文件观察器FileAlterationObserver，如果有文件的变化，则根据相关的文件比较器，判断文件时新增，还是删除，还是更改。（默认为1000毫秒执行一次扫描） 。

监控步骤：

1、创建一个FileAlterationObserver对象，传入一个要监控的目录，这个对象会观察这些变化。

2、通过调用addListener()方法，为observer对象添加一个 FileAlterationListener对象。可以通过很多种方式来创建，继承适配器类或者实现接口或者使用匿名内部类，实现所需要的监控方法。

3、创建一个FileAlterationMonitor 对象，将已经创建好的observer对象添加其中并且传入时间间隔参数（单位是毫秒）。

4、调用start()方法即可开启监视器，如果想停止监视器，调用stop()方法即可。

```java
package com.rocks.commons.io.monitor;

import org.apache.commons.io.monitor.FileAlterationListener;
import org.apache.commons.io.monitor.FileAlterationMonitor;
import org.apache.commons.io.monitor.FileAlterationObserver;
import java.io.File;

/**
 * 文件监控示例
 * @author lizhaoxuan
 */
public class MonitorDemo {

    private static final String filePath = "D:\\system\\target";

    public static void main(String[] args) throws Exception {
        // 1.创建文件监听对象  也可以监听目录 第二个参数可以传入过滤器
        FileAlterationObserver fileAlterationObserver = new FileAlterationObserver(filePath, null);
        // 2.添加文件监听器
        fileAlterationObserver.addListener(new FileAlterationListener() {
            @Override
            public void onStart(FileAlterationObserver fileAlterationObserver) {
                // 开始新一轮扫描
                // System.out.println("file listener scanning start ...");
            }
            @Override
            public void onDirectoryCreate(File file) {
                System.out.println("dir create : " + file.getAbsolutePath());
            }
            @Override
            public void onDirectoryChange(File file) {
                System.out.println("dir changed : " + file.getAbsolutePath());
            }
            @Override
            public void onDirectoryDelete(File file) {
                System.out.println("dir deleted : " + file.getAbsolutePath());
            }
            @Override
            public void onFileCreate(File file) {
                System.out.println("file created : " + file.getAbsolutePath());
            }
            @Override
            public void onFileChange(File file) {
                System.out.println("file changed : " + file.getAbsolutePath());
            }
            @Override
            public void onFileDelete(File file) {
                System.out.println("file deleted : " + file.getAbsolutePath());
            }
            @Override
            public void onStop(FileAlterationObserver fileAlterationObserver) {
                // 新一轮扫描结束
                // System.out.println("file listener scanning stop ...");
            }
        });
        // 3.创建监视器 第一个参数是监控间隔 5s  第二个参数是要监控的对象 可以传入多个
        FileAlterationMonitor fileAlterationMonitor = new FileAlterationMonitor(5000, fileAlterationObserver);
        // 4.开始监控
        fileAlterationMonitor.start();
        System.out.println("waiting event ...");
        // 5.停止监控
        Thread.sleep(1000*60*60);
        System.out.println("waiting event end ...");
        fileAlterationMonitor.stop();
        System.out.println("end ...");
    }

}
```

```java
package com.rocks.commons.io.monitor;

import lombok.SneakyThrows;
import org.apache.commons.io.FileUtils;
import java.io.File;
import java.util.Arrays;

/**
 * 对监控的文件进行操作 测试监控API
 * @author lizhaoxuan
 */
public class Demo {

    private static final String filePath = "D:\\system\\target\\demo.txt";

    @SneakyThrows
    public static void main(String[] args) {
        File file = new File(filePath);
        FileUtils.writeLines(file, Arrays.asList("line1", "line2"),"\n",true);
        FileUtils.touch(new File("D:\\system\\target\\demo2.txt"));
        FileUtils.writeLines(new File("D:\\system\\target\\demo2.txt"),Arrays.asList("line1", "line2"),"\n",true);
    }

}
```



