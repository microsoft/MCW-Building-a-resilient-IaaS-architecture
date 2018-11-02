![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Building a resilient IaaS architecture
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
November 2018
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.
© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

<!-- TOC -->

- [Building a resilient IaaS architecture hands-on lab step-by-step](#building-a-resilient-iaas-architecture-hands-on-lab-step-by-step)
    - [Abstract and learning objectives](#abstract-and-learning-objectives)
    - [Overview](#overview)
    - [Solution architecture](#solution-architecture)
    - [Requirements](#requirements)
        - [Help references](#help-references)
    - [Exercise 1: Prepare connectivity between regions](#exercise-1-prepare-connectivity-between-regions)
        - [Task 1: Deploy the lab environment](#task-1-deploy-the-lab-environment)
        - [Task 2: Create a VNET in the second region](#task-2-create-a-vnet-in-the-second-region)
        - [Task 3: Configure VNET Peering between Azure regions](#task-3-configure-vnet-peering-between-azure-regions)
    - [Exercise 2: Build the primary DCs for resiliency](#exercise-2-build-the-primary-dcs-for-resiliency)
        - [Task 1: Create Resilient Active Directory Deployment](#task-1-create-resilient-active-directory-deployment)
        - [Task 2: Create the Active Directory deployment in the second Azure region](#task-2-create-the-active-directory-deployment-in-the-second-azure-region)
        - [Task 3: Add data disks to Active Directory domain controllers (both regions)](#task-3-add-data-disks-to-active-directory-domain-controllers-both-regions)
        - [Task 4: Format data disks on DCs and configure DNS settings across connection](#task-4-format-data-disks-on-dcs-and-configure-dns-settings-across-connection)
        - [Task 5: Promote DCs as additional domain controllers](#task-5-promote-dcs-as-additional-domain-controllers)
        - [Summary](#summary)
    - [Exercise 3: Build web tier and SQL for resiliency](#exercise-3-build-web-tier-and-sql-for-resiliency)
        - [Task 1: Deploy SQL Always-On Cluster](#task-1-deploy-sql-always-on-cluster)
        - [Task 2: Convert the SQL deployment to Managed Disks](#task-2-convert-the-sql-deployment-to-managed-disks)
        - [Task 3: Build a scalable and resilient web tier](#task-3-build-a-scalable-and-resilient-web-tier)
        - [Summary](#summary-1)
    - [Exercise 4: Configure SQL Server Managed Backup](#exercise-4-configure-sql-server-managed-backup)
        - [Task 1: Create an Azure Storage Account](#task-1-create-an-azure-storage-account)
        - [Task 2: Configure managed backup in SQL Server](#task-2-configure-managed-backup-in-sql-server)
    - [Exercise 5: Validate resiliency](#exercise-5-validate-resiliency)
        - [Task 1: Validate resiliency for the CloudShop application](#task-1-validate-resiliency-for-the-cloudshop-application)
        - [Task 2: Validate SQL Always On](#task-2-validate-sql-always-on)
        - [Task 3: Validate backups are taken](#task-3-validate-backups-are-taken)
    - [After the hands-on lab](#after-the-hands-on-lab)
        - [Task 1: Delete the resource groups created](#task-1-delete-the-resource-groups-created)

<!-- /TOC -->

# Building a resilient IaaS architecture hands-on lab step-by-step 

## Abstract and learning objectives 

In this hands-on lab, you will deploy a pre-configured IaaS environment and then redesign and update it to account for resiliency and in general high availability. Throughout the hands-on lab you will use various configuration options and services to help build a resilient architecture.

At the end of the lab, you will be better able to design and use availability sets, Managed Disks, SQL Server Always on Availability Groups, as well as design principles when provisioning storage to VMs. In addition, you'll learn effective employment of Azure Backup to provide point-in-time recovery.

## Overview

Contoso has asked you to deploy their infrastructure in a resilient manner to insure their infrastructure will be available for their users and gain an SLA from Microsoft.

## Solution architecture

Highly resilient deployment of Active Directory Domain Controllers in Azure.
    ![Highly resilient deployment of Active Directory Domain Controllers in Azure.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image11.png "Solution architecture")

Deployment of a web app using scale sets, and a highly available SQL Always On deployment.
    ![Deployment of a web app using scale sets, and a highly available SQL Always On deployment.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image3.png "Solution architecture")

## Requirements

1.  Microsoft Azure Subscription

2.  Virtual Machine Built during this hands-on lab or local machine with the following:

    - Visual Studio 2017 Community or Enterprise Edition
    - Latest Azure PowerShell cmdlets
        - <https://azure.microsoft.com/en-us/downloads/>
        - <https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps>
        - Ensure you reboot after installing the SDK or Azure PowerShell may not work correctly.

### Help references
|    |            |
|----------|:-------------:|
| **Description** | **Links** |
| Authoring ARM Templates | <https://azure.microsoft.com/en-us/documentation/articles/resource-group-authoring-templates/> |
| Virtual Machine Scale Set Samples | <https://github.com/gbowerman/azure-myriad> |
| Azure Quick Start Templates | <https://github.com/Azure/azure-quickstart-templates> |
| Network Security Groups | <https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-nsg/> |
| Managed Disks | <https://azure.microsoft.com/en-us/services/managed-disks> |
| Always-On Availability Groups | <https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017> |
| SQL Server Managed Backup to Azure | <https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017> |
| Virtual Network Peering | <https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview> |
| Azure Backup |  <https://azure.microsoft.com/en-us/services/backup/> |


## Exercise 1: Prepare connectivity between regions

Duration: 30 minutes

Contoso is planning to deploy infrastructure in multiple regions in Azure to provide infrastructure closer to their employees in each region as well as the ability to provide additional resiliency in the future for certain workloads. In this exercise, you will configure connectivity between the two regions.

### Task 1: Deploy the lab environment

1.  Login to the Azure portal (<https://portal.azure.com>) with the credentials that you want to deploy the lab environment to.

2.  In a separate tab, navigate to: <https://github.com/opsgility/cw-building-resilient-iaas-architecture>.

3.  Click the button **Deploy to Azure**.

    ![A screen with the Deploy to Azure button visible.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image24.png "Sample Application in GitHub")

4.  Specify the Resource group name as **ContosoRG** and the region as **East US 2**, check the **I agree to the terms and conditions state above** checkbox on the page and click **Purchase**.

    ![The custom deployment screen with ContosoRG as the resource group and West Central US as the region.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image111.png "Custom deployment")
    
    **Note**: The deployment may take up to 20 minutes.

5.  Once the deployment is successful, validate the deployment by opening the **CloudShopWeb** virtual machine and navigating your browser to its public IP address.

    ![The CloudShopDemo window displays. Under Select a product from the list, a product list displays.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image27.png "CloudShopDemo window")

### Task 2: Create a VNET in the second region

1.  Browse to the Azure portal and authenticate at <https://portal.azure.com/>.

2.  In the left pane, click **+ Create a resource**.

3.  In the **New** blade, select **Networking \>** **Virtual Network**.

    ![In the New Blade, under Azure Marketplace, Networking is selected. Under Featured, Virtual Network is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image28.png "New Blade")

4.  In the **Create virtual network** blade, enter the following information:

    - Name: **VNET2**
    - Address space: **172.16.0.0/16**
    - Subscription: **Choose your subscription**
    - Resource group (create new): **CUSRG**
    - Location: **Central US**
    - Subnet name: **Apps**
    - Subnet address range: **172.16.0.0/24**
    - DDoS protection: **Basic**
    - Service endpoints: **Disabled**
    - Firewall: **Disabled**

    ![A blade showing the creation of a virtual network in the Azure portal.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image112.png "Create virtual network")

1. Click the **Create** button to continue.

5.  Once the deployment is complete, add two more subnets to the virtual network. To do this, select the **Subnets \>** icon in the **Settings** area.

    ![Under Settings, Subnets is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image30.png "Settings section")

6.  Click the **+ Subnet** option, and enter the following settings:

    ![Screenshot of the Subnets button.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image31.png "Subnets button")

    -   Name: **Data**
    -   Address range (CIDR block): **172.16.1.0/24**
    -   Click the **OK** button to add this subnet.

    ![In the Add subnet blade, the Name field is set to Data, and Add range (CIDR block) is set to 172.16.1.0/24.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image32.png "Add subnet blade")

7.  Once the subnet is created successfully, repeat the above step for an **Identity** subnet with the following settings:

    -   Name: **Identity**
    -   Address range (CIDR block): **172.16.2.0/24**
    -   Click the **OK** button to add this subnet:

    ![In the Add subnet blade, the Name field is set to Identity, and Add range (CIDR block) is set to 172.16.2.0/24.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image33.png "Add subnet blade")

8.  The subnets will look like this once complete:

    ![The following subnets display: Apps, Data, and Identity.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image34.png "Subnets")

### Task 3: Configure VNET Peering between Azure regions

1.  Open the first virtual network (VNET1) by clicking **All Services -\> Virtual networks** and clicking the name.

2.  Click on **Peerings** and click **+Add**.

    ![A screen highlighting the peerings link in the Azure portal.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image35.png "Peerings")

3.  Name the peering, **VNET1TOVNET2** and change the Virtual network dropdown to **VNET2** click **Allow forwarded traffic,** and then click **OK**.

    ![A screen that shows the name Peering, the virtual network VNET2, and Allow forwarded traffic checked.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image118.png "Add peering")

4.  Open the second virtual network (VNET2) by clicking **All Services -\> Virtual networks** and clicking the name.

5.  Click on **Peerings** and click **+Add**.

    ![A screen highlighting the peerings link in the Azure portal.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image35.png "Peerings")

6.  Name the peering, **VNET2TOVNET1** and change the Virtual network dropdown to **VNET1** click **Allow forwarded traffic,** and then click **OK**.

    ![A screen that shows the name Peering, the virtual network VNET, and Allow forwarded traffic checked.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image119.png "Add peering")

## Exercise 2: Build the primary DCs for resiliency

Duration: 30 minutes

In this exercise, you will deploy Windows Server Active Directory configured for resiliency using Azure Managed Disks and Availability Sets in the primary region. You will then deploy additional domain controllers in a second region for future expansion of the Azure footprint.

### Task 1: Create Resilient Active Directory Deployment 

In this task, you will change the disk cache settings on the existing domain controller **Read Only** to avoid corruption of Active Directory database.

1.  Select **Virtual machines** in the left menu pane of the Azure portal.

2.  Click on **ADVM**, and in the **Settings** area, select **Disks**.

    ![Under Settings, Disks is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image38.png "Settings section")

3.  On the Disks blade, click **Edit**.

    ![On the Disks blade, the Edit icon is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image39.png "Disks blade")

4.  Change the **Host caching** from **Read/Write** to **None** via the drop-down option, and click the **Save** icon.

    ![In the Edit blade, under Host Caching, None is selected. At the top, the Save button is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image40.png "Edit blade")

    **Note**: In production, we would not want to have any OS drives that do not have read/write cache enabled. This machine will be decommissioned, but first, we want to make sure the AD Database and SYSVOL will not be corrupted during our updates.

5.  In the left pane, click **+ Create a resource**.

6.  In the **New** blade, select **Compute** **\>** **Windows Server 2016 VM**.

    ![In the New blade, under Azure Marketplace, Compute is selected. Under Featured, Windows Server 2016 Datacenter is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image41.png "New blade")

7.  In the **Create virtual machine** blade, enter the **Basics** information:

    -   Subscription: **Select your subscription**
    -   Resource group (create new): **EU2ADRG**
    -   Virtual machine name: **DC01**
    -   Size: **Standard DS1\_v2**
    -   Region: **East US 2**
    -   Username: **demouser**
    -   Password: **demo\@pass123**
    -   Confirm password: **demo\@pass123**
    -   Public inbound ports: **Allow selected ports**
    -   Select inbound ports: **RDP (3389)**

    ![A screen that shows the basics blade of creating a new VM. The name is DC01, the user name is demouser, the resource group is EU2ADRG, and the location is East US 2.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image113.png "Basics")

8. For **Availability options**, select **Availability set**. Select **Create new** and enter the name **ADAV** and click **OK**.

    ![Azure portal screenshot showing the Create new availability set blade with the name set to ADAV.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image114.png "Create new Availability Set")

9. Click the **Networking** tab and select the existing virtual network **VNET1** and the **Identity** subnet.

    ![Azure portal screenshot showing the Networking tab of the VM create blade, selecting the virtual network VNET1.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image115.png "Networking settings")

10. Click the **Management** tab and configure as follows:

    - Boot diagnostics: **On**
    - Diagnostics storage account: **Create new and select a unique name**
    - Enable backup: **On**
    - Recovery Services vault: **Create new**
    - Recovery Services vault name: **BackupVault**
    - Resource group (create new): **BackupVaultRG**

    ![Azure portal screenshot showing the Management tab of the VM create blade, selecting the diagnostics and backup settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image116.png "Management settings")
    
    **Note**: Backup with a Domain Controller is a supported scenario. Care should be taken on restore. For more information see the following: <https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms#backup-for-restored-vms>.

11. Click the **Review + create** button or click on the **Review + create** tab. There will be a final validation and when this is passed, click the **Create** button to complete the deployment.

12. Give the deployment a few minutes to build the Availability Set resource. Then, repeat the virtual machine creation steps to create **DC02**, as that will be another Domain Controller making sure to place it in the **ADAV** availability set and select the existing diagnostics storage account and the existing **BackupVault**.

    ![Azure portal screenshot showing the Review and create screen for a virtual machine named DC02.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image117.png "Create new VM validation") 

### Task 2: Create the Active Directory deployment in the second Azure region

In this task, you will deploy Active Directory in the second region, so identity is available for new workloads. In this region, an additional level of resiliency will be introduced by using Availability Zones for the virtual machines hosting Active Directory.

1.  In the left pane, click **+ Create a resource**.

2.  In the **New** blade, select **Virtual Machines \>** **Windows Server 2016 VM**.

    ![In the New blade, under Azure Marketplace, Compute is selected. Under Featured, Windows Server 2016 VM is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image41.png "New blade")

3.  In the **Create virtual machine** blade, enter the **Basics** information:

    -   Subscription: **Select your subscription**
    -   Resource group (create new): **CUSADRG**
    -   Virtual machine name: **DC03**
    -   Size: **Standard DS1\_v2**
    -   Region: **Central US**
    -   Username: **demouser**
    -   Password: **demo\@pass123**
    -   Confirm password: **demo\@pass123**
    -   Public inbound ports: **Allow selected ports**
    -   Select inbound ports: **RDP (3389)**

    ![Azure portal screenshot showing the Basics tab of the new VM create blade for DC03.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image120.png "Create new VM")

4. For **Availability options**, select **Availability zone**. Select zone **1**.

    ![Azure portal screenshot the selection of Availability Zones and Zone 1.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image121.png "Select availability zone")

9. Click the **Networking** tab and select the existing virtual network **VNET2** and the **Identity** subnet.

    ![Azure portal screenshot showing the Networking tab of the VM create blade, selecting the virtual network VNET2.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image122.png "Networking settings")

1. Click the **Management** tab and configure as follows:

    - Boot diagnostics: **On**
    - Diagnostics storage account: **Create new and select a unique name**
    - Enable backup: **On**
    - Recovery Services vault: **Create new**
    - Recovery Services vault name: **BackupVault2**
    - Resource group (create new): **BackupVault2RG**

    ![Azure portal screenshot showing the Management tab of the VM create blade, selecting the diagnostics and backup settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image123.png "Management settings")
    
    **Note**: Backup with a Domain Controller is a supported scenario. Care should be taken on restore. For more information see the following: <https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms#backup-for-restored-vms>.

1. Click the **Review + create** button or click on the **Review + create** tab. There will be a final validation and when this is passed, click the **Create** button to complete the deployment.

2. Give the deployment a few minutes to start and repeat those steps to create **DC04**, as that will be another Domain Controller making sure to place it in Availability Zone **2** and the existing **BackupVault2**.

    ![Azure portal screenshot showing the review and create tab of the new VM create blade for DC04.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image124.png "Review and create tab")

### Task 3: Add data disks to Active Directory domain controllers (both regions)

1.  Open **DC01** from the Azure portal.

2.  In the **Settings** blade, select **Disks**.

3.  Click on **Add data disk**.

    ![The + Add data disk button is visible.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image48.png "Data disks")

4.  On the settings for the **Data disk menu**, click on the drop-down menu under **Name**, and click **Create Disk**.

    ![Under Data disks, under Name, Create disk is selected from the drop-down list.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image49.png "Data disks section")

5.  On the Create managed disk blade, enter the following, and click **Create**:

    -   Name: **DC01-Data-Disk-1**
    -   Resource group: **Use existing / EU2ADRG**
    -   Account Type: **Premium SSD**
    -   Source Type: **None (empty disk)**
    -   Size: **32**

6.  Once the disk is created, the portal will move back to the **Disks** blade. Locate the new disk under **Data Disks**, change the **HOST CACHING** to **None**, and click **Save**.

    ![On the Disks blade, under Host Caching, None is selected. At the top, Save is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image125.png "Disks blade")

7.  Perform these same steps for **DC02** naming the disk **DC02-Data-Disk-1**. Also, make sure the Host caching is set to **None**.

8.  Perform the add disk steps for **DC03** and **DC04** naming the disks **DC03-Data-Disk-1** and **DC04-Data-Disk1** respectively. Make sure to set the Host caching to **None**. For these disks, select the resource group **CUSADRG**.

### Task 4: Format data disks on DCs and configure DNS settings across connection

1.  Browse to **DC01** in the Azure portal.

2.  Click the **Connect** icon on the menu bar to RDP into the server.

    ![Screenshot of the Connect icon.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image52.png "Connect icon")

3.  Login to the VM with **demouser** and password created during deployment.

    ![On the Windows security login window, the Use a different account option is circled.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image53.png "Windows security login window")

    **Note**: You might have to click "Use a different account," depending on which OS you are connecting from to put in the demouser credentials.

4.  Click **Yes** to continue to connect to DC01.

5.  Once the logged in, click on **File and Storage Services** in **Server Manager**.

    ![In Server Manager, File and Storage services is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image55.png "Server Manager ")

6.  Click on **Disks**, and let the data load. You should now see an **Unknown** partition disk in the list.

    ![In the Disks section, under DC01, the Unknown partition disk is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/2018-08-27-16-27-45.png "Disks section")

7.  Right-click on this disk and choose **New Volume...** from the context menu options.

    ![The Right-click menu for the Unknown partition disk, New Volume is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image57.png "Right-click menu")

8.  Follow the prompts in the **New Volume Wizard** to format this disk, as the **F:\\** drive for the domain controller.

9.  Perform these same steps for the remaining 3 DCs (**DC02**, **DC03**, and **DC04**).

10. Go back to the Azure portal dashboard and click on **DC01**. Next, click on **Networking** followed by the name of the NIC.

    ![Under Settings, Networking is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image58.png "Settings section")

    ![Next to Network Interface, dc01222 is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image59.png "Network Interface")

11. Select the **IP** **configurations**.

    ![Under Settings, IP configurations is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image60.png "Settings section")

12. Click the IP Configuration named **ipconfig1**.

    ![In the IP Configuration blade, under Name, ipconfig1 is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image61.png "IP Configuration blade")

13. On the **ipconfig1** blade, change the **Private IP address settings** to **Static.** Leave all the other settings at their defaults and click the **Save** icon.

14. Once Azure notifies the network interface change is saved, repeat these steps on the remaining 3 DCs (**DC02**, **DC03**, and **DC04**).

    **Note**: The Static IP for **DC02** should be 10.0.2.6. **DC03** should be 172.16.2.4 and **DC04** should be 172.16.2.5.

15. In the Azure portal, click **More Services \>** and in the filter, type in **Virtual Networks**. Select **VNET2** from the list.

16. In the **Settings** area, select **DNS Servers**.

    ![Under Settings, DNS servers is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image63.png "Settings section")

17. Change **DNS servers** to **Custom**, and provide the address of **10.0.2.4** in the **Add DNS server** box. Click the **Save** icon to commit the changes.

    ![In the DNS Servers blade, under DNS servers, the Custom radio button is selected, and the field below it is set to 10.0.2.4. ](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image64.png "DNS Servers blade")

18. At this point, restart **DC03** and **DC04**, so they can get their new DNS Settings.

    **Note**: **DC01** and **DC02** received the correct DNS settings from the VNET DNS configured prior to their deployment, as the custom DNS was set before the hands-on lab for that VNET. **DC03** and **DC04** must be rebooted to receive the updated DNS settings from their virtual network.

19. While these two DCs are rebooting, RDP into **ADVM** using the credentials for **demouser**, and run the following PowerShell command:

    ```powershell
    Set-DnsServerPrimaryZone -Name contoso.com -DynamicUpdate NonsecureAndSecure
    ```

    **Note**: This would not be done in a production environment, but for purposes of our hands-on lab, we need to perform this step for the SQL Cluster in the coming tasks.

20. After the PowerShell command runs, sign out of **ADVM**.

### Task 5: Promote DCs as additional domain controllers 

1.  Login to **LABVM** created before the hands-on lab or the machine where you have downloaded the exercise files.

2.  Browse to the Azure portal and authenticate at <https://portal.azure.com/>.

3.  Click on **DC01** on the Azure dashboard.

4.  In the **Settings** area, click **Extensions**.

    ![Under Settings, Extensions is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image65.png "Settings section")

5.  Click the **+ Add** icon.

    ![Screenshot of the Add icon.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image66.png "Add icon")

6.  Choose **Custom Script Extension** by Microsoft Corp., and click the **Create** button to continue.

    ![The Custom Script Extention option displays.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image67.png "Custom Script Extention option")

7.  Browse to the **C:\\HOL** folder and select the **AddDC.ps1** script by clicking the folder icon for **Script file (Required)**. Then, click the **OK** button to continue.

    ![The Script file field is set to AddDC.ps1, and the OK button is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image68.png "Script section")

8.  This script will run the commands to add this DC to the domain as an additional DC in the contoso.com domain. Repeat these steps for **DC02**, **DC03**, and **DC04**.

9.  Once this succeeds, you will see a **Provisioning succeeded** message under **Extensions** for all four domain controllers.

    ![In the Extensions blade, the status for CustomScriptExtensions is Provisioning succeeded.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image69.png "Extensions blade")

    **Note**: If this was a live production environment, there would need to be some additional steps to clean up Region 1 and to configure DNS, Sites and Services, Subnets, etc. Please refer to documentation on running Active Directory Virtualized or in Azure for details. ADVM should be demoted gracefully, and if required, a new DC can be added to the ADAV Availability Set and data disk attached for F:\\.

10. Open the settings for **VNET2** in the Azure portal. Under DNS servers, remove the exiting custom DNS entry and add the two new domain controller IP addresses and click **Save**.

    - DNS servers
        - **172.16.2.4**
        - **172.16.2.5**

    ![A screen that shows setting the IP addresses for the two new DNS servers on the virtual network.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image70.png "DNS servers")

### Summary

In this exercise, you deployed Windows Server Active Directory and configured for resiliency using Azure Managed Disks and Availability Sets in the primary region and Availability Zones in the failover region. Availability Sets offer an SLA of 99.95% and Availability Zones offer an SLA of 99.99%. 

## Exercise 3: Build web tier and SQL for resiliency

Duration: 60 minutes

In this exercise, you will deploy resilient web servers using VM scale sets and a SQL Always-On Cluster for resiliency at the data tier.

### Task 1: Deploy SQL Always-On Cluster 

In this task, you will deploy a SQL Always-On cluster using an ARM template that deploys to your existing Virtual Network and Active Directory infrastructure.

1.  Navigate to <https://github.com/opsgility/cw-building-resilient-iaas-architecture-sql> and click the **Deploy to Azure Button**.

    ![The Deploy to Azure button is highlighted for deploying a sample from GitHub.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image71.png "Sample page")

2.  Specify the resource group name as **CloudShopRG** and ensure the region is set to **East US 2**. Leave the rest of the template parameters with their default values.

    ![The custom deployment blade is displayed with CloudShopRG as the resource group and East US 2 as the location.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image126.png "Custom deployment")

3.  Check the **I agree to the terms and conditions state above** checkbox on the page and click **Purchase**.

    ![A screen with the checkbox for I agree to the terms and conditions checked and the purchase button highlighted.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/2018-08-27-20-21-18.png "Terms and Conditions")

4.  Wait until the template deployment is complete before continuing. 

    **Note**: The deployment may take up to 20 minutes.

5.  Open a remote desktop connection to the **SQLVM-1** virtual machine you created in the previous task, and login using the **contoso\\demouser** account with the password **demo@pass123**.

    ![Screenshot of the Connect icon.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image52.png "Connect icon")

6.  Once connected, open the Windows Explorer, check to make sure the **F:\\** Drive is present, and the Database was restored to the **F:\\Data** directory.

7.  Next, run this command from **SQLVM-1** to create a Cluster for the SQL Always-On Group. **Start \> PowerShell \> Enter**, and execute the following commands:

    ```powershell
    New-Cluster -Name CLUST-1 -Node SQLVM-1,SQLVM-2,WITNESSVM -StaticAddress 10.0.1.8
    ```

8.  This will create a three-node cluster with a static IP address. It is also possible to use a wizard for this task, but the resulting cluster will require additional configuration to set the static IP address to be viable in Azure. This is due to the way Azure DHCP distributes IP addresses causing the cluster to receive the same IP address as the node it is executing on resulting in a duplicate IP address and failure of the cluster service.

    ![In the Administrator: Windows PowerShell window, PowerShell commands display. At this time, we are unable to capture all of the information in the window. Future versions of this course should address this.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image75.png "Administrator: Windows PowerShell window")

9.  Once the PowerShell command has completed, open the **Failover Cluster Manager**, expand the **CLUST-1** cluster, select Nodes, validate all nodes are online and Assigned Vote and Current Vote are listed as "1" for all nodes of the cluster.

    ![In Failover Cluster Manager, in the Nodes pane, three Nodes display: SQLVM-1, SQLVM-2, and Witness VM. Their Assigned Votes and Current votes are all 1.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image76.png "Failover Cluster Manager")

10. Launch **SQL Server 2016 Configuration Manager** on **SQLVM-1**.

    ![SQL Server 2016 Configuration Manager is typed in the search field, and below, SQL Server 2016 Configuration Manager is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image77.png "Search field and results")

11. Click **SQL Server Services**, right-click **SQL Server (MSSQLSERVER)**, and select **Properties**.

    ![In SQL Server 2016 Configuration Manager, in the left pane, under SQL Server Configuration Manager (Local), SQL Server Services is selected. In the right pane, under Name, SQL Server (MSSQLSERVER) is selected, and Properties is selected from its right-click menu.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image78.png "SQL Server 2016 Configuration Manager")

12. Select the **AlwaysOn High Availability** tab, check the box for **Enable AlwaysOn Availability Groups**, click **Apply**, and click **OK** on the message that changes will not take effect until after the server is restarted.

    ![In the SQL Server (MSSQLSERVER) Properties dialog box, on the AlwaysOn High Availability tab, the Enable AlwaysOn Availability Groups checkbox is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image79.png "SQL Server (MSSQLSERVER) Properties dialog box")

13. On the **Log On** tab, change the service account to **contoso\\demouser** using **demo\@pass123** for the password. Click **OK** to accept the changes, and click **Yes** to confirm the restart of the server.

    ![In the SQL Server (MSSQLSERVER) Properties dialog box, on the Log On tab, in the Account Name field, contoso\\demouser is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image80.png "SQL Server (MSSQLSERVER) Properties dialog box")

    ![On the Confirm Account Change pop-up, the Yes button is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image81.png "Confirm Account Change pop-up")

14. Minimize the RDP Window for **SQLVM-1**.

15. From the Azure portal, locate **SQLVM-2**, and click **Connect.** Make sure to Sign On using the **contoso\\demouser** domain account.

    ![On the Windows Security login page, the contoso\\demouser credentials are called out.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image82.png "Windows Security login page")

16. From the RPD Session on **SQLVM-2**, repeat steps to configure **AlwaysOn High Availability** and **Log On** using SQL 2016 Configuration Manager.

17. Move back to RDP session with **SQLVM-1**.

18. Launch **SQL Server Management Studio 17**, and connect to the local instance of SQL Server.

19. Click **Connect** to login to SQL Server.

    ![The Connect to Server dialog box displays.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/2018-08-28-19-36-49.png "Connect to Server dialog box")

    **Note**: Availability Groups require that the databases be in full recovery mode and that an initial backup has been taken. The deployment through an ARM template has completed this for you.

20. Minimize your **SQLVM-1** RDP Session and then Copy from your **LABVM** the file **C:\\HOL\\CreateAGRegion1.sql** and then back on **SQLVM-1** paste it into the **C:\\SQDATA** directory.

21. Within SQL Server Management Studio, open the **C:\\SQDATA\\CreateAGRegion1.sql** file.

    ![In the SQL Server Management Studio (Administrator) window, on the menu bar, File is selected. On the file menu, Open is selected, and from its menu, File is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image85.png "SQL Server Management Studio (Administrator) window")

22. Select the **Query** menu and click **SQLCMD Mode**.

    ![On the Query tab, SQLCMD Mode is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image86.png "Query tab")

23. Click the **Execute** button to configure the Availability Group.

    ![Screenshot of the Execute button.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image127.png "Execute button")

    **Note**: Some security messages are expected. This script was generated by the SQL Server New Availability Group Wizard and modified to support AUTOMATIC\_SEEDING. Automatic seeding makes initializing replicas much easier, and the speed of the process is increased significantly. For more details on automatic seeding and performance improvements please refer to SQLCAT's blog: <https://blogs.msdn.microsoft.com/sqlcat/2016/06/28/sqlsweet16-episode-2-availability-groups-automatic-seeding-2/>.

    ![On the Messages tab, Security messages showing Connecting and Disconnecting activity display. At this time, we are unable to capture all of the information in the messages. Future versions of this course should address this.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image88.png "Messages tab")

24. Expand **AlwaysOn High Availability -\> Availability Groups**, right-click **AdventureWorksAG** (Primary), and choose **Show Dashboard**. Your dashboard should look similar to this:

    ![On the Dashboard, a green checkmark displays next to AdventureWorksAG: hosted by SQLVM - 1 (Replica role: Primary). The Availability group state is Healthy, and Synchronization state for SQLVM-1 SQLVM-2, AdventureWorks SQLVM-1 and AdventureWorks SQLVM-1 is Synchronized.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image89.png "Dashboard")

25. In the Azure portal, open the settings of the **BackendLB** load balancer in the **CloudShopRG** resource group.

    ![The BackendLB Load balancer option displays.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image90.png "BackendLB option")

26. Click on **Backend pools**.

    ![Under Settings, Backend pools is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image91.png "Settings section")

27. Click **BackendPool1** which will open a window showing **SQLVM-1**. Click the **Add a target network IP configuration**.

    ![In the BackendPool1 blade, the Add a target network IP configuration link is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image92.png "BackendPool1 blade")

28. From the List for Target Virtual Machine select the **SQLVM-2** and the Network IP Configuration **ipconfig1**. Note that your IP address may be different.

    ![Fields in the BackendPool1 blade are set to the previously defined settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image93.png "BackendPool1 blade")

29. Click the **Save** to add **SQLVM-2** to the **BackendPool1**.

    ![Under BackendPool1 (2 virtual machines), SQLVM-1 and SQLVM-2 display.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image94.png "BackendPool1 list")

30. Go back to **SQLVM-1** and open an **Administrative PowerShell\_ISE** session (Right click > More > Run as administrator). Execute the following PowerShell to configure your cluster for the probe port.

    ```powershell
    $ClusterNetworkName = "Cluster Network 1"
    $IPResourceName = "AdventureWorksAG_10.0.1.9"
    $ILBIP = "10.0.1.50"
    Import-Module FailoverClusters
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    Stop-ClusterResource -Name $IPResourceName
    Start-ClusterResource -Name "AdventureWorksAG"
    ```

    ![Commands display in the Administrator PowerShell ISE window with the output of the script showing that the cluster resources has been started.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image128.png "Administrator PowerShell ISE window")

31. Connect to **SQLVM-02** and launch **SQL Server Management Studio 17Ad**.

32. Open a Server connection to the **AdventureWorks** listener endpoint to verify connectivity. The listener is like entering a SQL Server's Name, but this is the Availability Group.

    ![The Connect to Server for SQL Server dialog box displays. Server type is Database Engine, Server name is AdventureWorks, and Authentication is Windows Authentication.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image96.png "Connect to Server for SQL Server dialog box")

    ![In Object Explorer, AdventureWorks (SQL Server 13.0.2164.0 - contoso\\demouser is selected.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image97.png "Object Explorer")

33. After successfully connecting to the AOG listener, disconnect from both SQLVM-1 and SQLVM-2 by using Sign Out from the RDP windows.

### Task 2: Convert the SQL deployment to Managed Disks 

In this task, you will convert the disks of the SQL deployment to managed disks. This task could be automated as part of the template deployment; however, it is important to understand how to migrate existing infrastructure to managed disks.

1.  On **LABVM** open the PowerShell ISE Tool.

    **Note**: In the next few steps, you will use PowerShell to migrate the disks for the SQL cluster to Managed Disks.

2.  In the execution pane, login to Azure using the **Connect-AzureRmAccount**, and press Enter.

    ```powershell
    Connect-AzureRmAccount
    ```

3.  At the Azure login screen, enter your Account and Password.

    ![The Microsoft Azure login screen displays.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image98.png "Azure login screen")

4.  Once logged in, make sure to set your subscription that is the default for this hands-on lab.

    ```powershell
    Get-AzureRMSubscription
    Select-AzureRmSubscription -SubscriptionName <Your subscription name>
    ```

5.  Once this is completed, run the following command to verify your VMs for the hands-on lab are present.

    ```powershell
    Get-AzureRMVM -ResourceGroupName CloudShopRG
    ```
    
6.  Now, move to the scripting pane of the PowerShell ISE tool. Paste this code into the window.

    ```powershell
    <#
        The following code converts the existing availability set to aligned/managed and then converts the disks to managed as well. 
        Note: The PlatformFaultDomainCount is set to 3 - this is because the region currently supports three managed fault domains.
        Some regions only support 2. Refer to the documentation.
    #>

    $rgName = 'CloudShopRG'

    $avSetName = 'SQLAVSet'

    $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

    $avSet.PlatformFaultDomainCount = 3

    Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned

    foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
        $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

        Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force

        ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
    }
    ```

    **Note**: The documentation for the maximum number of fault domains per-region can be found at <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region>.

7.  Next, click the **Run script** button in the PowerShell ISE. This will deallocate all of the machines in the Availability Set SQLAvSet and migrate them to a Managed AVSET and the disk to Managed Disks.

    **Note**: This process will take about 10-15 minutes to complete. Do not stop the process or close the ISE.

8.  Open the Azure portal and browse to the **CloudShopRG** Resource Group. Notice now, the machines are using Managed Disks, and the disk objects now appear.

    ![Four Disk objects display: SQLVM-1, SQLVM-1\_SQL1datadisk1, SQLVM-1\_SQL1datadisk2, and SQLVM-1\_SQLVM-1OSDisk.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image99.png "Disk objects")

### Task 3: Build a scalable and resilient web tier

In this task, you will deploy a VM scale set that can automatically scale up or down based on the CPU criteria. The application the scale set deploys points to the new SQL AlwaysOn availability group created previously.

1.  Navigate to <https://github.com/opsgility/cw-building-resilient-iaas-architecture-ss> and click the **Deploy to Azure Button**.

    ![The Deploy to Azure button is highlighted for deploying a sample from GitHub.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image100.png "Sample screen")

2.  Specify the existing resource group **CloudShopRG** and set the **Instance Count to 2**.

    **Note**: The instance count is the initial number of servers deployed. The number can change based on the auto scale rules set in the ARM template.

3.  Check the **I agree to the terms and conditions state above** checkbox on the page and click **Purchase**.

4.  While the scale set is deploying, open the ARM template you just deployed by navigating to: <https://github.com/opsgility/cw-building-resilient-iaas-architecture-ss/blob/master/azure-deploy.json>. Review the auto scale settings in the **autoscalewad** resource to understand how the default auto scale settings are configured.

### Summary

In this exercise, you deployed resilient web servers behind a load balancer, and a SQL Always-On Availability Group for database resiliency through an ARM template.

## Exercise 4: Configure SQL Server Managed Backup 

Duration: 15 minutes

In this exercise, you will configure SQL Server Managed Backup to back up to an Azure Storage Account.

### Task 1: Create an Azure Storage Account

In this task, you will add a 3rd node to the SQL Always-On deployment in a second region that you can then failover with Azure Site Recovery in the event of a failure in the primary region.

1.  From **LABVM**, execute the following PowerShell commands in the PowerShell ISE to create a new storage account and generate the T-SQL needed to configure managed backup for the AdventureWorks database.

    ```powershell
    $storageAcctName = "[unique storage account name]"

    $resourceGroupName = "CloudShopRG"
    $containerName= "backups"
    $location = "East US 2"
    $storageSkuName = "Standard_LRS"

    "Creating Storage Account $storageAcctName"
    $sa = New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName  `
        -Name $storageAcctName `
        -Location $location `
        -SkuName $storageSkuName 

    $storageKey = (Get-AzureRmStorageAccountKey -Name $storageAcctName -ResourceGroupName $resourceGroupName )[0].Value
    $context = New-AzureStorageContext -StorageAccountName $storageAcctName -StorageAccountKey $storageKey

    Write-Host "Creating New Storage Container  $containerName" 
    New-AzureStorageContainer -name $containerName -permission container -context $context

    $fullSasToken = New-AzureStorageContainerSASToken -Name $containerName -Permission rwdl -FullUri -Context $context  
    $containerUrl = $fullSasToken.Substring(0,$fullSasToken.IndexOf("?"))
    $sasToken = $fullSasToken.Substring($fullSasToken.IndexOf("?")+1)

    $enableManagedBackupScript = @"
    --------------------
    ---BEGIN TSQL Script
    --------------------
    CREATE CREDENTIAL [$containerUrl] 
    WITH IDENTITY = 'Shared Access Signature', 
         SECRET = '$sasToken' 

    GO

    EXEC msdb.managed_backup.sp_backup_config_basic   
     @enable_backup = 1,   
     @database_name = 'AdventureWorks',  
     @container_url = '$containerUrl',   
     @retention_days = 30
       
     --------------------
     ---END TSQL Script
     --------------------
    "@

    Write-Host $enableManagedBackupScript 
    ```

2.  Execute the code using PowerShell ISE. Make sure you change the **\$storageAcctName = \"\[unique storage account name\]\"** field to a unique storage account name across Azure prior to execution. 

3.  Save the T-SQL code generated between the **Begin TSQL Script** and **End TSQL Script** in your PowerShell ISE output after execution into a notepad file. This code creates an identity using a Shared Access Signature (SAS) to a container in the storage account and configures managed backup when executed.

### Task 2: Configure managed backup in SQL Server

1.  Connect to **SQLVM-1** using remote desktop and launch SQL Server Management Studio and connect to the database instance.

2.  Right click on **SQLVM-1**, and click **New Query**.

    ![A screen showing how to launch the new query pane in SQL Server Management Studio.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image102.png "Launching the new query pane")

3.  Paste in the following code and click **Execute** to enable SQL Server Agent extended stored procedures. Refresh SQL Server Management Studio and if SQL Server Agent is stopped right click on it and click Start.

    ```sql
    EXEC sp_configure 'show advanced options', 1
    GO
    RECONFIGURE
    GO
    EXEC sp_configure 'Agent XPs', 1
    GO
    RECONFIGURE
    GO
    ```

4.  Paste the T-SQL code you copied in the previous task into the query window replacing the existing code and click **Execute**. This code creates the new SQL identity with a Shared Access Signature for your storage account. 

5.  Paste the code into the query window replacing the existing code and click **Execute** to create a custom backup schedule.

    ```sql
    USE msdb;  
    GO  
    EXEC managed_backup.sp_backup_config_schedule   
         @database_name =  'AdventureWorks'  
        ,@scheduling_option = 'Custom'  
        ,@full_backup_freq_type = 'Weekly'  
        ,@days_of_week = 'Monday'  
        ,@backup_begin_time =  '17:30'  
        ,@backup_duration = '02:00'  
        ,@log_backup_freq = '00:05'  
    GO
    ```
6.  Execute the following tSQL in the query window to generate a backup on-demand. You can also specify Log for \@type.

    ```sql
    EXEC msdb.managed_backup.sp_backup_on_demand   
    @database_name  = 'AdventureWorks',
    @type ='Database'
    ```
    
## Exercise 5: Validate resiliency

### Task 1: Validate resiliency for the CloudShop application 

1.  In the Azure portal, open the **CloudShopRG** resource group. Click the VM scale set created in the previous task.

2.  Click the Scaling menu item to review the auto scale settings that were deployed with the ARM template.

    ![The scaling menu item under settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image103.png "Scaling")

    ![The screen depicts the auto scale rules deployed by the ARM template.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image104.png "Auto scale settings")

3.  Click the **Overview** tab and copy the public IP address to the clipboard, and navigate to it in a different browser tab.

4.  After the application is loaded, click the Spike CPU button to simulate an auto scale event.

    ![A screen that shows the web page that allows for spiking the CPU,.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image105.png "CPU Spike Demo")

5.  After 15-20 minutes, click the instances button to validate that additional instances were added in response to the CPU spike.

    ![The instances icon under settings.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image106.png "Instances")

    You will see something like the following after a while with new instances starting.

    ![Multiple instances running and in the creating state for the VM scale set.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image107.png "Instance status")

### Task 2: Validate SQL Always On

1.  Within the Azure portal, click on Virtual Machines and open **SQLVM-1.** Click **Stop** at the top of the blade to shut the virtual machine off.

2.  After the VM is deallocated, refresh the CloudShop application in your browser. If the page loads with data in the dropdown list SQL has successfully failed over the primary node to the secondary. You can login to the secondary vm (SQLVM-2) and connect via SQL Server Management Studio to confirm.

### Task 3: Validate backups are taken 

1.  In the Azure portal, click All Services and search for Recovery Vault. Click the link and you should see the two recovery vaults created as part of the deployment of the Active Directory domain controllers.

2.  Open each vault and validate that a backup of the VM has occurred.

    ![The screen shows 2 backup items from one of the vaults.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image109.png "Usage")

    **Note**: Backup storage consumption may be 0 B if a backup has not occurred. The timing of backups is driven by the policy associated with the backup. Only one policy can be assigned to a virtual machine when using the Azure Backup Extension for Virtual Machines.

3.  To validate the SQL Server backup, open the Storage Account created earlier in the Azure portal and click **Blobs** -\> and then **backups**. If the backup has already completed, you will see the backup file in the container.

    ![An image that depicts SQL Server backup data in an Azure Storage Account.](images/Hands-onlabstep-bystep-BuildingaresilientIaaSarchitectureimages/media/image110.png "Backup files in storage")

## After the hands-on lab

### Task 1: Delete the resource groups created

1.  Within the Azure portal, click Resource Groups on the left navigation.

2.  Delete each of the resource groups created in this lab by clicking them followed by clicking the Delete Resource Group button. You will need to confirm the name of the resource group to delete.

You should follow all steps provided *after* attending the hands-on lab.

