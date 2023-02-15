# Querying inventory data from multiple Regions and accounts<a name="systems-manager-inventory-query"></a>

AWS Systems Manager Inventory integrates with Amazon Athena to help you query inventory data from multiple AWS Regions and AWS accounts\. Athena integration uses resource data sync so that you can view inventory data from all of your managed nodes on the **Detailed View** page in the AWS Systems Manager console\.

**Important**  
This feature uses AWS Glue to crawl the data in your Amazon Simple Storage Service \(Amazon S3\) bucket, and Amazon Athena to query the data\. Depending on how much data is crawled and queried, you can be charged for using these services\. With AWS Glue, you pay an hourly rate, billed by the second, for crawlers \(discovering data\) and ETL jobs \(processing and loading data\)\. With Athena, you're charged based on the amount of data scanned by each query\. We encourage you to view the pricing guidelines for these services before you use Amazon Athena integration with Systems Manager Inventory\. For more information, see [Amazon Athena pricing](https://aws.amazon.com/athena/pricing/) and [AWS Glue pricing](https://aws.amazon.com/glue/pricing/)\.

You can view inventory data on the **Detailed View** page in all AWS Regions where Amazon Athena is available\. For a list of supported Regions, see [Amazon Athena Service Endpoints](https://docs.aws.amazon.com/general/latest/gr/athena.html#athena_region) in the *Amazon Web Services General Reference*\.

**Before you begin**  
Athena integration uses resource data sync\. You must set up and configure resource data sync to use this feature\. For more information, see [Configuring resource data sync for Inventory](sysman-inventory-datasync.md)\.

Also, be aware that the **Detailed View** page displays inventory data for the *owner* of the central Amazon S3 bucket used by resource data sync\. If you aren't the owner of the central Amazon S3 bucket, then you won't see inventory data on the **Detailed View** page\.

## Configuring access<a name="systems-manager-inventory-query-iam"></a>

Before you can query and view data from multiple accounts and Regions on the **Detailed View** page in the Systems Manager console, you must configure your IAM entity with permission to view the data\.

If the inventory data is stored in an Amazon S3 bucket that uses AWS Key Management Service \(AWS KMS\) encryption, you must also configure your IAM entity and the `Amazon-GlueServiceRoleForSSM` service role for AWS KMS encryption\. 

**Topics**
+ [Configuring your IAM entity to access the Detailed View page](#systems-manager-inventory-query-iam-user)
+ [\(Optional\) Configure permissions for viewing AWS KMS encrypted data](#systems-manager-inventory-query-kms)

### Configuring your IAM entity to access the Detailed View page<a name="systems-manager-inventory-query-iam-user"></a>

The following describes the minimum permissions required to view inventory data on the **Detailed View** page\.

The `AWSQuicksightAthenaAccess` managed policy

The following `PassRole` and additional required permissions block

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowGlue",
            "Effect": "Allow",
            "Action": [
                "glue:GetCrawler",
                "glue:GetCrawlers",
                "glue:GetTables",
                "glue:StartCrawler",
                "glue:CreateCrawler"
            ],
            "Resource": "*"
        },
        {
            "Sid": "iamPassRole",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:PassedToService": "glue.amazonaws.com"
                }
            }
        },
        {
            "Sid": "iamRoleCreation",
           "Effect": "Allow",
            "Action": [
                "iam:CreateRole",
                "iam:AttachRolePolicy"
            ],
            "Resource": "arn:aws:iam::account_ID:role/*"
        },
        {
            "Sid": "iamPolicyCreation",
            "Effect": "Allow",
            "Action": "iam:CreatePolicy",
            "Resource": "arn:aws:iam::account_ID:policy/*"
        }
    ]
}
```

\(Optional\) If the Amazon S3 bucket used to store inventory data is encrypted by using AWS KMS, you must also add the following block to the policy\.

```
{
    "Effect": "Allow",
    "Action": [
        "kms:Decrypt"
    ],
    "Resource": [
        "arn:aws:kms:Region:account_ID:key/key_ARN"
    ]
}
```

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

### \(Optional\) Configure permissions for viewing AWS KMS encrypted data<a name="systems-manager-inventory-query-kms"></a>

If the Amazon S3 bucket used to store inventory data is encrypted by using the AWS Key Management Service \(AWS KMS\), you must configure your IAM entity and the **Amazon\-GlueServiceRoleForSSM** role with `kms:Decrypt` permissions for the AWS KMS key\. 

**Before you begin**  
To provide the `kms:Decrypt` permissions for the AWS KMS key, add the following policy block to your IAM entity:

```
{
    "Effect": "Allow",
    "Action": [
        "kms:Decrypt"
    ],
    "Resource": [
        "arn:aws:kms:Region:account_ID:key/key_ARN"
    ]
}
```

If you haven't done so already, complete that procedure and add `kms:Decrypt` permissions for the AWS KMS key\.

Use the following procedure to configure the **Amazon\-GlueServiceRoleForSSM** role with `kms:Decrypt` permissions for the AWS KMS key\. 

**To configure the **Amazon\-GlueServiceRoleForSSM** role with `kms:Decrypt` permissions**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, and then use the search field to locate the **Amazon\-GlueServiceRoleForSSM** role\. The **Summary** page opens\.

1. Use the search field to find the **Amazon\-GlueServiceRoleForSSM** role\. Choose the role name\. The **Summary** page opens\.

1. Choose the role name\. The **Summary** page opens\.

1. Choose **Add inline policy**\. The **Create policy** page opens\.

1. Choose the **JSON** tab\.

1. Delete the existing JSON text in the editor, and then copy and paste the following policy into the JSON editor\. 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "kms:Decrypt"
               ],
               "Resource": [
                   "arn:aws:kms:Region:account_ID:key/key_ARN"
               ]
           }
       ]
   }
   ```

