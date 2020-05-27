# 七牛云介绍

七牛云对象存储 Kodo 是七牛云提供的高可靠、强安全、低成本、可扩展的存储服务。您可通过控制台、API、SDK 等方式简单快速地接入七牛存储服务，实现海量数据的存储和管理。通过 Kodo 可以进行文件的上传、下载和管理。

此外，Kodo 的姊妹产品[融合 CDN](https://www.qiniu.com/products/fusion)可以对文件下载进行加速，[智能多媒体 API](https://www.qiniu.com/products/dora)更是提供了丰富的基于海量数据深度学习算法的计算机视觉服务，如人脸技术、场景物体识别、OCR 文字识别和内容审核等。

> 以上内容摘自七牛云官网。
>
> 七牛云提供对象存储服务，注册后每一月有免费的10G额度，并提供一个月的免费域名，适合新手测试开发OSS云服务。

# SpringBoot整合七牛云实现对象存储

> 在SpringBoot使用七牛云之前，首先需要完成七牛云的注册和空间的创建。

- 创建SpringBoot项目，引入七牛云依赖

```xml
        <!-- 七牛云依赖 -->
        <dependency>
            <groupId>com.qiniu</groupId>
            <artifactId>qiniu-java-sdk</artifactId>
            <version>[7.2.0, 7.2.99]</version>
        </dependency>
```

> Version中的`[7.2.0, 7.2.99]`指定了一个版本范围，每次更新`pom.xml`的时候会尝试去下载`7.2.x`版本中的最新版本，也可以手动指定一个固定的版本。

- Service接口定义

```java
package com.rocks.springboot.springbootstorage.service;

/**
 * @Author Rocks526
 * @ClassName StorageService
 * @Description 对象存储服务接口
 * @Date 2020/5/27 18:11
 **/
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

- Service实现类

```java
package com.rocks.springboot.springbootstorage.service.Impl;

import com.google.gson.Gson;
import com.qiniu.common.QiniuException;
import com.qiniu.http.Response;
import com.qiniu.storage.BucketManager;
import com.qiniu.storage.Configuration;
import com.qiniu.storage.Region;
import com.qiniu.storage.UploadManager;
import com.qiniu.storage.model.DefaultPutRet;
import com.qiniu.util.Auth;
import com.rocks.springboot.springbootstorage.exception.FastDfsException;
import com.rocks.springboot.springbootstorage.exception.QiNiuYunException;
import com.rocks.springboot.springbootstorage.service.StorageService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;


/**
 * @Author Rocks526
 * @ClassName QiNiuYunServiceImpl
 * @Description 七牛云存储服务
 * @Date 2020/5/27 18:25
 **/
@Slf4j
@Service("qiNiuYunServiceImpl")
public class QiNiuYunServiceImpl implements StorageService, InitializingBean {


    @Value("${storage.qiniuyun.ak}")
    private String AccessKey;

    @Value("${storage.qiniuyun.sk}")
    private String SecretKey;

    @Value("${storage.qiniuyun.bucket}")
    private String Bucket;

    @Value("${storage.qiniuyun.domain}")
    private String domain;

    private Auth auth;

    private String uploadToken;

    private UploadManager uploadManager;

    private BucketManager bucketManager;


    @Override
    public String upload(byte[] data, String extName) {
        //文件名 不指定的情况下默认以文件内容的hash为文件名
        String fileName = null;
        try {
            //上传文件
            Response response = uploadManager.put(data, fileName, uploadToken);
            //解析上传成功的结果
            DefaultPutRet putRet = new Gson().fromJson(response.bodyString(), DefaultPutRet.class);
            String filePath = domain + "/" + putRet.key;
            log.info("七牛云文件上传成功,filePath:{}",filePath);
            return filePath;
        } catch (Exception e) {
            throw new QiNiuYunException("七牛云文件上传失败！",e);
        }
    }

    @Override
    public int delete(String filePath) {
        try{
            bucketManager.delete(Bucket,filePath);
            log.info("七牛云文件删除成功,filePath:{}",filePath);
        }catch (QiniuException e){
            log.error("七牛云文件删除失败,Reason:{}",e.response.toString());
            throw new FastDfsException("七牛云文件删除失败！",e);
        }
        return 0;
    }

    @Override
    public byte[] downLoad(String filePath) {
        return new byte[0];
    }


    //初始化
    @Override
    public void afterPropertiesSet() throws Exception {
        //创建配置对象 指定Region为华北地区
        Configuration configuration = new Configuration(Region.region1());
        //获取上传管理器
        uploadManager = new UploadManager(configuration);
        //获取凭证
        auth = Auth.create(AccessKey, SecretKey);
        //获取Token
        uploadToken = auth.uploadToken(Bucket);
        //获取空间管理器
        bucketManager = new BucketManager(auth,configuration);
        log.info("Init QiNiuYunStorage Success!");
    }

}
```

- 测试Controller

```java
package com.rocks.springboot.springbootstorage.controller;

import com.rocks.springboot.springbootstorage.service.StorageService;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import javax.annotation.Resource;
import javax.imageio.stream.FileImageOutputStream;
import java.io.File;
import java.io.IOException;

/**
 * @Author Rocks526
 * @ClassName QiNiuYunController
 * @Description QiNiuYun测试Controller
 * @Date 2020/5/27 18:11
 **/
@RestController
@RequestMapping("/qiniuyun")
public class QiNiuYunController {


    @Resource(name = "qiNiuYunServiceImpl")
    private StorageService storageService;


    @RequestMapping(value = "/upload",method = RequestMethod.PUT)
    public String uploadFile(@RequestBody MultipartFile uploadFile) throws IOException {
        String filePath = storageService.upload(uploadFile.getBytes(),null);
        return filePath;
    }

    @RequestMapping(value = "/delete",method = RequestMethod.DELETE)
    public String uploadFile(@RequestParam(value = "filePath",required = true) String filePath)  {
        int delete = storageService.delete(filePath);
        if (delete == 0){
            return "success!";
        }
        return "failed!";
    }

}
```

> 七牛云上传文件支持两种方式：
>
> - 客户端根据上传凭证上传
> - 服务端直传
>
> 以上示例代码是基于服务端直传模式。
>
> 除了文件上传，删除之外，七牛云还支持文件重命名，移动等文件管理操作，本文只做七牛云的简单入门示例，更多操作可参考官方文档：https://developer.qiniu.com/kodo/sdk/1239/java