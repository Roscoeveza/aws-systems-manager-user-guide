# Updating software using Run Command<a name="run-command-tutorial-update-software"></a>

The following procedures describe how to update software on your managed nodes\.

## Updating the SSM Agent using Run Command<a name="rc-console-agentexample"></a>

The following procedure describes how to update the SSM Agent running on your managed nodes\. You can update to either the latest version of SSM Agent or downgrade to an older version\. When you run the command, the system downloads the version from AWS, installs it, and then uninstalls the version that existed before the command was run\. If an error occurs during this process, the system rolls back to the version on the server before the command was run and the command status shows that the command failed\.

**Note**  
If an instance is running macOS version 11\.0 \(Big Sur\) or later, the instance must have the SSM Agent version 3\.1\.941\.0 or higher to run the AWS\-UpdateSSMAgent document\. If the instance is running a version of SSM Agent released before 3\.1\.941\.0, you can update your SSM Agent to run the AWS\-UpdateSSMAgent document by running `brew update` and `brew upgrade amazon-ssm-agent` commands\.

To be notified about SSM Agent updates, subscribe to the [SSM Agent Release Notes](https://github.com/aws/amazon-ssm-agent/blob/mainline/RELEASENOTES.md) page on GitHub\.

**To update SSM Agent using Run Command**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Run Command**\.

1. Choose **Run command**\.

1. In the **Command document** list, choose **`AWS-UpdateSSMAgent`**\.

1. In the **Command parameters** section, specify values for the following parameters, if you want:

   1. \(Optional\) For **Version**, enter the version of SSM Agent to install\. You can install [older versions](https://github.com/aws/amazon-ssm-agent/blob/mainline/RELEASENOTES.md) of the agent\. If you don't specify a version, the service installs the latest version\.

   1. \(Optional\) For **Allow Downgrade**, choose **true** to install an earlier version of SSM Agent\. If you choose this option, specify the [earlier](https://github.com/aws/amazon-ssm-agent/blob/mainline/RELEASENOTES.md) version number\. Choose **false** to install only the newest version of the service\.

1. In the **Targets** section, choose the managed nodes on which you want to run this operation by specifying tags, selecting instances or edge devices manually, or specifying a resource group\.
**Note**  
If a managed node you expect to see isn't listed, see [Troubleshooting managed node availability](troubleshooting-managed-instances.md) for troubleshooting tips\.

1. For **Other parameters**:
   + For **Comment**, enter information about this command\.
   + For **Timeout \(seconds\)**, specify the number of seconds for the system to wait before failing the overall command execution\. 

1. For **Rate control**:
   + For **Concurrency**, specify either a number or a percentage of managed nodes on which to run the command at the same time\.
**Note**  
If you selected targets by specifying tags applied to managed nodes or by specifying AWS resource groups, and you aren't certain how many managed nodes are targeted, then restrict the number of targets that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other managed nodes after it fails on either a number or a percentage of nodes\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Managed nodes still processing the command might also send errors\.

1. \(Optional\) For **Output options**, to save the command output to a file, select the **Write command output to an S3 bucket** box\. Enter the bucket and prefix \(folder\) names in the boxes\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile \(for EC2 instances\) or IAM service role \(on\-premises machines\) assigned to the instance, nor those of the user performing this task\. For more information, see [Create an IAM instance profile for Systems Manager](setup-instance-profile.md) or [Create an IAM service role for a hybrid environment](sysman-service-role.md)\. In addition, if the specified S3 bucket is in a different AWS account, make sure that the instance profile or IAM service role associated with the managed node has the necessary permissions to write to that bucket\.

1. In the **SNS notifications** section, if you want notifications sent about the status of the command execution, select the **Enable SNS notifications** check box\.

   For more information about configuring Amazon SNS notifications for Run Command, see [Monitoring Systems Manager status changes using Amazon SNS notifications](monitoring-sns-notifications.md)\.

1. Choose **Run**\.

## Updating PowerShell using Run Command<a name="rc-console-pwshexample"></a>

The following procedure describes how to update PowerShell to version 5\.1 on your Windows Server 2012 and 2012 R2 managed nodes\. The script provided in this procedure downloads the Windows Management Framework \(WMF\) version 5\.1 update, and starts the installation of the update\. The node reboots during this process because this is required when installing WMF 5\.1\. The download and installation of the update takes approximately five minutes to complete\.

**To update PowerShell using Run Command**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Run Command**\.

1. Choose **Run command**\.

1. In the **Command document** list, choose **`AWS-RunPowerShellScript`**\.

1. In the **Commands** section, paste the following commands for your operating system\.

------
#### [ Windows Server 2012 R2 ]

   ```
   Set-Location -Path "C:\Windows\Temp"
   
   Invoke-WebRequest "https://go.microsoft.com/fwlink/?linkid=839516" -OutFile "Win8.1AndW2K12R2-KB3191564-x64.msu"
   
   Start-Process -FilePath "$env:systemroot\system32\wusa.exe" -Verb RunAs -ArgumentList ('Win8.1AndW2K12R2-KB3191564-x64.msu', '/quiet')
   ```

------
#### [ Windows Server 2012 ]

   ```
   Set-Location -Path "C:\Windows\Temp"
   
   Invoke-WebRequest "https://go.microsoft.com/fwlink/?linkid=839513" -OutFile "W2K12-KB3191565-x64.msu"
   
   Start-Process -FilePath "$env:systemroot\system32\wusa.exe" -Verb RunAs -ArgumentList ('W2K12-KB3191565-x64.msu', '/quiet')
   ```

------

1. In the **Targets** section, choose the managed nodes on which you want to run this operation by specifying tags, selecting instances or edge devices manually, or specifying a resource group\.
**Note**  
If a managed node you expect to see isn't listed, see [Troubleshooting managed node availability](troubleshooting-managed-instances.md) for troubleshooting tips\.

1. For **Other parameters**:
   + For **Comment**, enter information about this command\.
   + For **Timeout \(seconds\)**, specify the number of seconds for the system to wait before failing the overall command execution\. 

1. For **Rate control**:
   + For **Concurrency**, specify either a number or a percentage of managed nodes on which to run the command at the same time\.
**Note**  
If you selected targets by specifying tags applied to managed nodes or by specifying AWS resource groups, and you aren't certain how many managed nodes are targeted, then restrict the number of targets that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other managed nodes after it fails on either a number or a percentage of nodes\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Managed nodes still processing the command might also send errors\.

1. \(Optional\) For **Output options**, to save the command output to a file, select the **Write command output to an S3 bucket** box\. Enter the bucket and prefix \(folder\) names in the boxes\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile \(for EC2 instances\) or IAM service role \(on\-premises machines\) assigned to the instance, nor those of the user performing this task\. For more information, see [Create an IAM instance profile for Systems Manager](setup-instance-profile.md) or [Create an IAM service role for a hybrid environment](sysman-service-role.md)\. In addition, if the specified S3 bucket is in a different AWS account, make sure that the instance profile or IAM service role associated with the managed node has the necessary permissions to write to that bucket\.

1. In the **SNS notifications** section, if you want notifications sent about the status of the command execution, select the **Enable SNS notifications** check box\.

   For more information about configuring Amazon SNS notifications for Run Command, see [Monitoring Systems Manager status changes using Amazon SNS notifications](monitoring-sns-notifications.md)\.

1. Choose **Run**\.

After the managed node reboots and the installation of the update is complete, connect to your node to confirm that PowerShell successfully upgraded to version 5\.1\. To check the version of PowerShell on your node, open PowerShell and enter `$PSVersionTable`\. The `PSVersion` value in the output table shows 5\.1 if the upgrade was successful\.

If the `PSVersion` value is different than 5\.1, for example 3\.0 or 4\.0, review the **Setup** logs in Event Viewer under **Windows Logs**\. These logs indicate why the update installation failed\.