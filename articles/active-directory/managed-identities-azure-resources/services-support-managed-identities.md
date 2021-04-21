---
title: Usługi platformy Azure, które obsługują tożsamości zarządzane — Azure AD
description: Lista usług, które obsługują tożsamości zarządzane dla zasobów platformy Azure i uwierzytelniania usługi Azure AD
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: c4cd9140d03bba1f9d95ed64c3628da4fe32ecd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771483"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Usługi, które obsługują tożsamości zarządzane dla zasobów platformy Azure

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Przy użyciu tożsamości zarządzanej można uwierzytelnić się w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie. Jesteśmy w trakcie integrowania tożsamości zarządzanych dla zasobów platformy Azure i uwierzytelniania usługi Azure AD na platformie Azure. Często sprawdzaj aktualizacje.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure obsługujące tożsamości zarządzane dla zasobów platformy Azure

Następujące usługi platformy Azure obsługują tożsamości zarządzane dla zasobów platformy Azure:


### <a name="azure-api-management"></a>Usługa Azure API Management

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępne | Wersja zapoznawcza |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure API Management (w regionach, gdzie są dostępne):

- [Szablon usługi Azure Resource Manager](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępny | ![Dostępne][check] |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check]  | Niedostępny  | ![Dostępne][check] |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure App Configuration (w regionach, w których jest dostępna):

- [Interfejs wiersza polecenia platformy Azure](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check]  | ![Dostępne][check]  | ![Dostępne][check] |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure App Service (w regionach, gdzie są dostępne):

