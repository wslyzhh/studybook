## 第七节:Trigger(SimpleTrigger、CronTrigger)哑火(MisFire)策略

------

一. 简介

\1. 什么是哑火

　　由于某些原因导致触发器(trigger)在该触发的时候没有得到触发,后续对应的解决策略即为哑火策略。(个人理解)

\2. 哑火触发的条件

　　①：所有的工作线程都在忙碌，导致某些trigger得不到触发.(如：simplethreadpool 默认是10个工作线程，但我有15个trigger同时触发， 恰巧这10个trigger关联的job耗时都很长，剩下的5个trigger超过了等待时间仍然没有得到触发)

　　②：调度器(sheduler)中途挂了，某个时间又恢复了

　　③：设置的trigger的开始时间早于当前时间

　**如果没有触发哑火的条件，则不会触发哑火策略，之前的全部都是按照忽略来计算的，后续按照正常规律来进行。如果不设置开始时间，即为当前时间开始，不触发哑火策略。**

 下面介绍两类Trigger对应的哑火策略：SimpleTrigger和CronTrigger **（前提：案例设置时间早于当前时间的，即都是有错过的）**

 

二. SimpleTrigger哑火策略详解

 　这里要分三种情况来讨论，执行指定次数的情况WithRepeatCount(n)，n=1、n>1、n=forever三种情况，重点理解n>1的情况，另外两种都是他的特殊情况而已。

**(一)：执行指定次数的情况（WithRepeatCount(n) n>1） 只要记住这种情况，下面的B和C都能分析出来**

(1).默认 ：立即执行，修改当前调度时间，总数保持不变 （等价于WithMisfireHandlingInstructionNowWithExistingCount）

　　**详见下面解析**

(2).WithMisfireHandlingInstructionIgnoreMisfires：错过的立即追赶，然后正常调度。

　　**PS：设置的时间早于当前时间，执行的时候，会将当前时间之前错过的次数一次性执行完，然后正常按照设置的开始时间及规律进行执行。 如果设置的RepeatCount(n)中n的次数小于错过的次数，只能执行n次，执行完后，将不会在执行了，因为执行次数已经用完了。**

　　**案例：设置的开始时间为8：00，每隔半小时执行一次，执行总次数为5次，当前时间为9:05，那么开始时候会先执行3次，将错过的一次性执行了，然后按照正常调度执行，下一次执行的时间为9:30，还能执行两次。**

(3).WithMisfireHandlingInstructionNextWithExistingCount：错过的不管了，按计划等待下次调度，总数不变，结束时间推迟。
　　**PS：错过的次数不处理，仍按照设置的规律来执行，执行次数不变，要执行完的RepeatCount(n)中的n.**

　　**案例：设置的开始时间为8：00，每隔半小时执行一次，执行总次数为5次，当前时间为9:05，那么开始时候并没有调度执行，第一次执行调度的时间为9:30，然后按照正常调度执行，总共执行5次，最后一次时间为11:30。**

(4).WithMisfireHandlingInstructionNextWithRemainingCount：错过的不管了，按计划等待下次调度，但总数要减去misfire错过的次数
　　**PS：错过的次数不处理，仍按照设置的规律来执行，执行总数要 减去 错过的次数！**

　  **案例：设置的开始时间为8：00，每隔半小时执行一次，执行总次数为5次，当前时间为9:05，那么开始时候并没有调度执行，第一次执行调度的时间为9:30，然后按照正常调度执行，总共执行    5-3=2 次，最后一次时间为10:00。**

(5).WithMisfireHandlingInstructionNowWithExistingCount: 立即执行，修改当前调度时间，总数保持不变
　　**PS：立即执行，修改当前调度时间的含义为，即使我设置的开始时间早于当前时间，但该哑火策略会立马执行该触发器，即运行后，马上执行了一次，后续的时间间隔****均是是以当前执行时间为基础来进行的，言外之意，之前设置的开始时间没用了，执行总数不变。**

 　***\*案例：设置的开始时间为8：00，每隔半小时执行一次，执行总次数为5次，当前时间为9:05，那么开始时候立即执行，第一次执行调度的时间为9:05，然后以9:05为基础，按照正常调度规律执行，总共执行5次，第二次时间为9:35，最后一次执行时间为11:05。\****

