### Automatic configuration with _logback-test.xml \_or \_logback.xml_

As mentioned earlier, logback will try to configure itself using the files `logback-test.xml` or `logback.xml` if found on the class path. Here is a configuration file equivalent to the one established by **BasicConfigurator** we've just seen.

**Example: Basic configuration file** \(_logback-examples/src/main/resources/chapters/configuration/sample0.xml_\)

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

After you have renamed _sample0.xml_ as _logback.xml_ \(or _logback-test.xml_\) place it into a directory accessible from the class path. Running the `MyApp1` application should give identical results to its previous run.

#### Automatic printing of status messages in case of warning or errors

If warnings or errors occur during the parsing of the configuration file, logback will automatically print its internal status data on the console. Note that to avoid duplication, automatic status printing is disabled if the user explicitly registers a status listener \(defined below\).

In the absence of warnings or errors, if you still wish to inspect logback's internal status, then you can instruct logback to print status data by invoking the _**print\(\)**_ of the **StatusPrinter** class. The `MyApp2` application shown below is identical to `MyApp1` except for the addition of two lines of code for printing internal status data.

**Example: Print logback's internal status information** \([_logback-examples/src/main/java/chapters/configuration/MyApp2.java_](https://logback.qos.ch/xref/chapters/configuration/MyApp2.html)\)

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

Instead of invoking **StatusPrinter** programmatically from your code, you can instruct the configuration file to dump status data, even in the absence of errors. To achieve this, you need to set the `debug` attribute of the configuration element, i.e. the top-most element in the configuration file, as shown below. Please note that this `debug` attribute relates only to the status data. It does _**not**_ affect logback's configuration otherwise, in particular with respect to logger levels. \(If you are asking, no, the root logger will _**not**_ be set to **DEBUG**.\)

**Example: Basic configuration file using debug mode** \(_logback-examples/src/main/resources/chapters/configuration/sample1.xml_\)

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

If any of these two conditions is not fulfilled, Joran cannot interpret the `debug` attribute since the configuration file cannot be read. If the configuration file is found but is malformed, then logback will detect the error condition and automatically print its internal status on the console. However, if the configuration file cannot be found, logback will not automatically print its status data, since this is not necessarily an error condition. Programmatically invoking _**StatusPrinter.print\(\)**_ as shown in the `MyApp2` application above ensures that status information is printed in every case.

`FORCING STATUS OUTPUT` In the absence of status messages, tracking down a rogue _logback.xml_ configuration file can be difficult, especially in production where the application source cannot be easily modified. To help identify the location of a rogue configuration file, you can set a **StatusListener** via the "`logback.statusListenerClass`" system property \(defined below\) to force output of status messages. The "`logback.statusListenerClass`" system property can also be used to silence output automatically generated in case of errors.

By the way, setting `debug="true"` is strictly equivalent to installing an **OnConsoleStatusListener**. Status listeners are disccussed further below. The installation of **OnConsoleStatusListener** is shown next.

**Example: Registering a status listener** \(_logback-examples/src/main/resources/chapters/configuration/onConsoleStatusListener.xml_\)

```
<configuration>
  <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />  

  ... the rest of the configuration file  
</configuration>
```

Enabling output of status data, either via the debug attribute or equivalently by installing **OnConsoleStatusListener**, will go a long way in helping you diagnose logback issues. As such, enabling logback status data is very highly recommended and should be considered as a recourse of **first** resort.

