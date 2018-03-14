#### Fixed Window Rolling Policy

When rolling over, **[FixedWindowRollingPolicy](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/FixedWindowRollingPolicy.html)** renames files according to a fixed window algorithm as described below.

The `fileNamePattern` option represents the file name pattern for the archived (rolled over) log files. This option is required and must include an integer token _%i_ somewhere within the pattern.

Here are the available properties for **FixedWindowRollingPolicy**

| Property Name | Type | Description |
| :--- | :--- | :--- |
| `minIndex` | int | This option represents the lower bound for the window's index. |
| `maxIndex` | int | This option represents the upper bound for the window's index. |
| `fileNamePattern`  | String | This option represents the pattern that will be followed by the **FixedWindowRollingPolicy** when renaming the log files. It must contain the string _%i_, which will indicate the position where the value of the current window index will be inserted.<br><br>For example, using _MyLogFile%i.log_ associated with minimum and maximum values of _1_ and _3_ will produce archive files named _MyLogFile1.log_, _MyLogFile2.log_ and _MyLogFile3.log_.<br><br>Note that file compression is also specified via this property. For example, `fileNamePattern` set to _MyLogFile%i.log.zip_ means that archived files must be compressed using the _zip_ format; _gz_ format is also supported. |

Given that the fixed window rolling policy requires as many file renaming operations as the window size, large window sizes are strongly discouraged. When large values are specified by the user, the current implementation will automatically reduce the window size to 20.

Let us go over a more concrete example of the fixed window rollover policy. Suppose that `minIndex` is set to 1, `maxIndex` set to 3, `fileNamePattern` property set to _foo%i.log_, and that `file` property is set to _foo.log_.

| Number of rollovers | Active output target | Archived log files | Description |
| :--- | :--- | :--- | :--- |
| 0 | foo.log | - | No rollover has happened yet, logback logs into the initial file. |
| 1 | foo.log | foo1.log | First rollover. _foo.log_ is renamed as _foo1.log_. A new _foo.log_ file is created and becomes the active output target. |
| 2 | foo.log | foo1.log,<br>foo2.log | Second rollover. _foo1.log_ is renamed as _foo2.log_. _foo.log_ is renamed as _foo1.log_. A new _foo.log_ file is created and becomes the active output target. |
| 3 | foo.log | foo1.log,<br>foo2.log,<br>foo3.log | Third rollover. _foo2.log_ is renamed as _foo3.log_. _foo1.log_ is renamed as _foo2.log_. _foo.log_ is renamed as _foo1.log_. A new _foo.log_ file is created and becomes the active output target. |
| 4 | foo.log | foo1.log,<br>foo2.log,<br>foo3.log  | In this and subsequent rounds, the rollover begins by deleting _foo3.log_. Other files are renamed by incrementing their index as shown in previous steps. In this and subsequent rollovers, there will be three archive logs and one active log file. |


