#### Size and time based rolling policy

Sometimes you may wish to archive files essentially by date but at the same time limit the size of each log file, in particular if post-processing tools impose size limits on the log files. In order to address this requirement, logback ships with **SizeAndTimeBasedRollingPolicy**.

Note that **TimeBasedRollingPolicy** already allows limiting the combined size of archived log files. If you only wish to limit the combined size of log archives, then **TimeBasedRollingPolicy** described above and setting the totalSizeCap property should be amply sufficent.

Here is a sample configuration file demonstrating time and size based log file archiving.

**Example: Sample configuration for SizeAndTimeBasedFNATP** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-sizeAndTime.xml_)


```
<configuration>
  <appender name="ROLLING" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>mylog.txt</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <!-- rollover daily -->
      <fileNamePattern>mylog-%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
       <!-- each file should be at most 100MB, keep 60 days worth of history, but at most 20GB -->
       <maxFileSize>100MB</maxFileSize>    
       <maxHistory>60</maxHistory>
       <totalSizeCap>20GB</totalSizeCap>
    </rollingPolicy>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>


  <root level="DEBUG">
    <appender-ref ref="ROLLING" />
  </root>

</configuration>
```

Note the "_%i_" conversion token in addition to "_%d_". **Both the _%i_ and _%d_ tokens are mandatory.** Each time the current log file reaches `maxFileSize` before the current time period ends, it will be archived with an increasing index, starting at 0.

Size and time based archiving supports deletion of old archive files. You need to specify the number of periods to preserve with the `maxHistory` property. When your application is stopped and restarted, logging will continue at the correct location, i.e. at the largest index number for the current period.

In versions prior to 1.1.7, this document mentioned a component called **SizeAndTimeBasedFNATP**. However, given that **SizeAndTimeBasedFNATP** offers a simpler configuration structure, we no longer document **SizeAndTimeBasedFNATP**. Nevertheless, earlier configuration files using **SizeAndTimeBasedFNATP** will continue to work just fine. In fact, **SizeAndTimeBasedRollingPolicy** is implemented with a **SizeAndTimeBasedFNATP** subcomponent.


