![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Building a resilient IaaS architecture
</div>

<div class="MCWHeader2">
Whiteboard design session student guide
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

- [Building a resilient IaaS architecture whiteboard design session student guide](#building-a-resilient-iaas-architecture-whiteboard-design-session-student-guide)
    - [Abstract and learning objectives](#abstract-and-learning-objectives)
    - [Step 1: Review the customer case study](#step-1-review-the-customer-case-study)
        - [Customer situation](#customer-situation)
        - [Customer needs](#customer-needs)
        - [Customer objections](#customer-objections)
        - [Infographic for common scenarios](#infographic-for-common-scenarios)
    - [Step 2: Design a proof of concept solution](#step-2-design-a-proof-of-concept-solution)
    - [Step 3: Present the solution](#step-3-present-the-solution)
    - [Wrap-up](#wrap-up)
    - [Additional references](#additional-references)

<!-- /TOC -->

#  Building a resilient IaaS architecture whiteboard design session student guide

## Abstract and learning objectives 

In this whiteboard design session, you will look at how to design for converting/extending an existing IaaS deployment to account for resiliency and in general high availability. Throughout the whiteboard design session, you will look at the various configuration options and services to help build resilient architectures.

At the end of the workshop, you will be better able to design and use availability sets, Managed Disks, SQL Server Always on Availability Groups, as well as design principles when provisioning storage to VMs. In addition, you'll learn effective employment of Azure Backup to provide point-in-time recovery.

## Step 1: Review the customer case study 

**Outcome** 

Analyze your customer’s needs.

Time frame: 15 minutes

Directions: With all participants in the session, the facilitator/SME presents an overview of the customer case study along with technical tips. 
1.  Meet your table participants and trainer .
2.  Read all of the directions for steps 1–3 in the student guide .
3.  As a table team, review the following customer case study.
 
### Customer situation

Contoso, is a leading manufacturer, seller, distributor and servicer of parts for heating, venting and air-conditioning (HVAC) systems. Their customer base includes some of the largest corporations and independent firms in the US. Contoso specializes in the datacenter space, designing computer room air conditioning (CRAC) units and contracting in the planning of hyper-scale cloud provider datacenter cooling strategies. As such, the research and development group are one of the largest business units in the company. The company's headquarters in in Cheyenne, Wyoming with a second large location in Seattle, Washington along with three smaller branch offices scatted around the United States.

Contoso would be considered by most as a classic IT shop, mainly focused on their infrastructure. Their application development department's skill set is dated, predominantly focused on client/server development. Two years ago, the company began a project to move portions of their infrastructure to Azure to gain efficiencies and eventually exit the hardware obsolescence cycle. In the process Contoso developed a standard deployment policy for new infrastructure that has been followed ever since.

Their ordering system is running in Azure on virtual machines with a SQL Server data tier. For many years Contoso's ordering process was done mainly via phone by their sales team based in one of their offices. Recently, the company has moved to an Internet-based ordering system with Internet Information Services (IIS) web servers in Azure housing the front-end application for the ordering, invoicing, and support options. Customers have complained at times of intermittent problems/errors with the website.

There have been ongoing stability issues including a critical server running out of disk space. This along with recent stability issues with the ordering system prompted Contoso to perform a business impact analysis of the application. This exercise resulted in an executive mandate for a four-hour recovery time objective (RTO) with a recovery point objective (RPO) for the data of 8 hours.

In addition to the ordering system, they have a legacy software program where the data is tightly coupled with the application. Because of the time and effort required, a re-write of this application is not planned. The application is being backed up using a disk-to-disk-to-tape approach. The legacy application is running on aging hardware and a decision must be made as to whether to purchase new hardware, re-write the application, or move it as-is to Azure.

Each of the branch offices are small enough to not require an on-site server infrastructure. These locations have connectivity to the Cheyenne headquarters through a Virtual Private Network (VPN). At times, various branch offices have experienced connectivity issues over the VPN to Cheyenne. While there is some understanding of these occurrences, there is a desire to increase the stability of the connection as growth continues.

Several years ago, under the leadership of Lewis Franklin, head of infrastructure and operations, individual departments started migrating their servers into Azure. The Active Directory Domain Services (ADDS) team has deployed several Domain Controller (DC) Virtual Machines (VM) to a virtual network in the West Central US region. This region was chosen due to its proximity to the Cheyenne Headquarters. Some effort was made to follow the guidance of Microsoft on the use of Active Directory (AD) in Azure, but some configuration gaps remain.

The web application team has also deployed the Ordering application within the same West Central US region. While they do not have administrative rights for the SQL VMs that provide database services for the web application, they do have database rights and can access the databases through normal SQL toolsets.

Taking their cue from the AD and Web teams, the Database Administrators have also rolled out their SQL servers onto Azure VMs, choosing to host them in the West Central US region as well.

The Marketing department has recently been tasked with moving their server workloads into Azure by rebuilding each application. They have begun building their servers utilizing a single Azure storage account. They have around 40-50 VMs already but anticipate continued growth.

![The current storage account implementation has 40 to 50 virtual machines, with a Single Blob LRS Storage account, made up of 40 to 50 VHDs, one per VM.](images/Whiteboarddesignsessiontrainerguide-BuildingaresilientIaaSarchitectureimages/media/image2.png "Storage Account Current Implementation")
*Figure 2 -- Contoso's Storage Account Configuration*

Contoso is connected via a Windows Server Routing and Remote Access Service (RRAS) VPN connection to Azure via a Site-to-Site Gateway. They are looking for options to provide redundancy for the hybrid connectivity to Azure due to recent network issues.

While the Azure deployments have served Contoso well so far, they are concerned about expanding workloads for their Seattle datacenter. Janet Lewis, business continuity team director, says, "it appears that while services have moved to the cloud, the overall paradigm has not moved from the single datacenter model we have always deployed."

Over a recent three-day holiday weekend, there was an incident with one of the ADDS Domain Controllers where the disk drive housing the AD database filled up and corrupted the database. This prompted a high-priority support call to Microsoft. While the damage was mitigated, the team was fortunate that the consequences were minimal.

Retroactively, checks were made on other Azure VM disk drives and there were several of them that were getting close to capacity due to teams not proactively monitoring their servers. Management is now calling for a proactive approach to monitoring operating system (OS) disk and data disk free space as well as other key metrics in the environment.

The Human Resources department has requested that IT move their server workloads into Azure and this process has begun. Their organization has a pre-existing policy that they create a new storage account for each VM in the subscription. They have deployed about 40-50 VMs but anticipate growth that will more than double the size of their deployment numbers.

![The current HR Department Storage Account implementation is in Microsoft Azure. Every VM is deployed into its own storage account in the same subscription.](images/Whiteboarddesignsessiontrainerguide-BuildingaresilientIaaSarchitectureimages/media/image3.png "HR Department Storage Account Current Implementation")

Richard Wade, infrastructure lead, is researching options for making the overall Azure infrastructure more resilient; including looking at technologies to help recover from an Azure region-wide outage. He also is interested in protecting against data corruption or accidental deletion of data or VMs.

For the current ADDS implementation in Azure, the team has deployed a single domain controller in the West Central US. region. It is running on a Standard D1 instance with with Active Directory deployed on the C: drive.

![This image represents a single domain controller in the West Central US region.](images/Whiteboarddesignsessiontrainerguide-BuildingaresilientIaaSarchitectureimages/media/image4.png "West Central US region - Single Domain Controller")

Additionally, the SQL Server VM and Web site implementation are also housed at the same region. SQL has been deployed on a single VM with multiple disks. One disk is utilized for the data; the other disk is for backup and log file storage. The underlying storage account is configured for geo replication.

They have deployed a load balancer in front of the web servers and configured a default health probe to monitor the servers in the load balanced pool. When they need scalability, they manually configure another web server and often leave it running even after the need for additional capacity has passed.

Contoso has received multiple complaints from customers at times when they have intermittently received HTTP 500 errors on the website. Upon investigation, it was discovered that a recent deployment failed on one of the servers in the farm and resulted in files not being correctly copied to this server.

![The SQL and Web Server Current Implementation diagram depicts three virtual machines behind a load balancer and availability set, and a single virtual machine for SQL server with two disks for data.](images/Whiteboarddesignsessiontrainerguide-BuildingaresilientIaaSarchitectureimages/media/image5.png "SQL and Web Server Current Implementation")


Contoso's business critical applications include:
-   The authentication and authorization infrastructure.
-   The website with its SQL data tier, supporting both employees, customers and third-party manufacturing plants and factories. Use cases include:
    -   Employees: Sales lead tracking
    -   Customers: Search the catalog of inventory, order parts, schedule repairs and provide support
    -   Third-party manufacturing plants and factories: Supply manufacturing schedules to Contoso

### Customer needs 

1.  The IT department is using outdated guidance on Azure and they need updated guidance on current architectural and deployment best practices.

2.  They need assistance with enabling connectivity and authentication for new infrastructure that will be deployed for the Seattle office.

3.  Identify the infrastructure requirements that should to be configured to provide redundancy and resiliency to the web servers and the database servers for the ordering application for scale, backup and resiliency.

4.  A plan for recovery from data corruption or accidental deletion for all of the other infrastructure.

5.  A functional storage policy in place for the anticipation of growth in Azure.

6.  Monitoring option for issues that may arise on the servers and in Azure.

### Customer objections 

1.  Cost is a huge concern for us. With looming infrastructure and server replacement costs, we want to avoid any unnecessary expenditures.

2.  The web application needs to have the ability to scale as we grow. What aspect of the cloud will allow this to be a reality?

3.  Downtime is becoming more of an issue for us due to development and production environments not being separate. We need to separate these from one another to avoid outages.

4.  Bandwidth is becoming an issue for self-hosting our application's ordering system, support website, etc. We are concerned that the cloud maybe constrained as well.

5.  We are very concerned about the disk space issue that occurred earlier with our Active Directory Domain Services domain controller and nearly all of our Web Servers and Database Servers. Will this be addressed per the resiliency plan?

### Infographic for common scenarios

![An image that depicts various services in Azure Infrastructrure as a Service that could be used for the solution. Services included are: Virtual Machines, Virtual Networks, VPN Gateways, Hybrid Connectivity, Load Balancers, STorage, Azure Backup, Azure Site Recovery and SQL Server Managed Backup.](images/Whiteboarddesignsessiontrainerguide-BuildingaresilientIaaSarchitectureimages/media/image6.png "Common Scenarios")

## Step 2: Design a proof of concept solution

**Outcome** 
Design a solution and prepare to present the solution to the target customer audience in a 15-minute chalk-talk format. 

Time frame: 60 minutes

**Business needs**

Directions: With all participants at your table, answer the following questions and list the answers on a flip chart: 
1.  Who should you present this solution to? Who is your target customer audience? Who are the decision makers? 
2.  What customer business needs do you need to address with your solution?

**Design** 

Directions: With all participants at your table, respond to the following questions on a flip chart.

The desired outcome is to have authentication deployed using best practices in both the Cheyenne and the Seattle regions for current and future application migration.

For the ordering web app, redundancy and protection in case of failure is the main goal for all components of the design and the application should tolerate a failure in either region. The design should include network resiliency, as well as backup and restore methods in case of failure of the physical or VMs.

Directions: Design the solution architecture by drawing it on the board, and separately provide insight into how you will address the following requirements. Identify the steps needed to implement a proof of concept for the proposed solution(s) as well as what would need to be demonstrated to stakeholders.

*Virtual Network design in Azure*

1.  Document and diagram how you will build redundant Virtual Networks for Contoso. Address the following design points:

    -   Must allow for connectivity between two regions close to the Cheyenne and Seattle data centers

    -   Address the need for redundancy and resiliency in the site-to-site VPN connectivity from Contoso's offices to Azure

    -   How will you design the address space and subnets to support Contoso's requirements?

2.  Document what network security groups and rules should be put in place for protection. What ports would you open and why?

*Design for resiliency in Azure*

1.  What changes, if any, will be needed to provide resiliency for the AD servers? Diagram how many and where they need to be placed.

2.  How will you address the needs for resiliency and scalability with the ordering app?

3.  Consider storage account resiliency. What would best suit the needs for Contoso virtual machines? LRS, GRS, RA-GRS? Document why you chose the option you did. Should they move to Managed Disks?

4.  How would you address the needs of the legacy application, what storage tier and limitations do you have to work around? What SLA can Azure provide for this single instance VM?

5.  Provide Contoso with documentation concerning service limitations, quotas, subscription limits.

6.  What would you recommend Contoso enable for monitoring their environment?

**Prepare**

Directions: With all participants at your table: 

1.  Identify any customer needs that are not addressed with the proposed solution.
2.  Identify the benefits of your solution.
3.  Determine how you will respond to the customer’s objections.

Prepare a 15-minute chalk-talk style presentation to the customer. 

## Step 3: Present the solution

**Outcome**
 
Present a solution to the target customer audience in a 15-minute chalk-talk format.

Timeframe: 30 minutes

**Presentation** 

Directions:
1.  Pair with another table.
2.  One table is the Microsoft team and the other table is the customer.
3.  The Microsoft team presents their proposed solution to the customer.
4.  The customer makes one of the objections from the list of objections.
5.  The Microsoft team responds to the objection.
6.  The customer team gives feedback to the Microsoft team.
7.  Tables switch roles and repeat Steps 2–6.


##  Wrap-up 

Timeframe: 15 minutes

Directions: Tables reconvene with the larger group to hear the facilitator/SME share the preferred solution for the case study.

##  Additional references

|    |            |
|----------|:-------------:|
| **Description** | **Links** |
| Microsoft Azure Reference Architectures| <https://docs.microsoft.com/en-us/azure/guidance/guidance-architecture> |
| High availability checklist | <https://docs.microsoft.com/en-us/azure/resiliency/resiliency-high-availability-checklist> |
| Azure resiliency technical guidance | <https://azure.microsoft.com/en-us/documentation/articles/resiliency-technical-guidance/> |
| Guidelines for Deploying Windows Server AD on Azure VMs | <https://msdn.microsoft.com/en-us/library/azure/jj156090.aspx> |
| Running your AD in Windows Azure | <https://channel9.msdn.com/Events/TechDays/Techdays-2014-the-Netherlands/Running-your-Active-Directory-in-Windows-Azure> |
| Running VMs for an N-tier architecture on Azure | <https://docs.microsoft.com/en-us/azure/guidance/guidance-architecture> |
| Azure Managed Disks | <https://azure.microsoft.com/en-us/services/managed-disks/> <https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview> |
| Azure Subscription and Service Limits | <https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits> |
| Network Watcher and VPN Gateways | <https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitor-with-azure-automation> |
| High availability with VPN Gateway | <https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-highlyavailable> |
