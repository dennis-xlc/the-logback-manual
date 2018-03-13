### Viewing status messages

Logback collects its internal status data in a **[StatusManager](https://logback.qos.ch/xref/ch/qos/logback/core/status/StatusManager.html)** object, accessible via the **LoggerContext**.

Given a **StatusManager** you can access all the status data associated with a logback context. To keep memory usage at reasonable levels, the default **StatusManager** implementation stores the status messages in two separate parts: the header part and the tail part. The header part stores the first _H_ status messages whereas the tail part stores the last _T_ messages. At present time _H_=_T_=150, although these values may change in future releases.

Logback-classic ships with a servlet called **ViewStatusMessagesServlet**. This servlet prints the contents of the **StatusManager** associated with the current **LoggerContext** as an HTML table. Here is sample output.

![](lbClassicStatus.jpg)
