### Stopping logback-classic

In order to release the resources used by logback-classic, it is always a good idea to stop the logback context. Stopping the context will close all appenders attached to loggers defined by the context and stop any active threads in an orderly way. Please also read the section on "shutdown hooks" just below.


```
import org.sflf4j.LoggerFactory;
import ch.qos.logback.classic.LoggerContext;
...

// assume SLF4J is bound to logback-classic in the current environment
LoggerContext loggerContext = (LoggerContext) LoggerFactory.getILoggerFactory();
loggerContext.stop();
```

In web-applications the above code could be invoked from within the [contextDestroyed](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContextListener.html#contextDestroyed(javax.servlet.ServletContextEvent)) method of **ServletContextListener** in order to stop logback-classic and release resources. Starting with version 1.1.10, the appropriate **ServletContextListener** is installed automatically for you (see just below).


#### Stopping logback-classic via a shutdown hook

Installing a JVM shutdown hook is a convenient way for shutting down logback and releasing associated resources.


```
<configuration debug="true">
   <!-- in the absence of the class attribute, assume 
   ch.qos.logback.core.hook.DefaultShutdownHook -->
   <shutdownHook/>
  .... 
</configuration>
```

Note that you may install a shutdown hook of your own making by setting the `class` attribute to correspond to your shutdown hook's class name.

The default shutdown hook, namely **[DefaultShutdownHook](https://logback.qos.ch/apidocs/ch/qos/logback/core/hook/DefaultShutdownHook.html)**, will **stop** the logback context after a specified delay (0 by default). Stopping the context will allow up to 30 seconds for any log file compression tasks running in the background to finish. In standalone Java applications, adding a `<shutdownHook/>` directive to your configuration file is an easy way to ensure that any ongoing compression tasks are allowed to finish before JVM exit. In applications within a Web server, [webShutdownHook](https://logback.qos.ch/manual/configuration.html#webShutdownHook) will be installed automatically making `<shutdownHook/>` directive quite redundant and unnecessary.


#### WebShutdownHook or stopping logback-classic in web-applications

`SINCE 1.1.10` Logback-classic will **automatically** ask the web-server to install a **[LogbackServletContainerInitializer](https://logback.qos.ch/apidocs/ch/qos/logback/classic/servlet/LogbackServletContainerInitializer.html)** implementing the **ServletContainerInitializer** interface (available in servlet-api 3.x and later). This initializer will in turn install and instance of **[LogbackServletContextListener](https://logback.qos.ch/apidocs/ch/qos/logback/classic/servlet/LogbackServletContextListener.html)**. This listener will stop the current logback-classic context when the web-app is stopped or reloaded.

You may disable the automatic the installation of **LogbackServletContextListener** by setting a `<context-param>` named `logbackDisableServletContainerInitializer` in your web-application's web.xml file. Here is the relevant snippet.


```
<web-app>
    <context-param>
        <param-name>logbackDisableServletContainerInitializer</param-name>
        <param-value>true</param-value>
    </context-param>
    .... 
</web-app>
```

Note that `logbackDisableServletContainerInitializer` variable can also be set as a Java system property an OS environment variable. The most local setting has priority, i.e. web-app first, system property second and OS environment last.






