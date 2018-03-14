#### Overview of rolling policies

**[RollingPolicy](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/RollingPolicy.html)** is responsible for the rollover procedure which involves file moving and renaming.

The **RollingPolicy** interface is presented below:


```java
package ch.qos.logback.core.rolling;  

import ch.qos.logback.core.FileAppender;
import ch.qos.logback.core.spi.LifeCycle;

public interface RollingPolicy extends LifeCycle {

  public void rollover() throws RolloverFailure;
  public String getActiveFileName();
  public CompressionMode getCompressionMode();
  public void setParent(FileAppender appender);
}
```

The **_rollover_** method accomplishes the work involved in archiving the current log file. The **_getActiveFileName()_** method is called to compute the file name of the current log file (where live logs are written to). As indicated by **_getCompressionMode_** method a **RollingPolic**y is also responsible for determining the compression mode. Lastly, a **RollingPolicy** is given a reference to its parent via the **_setParent_** method.


##### TimeBasedRollingPolicy

**[TimeBasedRollingPolicy](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/TimeBasedRollingPolicy.html)** is possibly the most popular rolling policy. It defines a rollover policy based on time, for example by day or by month. **TimeBasedRollingPolicy** assumes the responsibility for rollover as well as for the triggering of said rollover. Indeed, **TimeBasedTriggeringPolicy** implements _both_ **RollingPolicy** and **TriggeringPolicy** interfaces.

**TimeBasedRollingPolicy**'s configuration takes one mandatory `fileNamePattern` property and several optional properties.

| Property Name | Type | Description |
| :--- | :--- | :--- |
| `fileNamePattern`  | String | The mandatory `fileNamePattern` property defines the name of the rolled-over (archived) log files. Its value should consist of the name of the file, plus a suitably placed _%d_ conversion specifier. The _%d_ conversion specifier may contain a date-and-time pattern as specified by the **java.text.SimpleDateFormat** class. If the date-and-time pattern is omitted, then the default pattern _yyyy-MM-dd_ is assumed. **The rollover period is inferred from the value of** `fileNamePattern`.<br><br>Note that the `file` property in **RollingFileAppender** (the parent of **TimeBasedRollingPolicy**) can be either set or omitted. By setting the `file` property of the containing **FileAppender**, you can decouple the location of the active log file and the location of the archived log files. The current logs will be always targeted at the file specified by the `file` property. It follows that the name of the currently active log file will not change over time. However, if you choose to omit the `file` property, then the active file will be computed anew for each period based on the value of `fileNamePattern`. The examples below should clarify this point.<br><br>The date-and-time pattern, as found within the accolades of _%d{}_ follow **java.text.SimpleDateFormat** conventions. The forward slash _'/'_ or backward slash _'\'_ characters anywhere within the `fileNamePattern` property or within the date-and-time pattern will be interpreted as directory separators.<br><br>**Multiple _%d_ specifiers**<br><br>It is possible to specify multiple _%d_ specifiers but only one of which can be primary, i.e. used to infer the rollover period. All other tokens _must_ be marked as auxiliary by passing the 'aux' parameter (see examples below).<br><br>Multiple _%d_ specifiers allow you to organize archive files in a folder structure different than that of the roll-over period. For example, the file name pattern shown below organizes log folders by year and month but roll-over log files every day at midnight.<br><br>/var/log/%d{yyyy/MM, aux}/myapplication.%d{yyyy-MM-dd}.log<br><br>**TimeZone**<br><br>Under certain circumstances, you might wish to roll-over log files according to a clock in a timezone different than that of the host. It is possible to pass a timezone argument following the date-and-time pattern within the _%d_ conversion specifier. For example:<br><br>aFolder/test.%d{yyyy-MM-dd-HH, UTC}.log<br><br>If the specified timezone identifier is unknown or misspelled, the GMT timezone is assumed as dictated by the **_TimeZone.getTimeZone(String)_** method specification. |
| `maxHistory` | int | The optional `maxHistory` property controls the maximum number of archive files to keep, asynchronously deleting older files. For example, if you specify monthly rollover, and set maxHistory to 6, then 6 months worth of archives files will be kept with files older than 6 months deleted. Note as old archived log files are removed, any folders which were created for the purpose of log file archiving will be removed as appropriate. |
| `totalSizeCap` | int | The optional `totalSizeCap` property controls the total size of all archive files. Oldest archives are deleted asynchronously when the total size cap is exceeded. The `totalSizeCap` property requires `maxHistory` property to be set as well. Moreover, the "max history" restriction is always applied first and the "total size cap" restriction applied second. |
| `cleanHistoryOnStart`  | boolean | If set to true, archive removal will be executed on appender start up. By default this property is set to false.<br><br>Archive removal is normally performed during roll over. However, some applications may not live long enough for roll over to be triggered. It follows that for such short-lived applications archive removal may never get a chance to execute. By setting `cleanHistoryOnStart` to true, archive removal is performed at appender start up. |

