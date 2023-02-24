# Sending SSM Agent logs to CloudWatch Logs<a name="monitoring-ssm-agent"></a>

AWS Systems Manager Agent \(SSM Agent\) is Amazon software that runs on your EC2 instances, edge devices, on\-premises servers, and virtual machines \(VMs\) that are configured for Systems Manager\. SSM Agent processes requests from the Systems Manager service in the cloud and configures your machine as specified in the request\. For more information about SSM Agent, see [Working with SSM Agent](ssm-agent.md)\.

In addition, using the following steps, you can configure SSM Agent to send log data to Amazon CloudWatch Logs\. 

**Before you begin**  
Create a log group in CloudWatch Logs\. For more information, see [Getting started with CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_GettingStarted.html) in the *Amazon CloudWatch Logs User Guide*\.

**To configure SSM Agent to send logs to CloudWatch**

1. Log into a node and locate the following file:

**Linux**  
On most Linux node types: `/etc/amazon/ssm/seelog.xml.template`\.

   On Ubuntu Server 20\.10 STR & 20\.04, 18\.04, and 16\.04 LTS: `/snap/amazon-ssm-agent/current/seelog.xml.template`

**macOS**  
`/opt/aws/ssm/seelog.xml.template`

**Windows**  
`%ProgramFiles%\Amazon\SSM\seelog.xml.template`

1. Change the file name from `seelog.xml.template` to `seelog.xml`
**Note**  
On Ubuntu Server 20\.10 STR & 20\.04, 18\.04, and 16\.04 LTS, the file `seelog.xml` must be created in the directory `/etc/amazon/ssm/`\. You can create this directory and file by running the following commands\.  

   ```
   sudo mkdir -p /etc/amazon/ssm
   ```

   ```
   sudo cp -pr /snap/amazon-ssm-agent/current/* /etc/amazon/ssm
   ```

   ```
   sudo cp -p /etc/amazon/ssm/seelog.xml.template /etc/amazon/ssm/seelog.xml
   ```

1. Open the `seelog.xml` file in a text editor, and locate the following section\.

------
#### [ Linux and macOS ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" filename="/var/log/amazon/ssm/amazon-ssm-agent.log" maxsize="30000000" maxrolls="5"/>
      <filter levels="error,critical" formatid="fmterror">
         <rollingfile type="size" filename="/var/log/amazon/ssm/errors.log" maxsize="10000000" maxrolls="5"/>
      </filter>
   </outputs>
   ```

------
#### [ Windows ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" maxrolls="5" maxsize="30000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\amazon-ssm-agent.log"/>
      <filter formatid="fmterror" levels="error,critical">
         <rollingfile type="size" maxrolls="5" maxsize="10000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\errors.log"/>
      </filter>
   </outputs>
   ```

------

1. Edit the file, and add a *custom name* element after the closing </filter> tag\. In the following example, the custom name as been specified as `cloudwatch_receiver`\.

------
#### [ Linux and macOS ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" filename="/var/log/amazon/ssm/amazon-ssm-agent.log" maxsize="30000000" maxrolls="5"/>
      <filter levels="error,critical" formatid="fmterror">
         <rollingfile type="size" filename="/var/log/amazon/ssm/errors.log" maxsize="10000000" maxrolls="5"/>
      </filter>
      <custom name="cloudwatch_receiver" formatid="fmtdebug" data-log-group="your-CloudWatch-log-group-name"/>
   </outputs>
   ```

------
#### [ Windows ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" maxrolls="5" maxsize="30000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\amazon-ssm-agent.log"/>
      <filter formatid="fmterror" levels="error,critical">
         <rollingfile type="size" maxrolls="5" maxsize="10000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\errors.log"/>
      </filter>
      <custom name="cloudwatch_receiver" formatid="fmtdebug" data-log-group="your-CloudWatch-log-group-name"/>
   </outputs>
   ```

------

1. Save your changes, and then restart SSM Agent or the node\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**, and then choose the name of your log group\.
**Tip**  
The log stream for SSM Agent log file data is organized by node ID\.