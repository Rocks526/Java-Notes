# <a id="js">FastDFS介绍</a>

- 一个使用C语言编写的开源的高性能的分布式文件系统
- 可以通过FastDFS实现对文件的管理，包括文件存储，文件同步，文件下载/上传等功能
- 适合存储中小文件（4KB - 500MB之间），HDFS适合存储大文件
- 可实现冗余备份，负载均衡，线性扩容等机制，并注重高性能，高可用指标

# <a id="sb">SpringBoot-FastDFS</a>

- 安装FastDfs的官方Java客户端

由于FastDfs的官方Java客户端在Maven仓库没有对应依赖，因此需要自己去github下载安装到本地

FastDfs的官方Java客户端地址：https://github.com/happyfish100/fastdfs-client-java

下载之后打开项目，用Maven编译打包到本地仓库

- 创建SpringBoot项目，引入web和fastdfs依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.csource</groupId>
            <artifactId>fastdfs-client-java</artifactId>
            <version>1.29-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

- 添加FastDfs配置文件

```properties
# app
server.port=8080
# 存储
storage.fastdfs.tracker_server=106.13.191.813:22122
```

除了TrackerServer的IP之外，其他属性可选，参考https://github.com/happyfish100/fastdfs-client-java

- 创建service层接口


```java
package com.rocks.springboot.fastdfs.service;



/**
 * 文件存储服务接口
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 13:17
 */
public interface StorageService {


    /**
     * 上传文件
     *
     * @param data    文件的二进制内容
     * @param extName 扩展名
     * @return 上传成功后返回生成的文件URL；失败，返回null
     */
    String upload(byte[] data, String extName);

    /**
     * 删除文件
     *
     * @param filePath 被删除的文件路径
     * @return 删除成功后返回0，失败后返回错误代码
     */
    int delete(String filePath);

    /**
     * 下载文件
     *
     * @param filePath 要下载的文件路径
     * @return 删除成功后返回0，失败后返回错误代码
     */
    byte[] downLoad(String filePath);


}
```

- 注入FastDfs服务实现类

```java
package com.rocks.springboot.fastdfs.service.Impl;

import com.rocks.springboot.fastdfs.exception.FastDfsException;
import com.rocks.springboot.fastdfs.service.StorageService;
import lombok.extern.slf4j.Slf4j;
import org.csource.common.NameValuePair;
import org.csource.fastdfs.*;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import java.io.File;
import java.io.FileWriter;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Date;


/**
 * FastDfs文件存储服务
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 13:17
 */
@Slf4j
@Service
public class FastDfsServiceImpl implements StorageService, InitializingBean {


    private TrackerServer trackerServer = null;

    private TrackerClient trackerClient = null;

    private StorageServer storageServer = null;

    private StorageClient storageClient = null;

    @Value("${storage.fastdfs.tracker_server}")
    private String trackerServerIP;

    private SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Override
    public String upload(byte[] data, String extName) {
        try{
            Date date = new Date();
            String createTime = simpleDateFormat.format(date);
            //元数据
            NameValuePair[] meta_list = new NameValuePair[2];
            meta_list[0] = new NameValuePair("author", "Rocks526");
            meta_list[1] = new NameValuePair("createTime", createTime);
            String[] res = storageClient.upload_file(data, extName,null);
            StringBuilder filePath = new StringBuilder();
            filePath.append(res[0]).append("/").append(res[1]);
            return filePath.toString();
        }catch (Exception e){
            throw new FastDfsException("FastDfs文件上传失败！",e);
        }
    }


    @Override
    public int delete(String filePath) {
        try{
            int index = filePath.indexOf('/');
            String groupName = filePath.substring(0, index);
            String fileName = filePath.substring(index+1);
            return storageClient.delete_file(groupName,fileName);
        }catch (Exception e){
            throw new FastDfsException("FastDfs文件删除失败！",e);
        }
    }

    @Override
    public byte[] downLoad(String filePath) {
        try{
            int index = filePath.indexOf('/');
            String groupName = filePath.substring(0, index);
            String fileName = filePath.substring(index+1);
            byte[] bytes = storageClient.download_file(groupName, fileName);
            return bytes;
        }catch (Exception e){
            throw new FastDfsException("FastDfs文件下载失败！",e);
        }
    }


    //初始化
    @Override
    public void afterPropertiesSet() throws Exception {
        File confFile = File.createTempFile("FastDfs", ".conf");
        PrintWriter confWriter = new PrintWriter(new FileWriter(confFile));
        confWriter.println("tracker_server=" + trackerServerIP);
        confWriter.close();
        ClientGlobal.init(confFile.getAbsolutePath());
        confFile.delete();
        trackerClient = new TrackerClient();
        trackerServer = trackerClient.getTrackerServer();
        storageClient = new StorageClient1(trackerServer, storageServer);
        log.info("Init FastDFS Success! Tracker_server : {}", trackerServer);
    }

}
```

