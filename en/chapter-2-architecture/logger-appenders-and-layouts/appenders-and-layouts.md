### Appenders and Layouts

The ability to selectively enable or disable logging requests based on their logger is only part of the picture. Logback allows logging requests to print to multiple destinations. In logback speak, an output destination is called an appender. Currently, appenders exist for the console, files, remote socket servers, to MySQL, PostgreSQL, Oracle and other databases, JMS, and remote UNIX Syslog daemons.

More than one appender can be attached to a logger.

The _**addAppender**_ method adds an appender to a given logger. Each enabled logging request for a given logger will be forwarded to all the appenders in that logger as well as the appenders higher in the hierarchy. In other words, appenders are inherited additively from the logger hierarchy. For example, if a console appender is added to the root logger, then all enabled logging requests will at least print on the console. If in addition a file appender is added to a logger, say _**L**_, then enabled logging requests for _**L**_ and _**L**_'s children will print on a file _and_ on the console. It is possible to override this default behavior so that appender accumulation is no longer additive by setting the additivity flag of a logger to false.

The rules governing appender additivity are summarized below.

> **Appender Additivity**  
> _The output of a log statement of logger **L** will go to all the appenders in **L** and its ancestors. This is the meaning of the term "appender additivity"._
>
> _However, if an ancestor of logger **L**, say **P**, has the additivity flag set to false, then **L**'s output will be directed to all the appenders in **L** and its ancestors up to and including **P** but not the appenders in any of the ancestors of **P**._
>
> _Loggers have their additivity flag set to true by default._

The table below shows an example:

| Logger Name | Attached Appenders | Additivity Flag | Output Targets | Comment |
| :--- | :--- | :--- | :--- | :--- |
| root | A1 | not applicable | A1 | Since the root logger stands at the top of the logger hierarchy, the additivity flag does not apply to it. |
| x | A-x1, A-x2 | true | A1, A-x1, A-x2 | Appenders of "x" and of root. |
| x.y | none | true | A1, A-x1, A-x2 | Appenders of "x" and of root. |
| x.y.z | A-xyz1 | true | A1, A-x1, A-x2, A-xyz1 | Appenders of "x.y.z", "x" and of root. |
| security | A-sec | false | A-sec | No appender accumulation since the additivity flag is set to false. Only appender A-sec will be used. |
| security.access | none | true | A-sec | Only appenders of "security" because the additivity flag in "security" is set to false. |

More often than not, users wish to customize not only the output destination but also the output format. This is accomplished by associating a _layout_ with an appender. The layout is responsible for formatting the logging request according to the user's wishes, whereas an appender takes care of sending the formatted output to its destination. The **PatternLayout**, part of the standard logback distribution, lets the user specify the output format according to conversion patterns similar to the C language _**printf**_ function.

For example, the **PatternLayout** with the conversion pattern "**%-4relative \[%thread\] %-5level %logger{32} - %msg%n**" will output something akin to:

```
176  [main] DEBUG manual.architecture.HelloWorld2 - Hello world.
```

The first field is the number of milliseconds elapsed since the start of the program. The second field is the thread making the log request. The third field is the level of the log request. The fourth field is the name of the logger associated with the log request. The text after the '-' is the message of the request.

