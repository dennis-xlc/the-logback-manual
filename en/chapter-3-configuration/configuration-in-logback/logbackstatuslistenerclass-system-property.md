### "logback.statusListenerClass" system property

One may also register a status listener by setting the "`logback.statusListenerClass`" Java system property to the name of the listener class you wish to register. For example,


```
java -Dlogback.statusListenerClass=ch.qos.logback.core.status.OnConsoleStatusListener ...
```


Logback ships with several status listener implementations. **[OnConsoleStatusListener](https://logback.qos.ch/xref/ch/qos/logback/core/status/OnConsoleStatusListener.html)** prints incoming status messages on the console, i.e. on `System.out`. **[OnErrorConsoleStatusListener](https://logback.qos.ch/xref/ch/qos/logback/core/status/OnErrorConsoleStatusListener.html)** prints incoming status messages on `System.err`. **[NopStatusListener](https://logback.qos.ch/xref/ch/qos/logback/core/status/NopStatusListener.html)** drops incoming status messages.

Note that [automatic status printing](https://logback.qos.ch/manual/configuration.html#automaticStatusPrinting) (in case of errors) is disabled if any status listener is registered during configuration and in particular if the user specifies a status listener via the "`logback.statusListenerClass`" system. Thus, by setting **NopStatusListener** as a status listener, you can silence internal status printing altogether.


```
java -Dlogback.statusListenerClass=ch.qos.logback.core.status.NopStatusListener ...
```




