# View session history<a name="session-manager-working-with-view-history"></a>

You can use the AWS Systems Manager console or the AWS Command Line Interface \(AWS CLI\) to view information about sessions in your account\. In the console, you can view session details such as the following:
+ The ID of the session
+ Which user connected to a managed node through a session
+ The ID of the managed node
+ When the session began and ended
+ The status of the session
+ The location specified for storing session logs \(if turned on\)

Using the AWS CLI, you can view a list of sessions in your account, but not the additional details that are available in the console\.

For information about logging session history information, see [Logging session activity](session-manager-logging.md)\.

**Topics**
+ [Viewing session history \(console\)](#view-console)
+ [Viewing session history \(AWS CLI\)](#view-history-cli)

## Viewing session history \(console\)<a name="view-console"></a>

You can use the AWS Systems Manager console to view details about the sessions in your account\.

**To view session history \(console\)**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Session Manager**\.

1. Choose the **Session history** tab\.

## Viewing session history \(AWS CLI\)<a name="view-history-cli"></a>

To view a list of sessions in your account using the AWS CLI, run the following command\.

```
aws ssm describe-sessions \
    --state History
```

**Note**  
This command returns only results for connections to targets initiated using Session Manager\. It doesn't list connections made through other means, such as Remote Desktop Protocol \(RDP\) or the Secure Shell Protocol \(SSH\)\.

For information about other options you can use with the describe\-sessions command, see [describe\-sessions](https://docs.aws.amazon.com/cli/latest/reference/ssm/describe-sessions.html) in the AWS Systems Manager section of the AWS CLI Command Reference\.
