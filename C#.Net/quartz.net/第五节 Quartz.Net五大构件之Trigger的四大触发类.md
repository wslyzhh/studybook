## 第五节: Quartz.Net五大构件之Trigger的四大触发类

------

一. WithSimpleSchedule（ISimpleTrigger）

\1. 用途：时、分、秒上的轮询(和timer类似)，实际开发中，该场景占绝大多数.

\2. 轮询的种类：永远轮询和限定次数轮询.

\3. 参数中的几个函数：

 A.执行间隔：

　　①.WithInterval(TimeSpan timeSpan)：通用的间隔执行方法

　　②.WithIntervalInHours(int hours)：以小时为间隔单位进行执行

　　③.WithIntervalInMinutes(int minutes)：以分钟为间隔单位进行执行

　　④.WithIntervalInSeconds(int seconds)：以秒为间隔单位进行执行

 B.执行时间：

　　①.WithRepeatCount(int repeatCount)：执行多少次以后结束

　　②.RepeatForever()：永远执行

　　③.repeatMinutelyForever()：一分钟执行一次(永远执行)

　　   repeatMinutelyForever(int minutes)：每隔几分钟执行一次(永远执行)

　　　repeatMinutelyForTotalCount(int count, int minutes)：每隔几分钟执行一次(执行次数为count)

　　类似的还有秒、小时。

 **代码分享：**

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1  public static void SimpleTriggrShow()
 2         {
 3             //1. 创建Schedule
 4             IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();      
 5             //2. 创建Job
 6             var job1 = JobBuilder.Create<HelloJob>().Build();
 7             //3. 创建Trigger
 8             //1s执行一次，永远执行
 9             var trigger = TriggerBuilder.Create()
10                                         .WithSimpleSchedule(x => x.WithIntervalInSeconds(1).RepeatForever())
11                                         .Build();
12             //2s执行一次，执行10次
13             //var trigger = TriggerBuilder.Create()
14             //                            .WithSimpleSchedule(x => x.WithIntervalInSeconds(2).WithRepeatCount(10))
15             //                            .Build();
16             //注意这种用法：WithScheduler，表示1s执行一次，执行了5次
17             //var trigger = TriggerBuilder.Create()
18             //                            .WithSchedule(SimpleScheduleBuilder.RepeatSecondlyForTotalCount(5, 1))
19             //                            .Build();
20             //4. 开始调度
21             scheduler.ScheduleJob(job1, trigger);
22             scheduler.Start();
23         }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

 

二. WithCalendarIntervalSchedule （ICalendarTrigger）

1.用途：与日历相关

2.参数中的几个函数：

　　①.WithInterval(TimeSpan timeSpan)：通用的间隔执行方法

　　②.WithIntervalInHours(int hours)：以小时为间隔单位进行执行

　　③.WithIntervalInMinutes(int minutes)：以分钟为间隔单位进行执行

　　④.WithIntervalInSeconds(int seconds)：以秒为间隔单位进行执行

　　⑤.WithIntervalInDays(int days)：以天为间隔单位进行执行

　　⑥.WithIntervalInMonths(int months)：以月为间隔单位进行执行

**代码分享：**

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1  public static void CalendarIntervalTriggerShow()
 2         {
 3             //1. 创建Schedule
 4             IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 5             scheduler.Start();
 6             //2. 创建Job
 7             var job1 = JobBuilder.Create<HelloJob>().Build();
 8             //3. 创建Trigger
 9             //3s执行一次，60s后结束
10             var trigger = TriggerBuilder.Create()
11                                         .WithCalendarIntervalSchedule(x => x.WithIntervalInSeconds(3))
12                                         .EndAt(DateTimeOffset.Now.AddSeconds(60))    //60s后结束
13                                         .Build();
14             //4. 开始调度
15             scheduler.ScheduleJob(job1, trigger);
16         }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

 

三. WithDailyTimeIntervalSchedule (IDailyTimeTrigger)

\1. 用途：解决时间点的增、减、排除。

\2. 核心函数：

　　a. OnEveryDay：每天

　　b. OnMondayThroughFriday:周一至周五,即工作日

　　c. OnSaturdayAndSunday:周六至周天，即休息日

　　d. OnDaysOfTheWeek:用数组的形式单独来指定一周中的哪几天

　　e. StartingDailyAt：表示开始于几点 （区别于前面的StartAt）

