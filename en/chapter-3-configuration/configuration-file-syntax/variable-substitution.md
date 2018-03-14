### Variable substitution

`NOTE` Earlier versions of this document used the term "`property substitution`" instead of the term "`variable`". Please consider both terms interchangeable although the latter term conveys a clearer meaning.

As in many scripting languages, logback configuration files support definition and substitution of variables. Variables have a _**scope**_ (see below). Moreover, variables can be defined within the configuration file itself, in an external file, in an external resource or even computed and defined on the fly.

Variable substitution can occur at any point in a configuration file where a value can be specified. The syntax of variable substitution is similar to that of Unix shells. The string between an opening _${_ and closing _}_ is interpreted as a reference to the _value_ of the property. For property `aName`, the string "`${aName}`" will be replaced with the value held by the `aName` property.

As they often come in handy, the `HOSTNAME` and `CONTEXT_NAME` variables are automatically defined and have context scope. Given that in some environments it may take some time to compute the hostname, its value is computed lazily (only when needed). Moreover, `HOSTNAME` can be set from within the configuration directly.

#### Defining variables

Variables can be defined one at a time in the configuration file itself or loaded wholesale from an external properties file or an external resource. For historical reasons, the XML element for defining variables is `<property>` although in logback 1.0.7 and later the element `<variable>` can be used interchangeably.

The next example shows a variable declared at the beginning of the configuration file. It is then used further down the file to specify the location of the output file.

**Example: Simple Variable substitution** (_logback-examples/src/main/resources/chapters/configuration/variableSubstitution1.xml_)

```
<configuration>

  <property name="USER_HOME" value="/home/sebastien" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${USER_HOME}/myApp.log</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

The next example shows the use of a System property to achieve the same result. The property is not declared in the configuration file, thus logback will look for it in the System properties. Java system properties can be set on the command line as shown next:


```
java -DUSER_HOME="/home/sebastien" MyApp2
```

**Example: System Variable substitution** (_logback-examples/src/main/resources/chapters/configuration/variableSubstitution2.xml_)


```
<configuration>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${USER_HOME}/myApp.log</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

When multiple variables are needed, it may be more convenient to create a separate file that will contain all the variables. Here is how one can do such a setup.

**Example: Variable substitution using a separate file** (_logback-examples/src/main/resources/chapters/configuration/variableSubstitution3.xml_)


```
<configuration>

  <property file="src/main/java/chapters/configuration/variables1.properties" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
     <file>${USER_HOME}/myApp.log</file>
     <encoder>
       <pattern>%msg%n</pattern>
     </encoder>
   </appender>

   <root level="debug">
     <appender-ref ref="FILE" />
   </root>
</configuration>
```

This configuration file contains a reference to a file named _variables1.properties_. The variables contained in that file will be read and then defined within local scope. Here is what the _variable.properties_ file might look like.

**Example: Variable file** (_logback-examples/src/main/resources/chapters/configuration/variables1.properties_)


```
USER_HOME=/home/sebastien
```

You may also reference a resource on the class path instead of a file.

```
<configuration>

  <property resource="resource1.properties" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
     <file>${USER_HOME}/myApp.log</file>
     <encoder>
       <pattern>%msg%n</pattern>
     </encoder>
   </appender>

   <root level="debug">
     <appender-ref ref="FILE" />
   </root>
</configuration>
```

#### Scopes

A property can be defined for insertion in **_local scope_**, in **_context scope_**, or in system scope. Local scope is the default. Although it is possible to read variables from the OS environment, it is not possible to write into the OS environment.

`LOCAL SCOPE` A property with local scope exists from the point of its definition in a configuration file until the end of interpretation/execution of said configuration file. As a corollary, each time a configuration file is parsed and executed, variables in local scope are defined anew.

`CONTEXT SCOPE` A property with context scope is inserted into the context and lasts as long as the context or until it is cleared. Once defined, a property in context scope is part of the context. As such, it is available in all logging events, including those sent to remote hosts via serialization.

`SYSTEM SCOPE` A property with system scope is inserted into the JVM's system properties and lasts as long as the JVM or until it is cleared.

During substitution, properties are looked up in the local scope first, in the context scope second, in the system properties scope third, and in the [OS environment](http://docs.oracle.com/javase/tutorial/essential/environment/env.html) fourth and last.

The `scope` attribute of the `<property>` element, <define> element or the `<insertFromJNDI>` element can be used to set the scope of a property. The `scope` attribute admits "local", "context" and "system" strings as possible values. If not specified, the scope is always assumed to be "local".


**Example: A variable defined in "context" scope** (_logback-examples/src/main/resources/chapters/configuration/contextScopedVariable.xml_)


```
<configuration>

  <property scope="context" name="nodeId" value="firstNode" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>/opt/${nodeId}/myApp.log</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

In the above example, given that the `nodeId` property is defined in the context scope, it will be available in every logging event, even those sent to remote hosts via serialization.




