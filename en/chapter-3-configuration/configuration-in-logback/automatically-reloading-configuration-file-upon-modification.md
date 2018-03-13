### Automatically reloading configuration file upon modification

If instructed to do so, logback-classic will scan for changes in its configuration file and automatically reconfigure itself when the configuration file changes. In order to instruct logback-classic to scan for changes in its configuration file and to automatically re-configure itself set the `scan` attribute of the `<configuration>` element to true, as shown next.

**Example: Scanning for changes in configuration file and automatic re-configuration** (_logback-examples/src/main/resources/chapters/configuration/scan1.xml_)


```
<configuration scan="true"> 
  ... 
</configuration> 
```

By default, the configuration file will be scanned for changes once every minute. You can specify a different scanning period by setting the `scanPeriod` attribute of the `<configuration>` element. Values can be specified in units of milliseconds, seconds, minutes or hours. Here is an example:

**Example: Specifying a different scanning period** (_logback-examples/src/main/resources/chapters/configuration/scan2.xml_)


```
<configuration scan="true" scanPeriod="30 seconds" > 
  ...
</configuration> 
```

`NOTE` If no unit of time is specified, then the unit of time is assumed to be milliseconds, which is usually inappropriate. If you change the default scanning period, do not forget to specify a time unit.

Behind the scenes, when you set the `scan` attribute to `true`, a **[ReconfigureOnChangeTask](https://logback.qos.ch/xref/ch/qos/logback/classic/joran/ReconfigureOnChangeTask.html)** will be installed. This task run in a separate thread and will check whether your configuration file has changed. **ReconfigureOnChangeTask** will automatically watch for any included files as well.

As it is easy to make errors while editing a configuration file, in case the latest version of the configuration file has XML syntax errors, it will fall back to a previous configuration file free of XML syntax errors.

#### Enabling packaging data in stack traces

`NOTE` As of version 1.1.4, packaging data is disabled by default.

If instructed to do so, logback can include packaging data for each line of the stack trace lines it outputs. Packaging data consists of the name and version of the jar file whence the class of the stack trace line originated. Packaging data can be very useful in identifying software versioning issues. However, it is rather expensive to compute, especially in application where exceptions are thrown frequently. Here is a sample output:


```
14:28:48.835 [btpool0-7] INFO  c.q.l.demo.prime.PrimeAction - 99 is not a valid value
java.lang.Exception: 99 is invalid
  at ch.qos.logback.demo.prime.PrimeAction.execute(PrimeAction.java:28) [classes/:na]
  at org.apache.struts.action.RequestProcessor.processActionPerform(RequestProcessor.java:431) [struts-1.2.9.jar:1.2.9]
  at org.apache.struts.action.RequestProcessor.process(RequestProcessor.java:236) [struts-1.2.9.jar:1.2.9]
  at org.apache.struts.action.ActionServlet.doPost(ActionServlet.java:432) [struts-1.2.9.jar:1.2.9]
  at javax.servlet.http.HttpServlet.service(HttpServlet.java:820) [servlet-api-2.5-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.servlet.ServletHolder.handle(ServletHolder.java:502) [jetty-6.1.12.jar:6.1.12]
  at ch.qos.logback.demo.UserServletFilter.doFilter(UserServletFilter.java:44) [classes/:na]
  at org.mortbay.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1115) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.servlet.ServletHandler.handle(ServletHandler.java:361) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.webapp.WebAppContext.handle(WebAppContext.java:417) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.handler.ContextHandlerCollection.handle(ContextHandlerCollection.java:230) [jetty-6.1.12.jar:6.1.12]
```

Packaging data is disabled by default but can be enabled by configuration:

```
<configuration packagingData="true">
  ...
</configuration>
```

Alternatively, packaging data can be enabled/disabled programmatically by invoking the **_[setPackagingDataEnabled(boolean)](https://logback.qos.ch/apidocs/ch/qos/logback/classic/LoggerContext.html#setPackagingDataEnabled(boolean))_** method in **LoggerContext**, as shown next:


```
LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
lc.setPackagingDataEnabled(true);
```




