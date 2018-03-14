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