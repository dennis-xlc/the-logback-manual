### File inclusion

Joran supports including parts of a configuration file from another file. This is done by declaring a `<include>` element, as shown below:

**Example: File include** (_logback-examples/src/main/resources/chapters/configuration/containingConfig.xml_)


```
<configuration>
  <include file="src/main/java/chapters/configuration/includedConfig.xml"/>

  <root level="DEBUG">
    <appender-ref ref="includedConsole" />
  </root>

</configuration>
```

The target file MUST have its elements nested inside an `<included>` element. For example, a **ConsoleAppender** could be declared as:

**Example: File include** (_logback-examples/src/main/resources/chapters/configuration/includedConfig.xml_)


```
<included>
  <appender name="includedConsole" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>"%d - %m%n"</pattern>
    </encoder>
  </appender>
</included>
```

Again, please note the mandatory `<included>` element.

The contents to include can be referenced as a file, as a resource, or as a URL.

* As a file:
  To include a file use the file attribute. You can use relative paths but note that the current directory is defined by the application and is not necessarily related to the path of the configuration file.




