### Effective Level aka Level Inheritance

Loggers may be assigned levels. The set of possible levels \(_**TRACE, DEBUG, INFO, WARN and ERROR**_\) are defined in the **ch.qos.logback.classic.Level** class. Note that in logback, the **Level** class is final and cannot be sub-classed, as a much more flexible approach exists in the form of **Marker** objects.

If a given logger is not assigned a level, then it inherits one from its closest ancestor with an assigned level. More formally:

> The effective level for a given logger _L_, is equal to the first non-null level in its hierarchy, starting at \_L \_itself and proceeding upwards in the hierarchy towards the root logger.

To ensure that all loggers can eventually inherit a level, the root logger always has an assigned level. By default, this level is DEBUG.

Below are four examples with various assigned level values and the resulting effective \(inherited\) levels according to the level inheritance rule.

_Example 1_

| Logger name | Assigned level | Effective level |
| :--- | :--- | :--- |
| root | DEBUG | DEBUG |
| X | none | DEBUG |
| X.Y | none | DEBUG |
| X.Y.Z | none | DEBUG |

In example 1 above, only the root logger is assigned a level. This level value, **DEBUG**, is inherited by the other loggers `X`, `X.Y` and `X.Y.Z`

_Example 2_

| Logger name | Assigned level | Effective level |
| :--- | :--- | :--- |
| root | ERROR | ERROR |
| X | INFO | INFO |
| X.Y | DEBUG | DEBUG |
| X.Y.Z | WARN | WARN |

In example 2 above, all loggers have an assigned level value. Level inheritance does not come into play.

_Example 3_

| Logger name | Assigned level | Effective level |
| :--- | :--- | :--- |
| root | DEBUG | DEBUG |
| X | INFO | INFO |
| X.Y | none | INFO |
| X.Y.Z | ERROR | ERROR |

In example 3 above, the loggers `root`, `X` and `X.Y.Z` are assigned the levels **DEBUG**, **INFO** and **ERROR** respectively. Logger `X.Y` inherits its level value from its parent `X`.

_Example 4_

| Logger name | Assigned level | Effective level |
| :--- | :--- | :--- |
| root | DEBUG | DEBUG |
| X | INFO | INFO |
| X.Y | none | INFO |
| X.Y.Z | none | INFO |

In example 4 above, the loggers `root` and `X` and are assigned the levels **DEBUG** and **INFO** respectively. The loggers `X.Y` and `X.Y.Z` inherit their level value from their nearest parent `X`, which has an assigned level.

