### Automatic configuration with _logback-test.xml _or _logback.xml_


As mentioned earlier, logback will try to configure itself using the files `logback-test.xml` or `logback.xml` if found on the class path. Here is a configuration file equivalent to the one established by **BasicConfigurator** we've just seen.


**Example: Basic configuration file** (_logback-examples/src/main/resources/chapters/configuration/sample0.xml_)


```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

After you have renamed _sample0.xml_ as _logback.xml_ (or _logback-test.xml_) place it into a directory accessible from the class path. Running the `MyApp1` application should give identical results to its previous run.

#### Automatic printing of status messages in case of warning or errors

If warnings or errors occur during the parsing of the configuration file, logback will automatically print its internal status data on the console. Note that to avoid duplication, automatic status printing is disabled if the user explicitly registers a status listener (defined below).

In the absence of warnings or errors, if you still wish to inspect logback's internal status, then you can instruct logback to print status data by invoking the **_print()_** of the **StatusPrinter** class. The `MyApp2` application shown below is identical to `MyApp1` except for the addition of two lines of code for printing internal status data.

**Example: Print logback's internal status information** (_[logback-examples/src/main/java/chapters/configuration/MyApp2.java](https://logback.qos.ch/xref/chapters/configuration/MyApp2.html)_)


```java
public static void main(String[] args) {
  // assume SLF4J is bound to logback in the current environment
  LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
  // print logback's internal status
  StatusPrinter.print(lc);
  ...
}
```

If everything goes well, you should see the following output on the console

```
17:44:58,578 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Found resource [logback-test.xml]
17:44:58,671 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - debug attribute not set
17:44:58,671 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - About to instantiate appender of type [ch.qos.logback.core.ConsoleAppender]
17:44:58,687 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Naming appender as [STDOUT]
17:44:58,812 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Popping appender named [STDOUT] from the object stack
17:44:58,812 |-INFO in ch.qos.logback.classic.joran.action.LevelAction - root level set to DEBUG
17:44:58,812 |-INFO in ch.qos.logback.core.joran.action.AppenderRefAction - Attaching appender named [STDOUT] to Logger[root]

17:44:58.828 [main] INFO  chapters.configuration.MyApp2 - Entering application.
17:44:58.828 [main] DEBUG chapters.configuration.Foo - Did it again!
17:44:58.828 [main] INFO  chapters.configuration.MyApp2 - Exiting application.
```

At the end of this output, you can recognize the lines that were printed in the previous example. You should also notice the logback's internal messages, a.k.a. **Status** objects, which allow convenient access to logback's internal state.


#### Status data

Instead of invoking **StatusPrinter** programmatically from your code, you can instruct the configuration file to dump status data, even in the absence of errors. To achieve this, you need to set the `debug` attribute of the configuration element, i.e. the top-most element in the configuration file, as shown below. Please note that this `debug` attribute relates only to the status data. It does _**not**_ affect logback's configuration otherwise, in particular with respect to logger levels. (If you are asking, no, the root logger will **_not_** be set to **DEBUG**.)

**Example: Basic configuration file using debug mode** (_logback-examples/src/main/resources/chapters/configuration/sample1.xml_)


```
<configuration debug="true"> 

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender"> 
    <!-- encoders are  by default assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder -->
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

Setting `debug="true"` within the `<configuration>` element will output status information, assuming that:

1. the configuration file is found

2. the configuration file is well-formed XML.


