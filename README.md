# Building a resilient IaaS architecture 

Contoso, is a leading manufacturer, seller, distributor and servicer of parts for heating, venting and air-conditioning (HVAC) systems. Their customer base includes some of the largest corporations and independent firms in the US. Contoso specializes in the datacenter space, designing computer room air conditioning (CRAC) units and contracting in the planning of hyper-scale cloud provider datacenter cooling strategies. As such, the research and development group are one of the largest business units in the company. The company's headquarters in in Cheyenne, Wyoming with a second large location in Seattle, Washington along with three smaller branch offices scatted around the United States.

Several years ago, under the leadership of Lewis Franklin, head of infrastructure and operations, individual departments started migrating their servers into Azure. The Active Directory Domain Services (ADDS) team has deployed several Domain Controller (DC) Virtual Machines (VM) to a virtual network in the West Central US region. This region was chosen due to its proximity to the Cheyenne Headquarters. Some effort was made to follow the guidance of Microsoft on the use of Active Directory (AD) in Azure, but some configuration gaps remain.

## Target audience

- Enterprise Architects
- Infrastructure Engineers

## Abstract

### Workshop

In this workshop, you will look at how an existing IaaS deployment can be redesigned and updated to account for resiliency and in general high availability. Throughout the whiteboard design session and hands-on lab, you will look at the various configuration options and services to help build resilient architectures.

At the end of this workshop, you will be better able to design and use Availability Zones, Managed Disks, SQL Server Always on Availability Groups, as well as design principles when provisioning storage to VMs. In addition, you'll learn effective employment of Azure Backup to provide point-in-time recovery.

### Whiteboard Design Session

In this whiteboard design session, you will look at how to design for converting/extending an existing IaaS deployment to account for resiliency and in general high availability. Throughout the whiteboard design session, you will look at the various configuration options and services to help build resilient architectures.

At the end of the workshop, you will be better able to design and use Availability Zones, Managed Disks, SQL Server Always on Availability Groups, as well as design principles when provisioning storage to VMs. In addition, you'll learn effective employment of Azure Backup to provide point-in-time recovery.

### Hands-on Lab

In this hands-on lab, you will deploy a pre-configured IaaS environment and then redesign and update it to account for resiliency and in general high availability. Throughout the hands-on lab you will use various configuration options and services to help build a resilient architecture.

At the end of the lab, you will be better able to design and use availability sets, Managed Disks, SQL Server Always on Availability Groups, as well as design principles when provisioning storage to VMs. In addition, you'll learn effective employment of Azure Backup to provide point-in-time recovery.

## Azure services and related products
- Azure Virtual Machines
- Azure Availability Zones
- Azure Portal
- Azure PowerShell
- Azure Storage
- Azure Active Directory
- Azure Web Apps
- Azure Virtual Networks
- SQL Server
- Operations Management Suite
- Azure Backup

## Azure solutions
DC Migration

## Related references
- [MCW](https://github.com/Microsoft/MCW)
- [Identity management](https://microsoft.sharepoint.com/sites/infopedia/pages/layouts/kcdoc.aspx?k=g01kc-1-30334)
- [Hybrid networking](https://microsoft.sharepoint.com/sites/infopedia/pages/layouts/kcdoc.aspx?k=g01kc-1-30331)
- [Design for resiliency](https://microsoft.sharepoint.com/sites/infopedia/pages/layouts/kcdoc.aspx?k=g01kc-1-30327)
- [Linux VMs](https://microsoft.sharepoint.com/sites/infopedia/pages/layouts/kcdoc.aspx?k=g01kc-1-30330)
- [Windows VMs](https://microsoft.sharepoint.com/sites/infopedia/pages/layouts/kcdoc.aspx?k=g01kc-1-30329)
- [DMZ](https://microsoft.sharepoint.com/sites/infopedia/pages/layouts/kcdoc.aspx?k=g01kc-1-30332)
- [Resiliency checklist](https://microsoft.sharepoint.com/sites/infopedia/pages/layouts/kcdoc.aspx?k=g01kc-1-30328)