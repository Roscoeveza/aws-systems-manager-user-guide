# Install or update packages<a name="distributor-working-with-packages-deploy"></a>

You can deploy packages to your AWS Systems Manager managed nodes by using Distributor, a capability of AWS Systems Manager\. To deploy the packages, use either the AWS Management Console or AWS Command Line Interface \(AWS CLI\)\. You can deploy one version of one package per command\. You can install new packages or update existing installations in place\. You can choose to deploy a specific version or choose to always deploy the latest version of a package for deployment\. We recommend using State Manager, a capability of AWS Systems Manager, to install packages\. Using State Manager helps ensure that your managed nodes are always running the most up\-to\-date version of your package\.


| Preference | AWS Systems Manager action | More info | 
| --- | --- | --- | 
|  Install or update a package immediately\.  |  Run Command  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with-packages-deploy.html)  | 
|  Install or update a package on a schedule, so that the installation always includes the default version\.  |  State Manager  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with-packages-deploy.html)  | 
|  Automatically install a package on new managed nodes that have a specific tag or set of tags\. For example, installing the Amazon CloudWatch agent on new instances\.  |  State Manager  |  One way to do this is to apply tags to new managed nodes, and then specify the tags as targets in your State Manager association\. State Manager automatically installs the package in an association on managed nodes that have matching tags\. See [About targets and rate controls in State Manager associations](systems-manager-state-manager-targets-and-rate-controls.md)\.  | 

