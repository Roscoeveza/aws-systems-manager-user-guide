# AWS Systems Manager Explorer<a name="Explorer"></a>

AWS Systems Manager Explorer is a customizable operations dashboard that reports information about your AWS resources\. Explorer displays an aggregated view of operations data \(OpsData\) for your AWS accounts and across AWS Regions\. In Explorer, OpsData includes metadata about your Amazon Elastic Compute Cloud \(Amazon EC2\) instances, edge devices, on\-premises servers, and virtual machines \(VMs\) in your hybrid environment\. OpsData also includes information provided by other Systems Manager capabilities, including Patch Manager patch compliance and State Manager association compliance details\. To further simplify how you access OpsData, Explorer displays information from supporting AWS services like AWS Config, AWS Trusted Advisor, AWS Compute Optimizer, and AWS Support \(support cases\)\.

To raise operational awareness, Explorer also displays operational work items \(OpsItems\)\. Explorer provides context about how OpsItems are distributed across your business units or applications, how they trend over time, and how they vary by category\. You can group and filter information in Explorer to focus on items that are relevant to you and that require action\. When you identify high priority issues, you can use Systems Manager OpsCenter to run Automation runbooks and quickly resolve those issues\. To get started with Explorer, open the [Systems Manager console](https://console.aws.amazon.com/systems-manager/explorer)\. In the navigation pane, choose **Explorer**\.

The following image shows some of the individual report boxes, called *widgets*, which are available in Explorer\.

![\[Explorer dashboard in AWS Systems Manager\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/Explorer-1-overview.png)

## What are the features of Explorer?<a name="Explorer-learn-more-features"></a>

Explorer includes the following features:
+ **Customizable display of actionable information**: Explorer includes drag\-and\-drop widgets that automatically display actionable information about your AWS resources\. Explorer displays information in two types of widgets\.
  + **Informational widgets**: These widgets summarize data from Amazon EC2, Patch Manager, State Manager, and supporting AWS services like AWS Trusted Advisor, AWS Compute Optimizer, and AWS Support\. These widgets provide important context to help you understand the state and operational risks of your AWS resources\. Examples of informational widgets include **Instance count**, **Instance by AMI**, **Total noncompliant nodes** \(patching\), **Noncompliant associations**, and **Support Center cases**\. 
  + **OpsItem widgets**: A Systems Manager *OpsItem* is an operational work item that is related to one or more AWS resources\. OpsItems are a feature of Systems Manager OpsCenter\. OpsItems might require DevOps engineers to investigate and potentially remediate an issue\. Examples of possible OpsItems include high EC2 instance CPU utilization, detached Amazon Elastic Block Store \(Amazon EBS\) volumes, AWS CodeDeploy deployment failure, or Systems Manager Automation execution failure\. Examples of OpsItem widgets include **Open OpsItem summary**, **OpsItem by status**, and **OpsItems over time**\.
+ **Filters**: Each widget offers the ability to filter information based on AWS account, AWS Region, and tag\. Filters help you quickly refine the information displayed in Explorer\.
+ **Direct links to service screens**: To help you investigate issues with AWS resources, Explorer widgets contain direct links to related service screens\. Filters applied to a widget remain in effect if you navigate to a related service screen\.
+ **Groups**: To help you understand the types of operational issues across your organization, some widgets allow you to group data based on account, Region, and tag\.
+ **Reporting tag keys**: When you set up Explorer, you can specify up to five tag keys\. These keys help you group and filter data in Explorer\. If a specified key matches a key on a resource that generates an OpsItem, then the key and value are included in the OpsItems\. 
+ **Three modes of AWS account and AWS Region display**: Explorer includes the following display modes for OpsData and OpsItems in AWS accounts and AWS Regions:
  + *Single\-account/single\-Region*: This is the default view\. This mode allows users to view data and OpsItems from their own account and the current Region\.
  + *Single\-account/multiple\-Region*: This mode requires you to create one or more resource data syncs by using the Explorer **Settings** page\. A resource data sync aggregates OpsData from one or more Regions\. After you create a resource data sync, you can toggle which sync to use on the Explorer dashboard\. You can then filter and group data based on Region\.
  + *Multiple\-account/multiple\-Region*: This mode requires that your organization or company use [AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/) with **All features** turned on\. After you configure AWS Organizations in your computing environment, you can aggregate all account data in a management account\. You can then create resource data syncs so that you can filter and group data based on Region\. For more information about Organizations **All features** mode, see [Enabling All Features in Your Organization](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_org_support-all-features.html)\.
+ **Reporting**: You can export Explorer reports as comma separated value \(\.csv\) files to an Amazon Simple Storage Service \(Amazon S3\) bucket\. You receive an alert from Amazon Simple Notification Service \(Amazon SNS\) when an export is complete\. 

## How does Explorer relate to OpsCenter?<a name="Explorer-learn-more-OpsCenter"></a>

[Systems Manager OpsCenter](OpsCenter.md) provides a central location where operations engineers and IT professionals view, investigate, and resolve OpsItems related to AWS resources\. Explorer is a report hub where DevOps managers view aggregated summaries of their operations data, including OpsItems, across AWS Regions and accounts\. Explorer helps users discover trends and patterns and, if necessary, quickly resolve issues using Systems Manager Automation runbooks\.

OpsCenter setup is now integrated with Explorer Setup\. If you already set up OpsCenter, then Explorer automatically displays operations data, including aggregated information about OpsItems\. If you haven't set up OpsCenter, then you can use Explorer Setup to get started with both capabilities\. For more information, see [Getting started with Systems Manager Explorer and OpsCenter](Explorer-setup.md)\.

## What is OpsData?<a name="Explorer-learn-more-OpsData"></a>

OpsData is any operations data that is displayed in the Systems Manager Explorer dashboard\. Explorer retrieves OpsData from the following sources:
+ **Amazon Elastic Compute Cloud \(Amazon EC2\)**

  Data displayed in Explorer includes: total number of nodes, total number of managed and unmanaged nodes, and a count of nodes using a specific Amazon Machine Image \(AMI\)\. 
+ **Systems Manager OpsCenter**

  Data displayed in Explorer includes: a count of OpsItems by status, a count of OpsItems by severity, a count of open OpsItems across groups and across 30\-day time periods, and historical data of OpsItems over time\.
+ **Systems Manager Patch Manager**

  Data displayed in Explorer includes a count of noncompliant and critical noncompliant nodes\.
+ **AWS Trusted Advisor**

  Data displayed in Explorer includes: status of best practice checks for EC2 Reserved Instances in the areas of cost optimization, security, fault tolerance, performance, and service limits\.
+ **AWS Compute Optimizer**

  Data displayed in Explorer includes: a count of **Under provisioned** and **Over provisioned** EC2 instances, optimization findings, on\-demand pricing details, and recommendations for instance type and price\.
+ **AWS Support Center cases**

  Data displayed in Explorer includes: case ID, severity, status, created time, subject, service, and category\. 
+ **AWS Config**

  Data displayed in Explorer includes: overall summary of compliant and non\-compliant AWS Config rules, the number of compliant and non\-compliant resources, and specific details about each \(when you drill down into a non\-compliant rule or resource\)\. 
+ **AWS Security Hub**

  Data displayed in Explorer includes: overall summary of Security Hub findings, the number of each finding grouped by severity, and specific details about finding\.

**Note**  
To view AWS Trusted Advisor and AWS Support Center cases in Explorer, you must have either an Enterprise or Business account set up with AWS Support\.

You can view and manage OpsData sources from the Explorer **Settings** page\. For information about setting up and configuring services that populate Explorer widgets with OpsData, see [Setting up related services](Explorer-setup-related-services.md)\.

## Is there a charge to use Explorer?<a name="Explorer-learn-more-cost"></a>

Yes\. When you turn on the default rules for creating OpsItems during Integrated Setup, you initiate a process that automatically creates OpsItems\. Your account is charged based on the number of OpsItems created per month\. Your account is also charged based on the number of `GetOpsItem`, `DescribeOpsItem`, `UpdateOpsItem`, and `GetOpsSummary` API calls made per month\. Additionally, you can be charged for public API calls to other services that expose relevant diagnostic information\. For more information, see [AWS Systems Manager Pricing](http://aws.amazon.com/systems-manager/pricing/)\.

**Topics**
+ [What are the features of Explorer?](#Explorer-learn-more-features)
+ [How does Explorer relate to OpsCenter?](#Explorer-learn-more-OpsCenter)
+ [What is OpsData?](#Explorer-learn-more-OpsData)
+ [Is there a charge to use Explorer?](#Explorer-learn-more-cost)
+ [Getting started with Systems Manager Explorer and OpsCenter](Explorer-setup.md)
+ [Using Systems Manager Explorer](Explorer-using.md)
+ [Exporting OpsData from Systems Manager Explorer](Explorer-exporting-OpsData.md)
+ [Troubleshooting Systems Manager Explorer](Explorer-troubleshooting.md)