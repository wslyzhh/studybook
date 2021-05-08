## 第八节: Quartz.Net五大构件之SimpleThreadPool及其四种配置方案

------

一. 简介

> **揭秘：** SimpleThreadPool是Quartz.Net中自带的线程池，默认个数为10个，代表一个Scheduler同一时刻并发的最多只能执行10个job，超过10个的job需要排队等待。

二. 四种配置方案

 \1. NameValueCollection的方式

　　需要利用StdSchedulerFactory的构造函数进行传进去，向哪个Sheduler中传，即配置哪个Sheduler的对应的线程池。

代码分享：

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1    {
 2                 var pairs = new System.Collections.Specialized.NameValueCollection() { };
 3                 pairs.Add("quartz.threadPool.ThreadCount", "20");   //设置线程池个数为20
 4 
 5                 var factory = new StdSchedulerFactory(pairs);      //将前面的配置加到Scheduler工厂中
 6                 var scheduler = factory.GetScheduler();
 7                 scheduler.Start();
 8 
 9                 var meta = scheduler.GetMetaData();
10                 int threadPoolSize = meta.ThreadPoolSize;
11                 Console.WriteLine("线程池的个数为：{0}", threadPoolSize);
12    }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

\2. App.config的方式配置，

　　详见：App.config文件，该模式代码中不需要进行任何的额外配置，适用于所有的Sheduler。

 配置文件代码分享：

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1 <?xml version="1.0" encoding="utf-8" ?>
 2 <configuration>
 3   <!--线程池个数设置   开始-->
 4   
 5   <configSections>
 6     <section name="quartz" type="System.Configuration.NameValueSectionHandler, System, Version=1.0.5000.0,Culture=neutral, PublicKeyToken=b77a5c561934e089"/>
 7   </configSections>
 8   <quartz>
 9     <!--设置Sheduler的线程池个数为22-->
10     <add key="quartz.threadPool.threadCount" value="22"/>    
11   </quartz>
12   
13   <!--线程池个数设置   结束-->
14   <startup>
15     <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6"/>
16   </startup>
17 </configuration>
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

\3. quartz.config文件的形式进行配置

　　该模式代码中不需要进行任何的额外配置，适用于所有的Sheduler。

　　用法：新建名为"quartz.config"的xml文件，在文件中写入：quartz.threadPool.threadCount=15 代表线程池个数设置为15, 同时要把该文件的属性**设置为始终复制**，使其可以生产到bin文件。

分享一下quartz.config中的代码

 　**PS：就一句话哦。**

```c#
1 quartz.threadPool.threadCount=15
```

\4. 通过代码设置电脑的环境变量来实现

　　一句代码：Environment.SetEnvironmentVariable("quartz.threadPool.threadCount", "26"); 设置后，适用于所有的Sheduler。

 代码分享：

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
   {
                //将线程池的个数设置为26
                Environment.SetEnvironmentVariable("quartz.threadPool.threadCount", "26");
                var factory = new StdSchedulerFactory();
                var scheduler = factory.GetScheduler();
                scheduler.Start();

                var meta = scheduler.GetMetaData();
                int threadPoolSize = meta.ThreadPoolSize;
                Console.WriteLine("线程池的个数为：{0}", threadPoolSize);
   }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

**总结：以上4种方式的优先级为：quartz.config < app.config < 环境变量 < namevaluecollection**  