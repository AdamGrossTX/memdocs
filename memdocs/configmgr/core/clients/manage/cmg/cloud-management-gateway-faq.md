---
title: CMG FAQ
titleSuffix: Configuration Manager
description: Use this article to answer frequently asked questions regarding the cloud management gateway
author: aczechowski
ms.author: aaroncz
manager: dougeby
ms.date: 06/10/2020
ms.topic: conceptual
ms.prod: configuration-manager
ms.technology: configmgr-client
ms.assetid: 4c1a128d-22fb-49f1-8e0b-36513a8dc117
---

# Frequently asked questions about the cloud management gateway

*Applies to: Configuration Manager (current branch)*

This article answers your frequently asked questions about the cloud management gateway. For more information, see [plan for cloud management gateway](plan-cloud-management-gateway.md).

## Frequently asked questions

### What certificates do I need?

For more detailed information, see [certificates for cloud management gateway](certificates-for-cloud-management-gateway.md).

### Do I need Azure ExpressRoute?

No. [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) lets you extend your on-premises network into the Microsoft cloud. ExpressRoute, or other such virtual network connections aren't required for the Configuration Manager cloud management gateway. The design of the cloud management gateway allows internet-based clients to communicate through the Azure service to on-premises site systems with no additional network configuration. For more information, see [Plan for cloud management gateway](plan-cloud-management-gateway.md)

<!-- SCCMDocs#1659 -->

### Do I need to maintain the Azure virtual machines?

No maintenance is required. The design of the cloud management gateway uses Azure platform as a service (PaaS). Using the subscription you provide, Configuration Manager creates the necessary virtual machines (VMs), storage, and networking. Azure secures and updates the virtual machine. These VMs aren't a part of your on-premises environment, as is the case with infrastructure as a service (IaaS). The cloud management gateway is a PaaS that extends your Configuration Manager environment into the cloud. For more information, see [Securing PaaS deployments](/azure/security/security-paas-deployments).

### How can I ensure service continuity during service updates?

By scaling CMG to include two or more instances, you automatically benefit from Update Domains in Azure. See [How to update a cloud service](/azure/cloud-services/cloud-services-update-azure-service).

### I'm already using IBCM. If I add CMG, how do clients behave?

If you already deployed [internet-based client management](../plan-internet-based-client-management.md) (IBCM), you can also deploy the cloud management gateway. Clients receive policy for both services. As they roam onto the internet, they randomly select and use one of these internet-based services.

### <a name="bkmk_tenant"></a> Do the user accounts have to be in the same Azure AD tenant as the tenant associated with the subscription that hosts the CMG cloud service?
<!--SCCMDocs-pr issue #2873-->
No, you can deploy CMG into any subscription that can host Azure cloud services.

To clarify terms:

- The _tenant_ is the directory of user accounts and app registrations. One tenant can have multiple subscriptions.
- A _subscription_ separates billing, resources, and services. It's associated with a single tenant.

This question is common in the following scenarios:  

- When you have distinct test and production Active Directory and Azure AD environments, but one single, centralized Azure hosting subscription.

- Your use of Azure has grown organically across different teams

When you're using a Resource Manager deployment, onboard the Azure AD tenant associated with the subscription. This connection allows Configuration Manager to authenticate to Azure to create, deploy, and manage the CMG.  

If you're using Azure AD authentication for the users and devices managed over the CMG, onboard that Azure AD tenant. For more information on Azure services for cloud management, see [Configure Azure services](../../../servers/deploy/configure/azure-services-wizard.md). When you onboard each Azure AD tenant, a single CMG can provide Azure AD authentication for multiple tenants, regardless of the hosting location.

#### Example 1: One tenant with multiple subscriptions

The user identities, device registrations, and app registrations are all in the same tenant. You can choose which subscription the CMG uses. You can deploy multiple CMG services from one site into separate subscriptions. The site has a one-to-one relationship with the tenant. You decide which subscriptions to use for various reasons such as billing or logical separation.

#### Example 2: Multiple tenants

In other words, your environment has more than one Azure AD. If you need to support user and device identities in both tenants, you need to attach the site to each tenant. This process requires an administrative account from each tenant to create the app registrations in that tenant. One site can then host CMG services in multiple tenants. You can create a CMG in any available subscription in either tenant. Devices that are joined or hybrid joined to either Azure AD could use a CMG.

If the user and device identities are in one tenant, but the CMG's subscription is in another tenant, you need to attach the site to both tenants. Technically, the client app isn't needed for the second tenant that only has the CMG service. The client app only provides user and device authentication for clients that use the CMG service.<!-- SCCMDocs#1902 -->

### How does CMG affect my clients connected via VPN?

Roaming clients that connect to your environment via a VPN are commonly detected as intranet-facing. They attempt to connect to your on-premises infrastructure such as management points and distribution points. Some customers prefer to have these roaming clients managed by cloud services even when connected via VPN. Starting in version 1902, associate the CMG with a boundary group. This action forces these clients to not use the on-premises site systems. For more information, see [Configure boundary groups](setup-cloud-management-gateway.md#configure-boundary-groups).

### If I enable a CMG, will my clients only connect to the CMG-enabled management point when they're connected to the intranet?

In order to secure sensitive traffic sent over a CMG, either configure an HTTPS management point or use Enhanced HTTP.

If you choose to deploy a CMG, and use PKI certificates for HTTPS communication on the CMG-enabled management point, select the option to **Allow internet-only clients** on the management point properties. This setting makes sure that internal clients continue to use HTTP management points in your environment.

If you use Enhanced HTTP, you don't need to configure this setting. Clients continue to use HTTP when communicating directly to the CMG-enabled management point. For more information, see [Enhanced HTTP](../../../plan-design/hierarchy/enhanced-http.md).

## Next steps

- [Plan for cloud management gateway](plan-cloud-management-gateway.md)
- [Set up cloud management gateway](setup-cloud-management-gateway.md)
- [Certificates for cloud management gateway](certificates-for-cloud-management-gateway.md)
- [Security and privacy for cloud management gateway](security-and-privacy-for-cloud-management-gateway.md)
- [Cloud management gateway size and scale numbers](../../../plan-design/configs/size-and-scale-numbers.md#bkmk_cmg)
