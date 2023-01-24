# Step 2: Create a managed\-node activation for a hybrid environment<a name="sysman-managed-instance-activation"></a>

To set up servers and virtual machines \(VMs\) in your hybrid environment as managed nodes, you need to create a managed\-node activation\. After you successfully complete the activation, you *immediately* receive an Activation Code and Activation ID\. You specify this Code and ID combination when you install AWS Systems Manager SSM Agent on servers and VMs in your hybrid environment\. The Code and ID provide secure access to the Systems Manager service from your managed nodes\.

**Important**  
Systems Manager immediately returns the Activation Code and ID to the console or the command window, depending on how you created the activation\. Copy this information and store it in a safe place\. If you navigate away from the console or close the command window, you might lose this information\. If you lose it, you must create a new activation\. 

**About activation expirations**  
An *activation expiration* is a window of time when you can register on\-premises machines with Systems Manager\. An expired activation has no impact on your servers or VMs that you previously registered with Systems Manager\. If an activation expires then you can’t register more servers or VMs with Systems Manager by using that specific activation\. You simply need to create a new one\.

Every on\-premises server and VM you previously registered remains registered as a Systems Manager managed node until you explicitly deregister it\. You can deregister a managed node on the **Managed nodes** tab in Fleet Manager in the Systems Manager console by using the AWS CLI command [deregister\-managed\-instance](https://docs.aws.amazon.com/cli/latest/reference/ssm/deregister-managed-instance.html), or by using the API call [DeregisterManagedInstance](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_DeregisterManagedInstance.html)\.

**About managed nodes**  
A managed node is any machine configured for AWS Systems Manager\. AWS Systems Manager supports Amazon Elastic Compute Cloud \(Amazon EC2\) instances, edge devices, and on\-premises servers or VMs, including VMs in other cloud environments\. Previously, managed nodes were all referred to as managed instances\. The term *instance* now refers to EC2 instances only\. The [deregister\-managed\-instance](https://docs.aws.amazon.com/cli/latest/reference/ssm/deregister-managed-instance.html) and [register\-managed\-instance](https://docs.aws.amazon.com/cli/latest/reference/ssm/register-managed-instance.html) commands were named before this terminology change\.

**About activation tags**  
If you create an activation by using either the AWS Command Line Interface \(AWS CLI\) or AWS Tools for Windows PowerShell, you can specify tags\. Tags are optional metadata that you assign to a resource\. Tags allow you to categorize a resource in different ways, such as by purpose, owner, or environment\. Here is an AWS CLI sample command to run on a local Linux machine that includes optional tags\.

```
aws ssm create-activation \
  --default-instance-name MyWebServers \
  --description "Activation for Finance department webservers" \
  --iam-role service-role/AmazonEC2RunCommandRoleForManagedInstances \
  --registration-limit 10 \
  --region us-east-2 \
  --tags "Key=Department,Value=Finance"
```

If you specify tags when you create an activation, then those tags are automatically assigned to your managed nodes when you activate them\.

You can't add tags to or delete tags from an existing activation\. If you don't want to automatically assign tags to your on\-premises servers and VMs using an activation, then you can add tags to them later\. More specifically, you can tag your on\-premises servers and VMs after they connect to Systems Manager for the first time\. After they connect, they're assigned a managed node ID and listed in the Systems Manager console with an ID that is prefixed with "mi\-"\. For information about how to add tags to your managed nodes without using the activation process, see [Tagging managed nodes](tagging-managed-instances.md)\.

**Note**  
You can't assign tags to an activation if you create it by using the Systems Manager console\. You must create it by using either the AWS CLI or Tools for Windows PowerShell\.

If you no longer want to manage an on\-premises server or virtual machine \(VM\) by using Systems Manager, you can deregister it\. For information, see [Deregistering managed nodes in a hybrid environment](systems-manager-managed-instances-advanced-deregister.md)\.

**Topics**
+ [Create an activation \(console\)](#create-managed-instance-activation-console)
+ [Create a managed\-node activation \(command line\)](#create-managed-instance-activation-commandline)

## Create an activation \(console\)<a name="create-managed-instance-activation-console"></a>

**To create a managed\-node activation**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Hybrid Activations**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Hybrid Activations**\.

1. Choose **Create activation**\.

   \-or\-

   If you are accessing **Hybrid Activations** for the first time in the current AWS Region, choose **Create an Activation**\. 

1. \(Optional\) For **Activation description**, enter a description for this activation\. We recommend entering a description if you plan to activate large numbers of servers and VMs\.

1. For **Instance limit**, specify the total number of nodes that you want to register with AWS as part of this activation\. The default value is 1 instance\.

1. For ** IAM role**, choose a service role option that allows your servers and VMs to communicate with AWS Systems Manager in the cloud:
   + **Option 1**: Choose **Use the default role created by the system** to use a role and managed policy provided by AWS\. 
   + **Option 2**: Choose **Select an existing custom IAM role that has the required permissions** to use the optional custom role you created earlier\. This role must have a trust relationship policy that specifies `"Service": "ssm.amazonaws.com"`\. If your IAM role doesn't specify this principle in a trust relationship policy, you receive the following error:

     ```
     An error occurred (ValidationException) when calling the CreateActivation
                                         operation: Not existing role: arn:aws:iam::<accountid>:role/SSMRole
     ```

     For more information about creating this role, see [Step 1: Create an IAM service role for a hybrid environment](sysman-service-role.md)\. 

1. For **Activation expiry date**, specify an expiration date for the activation\. The expiry date must be in the future, and not more than 30 days into the future\. The default value is 24 hours\.
**Note**  
If you want to register additional managed nodes after the expiry date, you must create a new activation\. The expiry date has no impact on registered and running nodes\.

1. \(Optional\) For **Default instance name** field, specify an identifying name value to be displayed for all managed nodes associated with this activation\. 

1. Choose **Create activation**\. Systems Manager immediately returns the Activation Code and ID to the console\. 

## Create a managed\-node activation \(command line\)<a name="create-managed-instance-activation-commandline"></a>

The following procedure describes how to use the AWS Command Line Interface \(AWS CLI\) \(on Linux or Windows\) or AWS Tools for PowerShell to create a managed node activation\.

**To create an activation**

1. Install and configure the AWS CLI or the AWS Tools for PowerShell, if you haven't already\.

   For information, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and [Installing the AWS Tools for PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up.html)\.

1. Run the following command to create an activation\.
**Note**  
In the following command, replace *region* with your own information\. For a list of supported *region* values, see the **Region** column in [Systems Manager service endpoints](https://docs.aws.amazon.com/general/latest/gr/ssm.html#ssm_region) in the *Amazon Web Services General Reference*\.
The role you specify for the *iam\-role* parameter must have a trust relationship policy that specifies `"Service": "ssm.amazonaws.com"`\. If your AWS Identity and Access Management \(IAM\) role doesn't specify this principle in a trust relationship policy, you receive the following error:  

     ```
     An error occurred (ValidationException) when calling the CreateActivation
                                             operation: Not existing role: arn:aws:iam::<accountid>:role/SSMRole
     ```
For more information about creating this role, see [Step 1: Create an IAM service role for a hybrid environment](sysman-service-role.md)\. 
For `--expiration-date`, provide a date in timestamp format, such as `"2021-07-07T00:00:00"`, for when the activation code expires\. You can specify a date up to 30 days in advance\. If you don't provide an expiration date, the activation code expires in 24 hours\.

------
#### [ Linux & macOS ]

   ```
   aws ssm create-activation \
       --default-instance-name name \
       --iam-role iam-service-role-name \
       --registration-limit number-of-managed-instances \
       --region region \
       --expiration-date "timestamp" \\  
       --tags "Key=key-name-1,Value=key-value-1" "Key=key-name-2,Value=key-value-2"
   ```

------
#### [ Windows ]

   ```
   aws ssm create-activation ^
       --default-instance-name name ^
       --iam-role iam-service-role-name ^
       --registration-limit number-of-managed-instances ^
       --region region ^
       --expiration-date "timestamp" ^
       --tags "Key=key-name-1,Value=key-value-1" "Key=key-name-2,Value=key-value-2"
   ```

------
#### [ PowerShell ]

   ```
   New-SSMActivation -DefaultInstanceName name `
       -IamRole iam-service-role-name `
       -RegistrationLimit number-of-managed-instances `
       –Region region `
       -ExpirationDate "timestamp" `
       -Tag @{"Key"="key-name-1";"Value"="key-value-1"},@{"Key"="key-name-2";"Value"="key-value-2"}
   ```

------

   Here is an example\.

------
#### [ Linux & macOS ]

   ```
   aws ssm create-activation \
       --default-instance-name MyWebServers \
       --iam-role service-role/AmazonEC2RunCommandRoleForManagedInstances \
       --registration-limit 10 \
       --region us-east-2 \
       --expiration-date "2021-07-07T00:00:00" \
       --tags "Key=Environment,Value=Production" "Key=Department,Value=Finance"
   ```

------
#### [ Windows ]

   ```
   aws ssm create-activation ^
       --default-instance-name MyWebServers ^
       --iam-role service-role/AmazonEC2RunCommandRoleForManagedInstances ^
       --registration-limit 10 ^
       --region us-east-2 ^
       --expiration-date "2021-07-07T00:00:00" ^
       --tags "Key=Environment,Value=Production" "Key=Department,Value=Finance"
   ```

------
#### [ PowerShell ]

   ```
   New-SSMActivation -DefaultInstanceName MyWebServers `
       -IamRole service-role/AmazonEC2RunCommandRoleForManagedInstances `
       -RegistrationLimit 10 `
       –Region us-east-2 `
       -ExpirationDate "2021-07-07T00:00:00" `
       -Tag @{"Key"="Environment";"Value"="Production"},@{"Key"="Department";"Value"="Finance"}
   ```

------

   If the activation is created successfully, the system immediately returns an Activation Code and ID\.