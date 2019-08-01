title=@Scheduled与@EnableScheduling注释
date=2018-04-25
type=post
tags=spring
status=published
~~~~~~

# Table of Contents

1.  [@Scheduled 注解](#org1957251)
2.  [@EnableScheduling](#orgf9297bb)


<a id="org1957251"></a>

# @Scheduled 注解

@Scheduled 注解用于创建spring定时任务。

创建cron风格的定时任务。

    @Component
    public class ScheduledTask {
    
      @Scheduled(cron = "0 10 * * * *")  // 没小时的整10分钟执行一次。
      public void doWork() {
        System.out.println("do the work!!!");
      }
    }

用fixedRate指定的任务会在指定时间之后再次执行，不用等待上次调用完成。

    @Component
    public class ScheduledTask {
      @Scheduled(fixedRate = 1000)  // 1 秒执行一次
      public void doJob() {
        // ...
      }
    }

用fixedDelay指定方法执行完成后，延迟一定的时间再执行。

例如:

    @Component
    public class ScheduledTask {
      @Scheduled(fixedDelay = 1000)
      public void doWork() {
        // 
      }
    }

用initialDelay指定首次执行延迟时间。

    @Scheduled(initialDelay = 1000*10, fixedDelay=1000*2)
    public void doWork() {
    
    }


<a id="orgf9297bb"></a>

# @EnableScheduling

@EnableScheduling用于告诉spring开启后台定时任务线程，通常添加在入口类上。只有开启了EnableScheduling, Scheduled注解
的任务才会生效。

    @SpringBootApplication
    @EnableScheduling
    public class MyApplication {
         // 
    }

