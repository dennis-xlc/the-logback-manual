# Chapter 1: Introduction to logback

> _The morale effects are startling. Enthusiasm jumps when there is a running system, even a simple one. Efforts redouble when the first picture from a new graphics software system appears on the screen, even if it is only a rectangle. One always has, at every stage in the process, a working system. I find that teams can grow much more complex entities in four months than they can build._
**—FREDERICK P. BROOKS, JR., The Mythical Man-Month**


## What is logback?

Logback is intended as a successor to the popular log4j project. It was designed by **Ceki Gülcü**, log4j's founder. It builds upon a decade of experience gained in designing industrial-strength logging systems. The resulting product, i.e. logback, is faster and has a smaller footprint than all existing logging systems, sometimes by a wide margin. Just as importantly, logback offers [unique and rather useful features](https://logback.qos.ch/reasonsToSwitch.html) missing in other logging systems.


## First Baby Step

### Requirements


Logback-classic module requires the presence of _slf4j-api.jar_ and _logback-core.jar_ in addition to _logback-classic.jar_ on the classpath.

The _logback-*.jar_ files are part of the logback distribution whereas _slf4j-api-1.8.0-beta1.jar_ ships with [SLF4J](http://www.slf4j.org/), a separate project.

Let us now begin experimenting with logback.

