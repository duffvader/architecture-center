---
title: Azure subscription design guide
description: Guidance for Azure subscription design as part of a cloud adoption strategy
author: alexbuckgit
---

# Azure subscription design guide

An [Azure subscription](subscription.md) provides you with access to available Azure services. Starting with a single subscription, you can begin to deploy your first workloads to Azure. You'll want to be aware of other aspects of Azure subscriptions as your organization's cloud adoption grows. 

## Administration and access control

Initially, Azure provided the Azure Service Model (now known as the classic deployment model). In this model, access controls for a subscription were minimal, and access to a subscription implied access to all the resources in the portal. This lack of fine-grained control led to a proliferation of subscriptions as organizations sought to limit access to only the appropriate personnel.

The Azure Resource Management (ARM) model (introduced in 2014) enables [Role-Based Access Control (RBAC)][docs-rbac], which provides fine-grained control for assigning administrative privileges to Azure resources. In this model, the subscription is no longer required to serve as the primary security boundary. As a result, the proliferation of subscriptions that was common in the classic deployment model is no longer necessary. In general, security considerations should be [managed through RBAC and policy][docs-manage-access], and billing considerations should be managed via [resource groups](resource-group-explainer.md) and tagging.

> [!NOTE] 
> Because of its more robust security and billing capabilities, the ARM model should be used for all new Azure deployments. This guidance focuses solely on deploying and managing Azure resources via the ARM model.

## Scale and subscription limits

A subscription is a logical limit of scale. Every subscription has a defined set of [subscription limits][docs-subscription-limits] for the resources associated with a subscription. These limits include quotas for various Azure resource types. Organizations often create multiple Azure subscriptions in order to avoid these limits.

## Ownership and administration

Each Azure subscription is assigned to an account. The owner of this account is known as the Account Adminstrator (AA). The AA is authorized to access the [Azure Account Center][azure-account-center] and perform various management tasks, such as creating new subscriptions, canceling subscriptions, changing the billing for a subscription, [transfering a subscription](/azure/billing/billing-subscription-transfer) to another account, and [reassigning the Service Administrator]. Conceptually, the AA is the billing owner of the subscription. In RBAC, the AA isn't assigned a role.

Each subscription has a Service Administrator (SA) who can add, remove, and modify Azure resources in that subscription by using the Azure portal. The default Service Administrator of a new subscription is the Account Administrator, but the AA can [reassign the SA](/azure/billing/billing-add-change-azure-subscription-administrator) in the Account Center.

Each Azure subscription is associated with exactly one Azure Active Directory (AAD) [tenant](/azure/active-directory/develop/active-directory-howto-tenant). Users, groups, and applications from that directory are assigned to RBAC roles that have permissions to manage resources in the Azure subscription. The scope of a role assignment can be a subscription, a resource group, or a single resource. A role assigned at a parent scope also grants access to the children contained within it. For more information, see [Understanding resource access in Azure][docs-understanding-resource-access].

## Managing multiple subscriptions

Many organizations using Azure will have multiple subscriptions for a variety of reasons. While each additional Azure subscription does not incur a direct cost, it can increase the complexity of managing your Azure resources.

