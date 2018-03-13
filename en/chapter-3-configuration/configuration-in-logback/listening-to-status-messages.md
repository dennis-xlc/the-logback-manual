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

