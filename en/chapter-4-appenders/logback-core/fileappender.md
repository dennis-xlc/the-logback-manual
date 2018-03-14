### FileAppender


The **[FileAppender](https://logback.qos.ch/xref/ch/qos/logback/core/FileAppender.html)**, a subclass of **OutputStreamAppender**, appends log events into a file. The target file is specified by the `File` option. If the file already exists, it is either appended to, or truncated depending on the value of the `append` property.


| Property Name | Type | Description |
| :--- | :--- | :--- |
| `append` | boolean | If true, events are appended at the end of an existing file. Otherwise, if `append` is false, any existing file is truncated. The `append` option is set to true by default. |
| `encoder` | **[Encoder](https://logback.qos.ch/xref/ch/qos/logback/core/encoder/Encoder.html)** | See **OutputStreamAppender** properties. |
| `file` | String | The name of the file to write to. If the file does not exist, it is created. On the MS Windows platform users frequently forget to escape back slashes. For example, the value _c:\temp\test.log_ is not likely to be interpreted properly as _'\t'_ is an escape sequence interpreted as a single tab character (_\u0009_). Correct values can be specified as c:/temp/test.log or alternatively as _c:\\temp\\test.log_. The `File` option has no default value.<br><br>If the parent directory of the file does not exist, **FileAppender** will automatically create it, including any necessary but nonexistent parent directories. |
| `prudent` | boolean | In prudent mode, **FileAppender** will safely write to the specified file, even in the presence of other **FileAppender** instances running in different JVMs, potentially running on different hosts. The default value for prudent mode is `false`.<br><br>Prudent mode can be used in conjunction with **RollingFileAppender** although some restrictions apply.<br><br>Prudent mode implies that `append` property is automatically set to true.<br><br>Prudent more relies on exclusive file locks. Experiments show that file locks approximately triple (x3) the cost of writing a logging event. On an "average" PC writing to a file located on a **local** hard disk, when prudent mode is off, it takes about 10 microseconds to write a single logging event. When prudent mode is on, it takes approximately 30 microseconds to output a single logging event. This translates to logging throughput of 100'000 events per second when prudent mode is off and approximately 33'000 events per second in prudent mode.<br><br>Prudent mode effectively serializes I/O operations between all JVMs writing to the same file. Thus, as the number of JVMs competing to access a file increases so will the delay incurred by each I/O operation. As long as the total number of I/O operations is in the order of 20 log requests per second, the impact on performance should be negligible. Applications generating 100 or more I/O operations per second can see an impact on performance and should avoid using `prudent` mode.<br><br>`NETWORKED FILE LOCKS` When the log file is located on a networked file system, the cost of prudent mode is even greater. Just as importantly, file locks over a networked file system can be sometimes strongly biased such that the process currently owning the lock immediately re-obtains the lock upon its release. Thus, while one process hogs the lock for the log file, other processes starve waiting for the lock to the point of appearing deadlocked.
<br><br>The impact of prudent mode is highly dependent on network speed as well as the OS implementation details. We provide an very small application called **[FileLockSimulator](https://gist.github.com/2794241)** which can help you simulate the behavior of prudent mode in your environment. |


`IMMEDIATE FLUSH` By default, each log event is immediately flushed to the underlying output stream. This default approach is safer in the sense that logging events are not lost in case your application exits without properly closing appenders. However, for significantly increased logging throughput, you may want to set the `immediateFlush` property to `false`.

Below is an example of a configuration file for **FileAppender**:

**Example: FileAppender configuration** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-fileAppender.xml_)


```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>testFile.log</file>
    <append>true</append>
    <!-- set immediateFlush to false for much higher logging throughput -->
    <immediateFlush>true</immediateFlush>
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
    </encoder>
  </appender>
        
  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

After changing the current directory to _logback-examples_, run this example by launching the following command:


```
java chapters.appenders.ConfigurationTester
   src/main/java/chapters/appenders/conf/logback-fileAppender.xml
```


#### Uniquely named files (by timestamp)

During the application development phase or in the case of short-lived applications, e.g. batch applications, it is desirable to create a new log file at each new application launch. This is fairly easy to do with the help of the `<timestamp>` element. Here's an example.

**Example: Uniquely named FileAppender configuration by timestamp** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-timestamp.xml_)


```
<configuration>

  <!-- Insert the current time formatted as "yyyyMMdd'T'HHmmss" under
       the key "bySecond" into the logger context. This value will be
       available to all subsequent configuration elements. -->
  <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <!-- use the previously created timestamp to create a uniquely
         named log file -->
    <file>log-${bySecond}.txt</file>
    <encoder>
      <pattern>%logger{35} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

The timestamp element takes two mandatory attributes `key` and `datePattern` and an optional `timeReference` attribute. The `key` attribute is the name of the key under which the timestamp will be available to subsequent configuration elements as a variable. The `datePattern` attribute denotes the date pattern used to convert the current time (at which the configuration file is parsed) into a string. The date pattern should follow the conventions defined in **[SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)**. The `timeReference` attribute denotes the time reference for the time stamp. The default is the interpretation/parsing time of the configuration file, i.e. the current time. However, under certain circumstances it might be useful to use the context birth time as time reference. This can be accomplished by setting the `timeReference` attribute to "_contextBirth_".

Experiment with the `<timestamp>` element by running the command:


```
java chapters.appenders.ConfigurationTester src/main/resources/chapters/appenders/conf/logback-timestamp.xml
```

To use the logger context birth date as time reference, you would set the `timeReference` attribute to "contextBirth" as shown below.

**Example: Timestamp using context birth date as time reference** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-timestamp-contextBirth.xml_)


```
<configuration>
  <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss" 
             timeReference="contextBirth"/>
  ...
</configuration>
```