　　f. EndingDailyAt：表示结束于几点 （区别于前面的EndAt）

 **代码分享：**

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1  public static void DailyTimeIntervalTriggerShow()
 2         {
 3             //1. 创建Schedule
 4             IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 5             //2. 创建Job
 6             var job1 = JobBuilder.Create<HelloJob>().Build();
 7             //3. 创建Trigger
 8             //每天8-20点，每半个小时执行一次（即8:00、8:30  。。。。   19:30、20:30）
 9             var trigger1 = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
10                                                    x => x.OnEveryDay()
11                                                        .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(8, 00))
12                                                        .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(20, 00))
13                                                        .WithIntervalInMinutes(30))
14                                                        .Build();
15 
16             //每个工作日的凌晨2点执行1次 （这里的设计是2点开始，2：01结束，每个一小时执行一次，说白了总共执行了一次）
17             //或者直接WithIntervalInHours替换成WithRepeatCount
18             var trigger2 = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
19                                                    x => x.OnMondayThroughFriday()
20                                                          .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(2, 00))
21                                                          .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(2, 01))
22                                                          .WithIntervalInHours(1))
23                                                          .Build();
24 
25             //每个周的周一和周四的2点执行1次  （这里的设计是2点开始，2：01结束，每个一小时执行一次，说白了总共执行了一次）
26             //或者直接WithIntervalInHours替换成WithRepeatCount
27             var trigger3 = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
28                                                  x => x.OnDaysOfTheWeek(new DayOfWeek[2] {
29                                                                         DayOfWeek.Monday, DayOfWeek.Thursday })
30                                                        .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(2, 00))
31                                                        .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(2, 01))
32                                                        .WithIntervalInHours(1))
33                                                        .Build();
34             //4. 开始调度
35             scheduler.ScheduleJob(job1, trigger1);
36             scheduler.Start();
37         }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

 

四. WithCronSchedule (ICronTrigger)

\1. 用途：使用cron表达式代替硬编码，可以替代以上三种Trigger (详见：www.cnblogs.com/knowledgesea/p/4705796.html)

\2. 规则：

 　a  整体规则排列如下，且日和周必须有一个位数是 ？

　　　　* * * * * *

　　　　秒 分 时 日 月 周

　　b  ?： 代表示模糊的意思，必须存在，且只能在日或周中的一个存在

　　c  *： 最小单位轮询，在分钟的字段域里，表示每分钟；在小时的字段域里，表示每小时

　　d  /： 表示递增： 如0/5在秒的字段域里，表示第0、5、15、20.... 秒  可以省略0，即 /5

　　e  -： 表示范围, 如1-10在秒字段域里，表示1s、2s、3s到10s都执行

　　f  ,： 表示并且， 如1,10,20在秒字段域里，表示1s，10s，20s都执行

　　g #： 只能存在周这一个域，表示第几周的星期几，如果超出范围，则忽略不记，如2#4，表示第四周的星期二

　　h  L： 表示last的意思： 天： 10L 表示本月的倒数第十天执行， 5L 表示本月的最后一个周四执行（暂不研究）

\3. 补充一下秒、分、时、日、月、周的字段域范围

　　秒： 0-59

　　分： 0-59

　　时： 0-23

　　日： 1-31

　　月： 1-12 或 JAN-DEC

　　周： 1-7 或 SUN-SAT

　　年：选填，可以留空, 1970-2099

\4. 补充几个事例帮助理解：

　　实例1：0**1*？ note：每月1号凌晨都会被执行。

　　实例2：0**？** note：每分钟的00秒被执行。

　　实例3：0 10 18 ? 3 WEB note:每年3月的每个星期三，下午6点10分都会被触发

　　实例4：0 10 18 15 3 ? note:每年三月的第15天，下午6点10分都会被触发

　　实例5：0 10 18 1-5 * ？ note:每月的1号到5号（包含每月1号和5号，每月共计5天都会被触发），下午6点10分都会被触发

　　实例6：0 10-15 * ？ * * note:每小时的第10分钟到第15分钟（包含每小时的第10分钟和第15分钟，每小时共计5分钟都会被触发），都会被触发

　　实例7：10,20 * * ? * * note:每分钟的第10秒与第20秒都会被触发

　　实例8：0 10,20 * 1,2 * ? note:每月的第1天与第2天的，每小时的第10分钟与第20分钟被触发。

　　实例9：5/20 * * ? * * note:每分钟的第5秒，第25秒，第45秒 都会被执行。

　　实例10：0 * 2/2 ? * * note:每天的第2小时，第4小时，第6小时，第8小时 ... 第22小时的00分00秒都会被触发。

　　实例11：* * * ? * 3#4 note:每月的第4个星期的周2，凌晨触发。

　　实例12：* * * ? * 6#2 note:每月的第2个星期的周5，凌晨触发

**代码分享：**

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
 1   public static void CronTriggerShow()
 2         {
 3             //1. 创建Schedule
 4             IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 5             scheduler.Start();
 6 
 7             //2. 创建Job
 8             var job1 = JobBuilder.Create<HelloJob>().Build();
 9 
10             //3. 创建Trigger
11 
12             //每天8-20点，每半个小时执行一次（即8:00、8:30  。。。。   19:30、20:30）
13             var trigger1 = TriggerBuilder.Create().WithCronSchedule("0 0/30 8-20 * * ?")
14                                                   .Build();
15             //每个工作日的凌晨2点执行1次     
16             var trigger2 = TriggerBuilder.Create().WithCronSchedule("0 0 2 ? * Mon-Fri")
17                                                   .Build();
18 
19             //每个周的周一和周四的2点执行1次
20             var trigger3 = TriggerBuilder.Create().WithCronSchedule("0 0 2 ? * Mon,Wes")
21                                                   .Build();
22 
23 
24             //4. 开始调度
25             scheduler.ScheduleJob(job1, trigger2);
26         }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

 

 