**Topics**
+ [Installing or updating a package one time \(console\)](#distributor-deploy-pkg-console)
+ [Scheduling a package installation or update \(console\)](#distributor-deploy-sm-pkg-console)
+ [Installing a package one time \(AWS CLI\)](#distributor-deploy-pkg-cli)
+ [Updating a package one time \(AWS CLI\)](#distributor-update-pkg-cli)
+ [Scheduling a package installation \(AWS CLI\)](#distributor-smdeploy-pkg-cli)
+ [Scheduling a package update \(AWS CLI\)](#distributor-smupdate-pkg-cli)

## Installing or updating a package one time \(console\)<a name="distributor-deploy-pkg-console"></a>

You can use the AWS Systems Manager console to install or update a package one time\. When you configure a one\-time installation, Distributor uses [AWS Systems Manager Run Command](run-command.md), a capability of AWS Systems Manager, to perform the installation\.

**To install or update a package one time \(console\)**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Distributor**\.

1. On the Distributor home page, choose the package that you want to install\.

1. Choose **Install one time**\.

   This command opens Run Command with the command document `AWS-ConfigureAWSPackage` and your Distributor package already selected\.

1. For **Document version**, select the version of the `AWS-ConfigureAWSPackage` document that you want to run\.

1. For **Action**, choose **Install**\.

1. For **Installation type**, choose one of the following: 
   + **Uninstall and reinstall**: The package is completely uninstalled, and then reinstalled\. The application is unavailable until the reinstallation is complete\.
   + **In\-place update**: Only new or changed files are added to the existing installation according to instructions you provide in an `update` script\. The application remains available throughout the update process\. This option isn't supported for AWS published packages except the `AWSEC2Launch-Agent` package\.

1. For **Name**, verify that the name of the package you selected is entered\.

1. \(Optional\) For **Version**, enter the version name value of the package\. If you leave this field blank, Run Command installs the default version that you selected in Distributor\.

1. In the **Targets** section, choose the managed nodes on which you want to run this operation by specifying tags, selecting instances or devices manually, or by specifying a resource group\.
**Note**  
If you don't see a managed node in the list, see [Troubleshooting managed node availability](troubleshooting-managed-instances.md)\.

1. For **Other parameters**:
   + For **Comment**, enter information about this command\.
   + For **Timeout \(seconds\)**, specify the number of seconds for the system to wait before failing the overall command execution\. 

1. For **Rate Control**:
   + For **Concurrency**, specify either a number or a percentage of targets on which to run the command at the same time\.
**Note**  
If you selected targets by specifying tags or resource groups and you aren't certain how many managed nodes are targeted, then restrict the number of targets that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other targets after it fails on either a number or a percentage of managed nodes\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Managed nodes still processing the command might also send errors\. 

1. \(Optional\) For **Output options**, to save the command output to a file, select the **Write command output to an S3 bucket** box\. Enter the bucket and prefix \(folder\) names in the boxes\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile \(for EC2 instances\) or IAM service role \(on\-premises machines\) assigned to the instance, nor those of the user performing this task\. For more information, see [Create an IAM instance profile for Systems Manager](setup-instance-profile.md) or [Create an IAM service role for a hybrid environment](sysman-service-role.md)\. In addition, if the specified S3 bucket is in a different AWS account, make sure that the instance profile or IAM service role associated with the managed node has the necessary permissions to write to that bucket\.

1. In the **SNS notifications** section, if you want notifications sent about the status of the command execution, select the **Enable SNS notifications** check box\.

   For more information about configuring Amazon SNS notifications for Run Command, see [Monitoring Systems Manager status changes using Amazon SNS notifications](monitoring-sns-notifications.md)\.

1. When you're ready to install the package, choose **Run**\.

1. The **Command status** area reports the progress of the execution\. If the command is still in progress, choose the refresh icon in the top\-left corner of the console until the **Overall status** or **Detailed status** column shows **Success** or **Failed**\.

1. In the **Targets and outputs** area, choose the button next to a managed node name, and then choose **View output**\.

   The command output page shows the results of your command execution\. 

1. \(Optional\) If you chose to write command output to an Amazon S3 bucket, choose **Amazon S3** to view the output log data\.

## Scheduling a package installation or update \(console\)<a name="distributor-deploy-sm-pkg-console"></a>

You can use the AWS Systems Manager console to schedule the installation or update of a package\. When you schedule package installation or update, Distributor uses [AWS Systems Manager State Manager](systems-manager-state.md) to install or update\.

**To schedule a package installation \(console\)**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Distributor**\.

1. On the Distributor home page, choose the package that you want to install or update\.

1. For **Package**, choose **Install on a schedule**\.

   This command opens State Manager to a new association that is created for you\.

1. For **Name**, enter a name \(for example, **Deploy\-test\-agent\-package**\)\. This is optional, but recommended\. Spaces aren't allowed in the name\.

1. In the **Document** list, the document name `AWS-ConfigureAWSPackage` is already selected\. 

1. For **Action**, verify that **Install** is selected\.

1. For **Installation type**, choose one of the following: 
   + **Uninstall and reinstall**: The package is completely uninstalled, and then reinstalled\. The application is unavailable until the reinstallation is complete\.
   + **In\-place update**: Only new or changed files are added to the existing installation according to instructions you provide in an `update` script\. The application remains available throughout the update process\.

1. For **Name**, verify that the name of your package is entered\.

1. For **Version**, if you to want to install a package version other than the latest published version, enter the version identifier\.

1. For **Targets**, choose **Selecting all managed instances in this account**, **Specifying tags**, or **Manually Selecting Instance**\. If you target resources by using tags, enter a tag key and a tag value in the fields provided\.
**Note**  
You can choose managed AWS IoT Greengrass core devices by choosing either **Selecting all managed instances in this account** or **Manually Selecting Instance**\.

1. For **Specify schedule**, choose **On Schedule** to run the association on a regular schedule, or **No Schedule** to run the association once\. For more information about these options, see [Creating associations](sysman-state-assoc.md)\. Use the controls to create a `cron` or rate schedule for the association\.

1. Choose **Create Association**\.

1. On the **Association** page, choose the button next to the association you created, and then choose **Apply association now**\.

   State Manager creates and immediately runs the association on the specified targets\. For more information about the results of running associations, see [Creating associations](sysman-state-assoc.md) in this guide\.

For more information about working with the options in **Advanced options**, **Rate control**, and **Output options**, see [Creating associations](sysman-state-assoc.md)\. 

## Installing a package one time \(AWS CLI\)<a name="distributor-deploy-pkg-cli"></a>

You can run send\-command in the AWS CLI to install a Distributor package one time\. If the package is already installed, the application will be taken offline while the package is uninstalled and the new version installed in its place\.

**To install a package one time \(AWS CLI\)**
+ Run the following command in the AWS CLI\.

  ```
  aws ssm send-command \
      --document-name "AWS-ConfigureAWSPackage" \
      --instance-ids "instance-IDs" \
      --parameters '{"action":["Install"],"installationType":["Uninstall and reinstall"],"name":["package-name (in same account) or package-ARN (shared from different account)"]}'
  ```
**Note**  
The default behavior for `installationType` is `Uninstall and reinstall`\. You can omit `"installationType":["Uninstall and reinstall"]` from this command when you're installing a complete package\.

  The following is an example\.

  ```
  aws ssm send-command \
      --document-name "AWS-ConfigureAWSPackage" \
      --instance-ids "i-00000000000000" \
      --parameters '{"action":["Install"],"installationType":["Uninstall and reinstall"],"name":["ExamplePackage"]}'
  ```

For information about other options you can use with the send\-command command, see [https://docs.aws.amazon.com/cli/latest/reference/ssm/send-command.html](https://docs.aws.amazon.com/cli/latest/reference/ssm/send-command.html) in the AWS Systems Manager section of the *AWS CLI Command Reference*\.

## Updating a package one time \(AWS CLI\)<a name="distributor-update-pkg-cli"></a>

You can run send\-command in the AWS CLI to update a Distributor package without taking the associated application offline\. Only new or updated files in the package are replaced\.

**To update a package one time \(AWS CLI\)**
+ Run the following command in the AWS CLI\.

  ```
  aws ssm send-command \
      --document-name "AWS-ConfigureAWSPackage" \
      --instance-ids "instance-IDs" \
      --parameters '{"action":["Install"],"installationType":["In-place update"],"name":["package-name (in same account) or package-ARN (shared from different account)"]}'
  ```
**Note**  
When you add new or changed files, you must include `"installationType":["In-place update"]` in the command\.

  The following is an example\.

  ```
  aws ssm send-command \
      --document-name "AWS-ConfigureAWSPackage" \
      --instance-ids "i-02573cafcfEXAMPLE" \
      --parameters '{"action":["Install"],"installationType":["In-place update"],"name":["ExamplePackage"]}'
  ```

For information about other options you can use with the send\-command command, see [https://docs.aws.amazon.com/cli/latest/reference/ssm/send-command.html](https://docs.aws.amazon.com/cli/latest/reference/ssm/send-command.html) in the AWS Systems Manager section of the *AWS CLI Command Reference*\.

## Scheduling a package installation \(AWS CLI\)<a name="distributor-smdeploy-pkg-cli"></a>

You can run create\-association in the AWS CLI to install a Distributor package on a schedule\. The value of `--name`, the document name, is always `AWS-ConfigureAWSPackage`\. The following command uses the key `InstanceIds` to specify target managed nodes\. If the package is already installed, the application will be taken offline while the package is uninstalled and the new version installed in its place\.

```
aws ssm create-association \
    --name "AWS-ConfigureAWSPackage" \
    --parameters '{"action":["Install"],"installationType":["Uninstall and reinstall"],"name":["package-name (in same account) or package-ARN (shared from different account)"]}' \
    --targets [{\"Key\":\"InstanceIds\",\"Values\":[\"instance-ID1\",\"instance-ID2\"]}]
```

**Note**  
The default behavior for `installationType` is `Uninstall and reinstall`\. You can omit `"installationType":["Uninstall and reinstall"]` from this command when you're installing a complete package\.

The following is an example\.

```
aws ssm create-association \
    --name "AWS-ConfigureAWSPackage" \
    --parameters '{"action":["Install"],"installationType":["Uninstall and reinstall"],"name":["Test-ConfigureAWSPackage"]}' \
    --targets [{\"Key\":\"InstanceIds\",\"Values\":[\"i-02573cafcfEXAMPLE\",\"i-0471e04240EXAMPLE\"]}]
```

For information about other options you can use with the create\-association command, see [https://docs.aws.amazon.com/cli/latest/reference/ssm/create-association.html](https://docs.aws.amazon.com/cli/latest/reference/ssm/create-association.html) in the AWS Systems Manager section of the *AWS CLI Command Reference*\.

## Scheduling a package update \(AWS CLI\)<a name="distributor-smupdate-pkg-cli"></a>

You can run create\-association in the AWS CLI to update a Distributor package on a schedule without taking the associated application offline\. Only new or updated files in the package are replaced\. The value of `--name`, the document name, is always `AWS-ConfigureAWSPackage`\. The following command uses the key `InstanceIds` to specify target instances\.

```
aws ssm create-association \
    --name "AWS-ConfigureAWSPackage" \
    --parameters '{"action":["Install"],"installationType":["In-place update"],"name":["package-name (in same account) or package-ARN (shared from different account)"]}' \
    --targets [{\"Key\":\"InstanceIds\",\"Values\":[\"instance-ID1\",\"instance-ID2\"]}]
```

**Note**  
When you add new or changed files, you must include `"installationType":["In-place update"]` in the command\.

The following is an example\.

```
aws ssm create-association \
    --name "AWS-ConfigureAWSPackage" \
    --parameters '{"action":["Install"],"installationType":["In-place update"],"name":["Test-ConfigureAWSPackage"]}' \
    --targets [{\"Key\":\"InstanceIds\",\"Values\":[\"i-02573cafcfEXAMPLE\",\"i-0471e04240EXAMPLE\"]}]
```

For information about other options you can use with the create\-association command, see [https://docs.aws.amazon.com/cli/latest/reference/ssm/create-association.html](https://docs.aws.amazon.com/cli/latest/reference/ssm/create-association.html) in the AWS Systems Manager section of the *AWS CLI Command Reference*\.