# Azure Firewall Shutdown Schedule

Azure Firewall can be expensive ($1,317.65/month minimum for a standard firewall) if you are only using it for lab or testing purposes. You can shut down (to stop billing) and start back again an Azure firewall using PowerShell. You can also create a schedule to shut down Azure Firewall.

## Schedule Azure Firewall Automatic Shutdown Using the Portal

To schedule Azure Firewall automatic shutdown, you need to create an Azure Automation Account.

### 1. Create an Automation Account

- Log into the Azure portal and Type "Automated Accounts" in the search bar
- Create an Automation Account
- Select Subscription, Resource Group, Region and create a unique Name
- Leave all other settings by default
- Select "Review and Create" and click "create" to create the Automation Account
<img width="649" alt="Create automated account" src="https://github.com/user-attachments/assets/31f5ef7a-9402-4195-877b-e14f542df9dc" />
<img width="612" alt="Create automated account 3" src="https://github.com/user-attachments/assets/2fd6cc2e-a92f-4070-8529-7b4b27757fcb" />
<img width="630" alt="Create automated account 4" src="https://github.com/user-attachments/assets/99f18140-1dae-4440-acf0-590b747fc693" />

### 2. Configure Identity
- Go to the automation account created in the previous step and click on "Identity" under Account Settings
- Select "Role Assignment" under Permissions
- Add Role Assignment, select "Resource Group" under scope, select your subscription, resource group, and select the "Contributor" role and click "save"
<img width="809" alt="Create automated account 6" src="https://github.com/user-attachments/assets/65d96099-1b71-4f40-a890-4bab936d3c97" />
<img width="659" alt="Create automated account 8" src="https://github.com/user-attachments/assets/1860da0c-ad43-4dd4-badc-65df1e0b8f8a" />





### 3. Create a Schedule
- Open the automated account
- Go to Schedules under Shared Resources
- Create a new schedule with your desired configuration (for example, a recurring schedule that starts every day at 8:00pm local time). Set expiration to "No". With this configuration, you ensure that the firewall will shut down every day.
<img width="1733" alt="Create Schedule" src="https://github.com/user-attachments/assets/0bd2d233-6357-4987-8013-b93464096ff3" />


### 4. Create a Runbook

- Under "Process Automation" go to Runbooks and create a new runbook
- Give it a name
- Runbook Type: PowerShell
- On Runtime Environment select "Select from existing"
- Select PowerShell-7.2
- Click "Review + Create" and Create
- Paste the PowerShell script
- Click save and Publish
  
```
param(
    [string]$resourceGroupName = <"ResourceGroupName">,
    [string]$firewallName = <"FirewallName">
)

Import-Module Az

# Authenticate using the managed identity
Connect-AzAccount -Identity

# Stop/Deallocate the Firewall
$azfw = Get-AzFirewall -Name $firewallName -ResourceGroupName $resourceGroupName
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw

Write-Output "Azure Firewall $firewallName in resource group $resourceGroupName has been stopped."
```
<img width="622" alt="Runbook 1" src="https://github.com/user-attachments/assets/d0fb7e87-7bc3-4d2f-9b21-637c4298d768" />
<img width="850" alt="Runbook 2" src="https://github.com/user-attachments/assets/544899f8-7c3b-4c42-95d7-5041ff6e2257" />
<img width="1027" alt="RunbookScript" src="https://github.com/user-attachments/assets/8e3be773-6b4b-4d84-9ebd-295b34c669b1" />


### 5. Test Runbook

- Make sure the Azure Firewall is up and running
- Click on “Test Pane”
- Click “Start”
<img width="556" alt="Start runbook" src="https://github.com/user-attachments/assets/356ceca9-eed1-43f9-93eb-68a44fdd0dd3" />
<img width="979" alt="Runbook test" src="https://github.com/user-attachments/assets/9286a6c7-91e7-41c2-8e7f-00750e491006" />

### 6. Link a Schedule
- Go back to the runbook
- Select “Schedules”
- Add a schedule
- Select “Link a Schedule to Your Runbook”
- Select the schedule created in step 3
<img width="925" alt="Add Schedule" src="https://github.com/user-attachments/assets/a2109014-9678-4c33-b93a-04e2caa41ccc" />
<img width="706" alt="Link schedule" src="https://github.com/user-attachments/assets/86b6f116-2e3a-4103-8240-abcda432d5af" />
<img width="1020" alt="Select Schedule" src="https://github.com/user-attachments/assets/3864fa85-47fa-4608-ab1a-f46d9ac24004" />

### Verify Azure Firewall Status
1. Check Status
- Open Azure Firewall
- When Azure Firewall is deallocated, the subnet, Public IP, and Private IP are not visible
<img width="1298" alt="Firewall Off" src="https://github.com/user-attachments/assets/042fcb6a-b6f4-48d9-9fa6-93a497bbc062" />
<br>

2. Go to Metrics
- Select Firewall Health State
- Select last 30 minutes time range
- In the graphic, the solid line represents when the firewall is "On" or allocated, and the dotted line represents when the firewall is "Off" or deallocated
<img width="1747" alt="AFW Metrics" src="https://github.com/user-attachments/assets/1b407d19-6cef-4544-a346-17a831e90553" />

## More Information
### Azure Firewall FAQ
https://learn.microsoft.com/en-us/azure/firewall/firewall-faq#how-can-i-stop-and-start-azure-firewall
### Azure Automation
https://learn.microsoft.com/en-us/azure/automation/overview
### Azure Monitor
https://learn.microsoft.com/en-us/azure/azure-monitor/fundamentals/overview

