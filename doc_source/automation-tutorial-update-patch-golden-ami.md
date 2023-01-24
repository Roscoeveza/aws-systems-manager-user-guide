# Update a golden AMI using Automation, AWS Lambda, and Parameter Store<a name="automation-tutorial-update-patch-golden-ami"></a>

The following example uses the model where an organization maintains and periodically patches their own, proprietary AMIs rather than building from Amazon Elastic Compute Cloud \(Amazon EC2\) AMIs\.

The following procedure shows how to automatically apply operating system \(OS\) patches to an AMI that is already considered to be the most up\-to\-date or *latest* AMI\. In the example, the default value of the parameter `SourceAmiId` is defined by a AWS Systems Manager Parameter Store parameter called `latestAmi`\. The value of `latestAmi` is updated by an AWS Lambda function invoked at the end of the automation\. As a result of this Automation process, the time and effort spent patching AMIs is minimized because patching is always applied to the most up\-to\-date AMI\. Parameter Store and Automation are capabilities of AWS Systems Manager\.

**Before you begin**  
Configure Automation roles and, optionally, Amazon EventBridge for Automation\. For more information, see [Setting up Automation](automation-setup.md)\.

**Topics**
+ [Task 1: Create a parameter in Systems Manager Parameter Store](#create-parameter-ami)
+ [Task 2: Create an IAM role for AWS Lambda](#create-lambda-role)
+ [Task 3: Create an AWS Lambda function](#create-lambda-function)
+ [Task 4: Create a runbook and patch the AMI](#create-custom-ami-update-runbook)

## Task 1: Create a parameter in Systems Manager Parameter Store<a name="create-parameter-ami"></a>

Create a string parameter in Parameter Store that uses the following information:
+ **Name**: `latestAmi`\.
+ **Value**: An AMI ID\. For example:` ami-188d6e0e`\.

For information about how to create a Parameter Store string parameter, see [Creating Systems Manager parameters](sysman-paramstore-su-create.md)\.

## Task 2: Create an IAM role for AWS Lambda<a name="create-lambda-role"></a>

Use the following procedure to create an IAM service role for AWS Lambda\. These policies give Lambda permission to update the value of the `latestAmi` parameter using a Lambda function and Systems Manager\.

**To create an IAM service role for Lambda**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, and then choose **Create policy**\.

1. Choose the **JSON** tab\.

1. Replace the default contents with the following policy\. Replace each *example resource placeholder* with your own information\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "logs:CreateLogGroup",
               "Resource": "arn:aws:logs:region:123456789012:*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "logs:CreateLogStream",
                   "logs:PutLogEvents"
               ],
               "Resource": [
                   "arn:aws:logs:region:123456789012:log-group:/aws/lambda/function name:*"
               ]
           }
       ]
   }
   ```

1. Choose **Next: Tags**\.

1. \(Optional\) Add one or more tag\-key value pairs to organize, track, or control access for this policy\. 

1. Choose **Next: Review**\.

1. On the **Review policy** page, for **Name**, enter a name for the inline policy, such as **amiLambda**\.

1. Choose **Create policy**\.

1. Repeat steps 2 and 3\.

1. Paste the following policy\. Replace each *example resource placeholder* with your own information\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "ssm:PutParameter",
               "Resource": "arn:aws:ssm:region:123456789012:parameter/latestAmi"
           },
           {
               "Effect": "Allow",
               "Action": "ssm:DescribeParameters",
               "Resource": "*"
           }
       ]
   }
   ```

1. Choose **Next: Tags**\.

1. \(Optional\) Add one or more tag\-key value pairs to organize, track, or control access for this policy\. 

1. Choose **Next: Review**\.

1. On the **Review policy** page, for **Name**, enter a name for the inline policy, such as **amiParameter**\.

1. Choose **Create policy**\.

1. In the navigation pane, choose **Roles**, and then choose **Create role**\.

1. Immediately under **Use case**, choose **Lambda**, and then choose **Next**\.

1. On the **Add permissions** page, use the **Search** field to locate the two policies you created earlier\.

1. Select the check box next to the policies, and then choose **Next**\.

1. For **Role name**, enter a name for your new role, such as **lambda\-ssm\-role** or another name that you prefer\. 
**Note**  
Because various entities might reference the role, you cannot change the name of the role after it has been created\.