Here are a few `fileNamePattern` values with an explanation of their effects.

| fileNamePattern | Rollover schedule | Example |
| :--- | :--- | :--- |
| _/wombat/foo.%d_ | Daily rollover (at midnight). Due to the omission of the optional time and date pattern for the _%d_ token specifier, the default pattern of _yyyy-MM-dd_ is assumed, which corresponds to daily rollover. | `file` property not set: During November 23rd, 2006, logging output will go to the file _/wombat/foo.2006-11-23_. At midnight and for the rest of the 24th, logging output will be directed to _/wombat/foo.2006-11-24_.<br><br>`file` property set to _/wombat/foo.txt_: During November 23rd, 2006, logging output will go to the file _/wombat/foo.txt_. At midnight, _foo.txt_ will be renamed as _/wombat/foo.2006-11-23_. A new _/wombat/foo.txt_ file will be created and for the rest of November 24th logging output will be directed to _foo.txt_. |
| _/wombat/%d{yyyy/MM}/foo.txt_  | Rollover at the beginning of each month. | `file` property not set: During the month of October 2006, logging output will go to _/wombat/2006/10/foo.txt_. After midnight of October 31st and for the rest of November, logging output will be directed to _/wombat/2006/11/foo.txt_.<br><br>`file` property set to _/wombat/foo.txt_: The active log file will always be _/wombat/foo.txt_. During the month of October 2006, logging output will go to _/wombat/foo.txt_. At midnight of October 31st, _/wombat/foo.txt_ will be renamed as _/wombat/2006/10/foo.txt_. A new _/wombat/foo.txt_ file will be created where logging output will go for the rest of November. At midnight of November 30th, _/wombat/foo.txt_ will be renamed as _/wombat/2006/11/foo.txt_ and so on.|
| _/wombat/foo.%d{yyyy-ww}.log_ | Rollover at the first day of each week. Note that the first day of the week depends on the locale. | Similar to previous cases, except that rollover will occur at the beginning of every new week. |
| _/wombat/foo%d{yyyy-MM-dd_HH}.log_ | Rollover at the top of each hour. | Similar to previous cases, except that rollover will occur at the top of every hour. |
| _/wombat/foo%d{yyyy-MM-dd_HH-mm}.log_ | /wombat/foo%d{yyyy-MM-dd_HH-mm}.log | Similar to previous cases, except that rollover will occur at the beginning of every minute. |
| _/wombat/foo%d{yyyy-MM-dd_HH-mm, UTC}.log_ | Rollover at the beginning of every minute. | Similar to previous cases, except that file names will be expressed in UTC. |
| _/foo/%d{yyyy-MM,**aux**}/%d.log_ | Rollover daily. Archives located under a folder containing year and month. | In this example, the first _%d_ token is marked as **aux**iliary. The second _%d_ token, with time and date pattern omitted, is then assumed to be primary. Thus, rollover will occur daily (default for _%d_) and the folder name will depend on the year and month. For example, during the month of November 2006, archived files will all placed under the _/foo/2006-11/_ folder, e.g _/foo/2006-11/2006-11-14.log_. |


Any forward or backward slash characters are interpreted as folder (directory) separators. Any required folder will be created as necessary. You can thus easily place your log files in separate folders.

