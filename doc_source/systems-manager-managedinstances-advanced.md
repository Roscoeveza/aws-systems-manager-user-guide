# Turning on the advanced\-instances tier<a name="systems-manager-managedinstances-advanced"></a>

AWS Systems Manager offers a standard\-instances tier and an advanced\-instances tier for servers, edge devices, and VMs in a hybrid environment\. The standard\-instances tier lets you register a maximum of 1,000 hybrid\-activated machines per AWS account per AWS Region\. The advanced\-instances tier is also required to use Patch Manager to patch Microsoft\-released applications on non\-EC2 nodes, and to connect to non\-EC2 nodes using Session Manager\. For more information, see [Configuring instance tiers](systems-manager-managed-instances-tiers.md)\.

This section describes how to configure your hybrid environment to use the advanced\-instances tier\.

**Before you begin**  
Review pricing details for advanced instances\. Advanced instances are available on a per\-use\-basis\. For more information see, [AWS Systems Manager Pricing](http://aws.amazon.com/systems-manager/pricing/)\. 

## Configuring permissions to turn on the advanced\-instances tier<a name="systems-manager-managedinstances-advanced-permissions"></a>

Verify that you have permission in AWS Identity and Access Management \(IAM\) to change your environment from the standard\-instances tier to the advanced\-instances tier\. You must either have the `AdministratorAccess` policy attached to your IAM user, group, or role, or you must have permission to change the Systems Manager activation\-tier service setting\. The activation\-tier setting uses the following API operations: 
+ [GetServiceSetting](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_GetServiceSetting.html)
+ [UpdateServiceSetting](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_UpdateServiceSetting.html)
+ [ResetServiceSetting](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_ResetServiceSetting.html)

Use the following procedure to add an inline IAM policy to a user account\. This policy allows a user to view the current managed\-instance tier setting\. This policy also allows the user to change or reset the current setting in the specified AWS account and AWS Region\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. In the list, choose the name of the user to embed a policy in\.

1. Choose the **Permissions** tab\.

1. On the right side of the page, under **Permission policies**, choose **Add inline policy**\. 

1. Choose the **JSON** tab\.

1. Replace the default content with the following:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "ssm:GetServiceSetting"
                   
               ],
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "ssm:ResetServiceSetting",
                   "ssm:UpdateServiceSetting"
               ],
               "Resource": "arn:aws:ssm:region:aws-account-id:servicesetting/ssm/managed-instance/activation-tier"
           }
       ]
   }
   ```

1. Choose **Review policy**\.

1. On the **Review policy** page, for **Name**, enter a name for the inline policy\. For example: **Managed\-Instances\-Tier**\.

1. Choose **Create policy**\.

Administrators can specify read\-only permission by assigning the following inline policy to the user's account\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ssm:GetServiceSetting"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "ssm:ResetServiceSetting",
                "ssm:UpdateServiceSetting"
            ],
            "Resource": "*"
        }
    ]
}
```

For more information about creating and editing IAM policies, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide*\.

## Turning on the advanced\-instances tier \(console\)<a name="systems-manager-managedinstances-advanced-enabling"></a>

The following procedure shows you how to use the Systems Manager console to change *all* on\-premises servers, edge devices, and virtual machines \(VMs\) that were added using managed\-instance activation, in the specified AWS account and AWS Region, to use the advanced\-instances tier\.

**Important**  
The following procedure describes how to change an account\-level setting\. This change results in charges being billed to your account\.

**To turn on the advanced\-instances tier \(console\)**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Fleet Manager**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Fleet Manager** in the navigation pane\.

1. In the **Account management** menu, choose **Instance tier settings**\.

   If you don't see the **Settings** tab, then do the following:

   1. Verify that the console is open in the AWS Region where you created your managed instances\. You can switch Regions by using the list in the top, right corner of the console\. 

   1. Verify that your instances meet Systems Manager requirements\. For information, see [Systems Manager prerequisites](systems-manager-prereqs.md)\.

   1. For servers and VMs in a hybrid environment, verify that you completed the activation process\. For more information, see [Setting up Systems Manager for hybrid environments](systems-manager-managedinstances.md)\.

1. Choose **Change account settings**\.

1. Review the information in the pop\-up about changing account settings, and then, if you approve, choose the option to accept and continue\.

