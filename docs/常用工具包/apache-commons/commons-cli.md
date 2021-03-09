# 一：commons-cli介绍

Commons Cli是apache提供的一个Java命令行参数验证/解析工具包。

Commons Cli将命令行参数解析分为三个阶段：

- 定义阶段：定义命令行参数的选项配置等
- 解析：将命令行参数解析成实体对象，同时会验证一些必须的参数是否携带
- 审讯：通过解析阶段会返回一个Vo对象，根据Vo对象可以判断包含哪些参数，进行对应逻辑处理

> Commons Cli官网：https://github.com/apache/commons-cli

# 二：commons-cli常用API

- 以往kafka的topic发送日志样本为例，解析输入的参数：

```java
package com.rocks.commons.cli;

import lombok.extern.slf4j.Slf4j;
import org.apache.commons.cli.*;

/**
 * 命令行参数解析示例
 * @author lizhaoxuan
 */
@Slf4j
public class LineParamParseDemo {

    public static void main(String[] args) throws ParseException {
        // 初始化参数
        Options options = new Options();
        // 四个参数 分别是: 是否必须 参数名字 是否有参数值 描述信息
        options.addOption(Option.builder("help").required(false).hasArg(false).desc("help information").build());
        options.addOption(Option.builder("topic").required(false).hasArg(true).desc("kafka topic").build());
        options.addOption(Option.builder("time").required(false).hasArg(true).desc("send continued time (min)").build());
        options.addOption(Option.builder("eps").required(false).hasArg(true).desc("eps").build());
        options.addOption(Option.builder("log").required(true).hasArg(true).desc("log file path or directory path").build());
        options.addOption(Option.builder("host").required(false).hasArg(true).desc("kafka host").build());
        options.addOption(Option.builder("port").required(false).hasArg(true).desc("kafka port").build());
        options.addOption(Option.builder("dev_ip").required(true).hasArg(true).desc("msg dev_ip").build());
        options.addOption(Option.builder("collect_ip").required(true).hasArg(true).desc("collector ip").build());
        options.addOption(Option.builder("encoding").required(false).argName("encoding").hasArg(true).desc("encoding").build());
        options.addOption(Option.builder("m").required(false).argName("multiline").hasArg(false).desc("multiline flag").build());

        DefaultParser defaultParser = new DefaultParser();
        CommandLine cmd = defaultParser.parse(options, args);

        InputArgs inputArgs = new InputArgs();

        if(cmd.hasOption("help")) {
            // 调用默认的help函数打印
            HelpFormatter formatter = new HelpFormatter();
            formatter.printHelp( "java -jar SendLog2basp-1.0-SNAPSHOT-jar-with-dependencies.jar [OPTION]", options);
            System.exit(0);
        }

        if (cmd.hasOption("eps")){
            inputArgs.setEps(Integer.parseInt(cmd.getOptionValue("eps")));
        }
        if (cmd.hasOption("topic")){
            inputArgs.setTopic(cmd.getOptionValue("topic"));
        }
        if (cmd.hasOption("time")){
            inputArgs.setSendTime(Integer.parseInt(cmd.getOptionValue("time")));
        }
        if (cmd.hasOption("host")){
            inputArgs.setHost(cmd.getOptionValue("host"));
        }
        if (cmd.hasOption("port")){
            inputArgs.setPort(cmd.getOptionValue("port"));
        }
        if (cmd.hasOption("encoding")){
            inputArgs.setEncoding(cmd.getOptionValue("encoding"));
        }
        if (cmd.hasOption("m")){
            inputArgs.setIsMulti(true);
        }
        inputArgs.setDevIp(cmd.getOptionValue("dev_ip"));
        inputArgs.setCollectIp(cmd.getOptionValue("collect_ip"));
        inputArgs.setLogPath(cmd.getOptionValue("log"));

        System.out.println("Log Path : " + inputArgs.getLogPath());
        System.out.println("Log Read Format : " + (inputArgs.getIsMulti() ? "multiline" : "single"));
        System.out.println("Log Encoding : " + inputArgs.getEncoding());
        System.out.println("Kafka Host : " + inputArgs.getHost());
        System.out.println("Kafka Port : " + inputArgs.getPort());
        System.out.println("Kafka Topic : " + inputArgs.getTopic());
        System.out.println("Log Send Time : " + inputArgs.getSendTime());
        System.out.println("Send Eps : " + inputArgs.getEps());
        System.out.println("Msg dev_ip : " + inputArgs.getDevIp());
        System.out.println("Msg collect_ip : " + inputArgs.getCollectIp());
    }

}
```

