## 第六节: 六类Calander处理六种不同的时间场景

------

背景介绍及其使用

 　该章节主要补充介绍，在前一章四类触发器的基础上配合六大Canlander来动态删减某些时间，来满足更多的应用场景。

\1. DailyCalendar：动态排除某天的某些字段.

（需求：每天8-23点执行,每隔1s执行一次,但是21-22点这个区间不执行）

\2. WeeklyCalendar：适合在星期几的维度上做“减法操作”

（需求：每天8-23点执行,每隔1s执行一次,但是周五这一天不执行）

\3. HolidayCalendar：适合当年的某一天不能执行

（需求：每天8-23点执行,每隔1s执行一次,但是今年的6月16号这一天不执行）

\4. MonthlyCalendar：适合某个月中的某一天不能执行

（需求：每天8-23点执行,每隔1s执行一次,但是每月的27号不执行）

\5. AnnualCalendar：适合每年指定的某一天不能执行(有问题)

（需求：每天8-23点执行,每隔1s执行一次,但是每年的6月16号这一天不执行）

\6. CronCalendar：字符串表达式来排除某一天，某一个月份，某一年都可以

 (需求：每天8-23点执行,每隔1s执行一次,但是2月27号这天不执行)

 **代码分享：**

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

```c#
  1        public static void CalanderShow()
  2         {
  3             //1. 每天8-23点执行,每隔1s执行一次,但是21-22点这个区间不执行
  4             {
  5                 IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
  6                 scheduler.Start();
  7                 //单独记录一个区间段 21-22点
  8                 DailyCalendar dailyCalendar = new DailyCalendar(DateBuilder.DateOf(21, 0, 0).DateTime,
  9                                                                 DateBuilder.DateOf(22, 0, 0).DateTime);
 10                 scheduler.AddCalendar("mycalendar", dailyCalendar, true, true);
 11 
 12                 var job = JobBuilder.Create<HelloJob>().Build();
 13                 var trigger = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
 14                                                       x => x.OnEveryDay()
 15                                                           .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(8, 00))
 16                                                           .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(23, 00))
 17                                                           .WithIntervalInSeconds(1)
 18                                                                   )
 19                                                      .ModifiedByCalendar("mycalendar")
 20                                                      .Build();
 21                 scheduler.ScheduleJob(job, trigger);
 22             }
 23 
 24             //2. 每天8-23点执行,每隔1s执行一次,但是周五这一天不执行
 25             {
 26                 IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 27                 scheduler.Start();
 28                 //设定周五不能执行
 29                 WeeklyCalendar calendar = new WeeklyCalendar();
 30                 calendar.SetDayExcluded(DayOfWeek.Friday, true);
 31                 scheduler.AddCalendar("mycalendar", calendar, true, true);
 32 
 33                 var job = JobBuilder.Create<HelloJob>().Build();
 34                 var trigger = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
 35                                                       x => x.OnEveryDay()
 36                                                           .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(8, 00))
 37                                                           .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(23, 00))
 38                                                           .WithIntervalInSeconds(1)
 39                                                                   )
 40                                                      .ModifiedByCalendar("mycalendar")
 41                                                      .Build();
 42                 scheduler.ScheduleJob(job, trigger);
 43             }
 44 
 45             //3. 每天8-23点执行,每隔1s执行一次,但是当年6月16号这一天不执行
 46             {
 47                 IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 48                 scheduler.Start();
 49                 HolidayCalendar calendar = new HolidayCalendar();
 50                 calendar.AddExcludedDate(DateTime.Parse("06-16"));    //把当年6月16日排除在外
 51 
 52                 scheduler.AddCalendar("mycalendar", calendar, true, true);
 53                 var job = JobBuilder.Create<HelloJob>().Build();
 54                 var trigger = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
 55                                                       x => x.OnEveryDay()
 56                                                           .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(8, 00))
 57                                                           .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(23, 00))
 58                                                           .WithIntervalInSeconds(1)
 59                                                                   )
 60                                                      .ModifiedByCalendar("mycalendar")
 61                                                      .Build();
 62                 scheduler.ScheduleJob(job, trigger);
 63 
 64             }
 65 
 66             //4. 每天8-23点执行,每隔1s执行一次,但是每月的27号不执行
 67             {
 68                 IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 69                 scheduler.Start();
 70 
 71                 //指定月份中的某一天不能执行
 72                 MonthlyCalendar calendar = new MonthlyCalendar();
 73                 calendar.SetDayExcluded(27, true);  //将27号这天排除在外
 74                 scheduler.AddCalendar("mycalendar", calendar, true, true);
 75 
 76                 var job = JobBuilder.Create<HelloJob>().Build();
 77                 var trigger = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
 78                                                       x => x.OnEveryDay()
 79                                                           .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(8, 00))
 80                                                           .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(23, 00))
 81                                                           .WithIntervalInSeconds(1)
 82                                                                   )
 83                                                      .ModifiedByCalendar("mycalendar")
 84                                                      .Build();
 85 
 86                 scheduler.ScheduleJob(job, trigger);
 87             }
 88 
 89             //5. 每天8-23点执行,每隔1s执行一次,但是每年的6月16号这一天不执行
 90             {
 91                 IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
 92                 scheduler.Start();
 93 
 94                 AnnualCalendar calendar = new AnnualCalendar();
 95                 calendar.SetDayExcluded(DateTime.Parse("06-16"), true);  //把每年的6月16日排除在外
 96                 scheduler.AddCalendar("mycalendar", calendar, true, true);
 97 
 98                 var job = JobBuilder.Create<HelloJob>().Build();
 99                 var trigger = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
100                                                       x => x.OnEveryDay()
101                                                           .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(8, 00))
102                                                           .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(23, 00))
103                                                           .WithIntervalInSeconds(1)
104                                                                   )
105                                                      .ModifiedByCalendar("mycalendar")
106                                                      .Build();
107                 scheduler.ScheduleJob(job, trigger);
108             }
109 
110             //6.每天8-23点执行,每隔1s执行一次,但是2月27号这天不执行
111             {
112                 IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler();
113                 scheduler.Start();
114 
115                 CronCalendar calendar = new CronCalendar("* * * 27 2 ?");
116                 scheduler.AddCalendar("mycalendar", calendar, true, true);
117 
118                 var job = JobBuilder.Create<HelloJob>().Build();
119                 var trigger = TriggerBuilder.Create().WithDailyTimeIntervalSchedule(
120                                                       x => x.OnEveryDay()
121                                                           .StartingDailyAt(TimeOfDay.HourAndMinuteOfDay(8, 00))
122                                                           .EndingDailyAt(TimeOfDay.HourAndMinuteOfDay(23, 00))
123                                                           .WithIntervalInSeconds(1)
124                                                                   )
125                                                      .ModifiedByCalendar("mycalendar")
126                                                      .Build();
127                 scheduler.ScheduleJob(job, trigger);
128             }
129 
130         }
```

[![复制代码](../../image/copycode.gif)](javascript:void(0);)

 