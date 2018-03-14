#### Size Based Triggering Policy

**[SizeBasedTriggeringPolicy](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/SizeBasedTriggeringPolicy.html)** looks at the size of the currently active file. If it grows larger than the specified size, it will signal the owning **RollingFileAppender** to trigger the rollover of the existing active file.

**SizeBasedTriggeringPolicy** accepts only one parameter, namely `maxFileSize`, with a default value of 10 MB.

The `maxFileSize` option can be specified in bytes, kilobytes, megabytes or gigabytes by suffixing a numeric value with _KB_, _MB_ and respectively _GB_. For example, _5000000_, _5000KB_, _5MB_ and _2GB_ are all valid values, with the first three being equivalent.

Here is a sample configuration with a **RollingFileAppender** in conjunction with **SizeBasedTriggeringPolicy** triggering rollover when the log file reaches 5MB in size.

**Example: Sample configuration of a RollingFileAppender using a SizeBasedTriggeringPolicy** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-RollingSizeBased.xml_)


```
<configuration>
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>test.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
      <fileNamePattern>test.%i.log.zip</fileNamePattern>
      <minIndex>1</minIndex>
      <maxIndex>3</maxIndex>
    </rollingPolicy>

    <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
      <maxFileSize>5MB</maxFileSize>
    </triggeringPolicy>
    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
    </encoder>
  </appender>
        
  <root level="DEBUG">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