- [Witryna Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Platforma Kubernetes z włączoną usługą Azure Arc

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Azure Arc usługi Kubernetes obecnie [obsługuje tożsamość przypisaną przez system.](../../azure-arc/kubernetes/quickstart-connect-cluster.md) Certyfikat tożsamości usługi zarządzanej jest używany przez wszystkich Azure Arc kubernetes do komunikacji z platformą Azure.

### <a name="azure-arc-enabled-servers"></a>Serwery z usługą Azure Arc

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Wszystkie Azure Arc z włączoną obsługą systemu mają tożsamość przypisaną przez system. Nie można wyłączyć ani zmienić tożsamości przypisanej przez system na Azure Arc serwera. Zapoznaj się z następującymi zasobami, aby dowiedzieć się więcej na temat sposobu obsługi tożsamości zarządzanych na Azure Arc serwerach z włączoną obsługą usługi :

- [Uwierzytelnianie względem zasobów platformy Azure przy użyciu serwerów z usługą Arc](../../azure-arc/servers/managed-identity-authentication.md)
- [Używanie tożsamości zarządzanej z serwerami z usługą Arc](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującym dokumentem, aby ponownie skonfigurować tożsamość zarządzaną, jeśli subskrypcja została przeniesiona do nowej dzierżawy:
* [Naprawianie uszkodzonego konta automatycznego](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby użyć tożsamości zarządzanej [z](../../governance/blueprints/overview.md)Azure Blueprints :

- [Azure Portal — przypisywanie strategii](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [Interfejs API REST — przypisywanie strategii](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |


### <a name="azure-communication-services"></a>Usługi Azure Communication Services

Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | ![Dostępne][check] | Niedostępne | Niedostępne | Niedostępne |


### <a name="azure-container-instances"></a>Azure Container Instances

Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Linux: wersja zapoznawcza<br>Windows: niedostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Linux: wersja zapoznawcza<br>Windows: niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure Container Instances (w regionach, gdzie są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Szablon usługi Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Usługa Azure Container Registry Tasks

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla zadania usługi Azure Container Registry (w regionach, w których jest dostępna):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure Data Factory V2 (w regionach, jeśli są dostępne):

- [Witryna Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sdk](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure Digital Twins (w regionach, gdzie są dostępne):

- [Witryna Azure Portal](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Typ tożsamości zarządzanej |Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępne | Wersja zapoznawcza |
| Przypisana przez użytkownika | Niedostępne | Niedostępne  | Niedostępne  | Niedostępne |

### <a name="azure-firewall-policy"></a>Azure Firewall zasad

Typ tożsamości zarządzanej |Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Niedostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Wersja zapoznawcza | Niedostępne  | Niedostępne  | Niedostępne |

### <a name="azure-functions"></a>Azure Functions

Typ tożsamości zarządzanej |Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check]  | ![Dostępne][check]  | ![Dostępne][check]  |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure Functions (w regionach, gdzie jest dostępna):

- [Witryna Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure IoT Hub (w regionach, gdzie jest dostępna):

- [Witryna Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Usługa Azure Import/Export

Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne w regionie, w którym jest dostępna usługa eksportowania usługi Azure Import | Wersja zapoznawcza | Dostępne | Dostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |


Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych w u Azure Kubernetes Service](../../aks/use-managed-identity.md).

### <a name="azure-log-analytics-cluster"></a>Klaster usługi Azure Log Analytics

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |

Aby uzyskać więcej informacji, zobacz [jak działa tożsamość w Azure Monitor](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |


Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure Logic Apps (w regionach, gdzie są dostępne):

- [Witryna Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Szablon usługi Azure Resource Manager](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Wersja zapoznawcza | Niedostępny | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych z Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure Policy (w regionach, w których jest dostępna):

- [Witryna Azure Portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [Program PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Szablony usługi Azure Resource Manager](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Tożsamość zarządzana dla Service Fabric jest](../../service-fabric/concepts-managed-identity.md) dostępna we wszystkich regionach.

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | Niedostępny | Niedostępny | niedostępne |
| Przypisana przez użytkownika | ![Dostępne][check] | Niedostępny | Niedostępny |Niedostępny |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla aplikacji usługi Azure Service Fabric we wszystkich regionach:

- [Szablon usługi Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | Niedostępny | Niedostępny | ![Dostępne][check] |
| Przypisana przez użytkownika | Niedostępny | Niedostępny | Niedostępny | Niedostępny |


Aby uzyskać więcej informacji, [zobacz How to enable system-assigned managed identity for Azure Spring Cloud application (Jak](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)włączyć tożsamość zarządzaną przypisaną przez system Azure Spring Cloud aplikacji).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne w regionie, w którym Azure Stack Edge usługi | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Virtual Machine Scale Sets (w regionach, gdzie jest dostępna):

- [Witryna Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ tożsamości zarządzanej | Wszystko ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Przypisana przez użytkownika | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Virtual Machines (w regionach, gdzie jest dostępna):

- [Witryna Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Zestawy Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Niedostępny | Niedostępny | Niedostępny | Niedostępny |
| Przypisana przez użytkownika | [Dostępne w obsługiwanych regionach](../../virtual-machines/image-builder-overview.md#regions) | Niedostępny | Niedostępny | Niedostępny |

Aby dowiedzieć się, jak skonfigurować tożsamość zarządzaną dla Azure VM Image Builder (w regionach, w których jest dostępna), zobacz [Image Builder omówienie .](../../virtual-machines/image-builder-overview.md#permissions)
### <a name="azure-signalr-service"></a>Azure SignalR Service

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępne | Wersja zapoznawcza |
| Przypisana przez użytkownika | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępne | Wersja zapoznawcza |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla Azure SignalR Service (w regionach, w których jest dostępna):

- [Szablon usługi Azure Resource Manager](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisana przez system | Dostępne w regionach, w których Azure Resource Mover usługi | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującym dokumentem, aby użyć Azure Resource Mover:

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD

Następujące usługi obsługują uwierzytelnianie usługi Azure AD i zostały przetestowane z usługami klienta, które używają tożsamości zarządzanych dla zasobów platformy Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Zapoznaj się z następującą listą, aby skonfigurować dostęp do Azure Resource Manager:

- [Przypisywanie dostępu za pośrednictwem Azure Portal](howto-assign-access-portal.md)
- [Przypisywanie dostępu za pośrednictwem programu PowerShell](howto-assign-access-powershell.md)
- [Przypisywanie dostępu za pośrednictwem interfejsu wiersza polecenia platformy Azure](howto-assign-access-CLI.md)
- [Przypisywanie dostępu za pośrednictwem Azure Resource Manager szablonu](../../role-based-access-control/role-assignments-template.md)

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Dostępne][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Dostępne][check] |
| Azure (Niemcy) | `https://management.microsoftazure.de/` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://management.chinacloudapi.cn` | ![Dostępne][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Dostępne][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) |  `https://vault.microsoftazure.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://vault.azure.cn` | ![Dostępne][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Dostępne][check] |
| Azure Government |  | Niedostępny |
| Azure (Niemcy) |   | Niedostępny |
| Azure w Chinach — 21Vianet |  | Niedostępny |

### <a name="azure-sql"></a>Azure SQL

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Dostępne][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Dostępne][check] |
| Azure (Niemcy) | `https://database.cloudapi.de/` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://database.chinacloudapi.cn/` | ![Dostępne][check] |

### <a name="azure-data-explorer"></a>Azure Data Explorer

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Dostępne][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) | `https://<account>.<region>.kusto.cloudapi.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Dostępne][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Dostępne][check] |
| Azure Government |  | Niedostępny |
| Azure (Niemcy) |   | Niedostępny |
| Azure w Chinach — 21Vianet |  | Niedostępny |

### <a name="azure-service-bus"></a>Usługa Azure Service Bus

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Dostępne][check] |
| Azure Government |  | ![Dostępne][check] |
| Azure (Niemcy) |   | Niedostępny |
| Azure w Chinach — 21Vianet |  | Niedostępny |









### <a name="azure-storage-blobs-and-queues"></a>Obiekty blob i kolejki usługi Azure Storage

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Dostępne][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Dostępne][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Dostępne][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) | `https://*.asazure.cloudapi.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Dostępne][check] |

> [!Note]
> Usługa Microsoft Power BI [obsługuje również tożsamości zarządzane.](../../stream-analytics/powerbi-output-managed-identity.md)


[check]: media/services-support-managed-identities/check.png "Dostępne"
