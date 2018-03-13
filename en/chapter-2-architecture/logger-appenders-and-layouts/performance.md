### Performance
One of the often-cited arguments against logging is its computational cost. This is a legitimate concern as even moderately-sized applications can generate thousands of log requests. Much of our development effort is spent measuring and tweaking logback's performance. Independently of these efforts, the user should still be aware of the following performance issues.


**1. Logging performance when logging is turned off entirely**

You can turn off logging entirely by setting the level of the root logger to **Level.OFF**, the highest possible level. When logging is turned off entirely, the cost of a log request consists of a method invocation plus an integer comparison. On a 3.2Ghz Pentium D machine this cost is typically around 20 nanoseconds.

However, any method invocation involves the "hidden" cost of parameter construction. For example, for some logger `x` writing,


```java
x.debug("Entry number: " + i + "is " + entry[i]);
```

incurs the cost of constructing the message parameter, i.e. converting both integer `i` and `entry[i]` to a string, and concatenating intermediate strings, regardless of whether the message will be logged or not.

The cost of parameter construction can be quite high and depends on the size of the parameters involved. To avoid the cost of parameter construction you can take advantage of SLF4J's parameterized logging:


```java
x.debug("Entry number: {} is {}", i, entry[i]);
```

This variant will not incur the cost of parameter construction. Compared to the previous call to the **_debug()_** method, it will be faster by a wide margin. The message will be formatted only if the logging request is to be sent to attached appenders. Moreover, the component that formats messages is highly optimized.

Notwithstanding the above placing log statements in tight loops, i.e. very frequently invoked code, is a lose-lose proposal, likely to result in degraded performance. Logging in tight loops will slow down your application even if logging is turned off, and if logging is turned on, will generate massive (and hence useless) output.

**2. The performance of deciding whether to log or not to log when logging is turned on.**

In logback, there is no need to walk the logger hierarchy. A logger knows its effective level (that is, its level, once level inheritance has been taken into consideration) when it is created. Should the level of a parent logger be changed, then all child loggers are contacted to take notice of the change. Thus, before accepting or denying a request based on the effective level, the logger can make a quasi-instantaneous decision, without needing to consult its ancestors.


**3. Actual logging (formatting and writing to the output device)**

This is the cost of formatting the log output and sending it to its target destination. Here again, a serious effort was made to make layouts (formatters) perform as quickly as possible. The same is true for appenders. The typical cost of actually logging is about 9 to 12 microseconds when logging to a file on the local machine. It goes up to several milliseconds when logging to a database on a remote server.

Although feature-rich, one of the foremost design goals of logback was speed of execution, a requirement which is second only to reliability. Some logback components have been rewritten several times to improve performance.