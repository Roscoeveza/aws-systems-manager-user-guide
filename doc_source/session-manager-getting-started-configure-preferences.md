# Step 4: Configure session preferences<a name="session-manager-getting-started-configure-preferences"></a>

A user that has been granted administrator permissions by their AWS Identity and Access Management \(IAM\) policy can perform the following operations:
+ Turn on Run As support for Linux managed nodes\. This makes it possible to start sessions using the credentials of a specified operating system user instead of the credentials of a system\-generated `ssm-user` account that AWS Systems Manager Session Manager can create on a managed node\.
+ Configure Session Manager to use AWS KMS key encryption to provide additional protection to the data transmitted between client machines and managed nodes\.
+ Configure Session Manager to create and send session history logs to an Amazon Simple Storage Service \(Amazon S3\) bucket or an Amazon CloudWatch Logs log group\. The stored log data can then be used to audit or report on the session connections made to your managed nodes and the commands run on them during the sessions\.
+ Configure session timeouts\. You can use this setting to specify when to end a session after a period of inactivity\.
+ Configure Session Manager to use configurable shell profiles\. These customizable profiles allow you to define preferences within sessions such as shell preferences, environment variables, working directories, and running multiple commands when a session is started\.

**Note**  
Before a user can update Session Manager preferences, they must have been granted the specific permissions that will let them make these updates, if they don't possess them already\. Without these permissions, the user can't configure logging options or set other session preferences for your account\.

**Topics**
+ [Grant or deny a user permissions to update Session Manager preferences](preference-setting-permissions.md)
+ [Specify an idle session timeout value](session-preferences-timeout.md)
+ [Specify maximum session duration](session-preferences-max-timeout.md)
+ [Allow configurable shell profiles](session-preferences-shell-config.md)
+ [Turn on run as support for Linux and macOS managed nodes](session-preferences-run-as.md)
+ [Turn on KMS key encryption of session data \(console\)](session-preferences-enable-encryption.md)
+ [Create Session Manager preferences \(command line\)](getting-started-create-preferences-cli.md)
+ [Update Session Manager preferences \(command line\)](getting-started-configure-preferences-cli.md)

For information about using the Systems Manager console to configure options for logging session data, see the following topics:
+  [Logging session data using Amazon S3 \(console\)](session-manager-logging.md#session-manager-logging-s3) 
+ [Streaming session data using Amazon CloudWatch Logs \(console\)](session-manager-logging.md#session-manager-logging-cwl-streaming)
+  [Logging session data using Amazon CloudWatch Logs \(console\)](session-manager-logging.md#session-manager-logging-cloudwatch-logs) 