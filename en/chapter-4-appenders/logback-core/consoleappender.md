### ConsoleAppender

The **[ConsoleAppender](https://logback.qos.ch/xref/ch/qos/logback/core/ConsoleAppender.html)**, as the name indicates, appends on the console, or more precisely on `System.out` or `System.err`, the former being the default target. **ConsoleAppender** formats events with the help of an encoder specified by the user. Encoders will be discussed in a subsequent chapter. Both `System.out` and `System.err` are of type **java.io.PrintStream**. Consequently, they are wrapped inside an **OutputStreamWriter** which buffers I/O operations.

| Property Name | Type | Description |
| :--- | :--- | :--- |
| `encoder` | **[Encoder](https://logback.qos.ch/xref/ch/qos/logback/core/encoder/Encoder.html)** | See **OutputStreamAppender** properties. |
| `target` | String | One of the String values `System.out` or `System.err`. The default target is `System.out`. |
| `withJansi`| boolean | By the default `withJansi` property is set to `false`. Setting `withJansi` to `true` activates the [Jansi](http://jansi.fusesource.org/) library which provides support for ANSI color codes on Windows machines. On a Windows host, if this property is set to true, then you should put "_org.fusesource.jansi:jansi:1.9_" on the class path. Note that Unix-based operating systems such as Linux and Mac OS X support ANSI color codes by default. <br><br>Under the Eclipse IDE, you might want to try the [ANSI in Eclipse Console plugin](http://www.mihai-nita.net/eclipse/).|


Here is a sample configuration that uses **ConsoleAppender**.

**Example: ConsoleAppender configuration** (_logback-examples/src/main/resources/chapters/appenders/conf/logback-Console.xml_)


```
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

After you have set your current path to the _logback-examples_ directory and [set up your class path](https://logback.qos.ch/setup.html), you can give the above configuration file a whirl by issuing the following command:


```
java chapters.appenders.ConfigurationTester src/main/java/chapters/appenders/conf/logback-Console.xml
```



