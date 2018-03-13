## Configuration in logback

Inserting log requests into the application code requires a fair amount of planning and effort. Observation shows that approximately four percent of code is dedicated to logging. Consequently, even a moderately sized application will contain thousands of logging statements embedded within its code. Given their number, we need tools to manage these log statements.

Logback can be configured either programmatically or with a configuration script expressed in XML or Groovy format. By the way, existing log4j users can convert their _log4j.properties_ files to _logback.xml_ using our [PropertiesTranslator](http://logback.qos.ch/translator/) web-application.

Let us begin by discussing the initialization steps that logback follows to try to configure itself:

1. Logback tries to find a file called _logback-test.xml_ [in the classpath](https://logback.qos.ch/faq.html#configFileLocation).

2. If no such file is found, logback tries to find a file called _logback.groovy_ [in the classpath](https://logback.qos.ch/faq.html#configFileLocation).

3. If no such file is found, it checks for the file _logback.xml_ [in the classpath](https://logback.qos.ch/faq.html#configFileLocation).

4. If no such file is found, [service-provider loading facility](http://docs.oracle.com/javase/6/docs/api/java/util/ServiceLoader.html) (introduced in JDK 1.6) is used to resolve the implementation of **com.qos.logback.classic.spi.Configurator** interface by looking up the file _**META-INF\services\ch.qos.logback.classic.spi.Configurator**_ in the class path. Its contents should specify the fully qualified class name of the desired **Configurator** implementation.

5. If none of the above succeeds, logback configures itself automatically using the **[BasicConfigurator](https://logback.qos.ch/xref/ch/qos/logback/classic/BasicConfigurator.html)** which will cause logging output to be directed to the console.

The last step is meant as last-ditch effort to provide a default (but very basic) logging functionality in the absence of a configuration file.

If you are using Maven and if you place the _logback-test.xml_ under the _src/test/resources_ folder, Maven will ensure that it won't be included in the artifact produced. Thus, you can use a different configuration file, namely _logback-test.xml_ during testing, and another file, namely, _logback.xml_, in production.

`FAST START-UP` It takes about 100 miliseconds for Joran to parse a given logback configuration file. To shave off those miliseconds at aplication start up, you can use the service-provider loading facility (item 4 above) to load your own custom **Configurator** class with **[BasicConfigrator](https://logback.qos.ch/xref/ch/qos/logback/classic/BasicConfigurator.html)** serving as a good starting point.