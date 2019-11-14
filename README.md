https://github.com/jbinko/AZ-300-Microsoft-Azure-Architect-Technologies

# Deploy and Configure Infrastructure (25-30%)

## Analyze resource utilization and consumption

**Configure diagnostic settings on resources**

- Diagnostic logs provide rich, frequent data about the operation of an Azure resource
- Two types of diagnostics logs:
  - Tenant logs - these logs come from tenant-level services that exist outside of an Azure subscription, such as Azure Active Directory logs.
  - Resource logs - these logs come from Azure services that deploy resources within an Azure subscription, such as Network Security Groups or Storage Accounts.
- These logs differ from Activity logs, which provide insight into the operations that were performed on resources using Resource Manager, for example, creating a virtual machine or deleting a logic app.
- Also different guest OS-level diagnostic logs.
- Captured by Azure platform itself.
- Diagnostics logs can be:
  - Stored in Storage account (can specify retention time)
  - Streamed to Event Hubs for 3rd party ingestion
  - Stored and analyzed Azure Monitor
- Which diagnostic data is captured can be configured

[Overview of Azure Diagnostic Logs](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-logs-overview)

**Create baseline for resources**

- Can extract ARM template for resources that is a baseline

**Create and rest alerts**

- Azure Monitor supports Metric Alerts to notify if some metric crosses a threshold.
- Select resource, condition and actions
- Metric Alert with Dynamic Thresholds detection leverages advanced machine learning (ML) to learn metrics' historical behavior, identify patterns and anomalies that indicate possible service issues
- Metric alerts can also be created on logs

![](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/media/alerts-overview/azure-monitor-alerts.svg)

**Analyze alerts across subscription**

- Azure Monitor collects all alerts in it
- Alerts have 3 states:
  - New — detected, not yet reviewed
  - Acknowledged — reviewed and working on
  - Closed — issue resolved

**Analyze metrics across subscription**

- Azure Monitor collects and aggregates data from a variety of sources into a common data platform where it can be used for analysis, visualization, and alerting

