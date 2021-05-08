## 第四节: Quartz.Net五大构件之Trigger通用用法(常用方法、优先级、与job关联等)

------

一. 简介

\1. 几个类型：

　　①：TriggerBuilder：用来创建ITrigger实例

　　②：ITrigger：触发器实例

2.常用的几个方法

　　①.StartNow：Trigger马上触发.

　　②.StartAt和EndAt：设置Trigger触发的开始时间和结束时间 （省略设置开始时间的话，默认从当前时间开始执行）

　　③.UsingJobData：给Trigger添加一些附加值（通过context.Trigger.JobDataMap获取）

　　④.WithDescription：用来描述该触发器，并没有什么实际左右

　　⑤.WithPriority：设置Trigger的优先级，默认为5，数字越大，优先级越高.（该优先级用于一个job对应多个Trigger，且Trigger的触发时间相同，优先级越大的越先执行）

　　⑥.ForJob：将job和trigger进行关联，该方法有多个重载，关联后ScheduleJob方法进行调度时，只需将trigger传入进去即可

 

二.  Job和Trigger关联问题

(1). 1个job对应1个trigger：调用ScheduleJob(IJobDetail jobDetail, ITrigger trigger)，直接关联即可，无须做特别处理

(2). 1个job对应多个trigger：有两种方案解决

　　方案A： 将job持久化（StoreDurably(true)）,然后通过AddJob方法加入调度池中，Trigger上通过ForJob方法和指定job进行关联，然后调用ScheduleJob(ITrigger trigger)方法，将trigger全部加入调度池中，最后Start开启即可

　　方案B： 创建job和trigger都不需要关联，也不需要特殊设置，最后将trigger加入一个HashSet<ITrigger>池中，通过一次性通过 ScheduleJob(IJobDetail jobDetail, Collection.ISet<ITrigger> triggersForJob, bool replace)进行关联

(3). 2个job对应1个trigger (不常用)

　　利用JobChainingJobListener实现

 **代码分享：**

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1             //1. 创建Schedule
 2             IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 3             //2. 创建Job
 4             var job1 = JobBuilder.Create<HelloJob3>()
 5                     .WithIdentity("job1", "jobGroup1")
 6                     .StoreDurably(true)
 7                     .Build();
 8             var job2 = JobBuilder.Create<HelloJob>()
 9                     .WithIdentity("job2", "jobGroup2")
10                     .StoreDurably(true)
11                     .Build();
12             /********************************* 下面测试job和 Trigger的关联问题  (与下面Trigger优先级的测试互不影响)**************************************/
13 
14             #region 情况1：1个job对应1个trigger
15             {
16                 var trigger = TriggerBuilder.Create()
17                                   .UsingJobData("name", "ypf1")   //附加信息
18                                   .UsingJobData("age", "age1")
19                                   .StartAt(DateBuilder.EvenSecondDateAfterNow()) //表示下一个秒整点开启，比如现在9点19分20秒，那么9点20分21s整开启 
20                                   .WithSimpleSchedule(x => x.WithIntervalInSeconds(2).RepeatForever())
21                                   .EndAt(DateTimeOffset.Now.AddSeconds(60))    //60s后结束
22                                   .Build();
23                 scheduler.ScheduleJob(job1, trigger);
24                 scheduler.Start();
25             }
26             #endregion
27 
28             #region 情况2:1个job对应多个Trigger
29             //方案1
30             {
31                 var trigger1 = TriggerBuilder.Create()
32                                  .UsingJobData("name", "ypf1")   //附加信息
33                                  .UsingJobData("age", "age1")
34                                  .StartNow()     //马上开启
35                                  .WithSimpleSchedule(x => x.WithIntervalInSeconds(2).RepeatForever())    //每隔两秒执行一次
36                                  .EndAt(DateTimeOffset.Now.AddSeconds(60))    //60s后结束
37                                  .ForJob("job1", "jobGroup1")    //通过表名和组名进行关联
38                                  .Build();
39                 var trigger2 = TriggerBuilder.Create()
40                                .UsingJobData("name", "ypf2")   //附加信息
41                                .UsingJobData("age", "age2")
42                                .StartNow()     //马上开启
43                                .WithSimpleSchedule(x => x.WithIntervalInSeconds(5).RepeatForever())      //每隔5秒执行一次
44                                .EndAt(DateTimeOffset.Now.AddSeconds(60))    //60s后结束
45                                .ForJob(job1)    //直接IJobDetail关联
46                                .Build();
47 
48                 scheduler.AddJob(job1, true);
49                 scheduler.ScheduleJob(trigger1);
50                 scheduler.ScheduleJob(trigger2);
51                 scheduler.Start();
52             }
53             //方案2
54             {
55                 var trigger1 = TriggerBuilder.Create()
56                                  .UsingJobData("name", "ypf1")   //附加信息
57                                  .UsingJobData("age", "age1")
58                                  .StartNow()     //马上开启
59                                  .WithSimpleSchedule(x => x.WithIntervalInSeconds(2).RepeatForever())    //每隔两秒执行一次
60                                  .EndAt(DateTimeOffset.Now.AddSeconds(60))    //60s后结束
61                                  .Build();
62                 var trigger2 = TriggerBuilder.Create()
63                                .UsingJobData("name", "ypf2")   //附加信息
64                                .UsingJobData("age", "age2")
65                                .StartNow()     //马上开启
66                                .WithSimpleSchedule(x => x.WithIntervalInSeconds(5).RepeatForever())      //每隔5秒执行一次
67                                .EndAt(DateTimeOffset.Now.AddSeconds(60))    //60s后结束
68                                .Build();
69 
70                 Quartz.Collection.HashSet<ITrigger> triggerHash = new Quartz.Collection.HashSet<ITrigger>();
71                 triggerHash.Add(trigger1);
72                 triggerHash.Add(trigger2);
73                 scheduler.ScheduleJob(job1, triggerHash, true);
74                 scheduler.Start();
75             }
76             #endregion
77 
78             #region 情况3：2个job对应1个trigger 
79             {
80                 //每s执行一次
81                 var trigger = TriggerBuilder.Create().WithCronSchedule("/1 * * * * ?").Build();
82 
83                 JobChainingJobListener listener = new JobChainingJobListener("mytest");
84                 listener.AddJobChainLink(job1.Key, job2.Key);
85                 scheduler.ListenerManager.AddJobListener(listener);
86 
87                 scheduler.AddJob(job2, true);
88                 scheduler.ScheduleJob(job1, trigger);
89                 scheduler.Start();
90             }
91             #endregion
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

三. Trigger的优先级（未完）

 　利用WithPriority方法，该优先级用于一个job对应多个Trigger，且Trigger的触发时间相同，优先级越大的越先执行。