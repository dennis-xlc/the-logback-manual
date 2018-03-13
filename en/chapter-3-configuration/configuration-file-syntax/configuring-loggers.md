### Configuring loggers, or the \<logger\> element

At this point you should have at least some understanding of level inheritance and the basic selection rule. Otherwise, and unless you are an Egyptologist, logback configuration will be no more meaningful to you than are hieroglyphics.

A logger is configured using the `<logger>` element. A `<logger>` element takes exactly one mandatory `name` attribute, an optional `level` attribute, and an optional `additivity` attribute, admitting the values `true` or `false`. The value of the `level` attribute admitting one of the case-insensitive string values **TRACE**, **DEBUG**, **INFO**, **WARN**, **ERROR**, **ALL** or **OFF**. The special case-insensitive value **INHERITED**, or its synonym **NULL**, will force the level of the logger to be inherited from higher up in the hierarchy. This comes in handy if you set the level of a logger and later decide that it should inherit its level.

The `<logger>` element may contain zero or more `<appender-ref>` elements; each appender thus referenced is added to the named logger. Note that unlike log4j, logback-classic does not close nor remove any previously referenced appenders when configuring a given logger.


#### Configuring the root logger, or the <root> element

The `<root>` element configures the root logger. It supports a single attribute, namely the `level` attribute. It does not allow any other attributes because the additivity flag does not apply to the root logger. Moreover, since the root logger is already named as `"ROOT"`, it does not allow a name attribute either. The value of the level attribute can be one of the case-insensitive strings **TRACE**, **DEBUG**, **INFO**, **WARN**, **ERROR**, **ALL** or **OFF**. Note that the level of the root logger cannot be set to **INHERITED** or **NULL**.

Similarly to the `<logger>` element, the `<root>` element may contain zero or more `<appender-ref>` elements; each appender thus referenced is added to the root logger. Note that unlike log4j, logback-classic does **_not_** close nor remove any previously referenced appenders when configuring the root logger.

#### Example