(6).WithMisfireHandlingInstructionNowWithRemainingCount: 立即执行，修改当前调度时间，总数要减去misfire错过的次数
　　**PS：立即执行，修改当前调度时间的含义为，即使我设置的开始时间早于当前时间，但该哑火策略会立马执行该触发器，即运行后，马上执行了一次，后续的时间间隔均是是以当前执行时间为基础来进行的，言外之意，之前设置的开始时间没用了。执行总数要 减去 错过的次数！**

 ***\*案例：设置的开始时间为8：00，每隔半小时执行一次，执行总次数为5次，当前时间为9:05，那么开始时候立即执行，第一次执行调度的时间为9:05，然后以9:05为基础，按照正常调度规律执行，总共执行 5-3=2 次，第二次（即最后一次）时间为9:35。\****

**B：只执行一次的情况 （WithRepeatCount(n) n=1） 对A情况的一个特殊分析**

(1). 默认：(等价于WithMisfireHandlingInstructionFireNow)

(2). WithMisfireHandlingInstructionFireNow:立即执行

　　**PS：设置的时间早于当前时间，执行的时候，立即把这一次执行完，后续将不再执行；但设置的时间晚于当前时间，则按照正常规律进行执行了**

(3).WithMisfireHandlingInstructionNextWithRemainingCount 和 WithMisfireHandlingInstructionNowWithRemainingCount ：不执行了

　　**PS：设置的时间早于当前时间，执行的时候按照原规律执行，但是次数要减去错过的次数，这里总共就执行一次，所以就不执行了**

 

**C：永久执行的情况（RepeatForever） 对A情况的一个特殊分析**

(1). 默认：等价与下面的(2)，按计划的正常调度执行，执行次数永久执行

(2). WithMisfireHandlingInstructionNextWithExistingCount 和 WithMisfireHandlingInstructionNextWithRemainingCount：按计划的正常调度执行，执行次数永久执行

(3). WithMisfireHandlingInstructionNowWithExistingCount 和 WithMisfireHandlingInstructionNowWithRemainingCount： 立即执行，执行的开始时间改为当前时间，执行次数永久执行

**代码分享：自行替换即可**

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1         public static void misfireShow()
 2         {
 3             //1.创建Schedule
 4             IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 5             //2.创建job (具体的job需要单独在一个文件中执行)
 6             var job = JobBuilder.Create<HelloJob4>().Build();
 7             //3.配置trigger 
 8             //下面的四个触发器对应了上面四种情况的测试，时间需要根据实际情况进行配置测试来设置
 9             var trigger1 = TriggerBuilder.Create().StartAt(DateBuilder.DateOf(20, 0, 0))
10                          .WithSimpleSchedule(x => x.WithIntervalInMinutes(30)
11                                                    .WithRepeatCount(8)
12                                                   .WithMisfireHandlingInstructionNowWithExistingCount()
13                                              ).Build();
14             var trigger2 = TriggerBuilder.Create().StartAt(DateBuilder.DateOf(18, 0, 0))
15                              .WithSimpleSchedule(x => x.WithIntervalInSeconds(1)
16                                                        .WithRepeatCount(1)
17                                                       .WithMisfireHandlingInstructionFireNow()
18                                                  ).Build();
19             var trigger3 = TriggerBuilder.Create()
20                                         .StartAt(DateBuilder.DateOf(15, 0, 0, 5, 3, 2018))
21                                         .WithSimpleSchedule(x => x.WithIntervalInMinutes(1)
22                                                                  .WithMisfireHandlingInstructionNowWithRemainingCount()
23                                                                  .RepeatForever()).Build();
24             var trigger4 = TriggerBuilder.Create()
25                                       .StartAt(DateBuilder.DateOf(14, 0, 0))
26                                       .WithCronSchedule("0 0-59 9-23 ? * MON-FRI",x=>x.WithMisfireHandlingInstructionIgnoreMisfires()).Build();
27 
28             //4.开始调度
29             scheduler.ScheduleJob(job, trigger1);
30             scheduler.Start();
31         }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

三. CronTrigger哑火策略详解

(1). 默认：错过的合并,于当前时间执行一次，不修改调度时间，按计划等待下一次调度（等价于下面的 WithMisfireHandlingInstructionFireAndProceed）

(2). WithMisfireHandlingInstructionIgnoreMisfires：错过的立即追赶，然后正常调度

　　**ps：错过多少次，初次执行的时候追赶多少次，追赶的次数的时间是按原规律执行的时间，然后按照原规律进行正常后续调度**

(3). WithMisfireHandlingInstructionFireAndProceed：错过的合并,于当前时间执行一次，不修改调度时间，按计划等待下一次调度

　　**PS：无论错过多少次，均在初次运行的时候，即当前时间执行一次，后续的执行仍按照原规律进行执行。**

(4). WithMisfireHandlingInstructionDoNothing：错过的不管了，后续按照正常调度进行

　　**PS：无论错过多少次，均忽略，后续的执行仍按照原规律进行执行。**