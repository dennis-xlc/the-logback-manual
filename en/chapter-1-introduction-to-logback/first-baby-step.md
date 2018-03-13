## First Baby Step

### Requirements

Logback-classic module requires the presence of _slf4j-api.jar_ and _logback-core.jar_ in addition to _logback-classic.jar_ on the classpath.

The _logback-\*.jar_ files are part of the logback distribution whereas _slf4j-api-1.8.0-beta1.jar_ ships with [SLF4J](http://www.slf4j.org/), a separate project.

Let us now begin experimenting with logback.

**Example 1.1: Basic template for logging ** \([logback-examples/src/main/java/chapters/introduction/HelloWorld1.java](https://logback.qos.ch/xref/chapters/introduction/HelloWorld1.html)\)

```java
package chapters.introduction;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld1 {

  public static void main(String[] args) {

    Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld1");
    logger.debug("Hello world.");

  }
}
```

**HelloWorld1** class is defined in the _chapters.introduction_ package. It starts by importing the [Logger](http://slf4j.org/api/org/slf4j/Logger.html) and [LoggerFactory](http://slf4j.org/api/org/slf4j/LoggerFactory.html) classes defined in the **SLF4J API**, specifically within the _org.slf4j_ package.

On the first line of the _**main\(\)**_ method, the variable named _logger_ is assigned a **Logger **instance retrieved by invoking the static _**getLogger **\_method from the **LoggerFactory **class. This logger is named "chapters.introduction.HelloWorld1". The main method proceeds to call the _**debug **\_method of this logger passing "Hello World" as an argument. We say that the main method contains a logging statement of level DEBUG with the message "Hello world".

Note that the above example does not reference any logback classes. In most cases, as far as logging is concerned, your classes will only need to import **SLF4J **classes. Thus, the vast majority, if not all, of your classes will use the SLF4J API and will be oblivious to the existence of logback.

You can launch the first sample application, _chapters.introduction.HelloWorld1_ with the command:

```bash
java chapters.introduction.HelloWorld1
```

Launching the **HelloWorld1** application will output a single line on the console. By virtue of logback's default configuration policy, when no default configuration file is found, logback will add a **ConsoleAppender** to the root logger.

```
20:49:07.962 [main] DEBUG chapters.introduction.HelloWorld1 - Hello world.
```

Logback can report information about its internal state using a built-in status system. Important events occurring during logback's lifetime can be accessed through a component called **StatusManager**. For the time being, let us instruct logback to print its internal state by invoking the static _**print\(\)**_ method of the **StatusPrinter** class.

**Example: Printing Logger Status** \([logback-examples/src/main/java/chapters/introduction/HelloWorld2.java](https://logback.qos.ch/xref/chapters/introduction/HelloWorld2.html)\)

```java
package chapters.introduction;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.core.util.StatusPrinter;

public class HelloWorld2 {

  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld2");
    logger.debug("Hello world.");

    // print internal state
    LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
    StatusPrinter.print(lc);
  }
}
```

Running the **HelloWorld2** application will produce the following output:

```
12:49:22.203 [main] DEBUG chapters.introduction.HelloWorld2 - Hello world.
12:49:22,076 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.groovy]
12:49:22,078 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback-test.xml]
12:49:22,093 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.xml]
12:49:22,093 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Setting up default configuration.
```

Logback explains that having failed to find the _logback-test.xml_ and _logback.xml_ configuration files \(discussed later\), it configured itself using its default policy, which is a basic **ConsoleAppender**. An **Appender** is a class that can be seen as an output destination. Appenders exist for many different destinations including the console, files, Syslog, TCP Sockets, JMS and many more. Users can also easily create their own Appenders as appropriate for their specific situation.

Note that in case of errors, logback will automatically print its internal state on the console.

The previous examples are rather simple. Actual logging in a larger application would not be that different. The general pattern for logging statements would not change. Only the configuration process would be different. However, you would probably want to customize or configure logback according to your needs. Logback configuration will be covered in subsequent chapters.

Note that in the above example we have instructed logback to print its internal state by invoking the _**StatusPrinter.print\(\)**_ method. Logback's internal status information can be very useful in diagnosing logback-related problems.

Here is a list of the three required steps in order to enable logging in your application.

1. Configure the logback environment. You can do so in several more or less sophisticated ways. More on this later.
2. In every class where you wish to perform logging, retrieve a **Logger** instance by invoking the **org.slf4j.LoggerFactory** class' _**getLogger\(\)**_ method, passing the current class name or the class itself as a parameter.
3. Use this logger instance by invoking its printing methods, namely the _**debug\(\)**_, _**info\(\)**_, _**warn\(\)**_ and _**error\(\)**_ methods. This will produce logging output on the configured appenders.



