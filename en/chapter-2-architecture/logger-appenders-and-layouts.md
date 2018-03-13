## Logger, Appenders and Layouts

Logback is built upon three main classes: **Logger**, **Appender** and **Layout**. These three types of components work together to enable developers to log messages according to message type and level, and to control at runtime how these messages are formatted and where they are reported.

The **Logger** class is part of the logback-classic module. On the other hand, the **Appender** and **Layout** interfaces are part of `logback-core`. As a general-purpose module, `logback-core` has no notion of loggers.

