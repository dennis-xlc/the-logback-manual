### Specifying the location of the default configuration file as a system property

You may specify the location of the default configuration file with a system property named "`logback.configurationFile`". The value of this property can be a URL, a resource on the class path or a path to a file external to the application.


```
java -Dlogback.configurationFile=/path/to/config.xml chapters.configuration.MyApp1
```

Note that the file extension must be ".xml" or ".groovy". Other extensions are ignored. Explicitly registering a status listener may help debugging issues locating the configuration file.

Given that "`logback.configurationFile`" is a Java system property, it may be set within your application as well. However, the system property must be set before any logger instance is created.


```java
import ch.qos.logback.classic.util.ContextInitializer;

public class ServerMain {
    public static void main(String args[]) throws IOException, InterruptedException {
       // must be set before the first call to  LoggerFactory.getLogger();
       // ContextInitializer.CONFIG_FILE_PROPERTY is set to "logback.configurationFile"
       System.setProperty(ContextInitializer.CONFIG_FILE_PROPERTY, /path/to/config.xml);
       ...
    }   
}
```






