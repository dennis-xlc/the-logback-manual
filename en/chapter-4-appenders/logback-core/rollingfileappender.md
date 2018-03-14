### RollingFileAppender

**[RollingFileAppender](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/RollingFileAppender.html)** extends **FileAppender** with the capability to rollover log files. For example, **RollingFileAppender** can log to a file named _log.txt_ file and, once a certain condition is met, change its logging target to another file.

There are two important sub-components that interact with **RollingFileAppender**. The first **RollingFileAppender** sub-component, namely **RollingPolicy**, (see below) is responsible for undertaking the actions required for a rollover. A second sub-component of **RollingFileAppender**, namely **TriggeringPolicy**, (see below) will determine if and exactly when rollover occurs. Thus, **RollingPolicy** is responsible for the what and **TriggeringPolicy** is responsible for the _when_.

To be of any use, a **RollingFileAppender** must have both a **RollingPolicy** and a **TriggeringPolicy** set up. However, if its **RollingPolicy** also implements the **TriggeringPolicy** interface, then only the former needs to be specified explicitly.

Here are the available properties for **RollingFileAppender**:

| Property Name | Type | Description |
| :--- | :--- | :--- |
| `file` | String | See **FileAppender** properties. |
| `append` | boolean | See **FileAppender** properties. |
| `encoder` | **[Encoder](https://logback.qos.ch/xref/ch/qos/logback/core/encoder/Encoder.html)** | See **OutputStreamAppender** properties. |
| `rollingPolicy` | **RollingPolicy** | This option is the component that will dictate **RollingFileAppender**'s behavior when rollover occurs. See more information below. |
| `triggeringPolicy` | **TriggeringPolicy** | This option is the component that will tell **RollingFileAppender** when to activate the rollover procedure. See more information below. |
| `prudent` | boolean | **[FixedWindowRollingPolicy](https://logback.qos.ch/manual/appenders.html#FixedWindowRollingPolicy)** is not supported in prudent mode.<br><br>**RollingFileAppender** supports the prudent mode in conjunction with **[TimeBasedRollingPolicy](https://logback.qos.ch/manual/appenders.html#TimeBasedRollingPolicy)** albeit with two restrictions.<br>1. In prudent mode, file compression is not supported nor allowed. (We can't have one JVM writing to a file while another JVM is compressing it.)<br>2. The file property of FileAppender cannot be set and must be left blank. Indeed, most operating systems do not allow renaming of a file while another process has it opened.<br><br>See also properties for FileAppender.|



