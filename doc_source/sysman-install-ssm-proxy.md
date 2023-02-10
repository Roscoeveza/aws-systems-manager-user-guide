# Configure SSM Agent to use a proxy for Windows Server instances<a name="sysman-install-ssm-proxy"></a>

The information in this topic applies to Windows Server instances created on or after November 2016 that do *not* use the Nano installation option\.

If your instance is a Windows Server 2008\-2012 R2 instance created *before* November 2016, then EC2Config processes AWS Systems Manager requests on your instance\. We recommend that you upgrade your existing instances to use the latest version of EC2Config\. By using the latest EC2Config installer, you install AWS Systems Manager Agent \(SSM Agent\) side\-by\-side with EC2Config\. This side\-by\-side version of SSM Agent is compatible with your instances created from earlier Windows Amazon Machine Images \(AMIs\) and allows you to use Systems Manager features published after November 2016\. For information about how to install the latest version of the EC2Config service, see [Install the latest version of EC2Config](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_Install.html) in the *Amazon EC2 User Guide for Windows Instances*\. If you don't upgrade to the latest version of EC2Config and use EC2Config to process Systems Manager requests, configure proxy settings for EC2Config\. For information about configuring EC2Config to use a proxy, see [Configure proxy settings for the EC2Config service](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2config-service.html#ec2config-proxy) in the *Amazon EC2 User Guide for Windows Instances*\. 

**Note**  
As of January 14, 2020, Windows Server 2008 is no longer supported for feature or security updates from Microsoft\. Legacy Amazon Machine Images \(AMIs\) for Windows Server 2008 and 2008 R2 still include version 2 of SSM Agent preinstalled, but Systems Manager no longer officially supports 2008 versions and no longer updates the agent for these versions of Windows Server\. In addition, [SSM Agent version 3\.0](ssm-agent-v3.md) might not be compatible with all operations on Windows Server 2008 and 2008 R2\. The final officially supported version of SSM Agent for Windows Server 2008 versions is 2\.3\.1644\.0\.

**To configure SSM Agent to use a proxy**

1. Using Remote Desktop or Windows PowerShell, connect to the instance that you would like to configure to use a proxy\. 

1. Run the following command block in PowerShell\. Replace *hostname* and *port* with the information about your proxy\.

   ```
   $serviceKey = "HKLM:\SYSTEM\CurrentControlSet\Services\AmazonSSMAgent"
   $keyInfo = (Get-Item -Path $serviceKey).GetValue("Environment")
   $proxyVariables = @("http_proxy=hostname:port", "https_proxy=hostname:port", "no_proxy=169.254.169.254")
   
   if ($keyInfo -eq $null) {
       New-ItemProperty -Path $serviceKey -Name Environment -Value $proxyVariables -PropertyType MultiString -Force
   } 
   else {
       Set-ItemProperty -Path $serviceKey -Name Environment -Value $proxyVariables
   }
   
   Restart-Service AmazonSSMAgent
   ```

After running the preceding command, you can review the SSM Agent logs to confirm the proxy settings were applied\. Entries in the logs look similar to the following\. For more information about SSM Agent logs, see [Viewing SSM Agent logs](sysman-agent-logs.md)\.

```
2020-02-24 15:31:54 INFO Getting IE proxy configuration for current user: The operation completed successfully.
2020-02-24 15:31:54 INFO Getting WinHTTP proxy default configuration: The operation completed successfully.
2020-02-24 15:31:54 INFO Proxy environment variables:
2020-02-24 15:31:54 INFO http_proxy: hostname:port
2020-02-24 15:31:54 INFO https_proxy: hostname:port
2020-02-24 15:31:54 INFO no_proxy: 169.254.169.254
2020-02-24 15:31:54 INFO Starting Agent: amazon-ssm-agent - v2.3.871.0
2020-02-24 15:31:54 INFO OS: windows, Arch: amd64
```

**To reset SSM Agent proxy configuration**

1. Using Remote Desktop or Windows PowerShell, connect to the instance to configure\.

1. If you connected using Remote Desktop, launch PowerShell as an administrator\.

1. Run the following command block in PowerShell\.

   ```
   Remove-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\AmazonSSMAgent -Name Environment
   Restart-Service AmazonSSMAgent
   ```

## SSM Agent proxy setting precedence<a name="ssm-agent-proxy-precedence"></a>

When configuring proxy settings for the SSM Agent on Windows Server instances, it's important to understand these settings are evaluated and applied to the agent configuration when the SSM Agent is started\. How you configure your proxy settings for a Windows Server instance can determine whether other settings might supersede your desired settings\.

**Important**  
SSM Agent communicates using the HTTPS protocol\. For this reason, you must configure the `HTTPS proxy` parameter by using one of the following settings options\.

SSM Agent proxy settings are evaluated in the following order\.

1. AmazonSSMAgent Registry settings \(`HKLM:\SYSTEM\CurrentControlSet\Services\AmazonSSMAgent`\)

1. System environment variables \(`http_proxy`, `https_proxy`, `no_proxy`\)

1. LocalSystem user account environment variables `http_proxy`, `https_proxy`, `no_proxy`\)

1. Internet Explorer settings \(`HTTP`, `secure`, `exceptions`\)

1. WinHTTP proxy settings \(`http=`, `https=`, `bypass-list=`\)

## SSM Agent proxy settings and Systems Manager services<a name="ssm-agent-proxy-services"></a>

If you configured the SSM Agent to use a proxy and are using AWS Systems Manager capabilities, such as Run Command and Patch Manager, that use PowerShell or the Windows Update client during their execution on Windows Server instances, configure additional proxy settings\. Otherwise, the operation might fail because proxy settings used by PowerShell and the Windows Update client aren't inherited from the SSM Agent proxy configuration\.

For Run Command, configure `WinINet` proxy settings on your Windows Server instances\. The `[System.Net.WebRequest]` commands provided are per\-session\. To apply these configurations to subsequent network commands that are run in Run Command, these commands must precede other PowerShell commands in the same `aws:runPowershellScript` plugin input\.

The following PowerShell commands return the current `WinINet` proxy settings, and apply your proxy settings to `WinINet`\.

```
[System.Net.WebRequest]::DefaultWebProxy

$proxyServer = "http://hostname:port"
$proxyBypass = "169.254.169.254"
$WebProxy = New-Object System.Net.WebProxy($proxyServer,$true,$proxyBypass)

[System.Net.WebRequest]::DefaultWebProxy = $WebProxy
```

For Patch Manager, configure system\-wide proxy settings so the Windows Update client can scan for and download updates\. We recommend that you use Run Command to run the following commands because they run on the SYSTEM account, and the settings apply system\-wide\. The following `netsh` commands return the current proxy settings, and apply your proxy settings to the local system\.

```
netsh winhttp show proxy

netsh winhttp set proxy proxy-server="hostname:port" bypass-list="169.254.169.254"
```

For more information about using Run Command, see [AWS Systems Manager Run Command](run-command.md)\.