1. \(Optional\) Add one or more tag key\-value pairs to organize, track, or control access for this role, and then choose **Create role**\.

## Task 3: Create an AWS Lambda function<a name="create-lambda-function"></a>

Use the following procedure to create a Lambda function that automatically updates the value of the `latestAmi` parameter\.

**To create a Lambda function**

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. On the **Create function** page, choose **Author from scratch**\.

1. For **Function name**, enter **Automation\-UpdateSsmParam**\.

1. For **Runtime**, choose **Python 3\.8**\.

1. For **Architecture**, select the type of computer processor for Lambda to use to run the function, **x86\_64** or **arm64**, 

1. In the **Permissions** section, expand **Change default execution role**\.

1. Choose **Use an existing role**, and then choose the service role for Lambda that you created in Task 2\.

1. Choose **Create function**\.

1. In the **Code source** area, on the **lambda\_function** tab, delete the pre\-populated code in the field, and then paste the following code sample\.

   ```
   from __future__ import print_function
   
   import json
   import boto3
   
   print('Loading function')
   
   
   #Updates an SSM parameter
   #Expects parameterName, parameterValue
   def lambda_handler(event, context):
       print("Received event: " + json.dumps(event, indent=2))
   
       # get SSM client
       client = boto3.client('ssm')
   
       #confirm  parameter exists before updating it
       response = client.describe_parameters(
          Filters=[
             {
              'Key': 'Name',
              'Values': [ event['parameterName'] ]
             },
           ]
       )
   
       if not response['Parameters']:
           print('No such parameter')
           return 'SSM parameter not found.'
   
       #if parameter has a Description field, update it PLUS the Value
       if 'Description' in response['Parameters'][0]:
           description = response['Parameters'][0]['Description']
           
           response = client.put_parameter(
             Name=event['parameterName'],
             Value=event['parameterValue'],
             Description=description,
             Type='String',
             Overwrite=True
           )
       
       #otherwise just update Value
       else:
           response = client.put_parameter(
             Name=event['parameterName'],
             Value=event['parameterValue'],
             Type='String',
             Overwrite=True
           )
           
       reponseString = 'Updated parameter %s with value %s.' % (event['parameterName'], event['parameterValue'])
           
       return reponseString
   ```

1. Choose **File, Save**\.

1. To test the Lambda function, from the **Test** menu, choose **Configure test event**\.

1. For **Event name**, enter a name for the test event, such as **MyTestEvent**\.

1. Replace the existing text with the following JSON\. Replace *AMI ID* with your own information to set your `latestAmi` parameter value\.

   ```
   {
      "parameterName":"latestAmi",
      "parameterValue":"AMI ID"
   }
   ```

1. Choose **Save**\.

1. Choose **Test** to test the function\. On the **Execution result** tab, the status should be reported as **Succeeded**, along with other details about the update\.

## Task 4: Create a runbook and patch the AMI<a name="create-custom-ami-update-runbook"></a>

Use the following procedure to create and run a runbook that patches the AMI you specified for the **latestAmi** parameter\. After the automation completes, the value of **latestAmi** is updated with the ID of the newly\-patched AMI\. Subsequent automations use the AMI created by the previous execution\.

**To create and run the runbook**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Documents**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Documents** in the navigation pane\.

1. For **Create document**, choose **Automation**\.

1. For **Name**, enter **UpdateMyLatestWindowsAmi**\.

1. Choose the **Editor** tab, and then choose **Edit**\.

1. Choose **OK** when prompted\.

