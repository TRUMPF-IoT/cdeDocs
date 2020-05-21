## Internet Information Services (IIS) Failed Request Tracing
Requests to an IIS site that result in errors can be tracked using a mechanism called "Failed Request Tracing Rules" in IIS.  This enables IIS to write an .xml file with information about an error every time one occurs.

To turn on this feature, first ensure that the Windows feature *Tracing* is checked under *Internet Information Services -> World Wide Web Services -> Health and Diagnostics*.  This can be found by opening the *Windows Features* window.

Next, open your website in IIS, and click *Failed Request Tracing Rules*.  Add a new rule and follow the directions in the dialog.  A recommended rule is tracing for "All Content (*)" with Status Codes of 400-600, an Event Severity of "Error", and the verbosity of all providers set to "Warnings".  This will ensure that only errors are logged, and the .xml files created do not contain an excess of information.

Note that all failed request trace log files are stored by default in `%SystemDrive%\inetpub\logs\FailedReqLogFiles`.  You can change this by clicking "Edit Site Tracing..." and choose a new directory, along with the maximum number of trace files.  Make sure that the *Enable* box is checked in order for tracing to occur.

## Logging with CDMyLogger
Once *Failed Request Tracing* is enabled, you can improve your debugging by installing the *CDMyLogger* plugin on your C-DEngine node.  Create a new Logger of type "IIS Logger" in the plugin and connect to it.  Fill in the "Path to IIS Trace Logs Folder" text box with the name of the folder where the trace log files are stored.  Note that the "Disable Write to SYSLOG" control should be turned off as well.  Note that an IIS Logger can also be created with a .cdeconfig file rather than using the NMI.

To test the logging capabilities, make a couple browser requests to your node, and choose a client certificate that is not trusted.  You should see new .xml files appear in the trace log directory, as well as errors in your C-DEngine node's system log.

The IIS Logger parses these .xml files and provides only the relevant information, such as the status code, request URL, time taken, HTTP verb, and error message.  The client IP address will also be shown if the verbosity of the trace rule is set higher, such as *General* or *Verbose*.

In addition, every time a failed request occurs that is handled by IIS, the C-DEngine KPI *IISRejectedClientConnections* will be incremented.  This number can be exposed through the Prometheus plugin and visualized in Grafana.

**NOTE**:  The IIS Logger does not currently have a throttling or queueing mechanism for log entries.  If a site receives a high number of failed requests, all of these will be written through to the system log, which may strain resources.  If necessary, disable the IIS Logger's logging capability by turning on "Disable Write to SYSLOG" on the IIS Logger.