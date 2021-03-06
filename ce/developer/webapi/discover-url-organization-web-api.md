---
title: "Discover the URL for your organization using the Web API (Developer Guide for Dynamics 365 for Customer Engagement)| MicrosoftDocs"
description: "Learn how you can use the Web API to discover at runtime the organizations, or instances that the logged-on user belongs to"
ms.custom: 
ms.date: 04/22/2019
ms.reviewer: susikka
ms.service: crm-online
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: article
applies_to: 
  - Dynamics 365 for Customer Engagement (online)
ms.assetid: 2db13b4e-0e7c-4f25-b7be-70a612fb96e2
caps.latest.revision: 18
author: susikka
ms.author: susikka
manager: annbe
search.audienceType: 
  - developer
search.app: 
  - D365CE
---
# Discover the URL for your organization using the Web API

[!INCLUDE[](../../includes/cc_applies_to_update_9_0_0.md)]

The Discovery service for the [!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] apps Web API enables your applications to determine at run-time the organizations, also known as *instances*, that the logged-on user belongs to.  You can retrieve detailed information about those instances like the instance service URL, the [!INCLUDE[pn_crm_shortest](../../includes/pn-crm-shortest.md)] release version, the instance ID and more. You can use standard `$filter` and `$select` parameters to a Web API service request to customize the  returned list of instance data. The Discovery service is supported by all [!INCLUDE[pn_crm_shortest](../../includes/pn-crm-shortest.md)] deployment types: Online, on-premises, and IFD.  

> [!NOTE]
> [!INCLUDE[](../includes/cc-cdsnote-2-topic.md)]
>
> The documentation that is applicable to Dynamics 365 for Customer Engagement app (online) users is now available in the PowerApps documentation at: [Discover the URL for your organization using Web API](/powerapps/developer/common-data-service/webapi/discover-url-organization-web-api). The code sample that shows how to use the Web API Global discovery service can be found here: [Web API Global Discovery Service sample (C#)](/powerapps/developer/common-data-service/webapi/samples/global-discovery-service-csharp).

Clients applications may need access to a [!INCLUDE[pn_crm_shortest](../../includes/pn-crm-shortest.md)] instance where the instance URL may change over time.  For example, when a [!INCLUDE[pn_crm_shortest](../../includes/pn-crm-shortest.md)] instance is moved from one on-premises datacenter to another. The Discovery service allows clients instance to persist the instance ID or instance unique name and then use the Discovery service to look-up the current instance access URL.  
  
## Global discovery service

In addition to datacenter specific (regional) discovery services that are available on the 2011 (SOAP) endpoint and through the Web API, there is also a Web API only *global* discovery service that spans all operational datacenters. For information about the regional discovery services on the 2011 endpoint, see  [Discover the URL for your organization using the Discovery Service](../org-service/discover-url-organization-organization-service.md)

> [!NOTE]
> It is recommended that users switch from the legacy regional discovery service (`https://disco.crm.dynamics.com`) to global discovery service (`https://globaldisco.crm.dynamics.com`).
>
> For Dynamics 365 US Government users, the global discovery service is available only for the **GCC** users and the URL is `https://globaldisco.crm9.dynamics.com`. More information: [Dynamics 365 Government URLs](https://docs.microsoft.com/dynamics365/customer-engagement/admin/government/microsoft-dynamics-365-government#dynamics-365-us-government-urls). 
  
## Information provided by the Discovery service

Organization information is stored in the `Instance` entity of the Discovery service.  To see the kind of information contained in that entity, send an HTTP GET request to the service for one of your instances.  
  
```http  
GET https://dev.{servername}/api/discovery/v1.0/Instances(UniqueName='myorg')  
```  
  
In the above example, the global Discovery service of  is used to obtain the organization information of the instance with a unique name of "myorg". More details about this request is expanded upon later in this topic.  
  
### Scope of the returned information

For the global Discovery service, the `Instances` entity set, returns the set of instances that the user has access to across all geographies, when no filters are applied.   The returned data has a scope as described below.  
  
-   Includes all instances in the commercial cloud where the user is provisioned and enabled, except sovereign clouds instances are not returned  
  
-   Does not  include instances where the user's account is disabled  
  
-   Does not include instances where users have been filtered out based on an instance security group  
  
-   Does not include instances where the user has access as a result of being a delegated administrator  
  
-   If the calling user has access to no instances, the response simply returns an empty list  
  
## How to access the Discovery services

In general, the Web API address of the Discovery service has the following format: `<service base address>/api/discovery/`.  The addresses for  each deployment type are identified below. You can easily  find the Web API addresses and version number for your deployment in the [!INCLUDE[pn_crm_shortest](../../includes/pn-crm-shortest.md)] web application by navigating to **Settings > Customization > Developer Resources**  
  
The service base address of the Discovery service for an on-premises or IFD deployment is : `http[s]://{servername}/` or `http[s]://dev.{servername}/`. This results in the service address of `http[s]://{servername}/api/discovery/` or `http[s]://dev.{servername}/api/discovery/`.  
  
## Using the Discovery service  

An entity set named `Instances` is used to obtain instance information. You can use `$select` and `$filter` with the Instances entity set to filter the returned data. You can also use `$metadata` to obtain the metadata document of the service.  
  
### Authentication

On-premise or IFD instances of the Discovery Web API adopt the authentication model of their deployment, supporting either Integrated Windows Authentication (IWA) or OAuth tokens from a trusted token provider. Web Application Session authentication is not supported.  
  
### Web API versioning

Versioning of the Discovery service for a datacenter or on-premises/IFD is supported and is consistent with version numbering as used by the Organization service. For example:  
  
```http  
GET https://dev.{servername}/api/discovery/v9.1/Instances(UniqueName='myorg')  
```  

### CORS support

The Discovery service Web API supports the CORS standard for cross-origin access as does the Web API.  For more information about CORS support see [Use OAuth with Cross-Origin Resource Sharing  to connect a Single Page Application  to Dynamics 365 for Customer Engagement apps](../oauth-cross-origin-resource-sharing-connect-single-page-application.md).  
  
### Examples  
  
-   Get the details of a specific instance. If you leave out the GUID, all instances that the authenticated user has access to are returned.  
  
    ```http  
    GET https://dev.{servername}/api/discovery/v9.1/Instances(<guid>)  
    ```  
  
-   You can use the UniqueName attribute as an alternate key.  
  
    ```http  
    GET https://dev.{servername}/api/discovery/v9.1/Instances(UniqueName='myorg')  
    ```  
  
-   Retrieve a list of available instances, filtered by production type.  
  
    ```http  
    GET https://dev.{servername}/api/discovery/v9.1/Instances?$select=DisplayName,Description&$filter=Type+eq+0   
    ```  
  
-   Retrieve a specific instance's ID property value.  
  
    ```http  
    GET https://dev.{servername}/api/discovery/v9.1/Instances(UniqueName='myorg')/Id/$value  
    ```
## See also

[Web API Global Discovery Service sample (C#)](/powerapps/developer/common-data-service/webapi/samples/global-discovery-service-csharp)