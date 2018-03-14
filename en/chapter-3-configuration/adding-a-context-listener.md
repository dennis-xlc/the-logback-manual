## Adding a context listener

Instances of the **[LoggerContextListener](https://logback.qos.ch/xref/ch/qos/logback/classic/spi/LoggerContextListener.html)** interface listen to events pertaining to the lifecycle of a logger context.

**JMXConfigurator** is one implementation of the **LoggerContextListener** interface. It is described in a subsequent chapter.

### LevelChangePropagator

As of version 0.9.25, logback-classic ships with **[LevelChangePropagator](https://logback.qos.ch/xref/ch/qos/logback/classic/jul/LevelChangePropagator.html)**, an implementation of **LoggerContextListener** which propagates changes made to the level of any logback-classic logger onto the java.util.logging framework. Such propagation eliminates the performance impact of disabled log statements. Instances of **[LogRecord](http://download.oracle.com/javase/1.5.0/docs/api/java/util/logging/LogRecord.html?is-external=true)** will be sent to logback (via SLF4J) only for enabled log statements. This makes it reasonable for real-world applications to use the [jul-to-slf4j](http://www.slf4j.org/legacy.html#jul-to-slf4j) bridge.

The contextListener element can be used to install **LevelChangePropagator** as shown next.


```
<configuration debug="true">
  <contextListener class="ch.qos.logback.classic.jul.LevelChangePropagator"/>
  .... 
</configuration>
```
Setting the `resetJUL` property of **LevelChangePropagator** will reset all previous level configurations of all _j.u.l._ loggers. However, previously installed handlers will be left untouched.


```
<configuration debug="true">
  <contextListener class="ch.qos.logback.classic.jul.LevelChangePropagator">
    <resetJUL>true</resetJUL>
  </contextListener>
  ....
</configuration>
```




