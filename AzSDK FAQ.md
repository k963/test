# Secure Dev Ops Kit for Azure – Onboarding FAQ 

Welcome to this article covering frequently asked questions about the Secure DevOps Kit for Azure (AzSDK).
We have organized these questions in 2 areas. The first section covers several questions and tips that should help with first-time use/onboarding for the toolkit and even PowerShell in some cases. The second section covers some specific technical questions about the Continuous Assurance feature. Please use the links below to navigate to the relevant section:
1. [Troubleshooting tips for first time AzSDK/PowerShell users ]()
    - [Should I run PowerShell ISE as administrator or regular user?]()
    - [Error message: “Running scripts is disabled on this system…”]()
    - [Error message: “WARNING: The version ‘3.x.y’ of module ‘AzureRM.Profile’ is currently in use. Retry the operation after closing…”]()
    - [Error message: “The property ‘Id’ cannot be found on this object. Verify that the property exists…”]()
    - [Message: “Warning :  Microsoft Azure PowerShell collects data about how users use PowerShell cmdlets…”]()
    - [Error message:“The Microsoft.Xyz provider is not registered for this subscription…” or “Please register to Microsoft.Security in order to view your security status…”]()
    - [Message: “Running AzSDK cmdlet using a generic (org-neutral) policy…”]()
2. [FAQ about Continuous Assurance]()
    - [What permission do I need to setup CA?]()
    - [Is it possible to setup CA if there is no OMS workspace?]()
    - [Which OMS workspace should I use for my team when setting up CA?]()
    - [Why does CA setup ask for resource groups?]()
    - [How can I find out if CA was previously setup in my subscription?]()
    - [How can I tell that my CA setup has worked correctly?]()
    - [Is providing resource groups mandatory?]()
    - [What if I need to change the resource groups after a few weeks?]()
    - [Do I need to also setup AzSDK OMS solution?]()
 

## Troubleshooting tips for first time AzSDK/PowerShell users

#### Should I run PowerShell ISE as administrator or regular user?
Please run PowerShell ISE as a regular user. The AzSDK has been thoroughly tested to run in normal user (non-elevated) mode. As much as possible, please do not launch your PS sessions in “Administrator” mode. There is nothing that the AzSDK does that needs elevated privileges on the local system. Even the installation command itself uses a ‘-Scope CurrentUser’ parameter internally.  

