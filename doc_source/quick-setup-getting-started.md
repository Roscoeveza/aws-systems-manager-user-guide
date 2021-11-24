# Getting started with Quick Setup<a name="quick-setup-getting-started"></a>

To get started with Quick Setup, a capability of AWS Systems Manager, you must choose a home AWS Region and then onboard with Quick Setup\. The home Region is where Quick Setup creates the AWS resources that are used to deploy your configurations\. The home Region can't be changed after you select it\. 

During onboarding, Quick Setup creates the following AWS Identity and Access Management \(IAM\) roles on your behalf: 
+ `AWS-QuickSetup-StackSet-Local-ExecutionRole` – Grants AWS CloudFormation permissions to use any template\.
+ `AWS-QuickSetup-StackSet-Local-AdministrationRole` – Grants permission to AWS CloudFormation to assume `AWS-QuickSetup-StackSet-Local-ExecutionRole`\.

If you're onboarding a management account, Quick Setup also creates the following roles on your behalf:
+ `AWS-QuickSetup-SSM-RoleForEnablingExplorer` – Grants permissions to the `AWS-EnableExplorer` automation runbook\. The `AWS-EnableExplorer` runbook configures Explorer, a capability of Systems Manager, to display information for multiple AWS accounts and Regions\.
+ `AWSServiceRoleForAmazonSSM` – A service\-linked role that grants access to AWS resources managed and used by Systems Manager\.
+ `AWSServiceRoleForAmazonSSM_AccountDiscovery` – A service\-linked role that grants permissions to Systems Manager to call AWS services to discover AWS account information when synchronizing data\. For more information, see [About the `AWSServiceRoleForAmazonSSM_AccountDiscovery` role](Explorer-setup-permissions.md#Explorer-service-role-details)\.

When onboarding a management account, Quick Setup enables trusted access between AWS Organizations and CloudFormation to deploy Quick Setup configurations across your organization\. To enable trusted access, your account must have administrator permissions\. After onboarding, you no longer need administrator permissions\. For more information, see [Enable trusted access with Organizations](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-orgs-enable-trusted-access.html)\.

**Note**  
Quick Setup uses AWS CloudFormation stack sets to deploy changes\. Stack sets aren't deployed to your organization's management account\. For more information, see [Considerations when creating a stack set with service\-managed permissions](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-getting-started-create.html?icmpid=docs_cfn_console#stacksets-orgs-considerations)\. 

You can use all features of Quick Setup if your IAM user, group, or role has access to the API operations listed in the following table\. There are two tabs of API operations, one for all accounts and one for the additional permissions you need for the management account of your organization\.

------
#### [ Non\-management account ]

```
"iam:CreateRole",
"iam:AttachRolePolicy",
"iam:PutPolicy",
"iam:GetRole",
"iam:ListRoles",
"iam:PassRole"
"ssm:ListAssociations",
"ssm:ListDocuments",
"ssm:GetDocument",
"ssm:DescribeAssociation",
"ssm:DescribeAutomationExecutions",
"cloudformation:DescribeStackSet",
"cloudformation:DescribeStackInstance",
"cloudformation:DescribeStacks",
"cloudformation:DescribeStackResources",
"cloudformation:ListStackSetOperations",
"cloudformation:ListStackSets",
"cloudformation:ListStacks",
"cloudformation:ListStackInstances",
"cloudformation:ListStackSetOperationResults",
"cloudformation:TagResource",
"cloudformation:DeleteStackSet",
"cloudformation:UpdateStackSet",
"cloudformation:CreateStackSet",
"cloudformation:DeleteStackInstances",
"cloudformation:CreateStackInstances"
```

------
#### [ Management account ]

```
"ssm:createResourceDataSync",
"ssm:listResourceDataSync",
"ssm:getOpsSummary",
"ssm:createAssociation",
"ssm:createDocument",
"ssm:startAssociationsOnce",
"ssm:startAutomationExecution",
"ssm:updateAssociation",
"ssm:listAssociations",
"ssm:listDocuments",
"ssm:getDocument",
"ssm:describeAssociation",
"ssm:describeAutomationExecutions",
"organizations:ListRoots",
"organizations:DescribeOrganization",
"organizations:ListOrganizationalUnitsForParent"
"organizations:EnableAWSServiceAccess",
"cloudformation:describe*"
```

------

**To configure the home AWS Region**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Quick Setup**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Quick Setup** in the navigation pane\.

1. Choose a **home Region**\. 

1. Choose **Get started**\.

To start using Quick Setup, choose a service or feature in the list of available configuration types\. A *configuration type* in Quick Setup is specific to an AWS service or feature\. When you choose a configuration type, you choose the options that you want to configure for that service or feature\. By default, configuration types help you set up the service or feature to use recommended best practices\. 

After setting up a configuration, you can view details about it and its deployment status across organizational units \(OUs\) and Regions\. You can also view State Manager association status for the configuration\. State Manager is a capability of AWS Systems Manager\. In the **Configuration details** pane, you can view a summary of the Quick Setup configuration\. This summary includes details from all accounts and any detected configuration drift\. 