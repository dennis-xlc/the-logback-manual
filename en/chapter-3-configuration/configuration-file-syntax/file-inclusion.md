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

* **As a file:**
  
  To include a file use the file attribute. You can use relative paths but note that the current directory is defined by the application and is not necessarily related to the path of the configuration file.

* **As a resource:**

  To include a resource, i.e a file found on the class path, use the resource attribute.
  
  ```
  <include resource="includedConfig.xml"/>
  ```

* **As a URL:**

  To include the contents of a URL use the url attribute.
  
  ```
  <include url="http://some.host.com/includedConfig.xml"/>
  ```

If it cannot find the file to be included, logback will complain by printing a status message. In case the included file is optional, you can suppress the warning message by setting `optional` attribute to **true** in the `<include>` element.


```
<include optional="true" ..../>
```


  