[back to top…]()
#### Error message: “Running scripts is disabled on this system…”
This is an indication that PowerShell script loading and execution is disabled on your machine. You will need to enable it before the AzSDK installation script (which itself is a PowerShell script) can run. 
```PowerShell
Get-ExecutionPolicy -Scope CurrentUser
```
If you run above command in the PS console, you will likely see that the policy level is either ‘Restricted’ or ‘Undefined’. For AzSDK cmdlets to run, it needs to be set to ‘RemoteSigned’.
To resolve this issue run the following command in your PS console:
```PowerShell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
The execution policy setting will be remembered and all future PS consoles opened in non-Admin (CurrentUser) mode will apply the ‘RemoteSigned’ execution policy.  

[back to top…]()
#### Error message: “WARNING: The version ‘3.x.y’ of module ‘AzureRM.Profile’ is currently in use. Retry the operation after closing…”
If you see multiple warning such as the above during setup, it is likely that one or more PowerShell instances are running and have AzureRm modules loaded which are conflicting with the AzSDK installation. In such as case, it is best to ensure that all PS sessions (including the current one) are closed and start the installer in a fresh PS ISE session.
In dire circumstances, you may need to close/kill all instances of PowerShell sessions running on your system (including VS if you have PS plugin installed). In that case, make sure you have saved any work in those sessions and then run the following in  a Windows Cmd console:
```
taskkill /im PowerShell_ise.exe & taskkill /im PowerShell.exe & taskkill /im PowerShellToolsProcessHost.exe
```  

[back to top…]()
#### Error message: “The property ‘Id’ cannot be found on this object. Verify that the property exists…”
This is typically caused by a version mis-match for underlying AzureRm PowerShell modules that are used by AzSDK! The AzSDK installation process will typically install AzureRm modules corresponding to the version that it depends on. However, it is possible that you also have a previous version of AzureRm on your machine.  
PowerShell works with the concept of ‘sessions’. Each PowerShell ISE or command prompt window is its own independent session. By default, when any cmdlet is run, PS will load the corresponding module (code) in the memory for that session. After that, subsequent references to cmdlets in that module will use the loaded module. Usually, the module loading follows a particular heuristic (based on version number, PS module path order, admin v. non-admin PS launch mode, etc.)  
The above error message is an indication that an AzSDK cmdlet is being run in a PowerShell session that already had an older version of AzureRm loaded in memory (which may be due to something as simple as doing a Login-AzureRmAccount and *then* installing AzSDK in that session). In most circumstances, one of the following remedies should work:
- Close the PS session and open a new one. In the new session, do an “Import-Module AzureRm -RequiredVersion 4.1.0” before running anything else (e.g., Login-AzureRmAccount)
- Close the PS session and open a new one. In the new session, do an “Import-Module AzSDK” before running anything else. (This will force-load the correct version of AzureRm that AzSDK needs.). 
  - If you suspect that you may have multiple versions of AzSDK itself installed, then use “import-module AzSDK -RequiredVersion 2.3.1” (June release).  
  
[back to top…]()
#### Message: “Warning :  Microsoft Azure PowerShell collects data about how users use PowerShell cmdlets…”
The AzSDK depends upon AzureRm PowerShell modules. AzureRm modules are created/maintained by the Azure product team and provide the core PowerShell libraries to interact with different Azure services. For example, you’d use the AzureRm Storage module to create/work with a storage account, etc.  
The AzSDK setup (when you use the “iwr …” command) installs the required version of AzureRm. It is possible that this is the first time your system is being setup for AzureRm. In such a situation, you will get a “data collection” related notice/warning from AzureRm. You can choose to “accept” or “decline” permission to collect data. The AzSDK functionality will not be affected by that.  

[back to top…]()
#### Error message: “The Microsoft.Xyz provider is not registered for this subscription…” or “Please register to Microsoft.Security in order to view your security status…” or “There was an error while configuring Automation Account. Failed to create AzSDK compliant Storage Account…”
The AzSDK cmdlets that provision Subscription Security or setup Continuous Assurance (CA) require the ability to create resources of certain types in your subscription. In the ARM model for Azure, resources for any particular type (e.g., Storage, Web App, SQL DB, etc.) are created by an ARM resource provider of that type. If an ARM resource provider for a particular type is not registered for a subscription (globally or for a specific region), the corresponding resource creation requests fail…sometimes with an explicit message in the console regarding the reason and at other times with a message indicating a ‘general failure’.  
**Resolution:**  
Use the following commands to query the status of resource provider (RP) registration in your subscription for the primary resource providers needed by AzSDK:
    
```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Automation | FT ResourceTypes, RegistrationState
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights | FT ResourceTypes, RegistrationState
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Security | FT ResourceTypes, RegistrationState
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage | FT ResourceTypes,  RegistrationState
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Scheduler | FT ResourceTypes,  RegistrationState
```
If any of the providers show up as ‘NotRegistered’ then run the corresponding register command from the below list:
```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Automation
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Security
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Scheduler
```
Once these providers are registered, you should be able to run the corresponding cmdlet fine. Note that if you had to register the providers after having run the Install-AzSDKContinuousAssurance then it may be better to uninstall CA completely using the Remove-AzSDKContinuousAssurance cmdlet and then rerun the installation using Install-AzSDKContinuousAssurance again.
We will work to automate this provider registration in future releases of the kit.  

[back to top…]()
#### Message: “”Running AzSDK cmdlet using a generic (org-neutral) policy…”
The PS console output for any AzSDK command you run (Set-AzSDKSubscriptionSecurity, Install-AzSDKContinuousAssurance, etc.) must show the following text (in yellow) just as it starts running:
> \> Running AzSDK cmdlet using MSIT policy...     <-- This is a good sign! 

If instead, you see the following text, it indicates that IT-specific policies did not get setup correctly for running AzSDK:

> \> Running AzSDK cmdlet using a generic (org-neutral) policy...    <-- This is *not* a good sign! IT policies are not being applied.

If this happens, close the current PowerShell ISE session and rerun the installation using the “iwr” command at http://aka.ms/azsdkdocs. If that does not fix the issue, email support (isrmazsdksup).  

[back to top…]()
## FAQ about “Continuous Assurance”

#### What permission do I need to setup CA?
You need to be “Owner” on the subscription.
This is required because, during CA setup, we add RBAC access to an Azure AD App (SPN) that’s utilized for running the “security scan” runbooks in Azure Automation. Only an “Owner” for a subscription has the right to change subscription RBAC.  

[back to top…]()
#### Is it possible to setup CA if there is no OMS workspace?
No. The intent of CA is to scan regularly and be able to monitor the outcomes for security drift. Out of the box, AzSDK CA uses OMS for the monitoring capabilities. (Basically, AzSDK sends control evaluation results to a connected OMS workspace.)  

[back to top…]()
#### Which OMS workspace should I use for my team when setting up CA?
Check with your service offering leader/org’s cloud lead.
You would typically use one of the following options:
- Utilize a workspace is shared across a related set of services from your SO
- Create a new OMS workspace and use that exclusively for your service (‘free’ tier is OK for just AzSDK use cases)
- Utilize an IT-wide shared workspace  

[back to top…]()
#### Why does CA setup ask for resource groups?
CA supports scanning a subscription and a set of cloud resources that make up an application. These cloud resources are assumed to be hosted within one or more resource groups. A typical CA installation takes both the subscription info and resource groups info.  

[back to top…]()
#### How can I find out if CA was previously setup in my subscription?
You can check using the “Get-AzSDKContinuousAssurance” cmdlet. If CA is correctly setup, it will show a list of artifacts that are deployed during CA setup (e.g., Automation Account, Connections, Schedules, OMS workspace info, etc.). If CA has not been setup, you will see a message indicating so.  

[back to top…]()
#### How can I tell that my CA setup has worked correctly?
There are 2 important things you should do to verify this:
Run the Get-AzSDKContinuousAssurance and confirm that the output tells you as in the previous question.
Verify that the runbooks have actually started scanning your subscription and resource groups. You can check for this in OMS.  

[back to top…]()
#### Is providing resource groups mandatory?
We would like teams to, at a minimum, provide the list of resource groups that cover the most critical components of their application. It is unlikely that you will just have a subscription but no important resources inside it. Still, if you absolutely can’t provide a resource group, then specify the “AzSDKRG” as the resource group when setting up CA. This resource group is used internally by AzSDK for CA so is guaranteed to exist in each subscription.  
Note that you can also provide “\*” as an option (to request CA to scan all resource groups). However, there are some hard limits for automation runbook run times… so this option may not quite work if you have more than 100 resources in the subscription. If you do provide “\*” as an option, CA will automatically grow/shrink the resource group list as you add/delete resource groups in your subscription.  

[back to top…]()
#### What if I need to change the resource groups after a few weeks?
That is easy! Just run the Update-AzSDKContinuousAssurance cmdlet with the new list of resource groups you would like monitored.  

[back to top…]()
#### Do I need to also setup AzSDK OMS solution?
This part is not mandatory for CA itself to work.
However, setting up the AzSDK OMS solution is recommended as it will help you get a richer view of continuous assurance for your subscription and resources as scanned by CA. Secondly, it will give you several out-of-box artefacts to assist with security monitoring for your service. For instance, you will start getting email alerts if any of the high or critical severity controls from AzSDK fail in your service.  

[back to top…]()
