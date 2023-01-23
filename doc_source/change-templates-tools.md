# Creating change templates using command line tools<a name="change-templates-tools"></a>

The following procedures describe how to use the AWS Command Line Interface \(AWS CLI\) \(on Linux, macOS, or Windows\) or AWS Tools for Windows PowerShell to create a change request in Change Manager, a capability of AWS Systems Manager\. 

**To create a change template**

1. Install and configure the AWS CLI or the AWS Tools for PowerShell, if you haven't already\.

   For information, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and [Installing the AWS Tools for PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up.html)\.

1. Create a JSON file on your local machine with a name such as `MyChangeTemplate.json`, and then paste the content for your change template into it\.
**Note**  
Change templates use a version of schema 0\.3 that doesn't include all the same support as for Automation runbooks\.

   The following is an example\.
**Note**  
The parameter `minRequiredApprovals` is used to specify how many reviewers at a specified level must approve a change request that is created using this template\.  
This example demonstrates two levels of approvals\. You can specify up to five levels of approvals, but only one level is required\.   
In the first level, the specific user "John\-Doe" must approve each change request\. After that, any three members of the IAM role `Admin` must approve the change request\.  
For more information about approvals for change templates, see [About approvals in your change templates](cm-approvals-templates.md)\.

   ```
   {
      "description": "This change template demonstrates the feature set available for creating
     change templates for Change Manager. This template starts a Runbook workflow
     for the Automation runbook called AWS-HelloWorld",
      "templateInformation": "### Document Name: HelloWorldChangeTemplate\n\n
       ## What does this document do?\n
       This change template demonstrates the feature set available for creating change templates for Change Manager. 
       This template starts a Runbook workflow for the Automation runbook called AWS-HelloWorld.\n\n
       ## Input Parameters\n* ApproverSnsTopicArn: (Required) Amazon Simple Notification Service ARN for approvers.\n
       * Approver: (Required) The name of the approver to send this request to.\n
       * ApproverType: (Required) The type of reviewer.  * Allowed Values: IamUser, IamGroup, IamRole, SSOGroup, SSOUser\n\n
       ## Output Parameters\nThis document has no outputs\n",
      "schemaVersion": "0.3",
      "parameters": {
         "ApproverSnsTopicArn": {
            "type": "String",
            "description": "Amazon Simple Notification Service ARN for approvers."
         },
         "Approver": {
            "type": "String",
            "description": "IAM approver"
         },
         "ApproverType": {
            "type": "String",
            "description": "Approver types for the request. Allowed values include IamUser, IamGroup, IamRole, SSOGroup, and SSOUser."
         }
      },
      "executableRunBooks": [
         {
            "name": "AWS-HelloWorld",
            "version": "1"
         }
      ],
      "emergencyChange": false,
      "autoApprovable": false,
      "mainSteps": [
         {
            "name": "ApproveAction1",
            "action": "aws:approve",
            "timeoutSeconds": 3600,
            "inputs": {
               "Message": "A sample change request has been submitted for your review in Change Manager. You can approve or reject this request.",
               "EnhancedApprovals": {
                  "NotificationArn": "{{ ApproverSnsTopicArn }}",
                  "Approvers": [
                     {
                        "approver": "John-Doe",
                        "type": "IamUser",
                        "minRequiredApprovals": 1
                     }
                  ]
               }
            }
         },
           {
            "name": "ApproveAction2",
            "action": "aws:approve",
            "timeoutSeconds": 3600,
            "inputs": {
               "Message": "A sample change request has been submitted for your review in Change Manager. You can approve or reject this request.",
               "EnhancedApprovals": {
                  "NotificationArn": "{{ ApproverSnsTopicArn }}",
                  "Approvers": [
                     {
                        "approver": "Admin",
                        "type": "IamRole",
                        "minRequiredApprovals": 3                  
                     }
                  ]
               }
            }
         }
      ]
   }
   ```

1. Run the following command to create the change template\. 

------
#### [ Linux & macOS ]

   ```
   aws ssm create-document \
       --name MyChangeTemplate \
       --document-format JSON \
       --document-type Automation.ChangeTemplate \
       --content file://MyChangeTemplate.json \
       --tags Key=tag-key,Value=tag-value
   ```

------
#### [ Windows ]

   ```
   aws ssm create-document ^
       --name MyChangeTemplate ^
       --document-format JSON ^
       --document-type Automation.ChangeTemplate ^
       --content file://MyChangeTemplate.json ^
       --tags Key=tag-key,Value=tag-value
   ```

------
#### [ PowerShell ]

   ```
   $json = Get-Content -Path "C:\path\to\file\MyChangeTemplate.json" | Out-String
   New-SSMDocument `
       -Content $json `
       -Name "MyChangeTemplate" `
       -DocumentType "Automation.ChangeTemplate" `
       -Tags "Key=tag-key,Value=tag-value"
   ```

------

   For information about other options you can specify, see [create\-document](https://docs.aws.amazon.com/cli/latest/reference/ssm/create-document.html)\.

   The system returns information like the following\.

   ```
   {
      "DocumentDescription":{
         "CreatedDate":1.585061751738E9,
         "DefaultVersion":"1",
         "Description":"Use this template to update an EC2 Linux AMI. Requires one
         approver specified in the template and an approver specified in the request.",
         "DocumentFormat":"JSON",
         "DocumentType":"Automation",
         "DocumentVersion":"1",
         "Hash":"0d3d879b3ca072e03c12638d0255ebd004d2c65bd318f8354fcde820dEXAMPLE",
         "HashType":"Sha256",
         "LatestVersion":"1",
         "Name":"MyChangeTemplate",
         "Owner":"123456789012",
         "Parameters":[
            {
               "DefaultValue":"",
               "Description":"Level one approvers",
               "Name":"LevelOneApprovers",
               "Type":"String"
            },
            {
               "DefaultValue":"",
               "Description":"Level one approver type",
               "Name":"LevelOneApproverType",
               "Type":"String"
            },
      "cloudWatchMonitors": {
         "monitors": [
            "my-cloudwatch-alarm"
         ]
      }
         ],
         "PlatformTypes":[
            "Windows",
            "Linux"
         ],
         "SchemaVersion":"0.3",
         "Status":"Creating",
         "Tags":[
   
         ]
      }
   }
   ```

The users in your organization or account who have been specified as template reviewers on the **Settings** tab in Change Manager are notified that a new change template is pending their review\. 

If an Amazon Simple Notification Service \(Amazon SNS\) topic has been specified for change templates, notifications are sent when the change template is rejected or approved\. If you don't receive notifications related to this change template, you can return to Change Manager later to check on its status\.