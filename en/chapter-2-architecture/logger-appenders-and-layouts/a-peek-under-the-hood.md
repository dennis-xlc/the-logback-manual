### A peek under the hood

After we have introduced the essential logback components, we are now ready to describe the steps that the logback framework takes when the user invokes a logger's printing method. Let us now analyze the steps logback takes when the user invokes the **_info()_** method of a logger named _com.wombat_.

**1. Get the filter chain decision**

If it exists, the **TurboFilter** chain is invoked. Turbo filters can set a context-wide threshold, or filter out certain events based on information such as **Marker**, **Level**, **Logger**, message, or the **Throwable** that are associated with each logging request. If the reply of the filter chain is **FilterReply.DENY**, then the logging request is dropped. If it is **FilterReply.NEUTRAL**, then we continue with the next step, i.e. step 2. In case the reply is **FilterReply.ACCEPT**, we skip the next and directly jump to step 3.


**2. Apply the basic selection rule**

At this step, logback compares the effective level of the logger with the level of the request. If the logging request is disabled according to this test, then logback will drop the request without further processing. Otherwise, it proceeds to the next step.

**3. Create a _LoggingEvent_ object**

If the request survived the previous filters, logback will create a **ch.qos.logback.classic.LoggingEvent** object containing all the relevant parameters of the request, such as the logger of the request, the request level, the message itself, the exception that might have been passed along with the request, the current time, the current thread, various data about the class that issued the logging request and the `MDC`. Note that some of these fields are initialized lazily, that is only when they are actually needed. The `MDC` is used to decorate the logging request with additional contextual information. `MDC` is discussed in a subsequent chapter.

**4. Invoking appenders**

After the creation of a **LoggingEvent** object, logback will invoke the **_doAppend()_** methods of all the applicable appenders, that is, the appenders inherited from the logger context.

All appenders shipped with the logback distribution extend the **AppenderBase** abstract class that implements the **_doAppend_** method in a synchronized block ensuring thread-safety. The **_doAppend()_** method of **AppenderBase** also invokes custom filters attached to the appender, if any such filters exist. Custom filters, which can be dynamically attached to any appender, are presented in a separate chapter.


**5. Formatting the output**

It is responsibility of the invoked appender to format the logging event. However, some (but not all) appenders delegate the task of formatting the logging event to a layout. A layout formats the **LoggingEvent** instance and returns the result as a **String**. Note that some appenders, such as the **SocketAppender**, do not transform the logging event into a string but serialize it instead. Consequently, they do not have nor require a layout.

**6. Sending out the _LoggingEvent_**

After the logging event is fully formatted it is sent to its destination by each appender.

Here is a sequence UML diagram to show how everything works. You might want to click on the image to display its bigger version.


![](underTheHoodSequence2.gif)
