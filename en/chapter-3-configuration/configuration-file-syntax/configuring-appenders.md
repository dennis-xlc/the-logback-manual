### Configuring Appenders

An appender is configured with the `<appender>` element, which takes two mandatory attributes `name` and `class`. The `name` attribute specifies the name of the appender whereas the `class` attribute specifies the fully qualified name of the appender class to instantiate. The `<appender>` element may contain zero or one `<layout>` elements, zero or more `<encoder>` elements and zero or more `<filter>` elements. Apart from these three common elements, `<appender>` elements may contain any number of elements corresponding to JavaBean properties of the appender class. Seamlessly supporting any property of a given logback component is one of the major strengths of **_Joran_** as discussed in a later chapter. The following diagram illustrates the common structure. Note that support for properties is not visible.

![](appenderSyntax.png)


The `<layout>` element takes a mandatory class attribute specifying the fully qualified name of the layout class to instantiate. As with the `<appender>` element, `<layout>` may contain other elements corresponding to properties of the layout instance. Since it's such a common case, if the layout class is **PatternLayout**, then the class attribute can be omitted as specified by default class mapping rules.

The `<encoder>` element takes a mandatory class attribute specifying the fully qualified name of the encoder class to instantiate. Since it's such a common case, if the encoder class is **PatternLayoutEncoder**, then the class attribute can be omitted as specified by default class mapping rules.

Logging to multiple appenders is as easy as defining the various appenders and referencing them in a logger, as the next configuration file illustrates:

**Example: Multiple loggers** (_logback-examples/src/main/resources/chapters/configuration/multiple.xml_)


```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>myApp.log</file>

    <encoder>
      <pattern>%date %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

These configuration scripts define two appenders called **FILE** and **STDOUT**. The **FILE** appender logs to a file called _myApp.log_. The encoder for this appender is a **PatternLayoutEncoder** that outputs the date, level, thread name, logger name, file name and line number where the log request is located, the message and line separator character(s). The second appender called `STDOUT` outputs to the console. The encoder for this appender outputs only the message string followed by a line separator.

The appenders are attached to the root logger by referencing them by name within an `appender-ref` element. Note that each appender has its own encoder. Encoders are usually not designed to be shared by multiple appenders. The same is true for layouts. As such, logback configuration files do not provide any syntactical means for sharing encoders or layouts.

#### Appenders accumulate

By default, **appenders are cumulative**: a logger will log to the appenders attached to itself (if any) as well as all the appenders attached to its ancestors. Thus, attaching the same appender to multiple loggers will cause logging output to be duplicated.

**Example: Duplicate appender** (_logback-examples/src/main/resources/chapters/configuration/duplicate.xml_)

```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration">
    <appender-ref ref="STDOUT" />
  </logger>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

Running `MyApp3` with _duplicate.xml_ will yield the following output:


```
14:25:36.343 [main] INFO  chapters.configuration.MyApp3 - Entering application.
14:25:36.343 [main] INFO  chapters.configuration.MyApp3 - Entering application.
14:25:36.359 [main] DEBUG chapters.configuration.Foo - Did it again!
14:25:36.359 [main] DEBUG chapters.configuration.Foo - Did it again!
14:25:36.359 [main] INFO  chapters.configuration.MyApp3 - Exiting application.
14:25:36.359 [main] INFO  chapters.configuration.MyApp3 - Exiting application.
```

Notice the duplicated output. The appender named `STDOUT` is attached to two loggers, to _root_ and to _chapters.configuration_. Since the root logger is the ancestor of all loggers and _chapters.configuration_ is the parent of both **chapters.configuration.MyApp3** and **chapters.configuration.Foo**, each logging request made with these two loggers will be output twice, once because `STDOUT` is attached to _chapters.configuration_ and once because it is attached to _root_.

Appender additivity is not intended as a trap for new users. It is quite a convenient logback feature. For instance, you can configure logging such that log messages appear on the console (for all loggers in the system) while messages only from some specific set of loggers flow into a specific appender.


**Example: Multiple appender** (_logback-examples/src/main/resources/chapters/configuration/restricted.xml_)

```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>myApp.log</file>
    <encoder>
      <pattern>%date %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration">
    <appender-ref ref="FILE" />
  </logger>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

In this example, the console appender will log all the messages (for all loggers in the system) whereas only logging requests originating from the _chapters.configuration_ logger and its children will go into the _myApp.log_ file.


#### Overriding the default cumulative behaviour

In case the default cumulative behavior turns out to be unsuitable for your needs, you can override it by setting the additivity flag to false. Thus, a branch in your logger tree may direct output to a set of appenders different from those of the rest of the tree.

**Example: Additivity flag** (_logback-examples/src/main/resources/chapters/configuration/additivityFlag.xml_)

```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>foo.log</file>
    <encoder>
      <pattern>%date %level [%thread] %logger{10} [%file : %line] %msg%n</pattern>
    </encoder>
  </appender>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <logger name="chapters.configuration.Foo" additivity="false">
    <appender-ref ref="FILE" />
  </logger>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

This example, the appender named `FILE` is attached to the **chapters.configuration.Foo** logger. Moreover, the **chapters.configuration.Foo** logger has its additivity flag set to false such that its logging output will be sent to the appender named `FILE` but not to any appender attached higher in the hierarchy. Other loggers remain oblivious to the additivity setting of the **chapters.configuration.Foo** logger. Running the `MyApp3` application with the _additivityFlag.xml_ configuration file will output results on the console from the **chapters.configuration.MyApp3** logger. However, output from the **chapters.configuration.Foo** logger will appear in the _foo.log_ file and only in that file.