1. In the **Document editor** field, replace the default content with the following YAML sample runbook content\.

   ```
   ---
   description: Systems Manager Automation Demo - Patch AMI and Update ASG
   schemaVersion: '0.3'
   assumeRole: '{{ AutomationAssumeRole }}'
   parameters:
     AutomationAssumeRole:
       type: String
       description: '(Required) The ARN of the role that allows Automation to perform the actions on your behalf. If no role is specified, Systems Manager Automation uses your IAM permissions to execute this document.'
       default: ''
     SourceAMI:
       type: String
       description: The ID of the AMI you want to patch.
       default: '{{ ssm:latestAmi }}'
     SubnetId:
       type: String
       description: The ID of the subnet where the instance from the SourceAMI parameter is launched.
     SecurityGroupIds:
       type: StringList
       description: The IDs of the security groups to associate with the instance that's launched from the SourceAMI parameter.
     NewAMI:
       type: String
       description: The name of of newly patched AMI.
       default: 'patchedAMI-{{global:DATE_TIME}}'
     InstanceProfile:
       type: String
       description: The name of the IAM instance profile you want the source instance to use.
     SnapshotId:
       type: String
       description: (Optional) The snapshot ID to use to retrieve a patch baseline snapshot.
       default: ''
     RebootOption:
       type: String
       description: '(Optional) Reboot behavior after a patch Install operation. If you choose NoReboot and patches are installed, the instance is marked as non-compliant until a subsequent reboot and scan.'
       allowedValues:
         - NoReboot
         - RebootIfNeeded
       default: RebootIfNeeded
     Operation:
       type: String
       description: (Optional) The update or configuration to perform on the instance. The system checks if patches specified in the patch baseline are installed on the instance. The install operation installs patches missing from the baseline.
       allowedValues:
         - Install
         - Scan
       default: Install
   mainSteps:
     - name: startInstances
       action: 'aws:runInstances'
       timeoutSeconds: 1200
       maxAttempts: 1
       onFailure: Abort
       inputs:
         ImageId: '{{ SourceAMI }}'
         InstanceType: m5.large
         MinInstanceCount: 1
         MaxInstanceCount: 1
         IamInstanceProfileName: '{{ InstanceProfile }}'
         SubnetId: '{{ SubnetId }}'
         SecurityGroupIds: '{{ SecurityGroupIds }}'
     - name: verifyInstanceManaged
       action: 'aws:waitForAwsResourceProperty'
       timeoutSeconds: 600
       inputs:
         Service: ssm
         Api: DescribeInstanceInformation
         InstanceInformationFilterList:
           - key: InstanceIds
             valueSet:
               - '{{ startInstances.InstanceIds }}'
         PropertySelector: '$.InstanceInformationList[0].PingStatus'
         DesiredValues:
           - Online
       onFailure: 'step:terminateInstance'
     - name: installPatches
       action: 'aws:runCommand'
       timeoutSeconds: 7200
       onFailure: Abort
       inputs:
         DocumentName: AWS-RunPatchBaseline
         Parameters:
           SnapshotId: '{{SnapshotId}}'
           RebootOption: '{{RebootOption}}'
           Operation: '{{Operation}}'
         InstanceIds:
           - '{{ startInstances.InstanceIds }}'
     - name: stopInstance
       action: 'aws:changeInstanceState'
       maxAttempts: 1
       onFailure: Continue
       inputs:
         InstanceIds:
           - '{{ startInstances.InstanceIds }}'
         DesiredState: stopped
     - name: createImage
       action: 'aws:createImage'
       maxAttempts: 1
       onFailure: Continue
       inputs:
         InstanceId: '{{ startInstances.InstanceIds }}'
         ImageName: '{{ NewAMI }}'
         NoReboot: false
         ImageDescription: Patched AMI created by Automation
     - name: terminateInstance
       action: 'aws:changeInstanceState'
       maxAttempts: 1
       onFailure: Continue
       inputs:
         InstanceIds:
           - '{{ startInstances.InstanceIds }}'
         DesiredState: terminated
     - name: updateSsmParam
       action: aws:invokeLambdaFunction
       timeoutSeconds: 1200
       maxAttempts: 1
       onFailure: Abort
       inputs:
           FunctionName: Automation-UpdateSsmParam
           Payload: '{"parameterName":"latestAmi", "parameterValue":"{{createImage.ImageId}}"}'
   outputs:
   - createImage.ImageId
   ```

1. Choose **Create automation**\.

1. In the navigation pane, choose **Automation**, and then choose **Execute automation**\.

1. In the **Choose document** page, choose the **Owned by me** tab\.

1. Search for the **UpdateMyLatestWindowsAmi** runbook, and select the button in the **UpdateMyLatestWindowsAmi** card\.

1. Choose **Next**\.

1. Choose **Simple execution**\.

1. Specify values for the input parameters\.

1. Choose **Execute**\.

1. After the automation completes, choose **Parameter Store** in the navigation pane and confirm that the new value for `latestAmi` matches the value returned by the automation\. You can also verify the new AMI ID matches the Automation output in the **AMIs** section of the Amazon EC2 console\.