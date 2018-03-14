### RollingFileAppender

**[RollingFileAppender](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/RollingFileAppender.html)** extends **FileAppender** with the capability to rollover log files. For example, **RollingFileAppender** can log to a file named _log.txt_ file and, once a certain condition is met, change its logging target to another file.

There are two important sub-components that interact with **RollingFileAppender**. The first **RollingFileAppender** sub-component, namely **RollingPolicy**, (see below) is responsible for undertaking the actions required for a rollover. A second sub-component of **RollingFileAppender**, namely **TriggeringPolicy**, (see below) will determine if and exactly when rollover occurs. Thus, **RollingPolicy** is responsible for the what and **TriggeringPolicy** is responsible for the _when_.

To be of any use, a **RollingFileAppender** must have both a **RollingPolicy** and a **TriggeringPolicy** set up. However, if its **RollingPolicy** also implements the **TriggeringPolicy** interface, then only the former needs to be specified explicitly.

Here are the available properties for **RollingFileAppender**: