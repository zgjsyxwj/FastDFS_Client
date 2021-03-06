FastDFS-Client 1.25.3-RELEASE(2017-03-11)
---

This is a java client lib for [FastDFS](https://github.com/happyfish100/fastdfs).

##介绍

在原作者YuQing与yuqih发布的java客户端基础上进行了大量重构工作，便于Java工作者学习与阅读。

主要特性

1. 对关键部分代码加入了单元测试，便于理解与服务端的接口交易，提高接口质量
2. 将以前对byte硬解析风格重构为使用 对象+注解 的形式，尽量增强了代码的可读性
3. 支持对服务端的连接池管理(commons-pool2)
4. 支持上传图片时候检查图片格式，并且自动生成缩略图

##运行环境要求

由于笔者主要工作环境是SpringBoot，因此目前客户端主要依赖于SpringBoot

    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.2.7.RELEASE</version>
    <relativePath />
    
* JDK环境要求  1.7
* FastDFS服务端 5.07 测试通过

##单元测试

由于工作时间关系与解析原代码的复杂性，单元测试无法完全做到脱离FastDFS服务端，请见谅。

执行单元测试需要配置TestConstants文件当中参数

在Tracker与Storage都在一个机器的环境下

      private static String ip_home = "192.168.1.105";
      public static InetSocketAddress address = new InetSocketAddress(ip_home, FdfsMockSocketServer.PORT);
      public static InetSocketAddress store_address = new InetSocketAddress(ip_home, FdfsMockSocketServer.STORE_PORT);
      
      public static final String DEFAULT_STORAGE_IP = ip_home;
  
      
在Tracker与Storage不在一个机器的环境下      
     
    private static String ip_work = "192.168.174.47";
    private static String ip_work_store = "192.168.174.49";
    public static InetSocketAddress address = new InetSocketAddress(ip_work, FdfsMockSocketServer.PORT);
    public static InetSocketAddress store_address = new InetSocketAddress(ip_work_store, FdfsMockSocketServer.STORE_PORT);
    
    public static final String DEFAULT_STORAGE_IP = ip_work_store;
   

##FastDFS-Client使用方式

###1.在项目Pom当中加入依赖

Maven依赖为

    <dependency>
        <groupId>com.github.tobato</groupId>
        <artifactId>fastdfs-client</artifactId>
        <version>1.25.3-RELEASE</version>
    </dependency>


###2.将Fdfs配置引入项目

将FastDFS-Client客户端引入本地化项目的方式非常简单，在SpringBoot项目当中

    /**
     * 导入FastDFS-Client组件
     * 
     * @author tobato
     *
     */
    @Configuration
    @Import(FdfsClientConfig.class)
    public class ComponetImport {
        // 导入依赖组件
    }
    
是的，只需要一行注解 @Import(FdfsClientConfig.class)

###3.在application.yml当中配置Fdfs相关参数
    # ===================================================================
    # 分布式文件系统FDFS配置
    # ===================================================================
    fdfs:
      soTimeout: 1501
      connectTimeout: 601 
      thumbImage:             #缩略图生成参数
        width: 150
        height: 150
      trackerList:            #TrackerList参数,支持多个
        - 192.168.1.105:22122
        - 192.168.1.106:22122 

如果有必要可以参考 apache.pool2 参数配置连接池属性

    fdfs:
       ..其他配置信息..
      pool:
        #从池中借出的对象的最大数目
        maxTotal: 153
        #获取连接时的最大等待毫秒数100
        maxWaitMillis: 102

###4.使用接口服务对Fdfs服务端进行操作

主要接口包括

1. TrackerClient - TrackerServer接口 
2. GenerateStorageClient - 一般文件存储接口 (StorageServer接口)
3. FastFileStorageClient - 为方便项目开发集成的简单接口(StorageServer接口)
4. AppendFileStorageClient - 支持文件续传操作的接口 (StorageServer接口)


##修改日志

查看[修改日志](/CHANGELOG.md)