1. Choose **Review policy**

1. On the **Review Policy** page, enter a name in the **Name** field\.

1. Choose **Create policy**\.

## Querying data on the inventory detailed view page<a name="systems-manager-inventory-query-detail-view"></a>

Use the following procedure to view inventory data from multiple AWS Regions and AWS accounts on the Systems Manager Inventory **Detailed View** page\.

**Important**  
The Inventory **Detailed View** page is only available in AWS Regions that offer Amazon Athena\. If the following tabs aren't displayed on the Systems Manager Inventory page, it means Athena isn't available in the Region and you can't use the **Detailed View** to query data\.  

![\[Displaying Inventory Dashboard | Detailed View | Settings tabs\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/inventory-detailed-view-for-error.png)

**To view inventory data from multiple Regions and accounts in the AWS Systems Manager console**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Inventory**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Inventory** in the navigation pane\.

1. Choose the **Detailed View** tab\.  
![\[Accessing the AWS Systems Manager Inventory Detailed View page\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/inventory-detailed-view.png)

1. Choose the resource data sync for which you want to query data\.  
![\[Displaying inventory data in the AWS Systems Manager console\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/inventory-display-data.png)

1. In the **Inventory Type** list, choose the type of inventory data that you want to query, and then press Enter\.  
![\[Choosing an inventory type in the AWS Systems Manager console\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/inventory-type.png)

1. To filter the data, choose the Filter bar, and then choose a filter option\.  
![\[Filtering inventory data in the AWS Systems Manager console\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/inventory-filter.png)

You can use the **Export to CSV** button to view the current query set in a spreadsheet application such as Microsoft Excel\. You can also use the **Query History** and **Run Advanced Queries** buttons to view history details and interact with your data in Amazon Athena\.

### Editing the AWS Glue crawler schedule<a name="systems-manager-inventory-glue-settings"></a>

AWS Glue crawls the inventory data in the central Amazon S3 bucket twice daily, by default\. If you frequently change the types of data to collect on your nodes then you might want to crawl the data more frequently, as described in the following procedure\.

**Important**  
AWS Glue charges your AWS account based on an hourly rate, billed by the second, for crawlers \(discovering data\) and ETL jobs \(processing and loading data\)\. Before you change the crawler schedule, view the [AWS Glue pricing](https://aws.amazon.com/glue/pricing/) page\.

**To change the inventory data crawler schedule**

1. Open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. In the navigation pane, choose **Crawlers**\.

1. In the crawlers list, choose the option next to the Systems Manager Inventory data crawler\. The crawler name uses the following format:

   `AWSSystemsManager-DOC-EXAMPLE-BUCKET-Region-account_ID`

1. Choose **Action**, and then choose **Edit crawler**\.

1. In the navigation pane, choose **Schedule**\.

1. In the **Cron expression** field, specify a new schedule by using a cron format\. For more information about the cron format, see [Time\-Based Schedules for Jobs and Crawlers](https://docs.aws.amazon.com/glue/latest/dg/monitor-data-warehouse-schedule.html) in the *AWS Glue Developer Guide*\.

**Important**  
You can pause the crawler to stop incurring charges from AWS Glue\. If you pause the crawler, or if you change the frequency so that the data is crawled less often, then the Inventory **Detailed View** might display data that isn't current\.