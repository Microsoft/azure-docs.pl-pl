---
title: Usługi platformy Azure, które obsługują tożsamości zarządzane — Azure AD
description: Lista usług, które obsługują zarządzane tożsamości dla zasobów platformy Azure i uwierzytelniania usługi Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 05/12/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 62d1f8d0e6fb5b6b6afb28f5db4ae8b818cc4705
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194873"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Przy użyciu tożsamości zarządzanej można uwierzytelniać się w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. Jesteśmy w trakcie integrowania tożsamości zarządzanych dla zasobów platformy Azure i uwierzytelniania usługi Azure AD na platformie Azure. Sprawdzaj często aktualizacje.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure obsługujące tożsamości zarządzane dla zasobów platformy Azure

Następujące usługi platformy Azure obsługują tożsamości zarządzane dla zasobów platformy Azure:


### <a name="azure-api-management"></a>Usługa Azure API Management

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisane przez użytkownika | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępne | Wersja zapoznawcza |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure API Management (w regionach, w których są dostępne):

- [Szablon Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)


### <a name="azure-app-service"></a>Azure App Service

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Przypisane przez użytkownika | ![Dostępne][check] | ![Dostępne][check]  | ![Dostępne][check]  | ![Dostępne][check] |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure App Service (w regionach, w których są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)


### <a name="azure-blueprints"></a>Azure Blueprints

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |

Zapoznaj się z poniższą listą, aby użyć zarządzanej tożsamości z [planami platformy Azure](../../governance/blueprints/overview.md):

- [Azure Portal — przypisanie strategii](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [Interfejs API REST — przypisanie planu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-container-instances"></a>Azure Container Instances

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | Linux: wersja zapoznawcza<br>Windows: niedostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | Linux: wersja zapoznawcza<br>Windows: niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Container Instances (w regionach, w których są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Szablon Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Usługa Azure Container Registry Tasks

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | Niedostępne | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Container Registry zadań (w regionach, w których są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)


### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisane przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Data Factory v2 (w regionach, w których są dostępne):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)


### <a name="azure-functions"></a>Azure Functions

Typ tożsamości zarządzanej |Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Przypisane przez użytkownika | ![Dostępne][check] | ![Dostępne][check]  | ![Dostępne][check]  | ![Dostępne][check]  |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Functions (w regionach, w których są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisane przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Data Factory v2 (w regionach, w których są dostępne):

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | - | - | - | 
| Przypisane przez użytkownika | ![Dostępne][check] | - | - | - |


Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/use-managed-identity).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisane przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |


Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Logic Apps (w regionach, w których są dostępne):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Szablon Azure Resource Manager](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)


### <a name="azure-service-fabric"></a>Azure Service Fabric
[Tożsamość zarządzana dla aplikacji Service Fabric](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) jest w wersji zapoznawczej i jest dostępna we wszystkich regionach.

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | Niedostępny | Niedostępny | Niedostępne |
| Przypisane przez użytkownika | ![Dostępne][check] | Niedostępny | Niedostępny |Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla aplikacji Service Fabric platformy Azure we wszystkich regionach:
- [Szablon Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)



### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |
| Przypisane przez użytkownika | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Virtual Machine Scale Sets (w regionach, w których są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza | 
| Przypisane przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Virtual Machines (w regionach, w których są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)




## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD

Poniższe usługi obsługują uwierzytelnianie usługi Azure AD i zostały przetestowane przy użyciu usług klienckich, które korzystają z tożsamości zarządzanych dla zasobów platformy Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Zapoznaj się z poniższą listą, aby skonfigurować dostęp do Azure Resource Manager:

- [Przypisz dostęp za pośrednictwem Azure Portal](howto-assign-access-portal.md)
- [Przypisywanie dostępu za pomocą programu PowerShell](howto-assign-access-powershell.md)
- [Przypisywanie dostępu za pośrednictwem interfejsu wiersza polecenia platformy Azure](howto-assign-access-CLI.md)
- [Przypisywanie dostępu za pomocą szablonu Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://management.azure.com/`| ![Dostępne][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Dostępne][check] |
| Azure (Niemcy) | `https://management.microsoftazure.de/` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://management.chinacloudapi.cn` | ![Dostępne][check] |

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://vault.azure.net`| ![Dostępne][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) |  `https://vault.microsoftazure.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://vault.azure.cn` | ![Dostępne][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://datalake.azure.net/` | ![Dostępne][check] |
| Azure Government |  | Niedostępny |
| Azure (Niemcy) |   | Niedostępny |
| Azure w Chinach — 21Vianet |  | Niedostępny |

### <a name="azure-sql"></a>Azure SQL 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://database.windows.net/` | ![Dostępne][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Dostępne][check] |
| Azure (Niemcy) | `https://database.cloudapi.de/` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://database.chinacloudapi.cn/` | ![Dostępne][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://eventhubs.azure.net` | ![Dostępne][check] |
| Azure Government |  | Niedostępny |
| Azure (Niemcy) |   | Niedostępny |
| Azure w Chinach — 21Vianet |  | Niedostępny |

### <a name="azure-service-bus"></a>Azure Service Bus

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://servicebus.azure.net`  | ![Dostępne][check] |
| Azure Government |  | ![Dostępne][check] |
| Azure (Niemcy) |   | Niedostępny |
| Azure w Chinach — 21Vianet |  | Niedostępny |









### <a name="azure-storage-blobs-and-queues"></a>Obiekty blob i kolejki usługi Azure Storage

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Dostępne][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Dostępne][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Globalne platformy Azure | `https://*.asazure.windows.net` | ![Dostępne][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) | `https://*.asazure.cloudapi.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Dostępne][check] |

> [!Note]
> Program Microsoft Power BI [obsługuje również zarządzane tożsamości](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity).


[check]: media/services-support-managed-identities/check.png "Udostępnione"
