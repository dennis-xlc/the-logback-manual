# Chapter 1: Introduction to logback

> _The morale effects are startling. Enthusiasm jumps when there is a running system, even a simple one. Efforts redouble when the first picture from a new graphics software system appears on the screen, even if it is only a rectangle. One always has, at every stage in the process, a working system. I find that teams can grow much more complex entities in four months than they can build._
>
> **—FREDERICK P. BROOKS, JR., The Mythical Man-Month**

## What is logback?

Logback is intended as a successor to the popular log4j project. It was designed by **Ceki Gülcü**, log4j's founder. It builds upon a decade of experience gained in designing industrial-strength logging systems. The resulting product, i.e. logback, is faster and has a smaller footprint than all existing logging systems, sometimes by a wide margin. Just as importantly, logback offers [unique and rather useful features](https://logback.qos.ch/reasonsToSwitch.html) missing in other logging systems.

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

On the first line of the _**main\(\)**_ method, the variable named _logger_ is assigned a **Logger **instance retrieved by invoking the static _**getLogger **_method from the **LoggerFactory **class. This logger is named "chapters.introduction.HelloWorld1". The main method proceeds to call the _**debug **_method of this logger passing "Hello World" as an argument. We say that the main method contains a logging statement of level DEBUG with the message "Hello world".

Note that the above example does not reference any logback classes. In most cases, as far as logging is concerned, your classes will only need to import **SLF4J **classes. Thus, the vast majority, if not all, of your classes will use the SLF4J API and will be oblivious to the existence of logback.

You can launch the first sample application, _chapters.introduction.HelloWorld1_ with the command:

```bash
java chapters.introduction.HelloWorld1
```

Launching the **HelloWorld1** application will output a single line on the console. By virtue of logback's default configuration policy, when no default configuration file is found, logback will add a **ConsoleAppender** to the root logger.



```bash
20:49:07.962 [main] DEBUG chapters.introduction.HelloWorld1 - Hello world.
```

Logback can report information about its internal state using a built-in status system. Important events occurring during logback's lifetime can be accessed through a component called **StatusManager**. For the time being, let us instruct logback to print its internal state by invoking the static **_print()_** method of the **StatusPrinter** class.

**Example: Printing Logger Status** ([logback-examples/src/main/java/chapters/introduction/HelloWorld2.java](https://logback.qos.ch/xref/chapters/introduction/HelloWorld2.html))