- 创建测试Controller

```java
package com.rocks.springboot.fastdfs.controller;

import com.rocks.springboot.fastdfs.service.StorageService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import javax.imageio.stream.FileImageOutputStream;
import java.io.*;

/**
 * FastDfs文件存储服务测试
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 15:43
 */
@RestController
public class DemoController {


    @Autowired
    private StorageService storageService;



    @RequestMapping(value = "/upload",method = RequestMethod.POST)
    public String uploadFile(@RequestBody MultipartFile uploadFile) throws IOException {
        // 获取文件后缀
        String fileName = uploadFile.getOriginalFilename();
        String extName = fileName.substring(fileName.lastIndexOf(".") + 1);
        String filePath = storageService.upload(uploadFile.getBytes(),extName);
        return filePath;
    }

    @RequestMapping(value = "/delete",method = RequestMethod.DELETE)
    public String uploadFile(@RequestParam(value = "filePath",required = true) String filePath)  {
        int delete = storageService.delete(filePath);
        if ( delete == 0){
            return "success!";
        }
        return "failed!";
    }

    @RequestMapping(value = "/downLoad",method = RequestMethod.GET)
    public String downLoadFile(@RequestParam(value = "filePath",required = true) String filePath) throws IOException {

        String extName = filePath.substring(filePath.lastIndexOf(".") + 1);
        byte[] bytes = storageService.downLoad(filePath);
        String path = "C:\\Users\\Rocks526\\Desktop\\downLoad." + extName;
        FileImageOutputStream imageOutput = new FileImageOutputStream(new File(path));
        imageOutput.write(bytes, 0, bytes.length);
        imageOutput.close();
        System.out.println("Make Picture success,Please find image in " + path);
        return "success!";
    }


}
```

- 自定义FastDfs服务异常

```java
package com.rocks.springboot.fastdfs.exception;

import lombok.Data;

/**
 * FastDfs异常
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 15:26
 */
@Data
public class FastDfsException extends RuntimeException {

    private String msg;

    private Throwable cause;

    public FastDfsException(String msg) {
        super(msg);
        this.msg = msg;
        this.cause = null;
    }

    public FastDfsException(String msg,Throwable cause) {
        super(msg,cause);
        this.msg = msg;
        this.cause = cause;
    }

}
```

****

### 注意事项

- 开放FastDfs服务器的23000端口
- 上传文件时的元数据可以自定义，但需要注意数组长度，如果数组存在空的位置，上传文件时会抛出NPE
- 删除文件时，groupName和fileName都没有之前的"/"，如果出现"/"，删除会返回状态码22，多次删除可能抛出异常
- 使用Java操作FastDfs时，除了这个原生客户端之外，github上还有一个基于原生客户端封装的Java客户端，适合集成SpringBoot，地址：https://github.com/tobato/FastDFS_Client