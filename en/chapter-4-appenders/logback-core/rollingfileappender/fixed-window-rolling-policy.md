#### Fixed Window Rolling Policy

When rolling over, **[FixedWindowRollingPolicy](https://logback.qos.ch/xref/ch/qos/logback/core/rolling/FixedWindowRollingPolicy.html)** renames files according to a fixed window algorithm as described below.

The `fileNamePattern` option represents the file name pattern for the archived (rolled over) log files. This option is required and must include an integer token _%i_ somewhere within the pattern.

Here are the available properties for **FixedWindowRollingPolicy**