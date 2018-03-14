#### Overview of rolling policies

**[RollingPolicy](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/RollingPolicy.html)** is responsible for the rollover procedure which involves file moving and renaming.

The **RollingPolicy** interface is presented below:


```java
package ch.qos.logback.core.rolling;  

import ch.qos.logback.core.FileAppender;
import ch.qos.logback.core.spi.LifeCycle;

public interface RollingPolicy extends LifeCycle {

  public void rollover() throws RolloverFailure;
  public String getActiveFileName();
  public CompressionMode getCompressionMode();
  public void setParent(FileAppender appender);
}
```

The **_rollover_** method accomplishes the work involved in archiving the current log file. The **_getActiveFileName()_** method is called to compute the file name of the current log file (where live logs are written to). As indicated by **_getCompressionMode_** method a **RollingPolic**y is also responsible for determining the compression mode. Lastly, a **RollingPolicy** is given a reference to its parent via the **_setParent_** method.







