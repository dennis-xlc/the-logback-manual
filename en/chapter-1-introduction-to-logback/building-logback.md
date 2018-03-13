## Building logback

As its build tool, logback relies on [Maven](http://maven.apache.org/), a widely-used open-source build tool.

Once you have installed Maven, building the logback project, including all its modules, should be as easy as issuing a`mvn install`command from within the directory where you unarchived the logback distribution. Maven will automatically download the required external libraries.

Logback distributions contain complete source code such that you can modify parts of logback library and build your own version of it. You may even redistribute the modified version, as long as you adhere to the conditions of the LGPL license or the EPL license.

For building logback under an IDE, please see the[relevant section on the class path setup page](https://logback.qos.ch/setup.html#ide).