The system can take several minutes to complete the process of moving all instances from the standard\-instances tier to the advanced\-instances tier\.

**Note**  
For information about changing back to the standard\-instances tier, see [Reverting from the advanced\-instances tier to the standard\-instances tier](systems-manager-managed-instances-advanced-reverting.md)\.

## Turning on the advanced\-instances tier \(AWS CLI\)<a name="systems-manager-managedinstances-advanced-enabling-cli"></a>

The following procedure shows you how to use the AWS Command Line Interface to change *all* on\-premises servers and VMs that were added using managed\-instance activation, in the specified AWS account and AWS Region, to use the advanced\-instances tier\.

**Important**  
The following procedure describes how to change an account\-level setting\. This change results in charges being billed to your account\.

**To turn on the advanced\-instances tier using the AWS CLI**

1. Open the AWS CLI and run the following command\. Replace each *example resource placeholder* with your own information\.

------
#### [ Linux & macOS ]

   ```
   aws ssm update-service-setting \
       --setting-id arn:aws:ssm:region:aws-account-id:servicesetting/ssm/managed-instance/activation-tier \
       --setting-value advanced
   ```

------
#### [ Windows ]

   ```
   aws ssm update-service-setting ^
       --setting-id arn:aws:ssm:region:aws-account-id:servicesetting/ssm/managed-instance/activation-tier ^
       --setting-value advanced
   ```

------

   There is no output if the command succeeds\.

1. Run the following command to view the current service settings for managed nodes in the current AWS account and AWS Region\.

------
#### [ Linux & macOS ]

   ```
   aws ssm get-service-setting \
       --setting-id arn:aws:ssm:region:aws-account-id:servicesetting/ssm/managed-instance/activation-tier
   ```

------
#### [ Windows ]

   ```
   aws ssm get-service-setting ^
       --setting-id arn:aws:ssm:region:aws-account-id:servicesetting/ssm/managed-instance/activation-tier
   ```

------

   The command returns information like the following\.

   ```
   {
       "ServiceSetting": {
           "SettingId": "/ssm/managed-instance/activation-tier",
           "SettingValue": "advanced",
           "LastModifiedDate": 1555603376.138,
           "LastModifiedUser": "arn:aws:sts::123456789012:assumed-role/Administrator/User_1",
           "ARN": "arn:aws:ssm:us-east-2:123456789012:servicesetting/ssm/managed-instance/activation-tier",
           "Status": "PendingUpdate"
       }
   }
   ```

## Turning on the advanced\-instances tier \(PowerShell\)<a name="systems-manager-managedinstances-advanced-enabling-ps"></a>

The following procedure shows you how to use the AWS Tools for Windows PowerShell to change *all* on\-premises servers and VMs that were added using managed\-instance activation, in the specified AWS account and AWS Region, to use the advanced\-instances tier\.

**Important**  
The following procedure describes how to change an account\-level setting\. This change results in charges being billed to your account\.

**To turn on the advanced\-instances tier using PowerShell**

1. Open AWS Tools for Windows PowerShell and run the following command\. Replace each *example resource placeholder* with your own information\.

   ```
   Update-SSMServiceSetting `
       -SettingId "arn:aws:ssm:region:aws-account-id:servicesetting/ssm/managed-instance/activation-tier" `
       -SettingValue "advanced"
   ```

   There is no output if the command succeeds\.

1. Run the following command to view the current service settings for managed nodes in the current AWS account and AWS Region\.

   ```
   Get-SSMServiceSetting `
       -SettingId "arn:aws:ssm:region:aws-account-id:servicesetting/ssm/managed-instance/activation-tier"
   ```

   The command returns information like the following\.

   ```
   ARN:arn:aws:ssm:us-east-2:123456789012:servicesetting/ssm/managed-instance/activation-tier
   LastModifiedDate : 4/18/2019 4:02:56 PM
   LastModifiedUser : arn:aws:sts::123456789012:assumed-role/Administrator/User_1
   SettingId        : /ssm/managed-instance/activation-tier
   SettingValue     : advanced
   Status           : PendingUpdate
   ```

The system can take several minutes to complete the process of moving all nodes from the standard\-instances tier to the advanced\-instances tier\.

**Note**  
For information about changing back to the standard\-instances tier, see [Reverting from the advanced\-instances tier to the standard\-instances tier](systems-manager-managed-instances-advanced-reverting.md)\.