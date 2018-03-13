### Variable substitution

`NOTE` Earlier versions of this document used the term "`property substitution`" instead of the term "`variable`". Please consider both terms interchangeable although the latter term conveys a clearer meaning.

As in many scripting languages, logback configuration files support definition and substitution of variables. Variables have a _**scope**_ (see below). Moreover, variables can be defined within the configuration file itself, in an external file, in an external resource or even computed and defined on the fly.

Variable substitution can occur at any point in a configuration file where a value can be specified. The syntax of variable substitution is similar to that of Unix shells. The string between an opening _${_ and closing _}_ is interpreted as a reference to the _value_ of the property. For property `aName`, the string "`${aName}`" will be replaced with the value held by the `aName` property.

As they often come in handy, the `HOSTNAME` and `CONTEXT_NAME` variables are automatically defined and have context scope. Given that in some environments it may take some time to compute the hostname, its value is computed lazily (only when needed). Moreover, `HOSTNAME` can be set from within the configuration directly.

#### Defining variables

Variables can be defined one at a time in the configuration file itself or loaded wholesale from an external properties file or an external resource. For historical reasons, the XML element for defining variables is `<property>` although in logback 1.0.7 and later the element `<variable>` can be used interchangeably.

The next example shows a variable declared at the beginning of the configuration file. It is then used further down the file to specify the location of the output file.