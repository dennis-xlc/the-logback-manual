## What is an Appender?

Logback delegates the task of writing a logging event to components called appenders. Appenders must implement the **ch.qos.logback.core.Appender** interface. The salient methods of this interface are summarized below:



```java
package ch.qos.logback.core;
  
import ch.qos.logback.core.spi.ContextAware;
import ch.qos.logback.core.spi.FilterAttachable;
import ch.qos.logback.core.spi.LifeCycle;
  

public interface Appender<E> extends LifeCycle, ContextAware, FilterAttachable {

  public String getName();
  public void setName(String name);
  void doAppend(E event);
  
}
```


Most of the methods in the **Appender** interface are setters and getters. A notable exception is the **_doAppend()_** method taking an object instance of type **_E_** as its only parameter. The actual type of **_E_** will vary depending on the logback module. Within the logback-classic module **_E_** would be of type **[ILoggingEvent](https://logback.qos.ch/apidocs/ch/qos/logback/classic/spi/ILoggingEvent.html)** and within the logback-access module it would be of type **[AccessEvent](https://logback.qos.ch/apidocs/ch/qos/logback/access/spi/AccessEvent.html)**. The **_doAppend()_** method is perhaps the most important in the logback framework. It is responsible for outputting the logging events in a suitable format to the appropriate output device.

Appenders are named entities. This ensures that they can be referenced by name, a quality confirmed to be instrumental in configuration scripts. The **Appender** interface extends the **FilterAttachable** interface. It follows that one or more filters can be attached to an appender instance. Filters are discussed in detail in a subsequent chapter.

Appenders are ultimately responsible for outputting logging events. However, they may delegate the actual formatting of the event to a **Layout** or to an **Encoder** object. Each layout/encoder is associated with one and only one appender, referred to as the owning appender. Some appenders have a built-in or fixed event format. Consequently, they do not require nor have a layout/encoder. For example, the **SocketAppender** simply serializes logging events before transmitting them over the wire.
