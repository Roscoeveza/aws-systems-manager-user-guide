# Install SSM Agent on Ubuntu Server 16\.04 and 14\.04 32\-bit<a name="agent-install-ubuntu-32"></a>

In most cases, the Amazon Machine Images \(AMIs\) Ubuntu Server 16\.04 that are provided by AWS come with AWS Systems Manager Agent \(SSM Agent\) preinstalled by default\. For more information, see [Amazon Machine Images \(AMIs\) with SSM Agent preinstalled](ami-preinstalled-agent.md)\.

In the event that SSM Agent isn’t preinstalled on a new Ubuntu Server 16\.04 instance, you are installing on Ubuntu Server 14\.04, or you need to manually reinstall the agent, use the information on this page to help you\.

## Quick installation commands for SSM Agent on Ubuntu Server 16\.04 and 14\.04 32\-bit \(deb\)<a name="quick-install-ub-16-14-32-bit"></a>

Use the following steps to manually install SSM Agent on a single instance\. This procedure uses globally available installation files\. 

**To install SSM Agent on Ubuntu Server 16\.04 and 14\.04 32\-bit \(deb\) using quick copy and paste commands**

1. Connect to your Ubuntu Server instance using your preferred method, such as SSH\.

1. Run the following command to create a temporary directory on the instance\.

   ```
   mkdir /tmp/ssm
   ```

1. Change to the temporary directory\.

   ```
   cd /tmp/ssm
   ```

1. Run the following commands\.
**Note**  
Even though URLs in the following commands include an `ec2-downloads-windows` directory, these are the correct global installation files for Ubuntu Server 16\.04 and 14\.04 32\-bit\. 

   ```
   wget https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/debian_386/amazon-ssm-agent.deb
   ```

   ```
   sudo dpkg -i amazon-ssm-agent.deb
   ```

1. \(Recommended\) Run one of the following commands to determine if SSM Agent is running\.   
Ubuntu Server 16\.04  

   ```
   sudo systemctl status amazon-ssm-agent
   ```  
Ubuntu Server 14\.04  

   ```
   sudo status amazon-ssm-agent
   ```

   In most cases, the command reports that the agent is running\.

   In rare cases, the command reports that the agent is installed but not running, as shown in the following example\.

1. Run one of the following commands to start the service if the previous command returned amazon\-ssm\-agent is stopped, inactive, or disabled\.

   Ubuntu Server 16\.04:

   ```
   sudo systemctl enable amazon-ssm-agent
   ```

   Ubuntu Server 14\.04:

   ```
   sudo start amazon-ssm-agent
   ```

## Create custom installation commands for SSM Agent on Ubuntu Server 16\.04 and 14\.04 32\-bit \(deb\) in your Region<a name="custom-url-ub-16-14-32-bit"></a>

When you install SSM Agent on multiple instances using a script or template, we recommended using installation files that are stored in the AWS Region you're working in\. 

For the following commands, we provide examples that use a publicly accessible S3 bucket in the US East \(Ohio\) Region \(`us-east-2`\)\. 

**Tip**  
You can also replace a global URL in the procedure [Quick installation commands for SSM Agent on Ubuntu Server 16\.04 and 14\.04 32\-bit \(deb\)](#quick-install-ub-16-14-32-bit) earlier in this topic with a custom Regional URL you construct\.

In the following command, replace *region* with your own information\. For a list of supported *region* values, see the **Region** column in [Systems Manager service endpoints](https://docs.aws.amazon.com/general/latest/gr/ssm.html#ssm_region) in the *Amazon Web Services General Reference*\.

```
wget https://s3.region.amazonaws.com/amazon-ssm-region/latest/debian_386/amazon-ssm-agent.deb
```

```
sudo dpkg -i amazon-ssm-agent.deb
```

See the following example\.

```
wget https://s3.us-east-2.amazonaws.com/amazon-ssm-us-east-2/latest/debian_386/amazon-ssm-agent.deb
```

```
sudo dpkg -i amazon-ssm-agent.deb
```