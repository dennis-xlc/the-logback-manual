## Logback's architecture

Logback's basic architecture is sufficiently generic so as to apply under different circumstances. At the present time, logback is divided into three modules, `logback-core`, `logback-classic` and `logback-access`.

The _core_  module lays the groundwork for the other two modules. The _classic_ module extends _core_. The _classic_ module corresponds to a significantly improved version of `log4j`. Logback-classic natively implements the [SLF4J API](http://www.slf4j.org/) so that you can readily switch back and forth between logback and other logging systems such as `log4j` or `java.util.logging` \(JUL\) introduced in JDK 1.4. The third module called _access_ integrates with Servlet containers to provide HTTP-access log functionality. A separate document covers [access module documentation](https://logback.qos.ch/access.html).

In the remainder of this document, we will write "logback" to refer to the logback-classic module.

