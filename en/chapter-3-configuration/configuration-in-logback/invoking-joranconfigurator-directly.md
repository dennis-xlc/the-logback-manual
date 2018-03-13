### Invoking _JoranConfigurator_ directly

Logback relies on a configuration library called Joran, part of logback-core. Logback's default configuration mechanism invokes **JoranConfigurator** on the default configuration file it finds on the class path. If you wish to override logback's default configuration mechanism for whatever reason, you can do so by invoking **JoranConfigurator** directly. The next application, `MyApp3`, invokes **JoranConfigurator** on a configuration file passed as a parameter.

**Example: Invoking JoranConfigurator directly** (_[logback-examples/src/main/java/chapters/configuration/MyApp3.java](https://logback.qos.ch/xref/chapters/configuration/MyApp3.html)_)


```java
package chapters.configuration;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.classic.joran.JoranConfigurator;
import ch.qos.logback.core.joran.spi.JoranException;
import ch.qos.logback.core.util.StatusPrinter;

public class MyApp3 {
  final static Logger logger = LoggerFactory.getLogger(MyApp3.class);

  public static void main(String[] args) {
    // assume SLF4J is bound to logback in the current environment
    LoggerContext context = (LoggerContext) LoggerFactory.getILoggerFactory();
    
    try {
      JoranConfigurator configurator = new JoranConfigurator();
      configurator.setContext(context);
      // Call context.reset() to clear any previous configuration, e.g. default 
      // configuration. For multi-step configuration, omit calling context.reset().
      context.reset(); 
      configurator.doConfigure(args[0]);
    } catch (JoranException je) {
      // StatusPrinter will handle this
    }
    StatusPrinter.printInCaseOfErrorsOrWarnings(context);

    logger.info("Entering application.");

    Foo foo = new Foo();
    foo.doIt();
    logger.info("Exiting application.");
  }
}
```

This application fetches the **LoggerContext** currently in effect, creates a new **JoranConfigurator**, sets the context on which it will operate, resets the logger context, and then finally asks the configurator to configure the context using the configuration file passed as a parameter to the application. Internal status data is printed in case of warnings or errors. Note that for multi-step configuration, **_context.reset()_** invocation should be omitted.