[](https://www.notion.so/146a472dbf404c978c4243942a8fd8a3#ffec956237e1429dabb07078dc88442c)

- Metrics are numerical values that describe some aspect of a system at a particular point in time
- Metrics can be collected from:
  - **Platform metrics** are created by Azure resources and give you visibility into their health and performance. Each type of resource creates a distinct set of metrics without any configuration required. Platform metrics are collected from Azure resources at one-minute frequency unless specified otherwise in the metric's definition.
  - **Guest OS metrics** are collected from the guest operating system of a virtual machine. Enable guest OS metrics for Windows virtual machines with Windows Diagnostic Extension (WAD) and for Linux virtual machines with InfluxData Telegraf Agent.
  - **Application metrics** are created by Application Insights for your monitored applications and help you detect performance issues and track trends in how your application is being used. This includes such values as Server response time and Browser exceptions.
  - **Custom metrics** are metrics that you define in addition to the standard metrics that are automatically available. You can define custom metrics in your application that's monitored by Application Insights or create custom metrics for an Azure service using the custom metrics API.

**Create action groups**

- An action group is a collection of notification preferences defined by the owner of an Azure subscription.
- Action group consists of a number of actions, such as send email/SMS or call webhook or logic app or function app

**Monitor for unused resources**

- Azure Advisor gives recommendations on:
  - High-availability
  - Security
  - Performance
  - Cost — such as unused resource

**Monitor spend**

- Pricing calculator allows to estimate spending before allocating
- Subscriptions page shows cost breakdown and burn rate per subscription
- Cost analysis in subscription details shows cost breakdown by resource
- Tags can be used to group billing data

**Report on spend**

- Invoices can be downloaded from Azure as pdf or csv

**Utilize Log Search query functions**

- Azure Monitor uses a query language to retrieve and analyze log data in variety of ways

## Create and configure storage accounts

**Configure network access to the storage account**

- Configure storage account to deny traffic from all networks (including internet). Grant from specific networks. Can allow access from specific IP ranges.
- Networks rules affect all protocols
- Applied network rules affect all requests, except VM disk traffic. Using SAS tokens also requires exception rule.
- VNet service endpoints allow you to secure Azure service resource to only your VNET. Also enables traffic to be routed over Azure backbone

**Create and configure storage account**

- Storage account contains all Azure Storage data objects: blobs, files, queues, tables, and disks.

  az storage account create \
   --name <account-name> \
   --resource-group storage-resource-group \
   --location westus \
   --sku Standard_RAGRS \
   --kind StorageV2

**Generate shared access signature**

- A shared access signature (SAS) is a URI that grants restricted access rights to Azure Storage resources
- Can be either account-level or service-level
- Can be fine-grained to access type (read, update, delete, etc.), service (blob, queue, etc.)
- Can be created from Azure Storage Explorer or Portal

**Install and use Azure Storage Explorer**

- Easily manage Storage anywhere from Windows, macOS, and Linux
- Create, delete, view, and edit storage resources
- View and edit Blob, Queue, Table, File, Cosmos DB storage and Data Lake Storage
- Obtain shared access signature (SAS) keys

**Manage access keys**

- Access can authorized with **Shared Key** or **Shared access signature**
- **Shared Key**
  - Should be viewed as root password to the storage account
  - This authorization relies on your account access keys to produce an encrypted signature string that is passed on the request in the Authorization header.
  - Storage account has primary and secondary access key, both which grant admin access to the account and resources
- **Shared access signature**
  - Grant access without sharing your account keys
  - More granular control over the type of access:
    - The interval over which the SAS is valid, including the start time and the expiry time.
    - The permissions granted by the SAS. E.g. read and write to blob
    - An optional IP address or range of IP addresses from which Azure Storage will accept the SAS
    - The protocol. E.g. HTTPS only
  - Two types of SAS:
    - Service SAS. Access only to one service: blob, queue, table or file service
    - Account SAS. Access to one or more storage services

**Monitor activity log by using Azure Monitor logs**

- The Azure Activity Log provides insight into subscription-level events that have occurred in Azure
- There is a single Activity Log for each Azure subscription
- Events are stored for 90 days. Can be stored longer by collecting it in Azure Monitor or exporting to storage or Event Hubs.
- Enable: Log Analytics Workspace → Workspace Data Sources → Azure Activity Log

**Implement Azure storage replication**

- Locally redundant storage (LRS) – Replicated within data center
- Zone-redundant storage (ZRS) – Replicated across 3 storage clusters in a single region
- Geo-redundant storage (GRS) – Replicated to a secondary region, but data is not readable from there unless MS initiates a failover from the primary to secondary
- Read-access geo-redundant storage (RA-GRS) – Replicated to another region, and data is readable from that another region
- Recovery Point Objective (RPO) – In GRS and RA-GRS if a failover is initiated, the number of minutes of potential data that's lost is known as the RPO
- Recovery Time Objective (RTP) – Measure of how long it takes to perform the failover and bring storage back online

## Create and configure a Virtual Machine (VM) for Windows and Linux

**Configure high availability**

- Protects against
  - Unplanned Hardware Maintenance Event – Azure migrates VMs from failing hardware to healthy. VM preserving operation that pauses the VM for a short time
  - An Unexpected Downtime – Hardware fails unexpectedly. Azure migrates VM to a healthy machine. VMs experience downtime (reboot) and some cases loss of temporary drive
  - Planned Maintenance events – Periodic updates to underlying platform. Rare occasions require reboot of VM
- Best practices for high availability
  - Group two or move VMs into an availability set → ensures at least one VM is available and meets 99.95% SLA. VMs are assigned into update and fault domains.
  - Use managed disks for VMs in availability sets. Ensures disks are isolated from each other. Or use separate storage accounts for unmanaged disks.
  - Use scheduled events to proactively respond to VM impacting events
  - Use a load balancer with an availability set
  - Use availability zones as an alternative to availability sets – Provides 99,99% SLA

**Configure monitoring, networking, storage, and virtual machine size**

- Monitoring
  - Can enable boot diagnostics, performance metrics
  - VM metrics are automatically collected for the host and viewed in portal
  - More granular and VM-specific metrics require Azure diagnostics extension on the VM
  - Can manage updates and patches directly from VM
- Networking
  - VMs can have multiple NICs. Can be added and removed entire lifecycle
  - Public / Private IP, Static / Dynamic
  - VNET, Subnet, NSG, LB
- Storage
  - For each VM two disk are automatically created:
    - OS disk – Caching config of the OS disk is optimized. Should not be used for data
    - Temporary disk – Highly performant SSD disk for temporary data processing. Size determined by VM size
  - Two types of data disks:
    - Standard disk – cost-effective HDD
    - Premium disk – high-performance, low-latency SSD
  - Can take disk snapshots. VM can be restored from snapshot

[VM sizes](https://www.notion.so/7a1e86e80efb45e488a798edb3ed2df0)

**Deploy and configure scale sets**

- Create and manage a group of identical, load balanced, and autoscaling VMs
- Can scale VMs in the scale set manually, or rules to autoscale on resource usage like CPU, memory or network traffic

### Automate deployment of VMs

**Modify Azure Resource Manager template**

- Export template:
  - from resource group or resource
  - from deployment or from history

**Configure location of new VMs**

    "location": "EastUS"

**Configure VHD template**

**Deploy from template**

- From portal: Create a resource —> Template deployment
- From cli:

        az group create --name ExampleGroup --location "Central US"
        az group deployment create \
          --name ExampleDeployment \
          --resource-group ExampleGroup \
          --template-file storage.json \
          --parameters storageAccountType=Standard_GRS

**Save a deployment as an Azure Resource Manager template**

**Deploy Windows and Linux VMs**

## Implement solutions that use virtual machines (VM)

**Provision VMs**

-

**Create Azure Resource Manager templates**

**Configure Azure Disk Encryption for VMs**

- Windows VM virtual disks are encrypted at rest by using BitLocker
- Cryptographic keys are stored in Azure Key Vault
- Process to enable encryption:
  - Create a cryptographic key in Azure Key Vault
  - Configure key to be usable for encrypting disks
  - Enable disk encryption for virtual disks

## Create connectivity between virtual networks

**Create and configure VNET peering**

- Allows connecting virtual networks in same (VNet peering) and different regions (Global VNet peering). Traffic between them is routed through MS backbone infra, not via internet.
- Once peered, resources on each VNET can directly connect with resources in the peered VNET
- Pricing per GB transferred
- Steps: first peer 1st vnet, then peer 2nd vnet

**Create and configure VNET to VNET**

- Similar to "Site-to-Site IPSec connection"
- Uses VPN gateway to create a IPsec/IKE tunnel

[](https://www.notion.so/146a472dbf404c978c4243942a8fd8a3#faf3f7672919424aaf0b5b611589da61)

- To create, need a subnet with a gateway in each subnet
- Pricing monthly for bandwidth
- Steps:
  - Plan IP address ranges. Ranges can't overlap between vnets
  - Create vnets
  - Create gateway for each vnet
  - Create network gateway connection on each gateway

**Verify virtual network connectivity**

    az network vpn-connection show --name <connection name>
        --resource-group <name>

**Create virtual network gateway**

- A VPN gateway is a specific type of virtual network gateway that is used to send encrypted traffic between an Azure virtual network and an on-premises location over the public Internet.
- Basic, VpnGw1, VpnGw2, VpnGw3
  |VPN GW Type | Price | Bandwith | S2S Tunnels | P2S Tunnels |
  |-------|-|-|-|-|-|
  |Basic|\$0.04/hr|100 Mbps |Max 10|Max 128|
  |VpnGw1|$0.19/hour|650 Mbps|Max 30|Max 250|
  |VpnGw2|$0.49/hour|1 Gbps|Max 30|Max 500|
  |VpnGw3|\$1.25/hour|1.25 Gbps|Max 30|Max 1000|

- Steps to create a VPN gateway
  - Create VNET
  - Create subnet for the gateway
  - Create local network gateway – Refers to you on-premises location. Needs a name, IP address of the on-premise VPN device. Can also specify IP address ranges of on-premises network
  - Request public IP
  - Create VPN gateway
  - Configure VPN device – Need a shared key and public IP of virtual network gateway
  - Create VPN connection
  - Verify VPN connection

## Implement and manage virtual networking

**Configure private and public IP addresses, network routes, network interface, subnets, and virtual network**

## Manage Azure Active Directory (AD)

- Pricing
  - Free, Basic, Premium P1, Premium P2
  - From Free to Basic: unlimited object count, adds group-based access mgmt, self-service password reset, company branding, SLA, proxy
  - From Basic to Premium P1: sync to on-premises AD, multi-factor for all users, self-service password reset, etc.
  - From P1 to P2: Identity protection, access reviews,
  - Basic: 0.844e user/month, premium: 5e user/month

**Add custom domains**

- https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/add-custom-domain
- Initial domain name, _domainname_.onmicrosoft.com. Can't be changed or deleted
- Can add custom domain names. Requires setting a TXT record for the domain
- Steps to add:
  - Add custom domain from Azure portal
  - Add DNS record, either
    - TXT Record with Alias (@), Destination and TTL
    - MX Record with Alias (@), Destination, TTL and Priority
  - Verify from Azure portal

**Configure Azure AD Identity Protection**

- https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/overview
- Identity Protection allows configuration of automated responses to ML detected suspicious actions related to user identities.
- Generates reports and alerts based on data
- Feature of the Azure AD Premium P2 edition
- https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/enable
  - Enabled from Azure portal: Marketplace -> Identity -> Azure AD Identity Protection
  - Get a consolidated view of flagged users and risk events detected using machine learning algorithms
  - Set risk-based Conditional Access policies to automatically protect your users
  - Improve security posture by acting on vulnerabilities

**Configure Azure AD Join**

- Brings devices to Azure AD
- Device states:
  - Azure AD Join – Device is joined only with Azure AD
    – Azure Hybrid AD Join – Device is joined with both Azure AD and on-premises AD
    – Azure AD registered – Mainly for BYOD, not managed by own IT
- [To configure Azure Hybrid AD join](https://docs.microsoft.com/en-us/azure/active-directory/devices/hybrid-azuread-join-managed-domains):
  1. Download and launch Azure AD Connect
  1. In the installer, select "Configure device options"
  1. Enter admin credentials to Connect to Azure AD

[**Configure Azure AD Enterprise State Roaming**](https://docs.microsoft.com/en-us/azure/active-directory/devices/enterprise-state-roaming-enable)

- Allows users to sync user settings and app settings to the cloud
- Requires Azure AD Premium or Enterprise Mobility + Security (EMS) license
- Azure Portal -> AAD -> Devices -> Enterprise State Roaming -> Select Users may sync settings and app data across devices
- Windows 10 devices require the devices to authenticate using an Azure AD identity. For devices that are joined to Azure AD, the user’s primary sign-in identity is their Azure AD identity, so no additional configuration is required. For devices that use on-premises Active Directory, the IT admin must Configure hybrid Azure Active Directory joined devices.

[**Configure self-service password reset**]()

- Allows users to reset their password themselves
- Steps required:
  1. Azure AD -> Password reset
  1. Configure number of authentication methods
  1. Require users to register their reset details when they sign in
  1. Configure notifications for users when password is reset

[**Implement conditional access policies**](https://docs.microsoft.com/en-gb/azure/active-directory/conditional-access/app-based-mfa#create-your-conditional-access-policy)

- Azure AD -> Conditional Access -> New policy
- For example require MFA when outside on-premises or high risk

[**Manage multiple directories**](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Creating-and-Managing-Multiple-Windows-Azure-Active-Directories/ba-p/243428)

- Azure AD -> Create directory
- Azure Portal -> Directory + subscription

[**Perform an access review**](https://docs.microsoft.com/en-gb/azure/active-directory/governance/access-reviews-overview)

- Requires Azure AD Premium P2 or Enterprise Mobility + Security (EMS) license
- Access for employees and guests changes over time. To reduce the risk associated with stale access assignments, admins can create access reviews for group members or application access. If you need to routinely review access, you can also create recurring access reviews.
- Create
  - Identity Governance -> Access Reviews
- Review
  - Decide whether to have each user review their own access or to have one or more users review everyone's access.

## Implement and manage hybrid identities

[**Install and configure Azure AD Connect**](https://docs.microsoft.com/en-gb/azure/active-directory/hybrid/how-to-connect-install-roadmap)

- Syncs data (users, groups, passwords, etc.) between on-premise AD and Azure AD
- Download and install Azure AD Connect
- Three ways to configure:

1. Password hash synchronization (PHS) – Password are synced between on-premises and cloud
2. Pass-through authentication (PTA) – User logs in at a MS page, password checked on-premises
3. Federation (AD FS) – Option to have a custom login page

**Configure federation and single sign-on**

- Federation is a collection of domains that have established trust
- You can federate your on-premises environment with Azure AD and use this federation for authentication and authorization. This sign-in method ensures that all user authentication occurs on-premises.
- Azure Active Directory (Azure AD) Connect lets you configure federation with on-premises Active Directory Federation Services (AD FS) and Azure AD. With federation sign-in, you can enable users to sign in to Azure AD-based services with their on-premises passwords and, while on the corporate network, without having to enter their passwords again.
- From Azure AD Connect, select "Add an additional Azure AD domain"

**Configure single sign-on**

- Azure Active Directory Seamless Single Sign-On (Azure AD Seamless SSO) automatically signs users in when they are on their corporate devices connected to your corporate network. When enabled, users don't need to type in their passwords to sign in to Azure AD, and usually, even type in their usernames.
- Seamless SSO can be combined with either the Password Hash Synchronization or Pass-through Authentication sign-in methods. Seamless SSO is not applicable to Active Directory Federation Services (ADFS). Seamless SSO needs the user's device to be domain-joined, but doesn't need for the device to be Azure AD Joined.
- At the User sign-in page (AD Connect), select the Enable single sign on option. Only if the Sign On method is Password Hash Synchronization or Pass-through Authentication.
- Verify that the Seamless single sign-on feature appears as Enabled. Azure Portal -> Azure Active Directory -> Azure AD Connect -> Seamless single sign-on is Enabled
- You start by adding the following Azure AD URL to all or selected users' Intranet zone settings by using Group Policy in Active Directory: https://autologon.microsoftazuread-sso.com
- You need to enable an Intranet zone policy setting called Allow updates to status bar via script through Group Policy.

**Manage Azure AD Connect**

**Manage password sync and writeback**

- Self-Service Password Reset/Change/Unlock with on-premises writeback is a premium feature of Azure AD.

# Implement workloads and security (20-25%)

## Migrate servers to Azure

[**Migrate by using Azure Site Recovery**](https://docs.microsoft.com/en-us/azure/site-recovery/tutorial-prepare-azure)

- Replicates workloads running on physical and virtual machines (VMs) from a primary site to a secondary location
- When an outage occurs, you fail over to secondary location, and access apps from there. After the primary location is running again, you can fail back to it.
- Site Recovery can manage replication for
  - Azure VMs replicating between Azure regions.
  - On-premises VMs, Azure Stack VMs and physical servers.
- Steps:
  1. Create Recovery Services Vault
  1. Setup Azure network for VMs
  1. Prepare on-premises VMs
     - VMware:
       1. Prepare account for automatic discovery – Site Reovery needs to discover VMs, orchestrate replication, failover and failback
       1. Prepare an account for Mobility service installation – Site recovery can install automatically or you can install manually.
       1. Check VMware requirements
       1. Prepare to connect to Azure VMs after failover
     - Hyper-V:
       1. Make sure Hyper-V hosts and VMs comply with requirements.
  1. Prepare infrastructure (Recovery services vault -> Site Recovery -> Prepare Infrastructure)
     1. Select protection goal
     1. Prepare source
        1. Deploy a configuration server
           - VMWare: Download & import the OVF template
           - Physical: Download & install Azure Site Recovery Unified setup
     1. Prepare target
     1. Set up replication policy
  1. Migrate to Azure
     - Replicated items -> Failover

[**Migrate using P2V (physical-to-virtual)**](https://docs.microsoft.com/en-us/azure/site-recovery/physical-azure-disaster-recovery)

- Like above, but need also process server on each VM

**Configure storage**

- By default, all disks on a machine are replicated
- To exclude a disk from replication, you must manually install the Mobility service on the machine before you enable replication if you are replicating from VMware to Azure

[**Create a backup vault**](https://docs.microsoft.com/en-us/azure/site-recovery/tutorial-prepare-azure#create-a-recovery-services-vault)

- Recovery services vault == backup vault

[**Prepare source and target environments**](https://docs.microsoft.com/en-us/azure/site-recovery/physical-azure-set-up-source)

- See above

**Backup and restore data**

- Can backup Azure VMs
- Steps:
  1. Select VM -> Management -> Enable backup
  1. Set backup policy – how frequently, how long backups are kept
  1. Backup manually
- Can restore from backups by number of ways
  - Create a new VM
  - Restore a disk
  - Replace existing disk
  - Recover individual files

[**Deploy Azure Site Recovery agent**](https://docs.microsoft.com/en-us/azure/site-recovery/vmware-physical-mobility-service-overview)

- You install the Site Recovery Mobility service on each on-premises VMware VM and physical server. The Mobility service captures data writes on the machine, and forwards them to the Site Recovery process server.
- Push installation (requires account for installing the service), Install manually (UI or command prompt), Automated deployment - E.g. System Center Configuration Manager.

[**Prepare virtual network**](https://docs.microsoft.com/en-us/azure/site-recovery/tutorial-prepare-azure#set-up-an-azure-network)

## Configure serverless computing

[**Manage a Logic App resource**](https://docs.microsoft.com/en-us/azure/logic-apps/)

- Schedule, automate, and orchestrate tasks, business processes, and workflows when you need to integrate apps, data, systems, and services
- Every logic app has:
  - A trigger – many connectors available
  - Actions that are executed
- Integrate into on-premise data with on-premise connectors
  - Needs on-premises data gateway

[**Manage Azure Function app settings**](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings)

- Azure serverless
- Each function has:
  - A trigger – E.g. http, timer, cosmos db, queue, etc.
  - Bindings – Input or ouput. Way of declaratively connecting another resource to the function
- Support C#, JS, F#, Java, PowerShell, Python, TypeScript

[**Manage Event Grid**](https://docs.microsoft.com/en-us/azure/event-grid/overview)

- Event Grid allows you to build applications with event-based architectures
- Connect event sources (many different Azure services) to event handlers (many Aure services)

![Event Grid](./images/eventgrid.png)

[**Manage Service Bus**](https://docs.microsoft.com/en-gb/azure/service-bus-messaging/)

- Fully managed enterprise integration message broker
- Support for
  - Queues (one-to-one)
  - Topics (one-to-many)
- Supports transactions, ordering, duplicate detection

## Implement application load balancing

- Load Balancer

  - "Level 4" load balancer
  - Backend pool: Can be associated with availability set, VM scaling set or single VM
  - Health probes check with VMs are alive and healthy
  - Rules decide how balancing is done
  - Pricing
    - Basic is free, Standard pricer per rule and per GB processed

- Application Gateway

  - "Level 7" load balancer
  - Smarter load balancer. Supports:
    - Auto-scaling
    - High-availability with availability zones
    - Routing based on HTTP params, such as URI path or headers
    - SSL termination
  - Backend pool: Can be associated with IP address or Fully Qualified Domain Name (FQDM), VMs, VMSS, App Services
  - Pricing
    - Small, Medium and Large types. Basic and Web App Firewall tiers. Billed by the hour and data processed

- Azure Traffic Manager
  - Uses DNS to redirect requests to an appropriate geographical location endpoint

**Configure application gateway and load balancing rules**

1. Create load balancer
1. Load balancing rules -> Add
1. Select protocol, port, backend port, backend pool, health probe

[**Implement front end IP configurations**](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-multivip-overview)

- Can have an internal or public IP

**Manage application load balancing**

## Integrate on-premises network with Azure virtual network

[**Create and configure Azure VPN Gateway**](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways)

- Encrypt traffic between Azure vnet and on-premises locations over public internet
- Type: Point-to-Site, Site-to-Site, VNet-to-VNet, VNet peering, ExpressRoute
- SKU: Basic(Legacy), VpnGw1(650Mbps), VpnGw2(1Gbps), VpnGw3(1.25Gbps)
- Steps:
  - Public IP, gateway subnet, gateway
  - Local network gateway (in on-premises location)

**Create and configure site to site VPN**

**Configure Express Route**

- Connect on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider (Azure, Office 365, and Dynamics 365)

**Verify on-premises connectivity**

- Azure portal -> virtual network gateway -> Connections -> Status is 'Succeeded' and 'Connected'

**Manage on-premises connectivity with Azure**

## Manage role-based access control (RBAC)

- Roles can be assigned at resource level, resource group level, subscription level
- Three key roles:
  1. Reader – Read-only access.
  2. Contributor – Full set of permissions for creating and deleting resources. Can't give the permission to other users.
  3. Owner – Can also grant access to other users.
- You can create custom roles with Powershell or CLI with a .json file (edit from existing)

[**Create a custom role**](https://docs.microsoft.com/en-us/azure/role-based-access-control/custom-roles)

- Custom roles are stored in an Azure Active Directory (Azure AD) directory and can be shared across subscriptions
- Create a custom role with a JSON template, add changes, and create a new role
- AssignableScopes, Actions, NotActions
- az role definition create --role-definition ~/roles/vmoperator.json

[**Configure access to Azure resources by assigning roles**](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal)

-

**Configure management access to Azure**

[**Troubleshoot RBAC**](https://docs.microsoft.com/en-us/azure/role-based-access-control/troubleshooting)

[**Implement RBAC policies**](https://docs.microsoft.com/en-us/azure/governance/policy/overview)

- Policy focuses on resources during deployment and for already existing resources. Policy controls properties such as the types or locations of resources. Unlike RBAC, Policy is a default allow and explicit deny system.
- Creating and implementing a policy begins with policy definition. Policy definition has conditions under which it's enforced. And, it has a defined effect that takes place if the conditions are met.
- A policy assignment is a policy definition that has been assigned to specific scope. This scope could range from a management group to a resource group.
- Scope refers to resource groups, subscriptions, or management groups.
- Policy assignments are inherited by all child resources to resources in resource group. You can exclude a subscope from the policy assignment.
- For example, at the subscription scope, you can assign a policy that prevents the creation of networking resources. You could exclude a resource group in that subscription that is intended for networking infrastructure. You then grant access to this networking resource group to users that you trust with creating networking resources.

**Assign RBAC roles**

# Create and deploy apps (5-10%)

## Create web apps by using PaaS

- All apps in App Service run in an App Service plan
- Each plan defines:
  - Region
  - Number of VM instances
  - Size of VM instances
  - Pricing tiers:
    - Dev/Test:
      - Free (F1) – shared infra, no custom domains
      - Shared (D1) – shared infra, custom domains
      - Basic (Bx) – dedicated infra, custom domains, manual scaling
    - Production:
      - Standard (Sx) – auto-scaling, staging slots, daily backups, traffic manager
      - Premium (P1x) – more auto-scaling, more backups
      - PremiumV2 (PxV2) – more auto-scaling, more backups
    - Isolated:
      - Isolated (Ix) – Single tenant system, isolated network, private app access

**Create an Azure App Service Web App**

- Steps:
  1. Select:
  1. App name (app will be available from <name>.azurewebsites.net)
  1. Resource group
  1. OS (windows / linux)
  1. Publish (code / docker)
  1. App service plan
  1. App Insights enabled?
  1. Deploy

**Create documentation for the API**

- Azure can create API documentation from Swagger 2.0 metadata
- Web App -> API -> API definition

**Create an App Service Web App for containers**

- Select publish type docker
- Can deploy as single container or docker compose from:
  - Azure Container Registry
  - Docker Hub
  - Private Registry

**Create an App Service background task by using WebJobs**

- Steps:
  1. Web App -> Settings -> WebJobs -> Add..
  1. Give:
  1. Name
  1. File
  1. Type
     - Triggered – Only run when triggered by a provided webhook
     - Continuous – Runs until stopped
  1. Scale
     - Single instance – Only single copy running
     - Multi instance – Scales across all instances

**Enable diagnostics logging**

- Web App -> App Service Logs

## Design and develop apps that run in containers

**Configure diagnostic settings on resources**

[**Create a container image by using a Docker file**](https://docs.microsoft.com/en-us/azure/app-service/containers/tutorial-custom-docker-image)

[**Create an Azure Kubernetes Service**](https://docs.microsoft.com/en-us/azure/aks/)

[**Publish an image to the Azure Container Registry**](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli)

**Implement an application that runs on an Azure Container Instance**

**Manage container settings by using code**

# Implement authentication and secure data (5-10%)

## Implement authentication

- You can restrict access to your Azure App Service app by enabling different types of authentication for it

**Implement authentication by using certificates, forms-based authentication, tokens, or Windows-integrated authentication**

- One way is to request a client certificate when the client request is over TLS/SSL and validate the certificate. This mechanism is called TLS mutual authentication or client certificate authentication
- SSL termination happens at frontend load balancer. App Service injects `X-ARR-ClientCert` header with the client certificate as base64 encoded

```bash
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

- App Service uses federated identity, in which a third-party identity provider manages the user identities and authentication flow for you.
  - Azure Active Directory | Microsoft Account | Facebook | Google | Twitter
- User claims – App Service makes the user's claims available to your code by injecting them into the request headers:
  - X-MS-CLIENT-PRINCIPAL-NAME
  - X-MS-CLIENT-PRINCIPAL-ID
  - For example X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN and X-MS-TOKEN-FACEBOOK-EXPIRES-ON
- Token store - App Service provides a built-in token store, which is a repository of tokens that are associated with the users of your web apps, APIs, or native mobile apps. When you enable authentication with any provider, this token store is immediately available to your app. If your application code needs to access data from these providers on the user's behalf,

[**Implement multi-factor authentication by using Azure AD**](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/app-based-mfa)

- Add conditional access policy from Azure AD and app

**Implement OAuth2 authentication**

1. Register your App Service app with Azure Active Directory
1. Add Azure Active Directory information to your App Service app

**Implement Managed identities for Azure resources Service Principal authentication**

- Can assign identities to web apps:
  - System assigned – A system assigned managed identity enables Azure resources to authenticate to cloud services (e.g. Azure Key Vault) without storing credentials in code. Once enabled, all necessary permissions can be granted via Azure role-based-access-control
  - User assigned – User assigned managed identities enable Azure resources to authenticate to cloud services (e.g. Azure Key Vault) without storing credentials in code. This type of managed identities are created as standalone Azure resources, and have their own lifecycle.

## Implement secure data solutions

**Encrypt and decrypt data at rest and in transit**

- Azure has Transparent Data Encryption (TDE). Data is stored in an encrypted state
- TDE supports Bring Your Own Key (BYOK). Key is stored in Azure Key Vault. DB server will use that key.
- All sessions with Azure services and data centers are secured using the Transport Layer Security (TLS) cryptographic protocol and Forward Secrecy (also known as Perfect Forward Secrecy or PFS), a key agreement protocol

**Encrypt data with Always Encrypted**

- TDE is intended to add a layer of security to protect data at rest
- Always Encrypted is a feature designed to protect sensitive data, stored in Azure SQL Database or SQL Server databases from access by database administrators. Always Encrypted leverages client-side encryption: a database driver inside an application transparently encrypts data, before sending the data to the database. Similarly, the driver decrypts encrypted data retrieved in query results.
- Column encryption keys are used to encrypt data in the database. These keys are stored in the database in the encrypted form (never in plaintext).
- Column master keys are used to encrypt column encryption keys. These keys are stored in an external key store, such as Windows Certificate Store, Azure Key Vault or hardware security modules. For keys stored in Azure Key Vault, only the client application has access to the keys, but not the database, unlike TDE.
- It is often advised to use Always Encrypted, TDE, and TLS together

**Implement Azure Confidential Compute and SSL/TLS communications**

**Create, read, update, and delete keys, secrets, and certificates by using the KeyVault API**

# Develop for the cloud and for Azure storage (20-25%)

Azure Cosmos DB is a fully managed database service with turnkey global distribution and transparent multi-master replication. You can run globally distributed, low-latency operational and analytics workloads and AI on transactional data within your database.

## Develop solutions that use Cosmos DB storage

**Create, read, update, and delete data by using appropriate APIs**

Multi-model DB, multiple APIs available:

- Core (SQL)
- Azure Cosmos DB for MongoDB API
- Cassandra
- Gremlin (graph)
- Table

[**Implement partitioning schemes**](https://docs.microsoft.com/en-us/azure/cosmos-db/partitioning-overview)

Items in a container are divided into logical partitions. Logical partitions are formed based on the value of a partition key that is associated with each item in a container.

Choosing partitioning key:

- Choose a partition key that spreads the workload evenly across all partitions and evenly over time
- Candidates for partition keys might include properties that appear frequently as a filter in your queries

![Logical partitions](./images/logical-partitions.png)

[**Set the appropriate consistency level for operations**](https://docs.microsoft.com/en-us/azure/cosmos-db/consistency-levels)

5 consistency levels:

- **Strong** – Linearizability guarantee. Reads always return most recent committed version.
- **Bounded staleness** – Reads are guaranteed to honor the consistent-prefix guarantee. The reads might lag behind writes by at most "K" versions (i.e., "updates") of an item or by "T" time interval.
- **Session** – Session consistency is scoped to a client session.
- **Consistent prefix** – Updates that are returned contain some prefix of all the updates, with no gaps. Consistent prefix consistency level guarantees that reads never see out-of-order writes.
- **Eventual** – There's no ordering guarantee for reads. In the absence of any further writes, the replicas eventually converge.

![Consistency levels](./images/five-consistency-levels.png)

## Develop solutions that use a relational database

**Provision and configure relational databases**

- There's a SQL server image for running it in a VM
- Managed SQL Server, MariaDB, MySQL, PostgreSQL available
- For SQL Server, [Virtual core (vCore) and db transaction unit (DTU) based pricing models available](https://docs.microsoft.com/en-gb/azure/sql-database/sql-database-purchase-models)
  - DTU – This model is based on a bundled measure of compute, storage, and I/O resources. Tiers:
    - Basic
    - Standard
    - Premium
  - vCore – This model allows you to independently choose compute and storage resources. Tiers:
    - General purpose
    - Business critical
    - Hyperscale
- Support for geo-replication

```bash
# Create a resource group
az group create \
    --name myResourceGroup \
    --location westeurope

# Create a logical server in the resource group
az sql server create \
    --name $servername \
    --resource-group myResourceGroup \
    --location westeurope  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a firewall rule for the server
az sql server firewall-rule create \
    --resource-group myResourceGroup \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a database in the server with zone redundancy as true
az sql db create \
    --resource-group myResourceGroup \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --service-objective S0 \
    --zone-redundant
```

**Configure elastic pools for Azure SQL Database**

- Multiple databases that share the same resources
- Good when DBs have varying and unpredictable usage demands

**Create, read, update, and delete data tables by using code**

- Need connection string to connect DB
- SDK / Lib available for many languages

## Configure a message-based integration architecture

**Configure an app or service to send emails, Event Grid, and the Azure Relay Service**

Email Messages

- SendGrid is a REST API for sending and receiving email
- Scalable, third party service
- Needs SendGrid account

Event Grid

- Connects a lot of event sources inside Azure to a lot of handlers

Azure Service Bus Relay

- Allows messages to be passed from the Internet inside the company firewall to specific endpoints such as WCF
- Does not require changes to firewall
- App inside network opens up outbound connection to Azure and connection stays open
- Messages pass over that link
- Hybrid Connections vs. Relay Service
  - Hybrid Connections are more sophisticated, based on BizTalk services. Uses standard web sockets
  - WCF Relay is for WCF. Only supports HTTP

**Create and configure Notification Hub, Event Hub, and Service Bus**

Notification Hub

- Send push notification for any platform
- Several ways to target messages
  - push to all devices
  - push to specific devices
  - push to specific users
  - location-based notifications
  - personalised notifications

Event Hub

- Big Data streaming platform and event ingestion service
- Can receive messages from millions of producers
- Typically used for IoT devices
- Supports HTTPS and AMQP 1.0
- Messages can be partitioned

Service Bus

- Enterprise grade messaging service, see above

Microsoft Graph

- API for interacting with Office 365, which has Outlook for email

**Configure queries across multiple products**

## [Develop for autoscaling](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/autoscale-overview)

**Implement autoscaling rules and patterns (schedule, operational/system metrics, code that addresses singleton application instances)**

- Autoscale applies to Virtual Machine Scale Sets, Cloud Services, Web Apps, and API Management
- Allows to add resources to handle increases in load and also save money by removing resources that are sitting idle. You specify a minimum and maximum number of instances to run and add or remove VMs automatically based on a set of rules.
- When rule conditions are met, one or more autoscale actions are triggered. You can add and remove VMs, or perform other actions.
- Resources emit metrics, these metrics are later processed by rules. Virtual machine scale sets use telemetry data from Azure diagnostics agents whereas telemetry for Web apps and Cloud services comes directly from the Azure Infrastructure. Some commonly used statistics include CPU Usage, memory usage, thread counts, queue length, and disk usage.
- Schedule-based rules are based on UTC. You must set your time zone properly when setting up your rules.
- Metric-based - For example, do this action when CPU usage is above 50%.
- Time-based - For example, trigger a webhook every 8 am on Saturday in a given time zone.
- Metric-based rules measure application load and add or remove VMs based on that load. Schedule-based rules allow you to scale when you see time patterns in your load and want to scale before a possible load increase or decrease occurs.
- Rules can trigger one or more types of actions.
  - Scale - Scale VMs in or out
  - Send email
  - Call webhooks, which can trigger multiple complex actions inside or outside Azure. (Azure Automation runbook, Azure Function, or Azure Logic App)

Different patterns for autoscaling

1. On and off – Turning resources on and off
2. Adding resources – Add more services manually
3. Unpredictable autoscaling – Monitor resources and reacting
4. Predictable autoscaling – Define a schedule and scale based on that

[**Implement code that addresses transient state**](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)

- You have to think about how to handle temporary service interruptions, You can frequently get little glitches that are typically self-healing
- Use smart retry/back-off logic to mitigate the effect of transient failures
  - You can recognize errors that are typically transient, and automatically retry the operation that resulted in the error, in hopes that before long you'll be successful. Most of the time the operation will succeed on the second try, and you'll recover from the error without the customer ever having been aware that there was a problem.
