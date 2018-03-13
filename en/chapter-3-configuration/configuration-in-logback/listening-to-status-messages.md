### Listening to status messages

You may also attach a **StatusListener** to a **StatusManager** so that you can take immediate action in response to status messages, especially to messages occurring after logback configuration. Registering a status listener is a convenient way to supervise logback's internal state without human intervention.

Logback ships with a **StatusListener** implementation called **[OnConsoleStatusListener](https://logback.qos.ch/xref/ch/qos/logback/core/status/OnConsoleStatusListener.html)** which, as its name indicates, prints all _new_ incoming status messages on the console.

Here is [sample code](https://logback.qos.ch/xref/chapters/configuration/AddStatusListenerApp.html) to register an **OnConsoleStatusListener** instance with the **StatusManager**.


```java
   LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory(); 
   StatusManager statusManager = lc.getStatusManager();
   OnConsoleStatusListener onConsoleListener = new OnConsoleStatusListener();
   statusManager.add(onConsoleListener);
```

Note that the registered status listener will only receive status events subsequent to its registration. It will not receive prior messages. Thus, it is usually a good idea to place status listener registration directives at top of the configuration file before other directives.

It is also possible to register one or more status listeners within a configuration file. Here is an example.

**Example: Registering a status listener** (_logback-examples/src/main/resources/chapters/configuration/onConsoleStatusListener.xml_)


```
<configuration>
  <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />  

  ... the rest of the configuration file  
</configuration>
```


