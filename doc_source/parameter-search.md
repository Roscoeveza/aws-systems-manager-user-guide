# Searching for Systems Manager parameters<a name="parameter-search"></a>

When you have a lot of parameters in your account, it can be difficult to find information about a single or several parameters at a time\. In this case, you can use filter tools to search for the ones you need information about, according to search criteria you specify\. You can use the AWS Systems Manager console, the AWS Command Line Interface \(AWS CLI\), the AWS Tools for PowerShell, or the [DescribeParameters](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_DescribeParameters.html) API to search for parameters\.

**Topics**
+ [Search for a parameter \(console\)](#parameter-search-console)
+ [Search for a parameter \(AWS CLI\)](#parameter-search-cli)

## Search for a parameter \(console\)<a name="parameter-search-console"></a>

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Parameter Store**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[The menu icon\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Parameter Store**\.

1. Select in the search box and choose how you want to search\. For example, Type or Name\.

1. Provide information for the search type you selected\. For example:
   + If you're searching by Type, choose from String, StringList, or SecureString\.
   + If you're searching by Name, choose contains, equals, or begins\-with, and then enter all or part of a parameter name\.
**Note**  
In the console, the default search type for Name is contains\.

1. Press Enter\.

The list of parameters is updated with the results of your search\.

## Search for a parameter \(AWS CLI\)<a name="parameter-search-cli"></a>

Use the `describe-parameters` command to view information about one or more parameters in the AWS CLI\. 

The following examples demonstrate various options you can use to view information about the parameters in your AWS account\. For more information about these options, see [describe\-parameters](https://docs.aws.amazon.com/cli/latest/reference/ssm/describe-parameters.html) in the *AWS Command Line Interface User Guide*\.

1. Install and configure the AWS Command Line Interface \(AWS CLI\), if you haven't already\.

   For information, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)\.

1. Replace the sample values in the following commands with values reflecting parameters that have been created in your account\.

------
#### [ Linux & macOS ]

   ```
   aws ssm describe-parameters \
       --parameter-filters "Key=Name,Values=MyParameterName"
   ```

------
#### [ Windows ]

   ```
   aws ssm describe-parameters ^
       --parameter-filters "Key=Name,Values=MyParameterName"
   ```

------
**Note**  
For `describe-parameters`, the default search type for Name is `Equals`\. In your parameter filters, specifying `"Key=Name,Values=MyParameterName"` is the same as specifying `"Key=Name,Option=Equals,Values=MyParameterName"`\.

   ```
   aws ssm describe-parameters \
       --parameter-filters "Key=Name,Option=Contains,Values=Product"
   ```

   ```
   aws ssm describe-parameters \
       --parameter-filters "Key=Type,Values=String"
   ```

   ```
   aws ssm describe-parameters \
       --parameter-filters "Key=Path,Values=/Production/West"
   ```

   ```
   aws ssm describe-parameters \
       --parameter-filters "Key=Tier,Values=Standard"
   ```

   ```
   aws ssm describe-parameters \
       --parameter-filters "Key=tag:tag-key,Values=tag-value"
   ```

   ```
   aws ssm describe-parameters \
       --parameter-filters "Key=KeyId,Values=key-id"
   ```
**Note**  
In the last example, *key\-id* represents the ID of an AWS Key Management Service \(AWS KMS\) key used to encrypt a `SecureString` parameter created in your account\. Alternatively, you can enter **alias/aws/ssm** to use the default AWS KMS key for your account\. For more information, see [Create a SecureString parameter \(AWS CLI\)](param-create-cli.md#param-create-cli-securestring)\.

   If successful, the command returns output similar to the following\.

   ```
   {
       "Parameters": [
           {
               "Name": "/Production/West/Manager",
               "Type": "String",
               "LastModifiedDate": 1573438580.703,
               "LastModifiedUser": "arn:aws:iam::111122223333:user/Mateo.Jackson",
               "Version": 1,
               "Tier": "Standard",
               "Policies": []
           },
           {
               "Name": "/Production/West/TeamLead",
               "Type": "String",
               "LastModifiedDate": 1572363610.175,
               "LastModifiedUser": "arn:aws:iam::111122223333:user/Mateo.Jackson",
               "Version": 1,
               "Tier": "Standard",
               "Policies": []
           },
           {
               "Name": "/Production/West/HR",
               "Type": "String",
               "LastModifiedDate": 1572363680.503,
               "LastModifiedUser": "arn:aws:iam::111122223333:user/Mateo.Jackson",
               "Version": 1,
               "Tier": "Standard",
               "Policies": []
           }
       ]
   }
   ```