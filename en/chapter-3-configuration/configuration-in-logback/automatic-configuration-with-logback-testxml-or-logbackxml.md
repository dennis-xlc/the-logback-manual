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