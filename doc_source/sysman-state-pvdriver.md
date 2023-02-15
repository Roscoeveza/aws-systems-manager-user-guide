# Walkthrough: Automatically update PV drivers on EC2 instances for Windows Server \(console\)<a name="sysman-state-pvdriver"></a>

Amazon Windows Amazon Machine Images \(AMIs\) contain a set of drivers to permit access to virtualized hardware\. These drivers are used by Amazon Elastic Compute Cloud \(Amazon EC2\) to map instance store and Amazon Elastic Block Store \(Amazon EBS\) volumes to their devices\. We recommend that you install the latest drivers to improve stability and performance of your EC2 instances for Windows Server\. For more information about PV drivers, see [AWS PV Drivers](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html#xen-driver-awspv)\.

The following walkthrough shows you how to configure a State Manager association to automatically download and install new AWS PV drivers when the drivers become available\. State Manager is a capability of AWS Systems Manager\.

**Before you begin**  
Before you complete the following procedure, verify that you have at least one Amazon EC2 instance for Windows Server running that is configured for Systems Manager\. For more information, see [Systems Manager prerequisites](systems-manager-prereqs.md)\. 

**To create a State Manager association that automatically updates PV drivers**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **State Manager**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose ** State Manager**\.

1. Choose **Create association**\.

1. In the **Name** field, enter a descriptive name\.

1. In the **Document** list, choose **`AWS-ConfigureAWSPackage`**\.

1. In the **Parameters** section, choose **Install** from the **Action** list\.

1. For **Installation type**, choose **Uninstall and reinstall**\.

1. In the **Name** field, enter **AWSPVDriver**\. You can keep the **Version** and **Additional Arguments** fields empty\.

1. In the **Targets** section, choose an option\.
**Note**  
If you choose to target instances by using tags, and you specify tags that map to Linux instances, the association succeeds on the Windows instance but fails on the Linux instances\. The overall status of the association shows **Failed**\.

1. In the **Specify schedule** section, choose an option\. Updated PV drivers are released a several times a year, so you can schedule the association to run once a month, if you want\.

1. In the **Advanced options** section, for **Compliance severity**, choose a severity level for the association\. Compliance reporting indicates whether the association state is compliant or noncompliant, along with the severity level you indicate here\. For more information, see [About State Manager association compliance](sysman-compliance-about.md#sysman-compliance-about-association)\.

1. For **Rate control**:
   + For **Concurrency**, specify either a number or a percentage of managed nodes on which to run the command at the same time\.
**Note**  
If you selected targets by specifying tags applied to managed nodes or by specifying AWS resource groups, and you aren't certain how many managed nodes are targeted, then restrict the number of targets that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other managed nodes after it fails on either a number or a percentage of nodes\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Managed nodes still processing the command might also send errors\.

1. \(Optional\) For **Output options**, to save the command output to a file, select the **Enable writing output to S3** box\. Enter the bucket and prefix \(folder\) names in the boxes\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile assigned to the managed node, not those of the IAM user performing this task\. For more information, see [Configure instance permissions for Systems Manager](setup-instance-permissions.md) or [Create an IAM service role for a hybrid environment](sysman-service-role.md)\. In addition, if the specified S3 bucket is in a different AWS account, verify that the instance profile or IAM service role associated with the managed node has the necessary permissions to write to that bucket\.

1. Choose **Create association**, and then choose **Close**\. The system attempts to create the association on the instances and immediately apply the state\. 

   If you created the association on one or more Amazon EC2 instances for Windows Server, the status changes to **Success**\. If your instances aren't configured for Systems Manager, or if you inadvertently targeted Linux instances, the status shows **Failed**\.

   If the status is **Failed**, choose the association ID, choose the **Resources** tab, and then verify that the association was successfully created on your EC2 instances for Windows Server\. If EC2 instances for Windows Server show a status of **Failed**, verify that the SSM Agent is running on the instance, and verify that the instance is configured with an AWS Identity and Access Management \(IAM\) role for Systems Manager\. For more information, see [Systems Manager prerequisites](systems-manager-prereqs.md)\.