**TimeBasedRollingPolicy** supports automatic file compression. This feature is enabled if the value of the `fileNamePattern` option ends with _.gz_ or _.zip_.

| fileNamePattern | Rollover schedule | Example |
| :--- | :--- | :--- |
| _/wombat/foo.%d.gz_  | Daily rollover (at midnight) with automatic GZIP compression of the archived files. | `file` property not set: During November 23rd, 2009, logging output will go to the file _/wombat/foo.2009-11-23_. However, at midnight that file will be compressed to become _/wombat/foo.2009-11-23.gz_. For the 24th of November, logging output will be directed to _/wombat/folder/foo.2009-11-24_ until it's rolled over at the beginning of the next day.<br><br>`file` property set to _/wombat/foo.txt_: During November 23rd, 2009, logging output will go to the file _/wombat/foo.txt_. At midnight that file will be compressed and renamed as _/wombat/foo.2009-11-23.gz_. A new _/wombat/foo.txt_ file will be created where logging output will go for the rest of November 24rd. At midnight November 24th, _/wombat/foo.txt_ will be compressed and renamed as _/wombat/foo.2009-11-24.gz_ and so on.|


The `fileNamePattern` serves a dual purpose. First, by studying the pattern, logback computes the requested rollover periodicity. Second, it computes each archived file's name. Note that it is possible for two different patterns to specify the same periodicity. The patterns _yyyy-MM_ and _yyyy@MM_ both specify monthly rollover, although the resulting archive files will carry different names.

By setting the `file` property you can decouple the location of the active log file and the location of the archived log files. The logging output will be targeted into the file specified by the `file` property. It follows that the name of the active log file will not change over time. However, if you choose to omit the `file` property, then the active file will be computed anew for each period based on the value of `fileNamePattern`. By leaving the `file` option unset you can avoid file [renaming errors](https://logback.qos.ch/codes.html#renamingError) which occur while there exist external file handles referencing log files during roll over.

The `maxHistory` property controls the maximum number of archive files to keep, deleting older files. For example, if you specify monthly rollover, and set `maxHistory` to 6, then 6 months worth of archives files will be kept with files older than 6 months deleted. Note as old archived log files are removed, any folders which were created for the purpose of log file archiving will be removed as appropriate.

For various technical reasons, rollovers are not clock-driven but depend on the arrival of logging events. For example, on 8th of March 2002, assuming the `fileNamePattern` is set to _yyyy-MM-dd_ (daily rollover), the arrival of the first event after midnight will trigger a rollover. If there are no logging events during, say 23 minutes and 47 seconds after midnight, then rollover will actually occur at 00:23'47 AM on March 9th and not at 0:00 AM. Thus, depending on the arrival rate of events, rollovers might be triggered with some latency. However, regardless of the delay, the rollover algorithm is known to be correct, in the sense that all logging events generated during a certain period will be output in the correct file delimiting that period.

Here is a sample configuration for **RollingFileAppender** in conjunction with a **TimeBasedRollingPolicy**.

**Example: Sample configuration of a RollingFileAppender using a TimeBasedRollingPolicy** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-RollingTimeBased.xml_)


```
<configuration>
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>logFile.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- daily rollover -->
      <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>

      <!-- keep 30 days' worth of history capped at 3GB total size -->
      <maxHistory>30</maxHistory>
      <totalSizeCap>3GB</totalSizeCap>

    </rollingPolicy>

    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
    </encoder>
  </appender> 

  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

The next configuration sample illustrates the use of **RollingFileAppender** associated with **TimeBasedRollingPolicy** in `prudent` mode.

**Example: Sample configuration of a _RollingFileAppender_ using a _TimeBasedRollingPolicy_** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-PrudentTimeBasedRolling.xml_)


```
<configuration>
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <!-- Support multiple-JVM writing to the same log file -->
    <prudent>true</prudent>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>
      <maxHistory>30</maxHistory> 
      <totalSizeCap>3GB</totalSizeCap>
    </rollingPolicy>

    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
    </encoder>
  </appender> 

  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```





