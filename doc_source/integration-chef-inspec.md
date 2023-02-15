# Using Chef InSpec profiles with Systems Manager Compliance<a name="integration-chef-inspec"></a>

AWS Systems Manager integrates with [Chef InSpec](https://www.chef.io/inspec/)\. InSpec is an open\-source testing framework that allows you to create human\-readable profiles to store in GitHub or Amazon Simple Storage Service \(Amazon S3\)\. Then you can use Systems Manager to run compliance scans and view compliant and noncompliant nodes\. A *profile* is a security, compliance, or policy requirement for your computing environment\. For example, you can create profiles that perform the following checks when you scan your nodes with Compliance, a capability of AWS Systems Manager:
+ Check if specific ports are open or closed\.
+ Check if specific applications are running\.
+ Check if certain packages are installed\.
+ Check Windows Registry keys for specific properties\.

You can create InSpec profiles for Amazon Elastic Compute Cloud \(Amazon EC2\) instances and on\-premises servers or virtual machines \(VMs\) that you manage with Systems Manager\. The following sample Chef InSpec profile checks if port 22 is open\.

```
control 'Scan Port' do
impact 10.0
title 'Server: Configure the service port'
desc 'Always specify which port the SSH server should listen to.
Prevent unexpected settings.'
describe sshd_config do
its('Port') { should eq('22') }
end
end
```

InSpec includes a collection of resources that help you quickly write checks and auditing controls\. InSpec uses the [InSpec Domain\-specific Language \(DSL\)](https://www.inspec.io/docs/reference/dsl_inspec/) for writing these controls in Ruby\. You can also use profiles created by a large community of InSpec users\. For example, the [DevSec chef\-os\-hardening](https://github.com/dev-sec/chef-os-hardening) project on GitHub includes dozens of profiles to help you secure your nodes\. You can author and store profiles in GitHub or Amazon S3\. 

## How it works<a name="integration-chef-inspec-how"></a>

Here is how the process of using InSpec profiles with Compliance works:

1. Either identify predefined InSpec profiles that you want to use, or create your own\. You can use [predefined profiles](https://github.com/search?p=1&q=topic%3Ainspec+org%3Adev-sec&type=Repositories) on GitHub to get started\. For information about how to create your own InSpec profiles, see [Chef InSpec Profiles](https://www.inspec.io/docs/reference/profiles/)\.

1. Store profiles in either a public or private GitHub repository, or in an S3 bucket\.

1. Run Compliance with your InSpec profiles by using the Systems Manager document \(SSM document\) `AWS-RunInspecChecks`\. You can begin a Compliance scan by using Run Command, a capability of AWS Systems Manager, for on\-demand scans, or you can schedule regular Compliance scans by using State Manager, a capability of AWS Systems Manager\.

1. Identify noncompliant nodes by using the Compliance API or the Compliance console\.

**Note**  
Note the following information\.  
Chef uses a client on your nodes to process the profile\. You don't need to install the client\. When Systems Manager runs the SSM document `AWS-RunInspecChecks`, the system checks if the client is installed\. If not, Systems Manager installs the Chef client during the scan, and then uninstalls the client after the scan is completed\.
Running the SSM document `AWS-RunInspecChecks`, as described in this topic, assigns a compliance entry of type `Custom:Inspec` to each targeted node\. To assign this compliance type, the document calls the [PutComplianceItems](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_PutComplianceItems.html) API operation\.

## Running an InSpec compliance scan<a name="integration-chef-inspec-running"></a>

This section includes information about how to run an InSpec compliance scan by using the Systems Manager console and the AWS Command Line Interface \(AWS CLI\)\. The console procedure shows how to configure State Manager to run the scan\. The AWS CLI procedure shows how to configure Run Command to run the scan\.

### Running an InSpec compliance scan with State Manager \(console\)<a name="integration-chef-inspec-running-console"></a>

**To run an InSpec compliance scan with State Manager by using the AWS Systems Manager console**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **State Manager**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose ** State Manager**\.

1. Choose **Create association**\.

1. In the **Provide association details** section, enter a name\.

1. In the **Document** list, choose **`AWS-RunInspecChecks`**\.

1. In the **Document version** list, choose **Latest at runtime**\.

1. In the **Parameters** section, in the **Source Type** list, choose either **GitHub** or **S3**\.

   If you choose **GitHub**, then enter the path to an InSpec profile in either a public or private GitHub repository in the **Source Info** field\. Here is an example path to a public profile provided by the Systems Manager team from the following location: [https://github\.com/awslabs/amazon\-ssm/tree/master/Compliance/InSpec/PortCheck](https://github.com/awslabs/amazon-ssm/tree/master/Compliance/InSpec/PortCheck)\.

   ```
   {"owner":"awslabs","repository":"amazon-ssm","path":"Compliance/InSpec/PortCheck","getOptions":"branch:master"}
   ```

   If you choose **S3**, then enter a valid URL to an InSpec profile in an S3 bucket in the **Source Info** field\. 

   For more information about how Systems Manager integrates with GitHub and Amazon S3, see [Running scripts from GitHub](integration-remote-scripts.md)\. 

1. In the **Targets** section, choose the managed nodes on which you want to run this operation by specifying tags, selecting instances or edge devices manually, or specifying a resource group\.
**Note**  
If a managed node you expect to see isn't listed, see [Troubleshooting managed node availability](troubleshooting-managed-instances.md) for troubleshooting tips\.

1. In the **Specify schedule** section, use the schedule builder options to create a schedule that specifies when you want the Compliance scan to run\.

1. For **Rate control**:
   + For **Concurrency**, specify either a number or a percentage of managed nodes on which to run the command at the same time\.
**Note**  
If you selected targets by specifying tags applied to managed nodes or by specifying AWS resource groups, and you aren't certain how many managed nodes are targeted, then restrict the number of targets that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other managed nodes after it fails on either a number or a percentage of nodes\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Managed nodes still processing the command might also send errors\.

1. \(Optional\) For **Output options**, to save the command output to a file, select the **Write command output to an S3 bucket** box\. Enter the bucket and prefix \(folder\) names in the boxes\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile \(for EC2 instances\) or IAM service role \(on\-premises machines\) assigned to the instance, not those of the IAM user performing this task\. For more information, see [Configure instance permissions for Systems Manager](setup-instance-permissions.md) or [Create an IAM service role for a hybrid environment](sysman-service-role.md)\. In addition, if the specified S3 bucket is in a different AWS account, make sure that the instance profile or IAM service role associated with the managed node has the necessary permissions to write to that bucket\.

1. Choose **Create Association**\. The system creates the association and automatically runs the Compliance scan\.

1. Wait several minutes for the scan to complete, and then choose **Compliance** in the navigation pane\.

1. In **Corresponding managed instances**, locate nodes where the **Compliance Type** column is **Custom:Inspec**\.

1. Choose a node ID to view the details of noncompliant statuses\.

### Running an InSpec compliance scan with Run Command \(AWS CLI\)<a name="integration-chef-inspec-running-cli"></a>

1. Install and configure the AWS Command Line Interface \(AWS CLI\), if you haven't already\.

   For information, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)\.

1. Run one of the following commands to run an InSpec profile from either GitHub or Amazon S3\.

   The command takes the following parameters:
   + **sourceType**: GitHub or Amazon S3
   + **sourceInfo**: URL to the InSpec profile folder either in GitHub or an S3 bucket\. The folder must contain the base InSpec file \(\*\.yml\) and all related controls \(\*\.rb\)\.

   **GitHub**

   ```
   aws ssm send-command --document-name "AWS-RunInspecChecks" --targets '[{"Key":"tag:tag_name","Values":["tag_value"]}]' --parameters '{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"owner_name\", \"repository\":\"repository_name\", \"path\": \"Inspec.yml_file"}"]}'
   ```

   Here is an example\.

   ```
   aws ssm send-command --document-name "AWS-RunInspecChecks" --targets '[{"Key":"tag:testEnvironment","Values":["webServers"]}]' --parameters '{"sourceType":["GitHub"],"getOptions":"branch:master","sourceInfo":["{\"owner\":\"awslabs\", \"repository\":\"amazon-ssm\", \"path\": \"Compliance/InSpec/PortCheck\"}"]}'
   ```

   **Amazon S3**

   ```
   aws ssm send-command --document-name "AWS-RunInspecChecks" --targets '[{"Key":"tag:tag_name","Values":["tag_value"]}]' --parameters'{"sourceType":["S3"],"sourceInfo":["{\"path\":\"https://s3.aws-api-domain/DOC-EXAMPLE-BUCKET/Inspec.yml_file\"}"]}'
   ```

   Here is an example\.

   ```
   aws ssm send-command --document-name "AWS-RunInspecChecks" --targets '[{"Key":"tag:testEnvironment","Values":["webServers"]}]' --parameters'{"sourceType":["S3"],"sourceInfo":["{\"path\":\"https://s3.aws-api-domain/DOC-EXAMPLE-BUCKET/InSpec/PortCheck.yml\"}"]}' 
   ```

1. Run the following command to view a summary of the Compliance scan\.

   ```
   aws ssm list-resource-compliance-summaries --filters Key=ComplianceType,Values=Custom:Inspec
   ```

1. Run the following command to see details of a node that isn't compliant\.

   ```
   aws ssm list-compliance-items --resource-ids node_ID --resource-type ManagedInstance --filters Key=DocumentName,Values=AWS-RunInspecChecks
   ```