## AppenderBase

The **ch.qos.logback.core.AppenderBase** class is an abstract class implementing the **Appender** interface. It provides basic functionality shared by all appenders, such as methods for getting or setting their name, their activation status, their layout and their filters. It is the super-class of all appenders shipped with logback. Although an abstract class, **AppenderBase** actually implements the **_doAppend()_** method in the **Append** interface. Perhaps the clearest way to discuss **AppenderBase** class is by presenting an excerpt of actual source code.


```java
public synchronized void doAppend(E eventObject) {

  // prevent re-entry.
  if (guard) {
    return;
  }

  try {
    guard = true;

    if (!this.started) {
      if (statusRepeatCount++ < ALLOWED_REPEATS) {
        addStatus(new WarnStatus(
            "Attempted to append to non started appender [" + name + "].",this));
      }
      return;
    }

    if (getFilterChainDecision(eventObject) == FilterReply.DENY) {
      return;
    }
    
    // ok, we now invoke the derived class's implementation of append
    this.append(eventObject);

  } finally {
    guard = false;
  }
}
```

This implementation of the **_doAppend()_** method is synchronized. It follows that logging to the same appender from different threads is safe. While a thread, say **_T_**, is executing the **_doAppend()_** method, subsequent calls by other threads are queued until **_T_** leaves the **_doAppend()_** method, ensuring **_T_**'s exclusive access to the appender.

Since such synchronization is not always appropriate, logback ships with **[ch.qos.logback.core.UnsynchronizedAppenderBase](https://logback.qos.ch/xref/ch/qos/logback/core/UnsynchronizedAppenderBase.html)** which is very similar to the **[AppenderBase](https://logback.qos.ch/xref/ch/qos/logback/core/AppenderBase.html)** class. For the sake of conciseness, we will be discussing **UnsynchronizedAppenderBase** in the remainder of this document.

The first thing the **_doAppend()_** method does is to check whether the `guard` is set to true. If it is, it immediately exits. If the `guard` is not set, it is set to true at the next statement. The `guard` ensures that the **_doAppend()_** method will not recursively call itself. Just imagine that a component, called somewhere beyond the **_append()_** method, wants to log something. Its call could be directed to the very same appender that just called it resulting in an infinite loop and a stack overflow.

In the following statement we check whether the `started` field is true. If it is not, **_doAppend()_** will send a warning message and return. In other words, once an appender is closed, it is impossible to write to it. **Appender** objects implement the **LifeCycle** interface, which implies that they implement **_start()_**, **_stop()_** and **_isStarted()_** methods. After setting all the properties of an appender, Joran, logback's configuration framework, calls the **_start()_** method to signal the appender to activate its properties. Depending on its kind, an appender may fail to start if certain properties are missing or because of interference between various properties. For example, given that file creation depends on truncation mode, **FileAppender** cannot act on the value of its **File** option until the value of the **Append** option is also known with certainty. The explicit activation step ensures that an appender acts on its properties _after_ their values become known.

If the appender could not be started or if it has been stopped, a warning message will be issued through logback's internal status management system. After several attempts, in order to avoid flooding the internal status system with copies of the same warning message, the **_doAppend()_** method will stop issuing these warnings.

The next `if` statement checks the result of the attached filters. Depending on the decision resulting from the filter chain, events can be denied or explicitly accepted. In the absence of a decision by the filter chain, events are accepted by default.

The **_doAppend()_** method then invokes the derived classes' implementation of the **_append()_** method. This method does the actual work of appending the event to the appropriate device.

Finally, the `guard` is released so as to allow a subsequent invocation of the **_doAppend()_** method.

For the remainder of this manual, we reserve the term "option" or alternatively "property" for any attribute that is inferred dynamically using JavaBeans introspection through setter and getter methods.