Many of your early decisions in architecting and planning your subscription model can affect future decisions and designs as your cloud environment grows. Get participation and input from several groups within your organization, including IT leadership and those responsible for networking, security, and identity within your organization. See the [subscription guidance](#guidance) later in this document for guidance on reasons you may need multiple subscriptions in your Azure environment. 

## Billing

Subscriptions determine how resource usage is reported and billed. Each subscription can be configured separately for billing and payment. Additionally, Azure Resource Manager provides the ability to assign resource tags to provide additional information for granular chargeback and showback scenarios; this can be done based on either a resource group or resource.

## Enterprises and governance

Organizations who have an Enterprise Agreement with Microsoft can manage multiple subscriptions through the Enterprise Portal. Azure enrollment hierarchies define how services are structured within an Enterprise Agreement. The Enterprise Portal allows customers to divide access to Azure resources associated with an Enterprise Agreement based on flexible hierarchies customizable to an organization's unique needs. The hierarchy pattern should match an organization's management and geographic structure so that the associated billing and resource access can be accurately tracked. For more information, see [Enterprise subscription guidance (TO BE ADDED)]().

## Offer types

An Azure offer is the type of Azure subscription you have. Each offer has different terms and some have special benefits. Azure provides a number of different offer types, such as Pay-As-You-Go, Enterprise Agreements, Visual Studio, and Dev/Test offers. You should evaluate the [available offers](https://azure.microsoft.com/en-us/support/legal/offer-details/) based on your organization's needs. If necessary, you can [change your subscription to a different offer][azure-change-subscription-offer].

## Subscriptions and tenants

As stated previously, each Azure subscription is associated with a single Azure Active Directory (AAD) tenant. Most of an organization's subscriptions will use the organization's primary AAD tenant. A few subscriptions may use a different tenant (e.g., development subscriptions with specific testing requirements). These tenants may be federated with the primary AAD tenant.  For more information, see [Subscriptions, licenses, accounts, and tenants for Microsoft’s cloud offerings][docs-subscriptions-licenses-accounts-tenants].

## Guidance

1. Review the [subscription design patterns](subscription-design.md) commonly used to support workloads in Azure, and identify the pattern that best supports what you think you'll need for your Azure workload deployments.  
2. Larger organizations that need multiple subscriptions with robust governance and centralized financial control should review the [Enterprise subscription guidance](). To establish an Enterprise Agreement (EA) and an associated Azure enrollment, see [Licensing Azure for the enterprise][azure-licensing].
3. Define your organizational structure in Azure.
  a. For larger organizations with an Enterprise Agreement, follow the [Azure Enterprise Portal Onboarding Guide][onboarding-guide] to define your organization's hierarchy of departments, accounts and subscriptions. You should enable EA Dev/Test subscriptions for your organization, to take advantage of lower costs for non-production environments. For more information, see [Enabling and creating EA Dev/Test subscriptions][enable-dev-test].  
  b. For smaller organizations, create an Azure account by creating your first subscription. If you need additional subscriptions for your design, decide for each subscription whether to associate it with your existing Azure account or a new account. **TODO: Add specific details**  
4. Begin creating subscriptions required for your workloads in alignment with your chosen subscription design pattern. Within this pattern, you will still need to decide when to create additional subscriptions to support your needs. Review the [guidance for multiple subscriptions](subscription-multiple.md). For each subscription, change the Service Administrator role to a different user account if the person responsible for managing access to Azure resources in that subscription is different from the person responsible as the billing owner of the subscription. 

<!-- links -->
[azure-get-started]: https://azure.microsoft.com/en-us/get-started/
[azure-offers]: https://azure.microsoft.com/en-us/support/legal/offer-details/
[azure-portal]: https://portal.azure.com
[azure-account-center]: https://account.azure.com/

[docs-manage-access]: /azure/active-directory/manage-access-to-azure-resources
[docs-rbac]: /azure/active-directory/role-based-access-control-what-is
[docs-subscription-limits]: /azure/azure-subscription-service-limits
[docs-subscriptions-licenses-accounts-tenants]: https://docs.microsoft.com/en-us/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings
[docs-understanding-resource-access]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-understanding-resource-access

[onboarding-guide]: https://eaportalonboardingvideos.blob.core.windows.net/onboardingvideos/AzureDirectEACustomerOnboardingGuide_En.pdf
[enable-dev-test]: https://channel9.msdn.com/blogs/EA.Azure.com/Enabling-and-Creating-EA-DevTest-Subscriptions-through-the-EA-Portal
[azure-change-subscription-offer]: /azure/billing/billing-how-to-switch-azure-offer
[azure-licensing]: https://azure.microsoft.com/en-us/pricing/enterprise-agreement/