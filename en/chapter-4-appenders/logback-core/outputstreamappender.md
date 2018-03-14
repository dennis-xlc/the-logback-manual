### OutputStreamAppender

**[OutputStreamAppender](https://logback.qos.ch/xref/ch/qos/logback/core/OutputStreamAppender.html)** appends events to a **java.io.OutputStream**. This class provides basic services that other appenders build upon. Users do not usually instantiate **OutputStreamAppender** objects directly, since in general the **java.io.OutputStream** type cannot be conveniently mapped to a string, as there is no way to specify the target **OutputStream** object in a configuration script. Simply put, you cannot configure a **OutputStreamAppender** from a configuration file. However, this does not mean that **OutputStreamAppender** lacks configurable properties. These properties are described next.

| Property Name | Type | Description |
| :--- | :--- | :--- |
| **encoder** | **[Encoder](https://logback.qos.ch/xref/ch/qos/logback/core/encoder/Encoder.html)** | Determines the manner in which an event is written to the underlying **OutputStreamAppender**. Encoders are described in a dedicated chapter. |
| **immediateFlush** | boolean | The default value for `immediateFlush` is 'true'. Immediate flushing of the output stream ensures that logging events are immediately written out and will not be lost in case your application exits without properly closing appenders. On the other hand, setting this property to 'false' is likely to quadruple (your mileage may vary) logging throughput. Again, if `immediateFlush` is set to 'false' and if appenders are not closed properly when your application exits, then logging events not yet written to disk may be lost. |

The **OutputStreamAppender** is the super-class of three other appenders, namely **ConsoleAppender**, **FileAppender** which in turn is the super class of **RollingFileAppender**. The next figure illustrates the class diagram for **OutputStreamAppender** and its subclasses.

![](appenderClassDiagram.jpg)

