### Printing methods and the basic selection rule

By definition, the printing method determines the level of a logging request. For example, if **L** is a logger instance, then the statement _**L.info\(".."\)**_ is a logging statement of level **INFO**.

A logging request is said to be _enabled_ if its level is higher than or equal to the effective level of its logger. Otherwise, the request is said to be _disabled_. As described previously, a logger without an assigned level will inherit one from its nearest ancestor. This rule is summarized below.

> **Basic Selection Rule**  
> _A log request of level p issued to a logger having an effective level q, is enabled if p &gt;= q._

This rule is at the heart of logback. It assumes that levels are ordered as follows: **TRACE** &lt; **DEBUG** &lt; **INFO** &lt;  **WARN** &lt; **ERROR**.

In a more graphic way, here is how the selection rule works. In the following table, the vertical header shows the level of the logging request, designated by _**p**_, while the horizontal header shows effective level of the logger, designated by _**q**_. The intersection of the rows \(level request\) and columns \(effective level\) is the boolean resulting from the basic selection rule.

|  | q=TRACE | q=DEBUG | q=INFO | q=WARN | q=ERROR | q=OFF |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **p=TRACE** | YES | **NO** | **NO** | **NO** | **NO** | **NO** |
| **p=DEBUG** | YES | YES | **NO** | **NO** | **NO** | **NO** |
| **p=INFO** | YES | YES | YES | **NO** | **NO** | **NO** |
| **p=WARN** | YES | YES | YES | YES | **NO** | **NO** |
| **p=ERROR** | YES | YES | YES | YES | YES | **NO** |

Here is an example of the basic selection rule.

```java
import ch.qos.logback.classic.Level;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
....

// get a logger instance named "com.foo". Let us further assume that the
// logger is of type  ch.qos.logback.classic.Logger so that we can
// set its level
ch.qos.logback.classic.Logger logger = 
        (ch.qos.logback.classic.Logger) LoggerFactory.getLogger("com.foo");
//set its Level to INFO. The setLevel() method requires a logback logger
logger.setLevel(Level. INFO);

Logger barlogger = LoggerFactory.getLogger("com.foo.Bar");

// This request is enabled, because WARN >= INFO
logger.warn("Low fuel level.");

// This request is disabled, because DEBUG < INFO. 
logger.debug("Starting search for nearest gas station.");

// The logger instance barlogger, named "com.foo.Bar", 
// will inherit its level from the logger named 
// "com.foo" Thus, the following request is enabled 
// because INFO >= INFO. 
barlogger.info("Located nearest gas station.");

// This request is disabled, because DEBUG < INFO. 
barlogger.debug("Exiting gas station search");
```

