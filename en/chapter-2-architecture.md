# Chapter 2: Architecture

> _All true classification is genealogical._
>
> **—CHARLES DARWIN,**_**The Origin of Species**_
>
> _It is difficult, if not impossible, for anyone to learn a subject purely by reading about it, without applying the information to specific problems and thereby forcing himself to think about what has been read. Furthermore, we all learn best the things that we have discovered ourselves._
>
> **—DONALD KNUTH,**_**The Art of Computer Programming**_

## Logback's architecture

Logback's basic architecture is sufficiently generic so as to apply under different circumstances. At the present time, logback is divided into three modules, `logback-core`, `logback-classic` and `logback-access`.

The _core_  module lays the groundwork for the other two modules. The _classic_ module extends _core_. The _classic_ module corresponds to a significantly improved version of `log4j`. Logback-classic natively implements the [SLF4J API](http://www.slf4j.org/) so that you can readily switch back and forth between logback and other logging systems such as `log4j` or `java.util.logging` \(JUL\) introduced in JDK 1.4. The third module called _access_ integrates with Servlet containers to provide HTTP-access log functionality. A separate document covers [access module documentation](https://logback.qos.ch/access.html).

In the remainder of this document, we will write "logback" to refer to the logback-classic module.

## Logger, Appenders and Layouts

Logback is built upon three main classes: **Logger**, **Appender** and **Layout**. These three types of components work together to enable developers to log messages according to message type and level, and to control at runtime how these messages are formatted and where they are reported.

The **Logger** class is part of the logback-classic module. On the other hand, the **Appender** and **Layout** interfaces are part of `logback-core`. As a general-purpose module, `logback-core` has no notion of loggers.

### Logger context

The first and foremost advantage of any logging API over plain _**System.out.println**_ resides in its ability to disable certain log statements while allowing others to print unhindered. This capability assumes that the logging space, that is, the space of all possible logging statements, is categorized according to some developer-chosen criteria. In `logback-classic`, this categorization is an inherent part of loggers. Every single logger is attached to a **LoggerContext** which is responsible for manufacturing loggers as well as arranging them in a tree like hierarchy.

Loggers are named entities. Their names are case-sensitive and they follow the hierarchical naming rule:

> **Named Hierarchy**  
> _A logger is said to be an ancestor of another logger if its name followed by a dot is a prefix of the descendant logger name. A logger is said to be a parent of a child logger if there are no ancestors between itself and the descendant logger._

For example, the logger named`"com.foo"`is a parent of the logger named`"com.foo.Bar"`. Similarly,`"java"`is a parent of`"java.util"`and an ancestor of`"java.util.Vector"`. This naming scheme should be familiar to most developers.

The root logger resides at the top of the logger hierarchy. It is exceptional in that it is part of every hierarchy at its inception. Like every logger, it can be retrieved by its name, as follows:

```java
Logger rootLogger = LoggerFactory.getLogger(org.slf4j.Logger.ROOT_LOGGER_NAME);
```

All other loggers are also retrieved with the class static _**getLogger**_ method found in the [org.slf4j.LoggerFactory](http://www.slf4j.org/api/org/slf4j/Logger.html) class. This method takes the name of the desired logger as a parameter. Some of the basic methods in the **Logger** interface are listed below.

```java
package org.slf4j; 
public interface Logger {

  // Printing methods: 
  public void trace(String message);
  public void debug(String message);
  public void info(String message); 
  public void warn(String message); 
  public void error(String message); 
}
```



