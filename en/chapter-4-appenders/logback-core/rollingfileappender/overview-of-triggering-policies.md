#### Overview of triggering policies

**[TriggeringPolicy](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/TriggeringPolicy.html)** implementations are responsible for instructing the **RollingFileAppender** when to rollover.

The **TriggeringPolicy** interface contains only one method.


```
package ch.qos.logback.core.rolling;

import java.io.File;
import ch.qos.logback.core.spi.LifeCycle;

public interface TriggeringPolicy<E> extends LifeCycle {

  public boolean isTriggeringEvent(final File activeFile, final <E> event);
}
```

The **_isTriggeringEvent()_** method takes as parameters the active file and the logging event currently being processed. The concrete implementation determines whether the rollover should occur or not, based on these parameters.

The most widely-used triggering policy, namely **TimeBasedRollingPolicy** which also doubles as a rolling policy, was already discussed earlier along with other rolling policies.

