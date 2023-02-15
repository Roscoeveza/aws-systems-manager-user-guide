# Step 2: Verify or create an IAM instance profile with Distributor permissions<a name="distributor-getting-started-instance-profile"></a>

By default, AWS Systems Manager doesn't have permission to perform actions on your instances\. You must grant access by using an AWS Identity and Access Management \(IAM\) instance profile\. An instance profile is a container that passes IAM role information to an Amazon Elastic Compute Cloud \(Amazon EC2\) instance at launch\. This requirement applies to permissions for all Systems Manager capabilities, not just Distributor, which is a capability of AWS Systems Manager\.

**Note**  
When you configure your edge devices to run AWS IoT Greengrass Core software and SSM Agent, you specify an IAM service role that enables Systems Manager to peform actions on it\. You don't need to configure managed edge devices with an instance profile\. 

If you already use other Systems Manager capabilities, such as Run Command and State Manager, an instance profile with the required permissions for Distributor is already attached to your instances\. The simplest way to ensure that you have permissions to perform Distributor tasks is to attach the **AmazonSSMManagedInstanceCore** policy to your instance profile\. For more information, see [Configure instance permissions for Systems Manager](setup-instance-permissions.md)\.