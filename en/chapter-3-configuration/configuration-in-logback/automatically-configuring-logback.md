### Automatically configuring logback

The simplest way to configure logback is by letting logback fall back to its default configuration. Let us give a taste of how this is done in an imaginary application called `MyApp1`.

**Example: Simple example of _BasicConfigurator_ usage** ([logback-examples/src/main/java/chapters/configuration/MyApp1.java](https://logback.qos.ch/xref/chapters/configuration/MyApp1.html))



```java
package chapters.configuration;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MyApp1 {
  final static Logger logger = LoggerFactory.getLogger(MyApp1.class);

  public static void main(String[] args) {
    logger.info("Entering application.");

    Foo foo = new Foo();
    foo.doIt();
    logger.info("Exiting application.");
  }
}
```

This class defines a static logger variable. It then instantiates a **Foo** object. The **Foo** class is listed below:

**Example: Small class doing logging** ([logback-examples/src/main/java/chapters/configuration/Foo.java](https://logback.qos.ch/xref/chapters/configuration/Foo.html))



```java
package chapters.configuration;
  
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
   
public class Foo {
  static final Logger logger = LoggerFactory.getLogger(Foo.class);
  
  public void doIt() {
    logger.debug("Did it again!");
  }
}
```

In order to run the examples in this chapter, you need to make sure that certain jar files are present on the class path. Please refer to the [setup page](https://logback.qos.ch/setup.html) for further details.

Assuming the configuration files _logback-test.xml_ or _logback.xml_ are not present, logback will default to invoking **[BasicConfigurator](https://logback.qos.ch/xref/ch/qos/logback/classic/BasicConfigurator.html)** which will set up a minimal configuration. This minimal configuration consists of a **ConsoleAppender** attached to the root logger. The output is formatted using a **PatternLayoutEncoder** set to the pattern **_%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n_**. Moreover, by default the root logger is assigned the **DEBUG** level.

Thus, the output of the command `java chapters.configuration.MyApp1` should be similar to:

```
16:06:09.031 [main] INFO  chapters.configuration.MyApp1 - Entering application.
16:06:09.046 [main] DEBUG chapters.configuration.Foo - Did it again!
16:06:09.046 [main] INFO  chapters.configuration.MyApp1 - Exiting application.
```


The `MyApp1` application links to logback via calls to **org.slf4j.LoggerFactory** and **org.slf4j.Logger** classes, retrieve the loggers it wishes to use, and chugs on. Note that the only dependencies of the **Foo** class on logback are through **org.slf4j.LoggerFactory** and **org.slf4j.Logger** imports. Except code that configures logback (if such code exists) client code does not need to depend on logback. Since SLF4J permits the use of any logging framework under its abstraction layer, it is easy to migrate large bodies of code from one logging framework to another.


