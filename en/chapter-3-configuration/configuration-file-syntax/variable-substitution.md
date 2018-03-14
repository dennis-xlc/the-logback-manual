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


#### Default values for variables

Under certain circumstances, it may be desirable for a variable to have a default value if it is not declared or its value is null. As in the [Bash shell](http://tldp.org/LDP/abs/html/parameter-substitution.html), default values can be specified using the "**:-**" operator. For example, assuming the variable named `aName` is not defined, "**${aName:-golden}**" will be interpreted as "**golden**".

#### Nested variables

Variable nesting is fully supported. Both the name, default-value and value definition of a variable can reference other variables.


##### value nesting

The value definition of a variable can contain references to other variables. Suppose you wish to use variables to specify not only the destination directory but also the file name, and combine those two variables in a third variable called "destination". The properties file shown below gives an example.

**Example: Nested variable references** (_logback-examples/src/main/resources/chapters/configuration/variables2.properties_)


```
USER_HOME=/home/sebastien
fileName=myApp.log
destination=${USER_HOME}/${fileName}
```

Note that in the properties file above, "destination" is composed from two other variables, namely "**USER_HOME**" and "**fileName**".
**
Example: Variable substitution using a separate file** (_logback-examples/src/main/resources/chapters/configuration/variableSubstitution4.xml_)


```
<configuration>

  <property file="variables2.properties" />

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${destination}</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

##### name nesting

When referencing a variable, the variable name may contain a reference to another variable. For example, if the variable named "userid" is assigned the value "alice", then "${${userid}.password}" references the variable with the name "alice.password".

##### default value nesting

The default value of a variable can reference a another variable. For example, assuming the variable 'id' is unassigned and the variable 'userid' is assigned the value "alice", then the expression "${id:-${userid}}" will return "alice".

#### HOSTNAME property

As it often comes in handy, the `HOSTNAME` property is defined automatically during configuration with context scope.

#### CONTEXT_NAME property

As its name indicates, the `CONTEXT_NAME` property corresponds to the name of the current logging context.

#### Setting a timestamp

The `timestamp` element can define a property according to current date and time. The `timestamp` element is explained in a subsequent chapter.

#### Defining properties on the fly

You may define properties dynamically using the `<define>` element. The define element takes two mandatory attributes: `name` and `class`. The `name` attribute designates the name of the property to set whereas the `class` attribute designates any class implementing the **PropertyDefiner** interface. The value returned by the **_getPropertyValue()_** method of the **PropertyDefiner** instance will be the value of the named property. You may also specify a scope for the named property by specifying a `scope` attribute.

Here is an example.


```
<configuration>

  <define name="rootLevel" class="a.class.implementing.PropertyDefiner">
    <shape>round</shape>
    <color>brown</color>
    <size>24</size>
  </define>
 
  <root level="${rootLevel}"/>
</configuration>
```

In the above example, shape, color and size are properties of "**a.class.implementing.PropertyDefiner**". As long as there is a setter for a given property in your implementation of the **PropertyDefiner** instance, logback will inject the appropriate values as specified in the configuration file.

At the present time, logback does ships with two fairly simple implementations of **PropertyDefiner**.

| Implementation name | Description |
| :--- | :--- |
| **[CanonicalHostNamePropertyDefiner](https://logback.qos.ch/apidocs/ch/qos/logback/core/property/CanonicalHostNamePropertyDefiner.html)** | Set the named variable to the canonical host name of the local host. Note that obtaining the canonical host name may take several seconds. |
| **[FileExistsPropertyDefiner](https://logback.qos.ch/apidocs/ch/qos/logback/core/property/FileExistsPropertyDefiner.html)** | Set the named variable to "true" if the file specified by `path` property exists, to "false" otherwise. |
| **[ResourceExistsPropertyDefiner](https://logback.qos.ch/apidocs/ch/qos/logback/core/property/FileExistsPropertyDefiner.html)** | Set the named variable to "true" if the `resource` specified by the user is available on the class path, to "false" otherwise. |



