---
title: Jak używać aplikacji Role-Based Access Control usłudze Azure API Management | Microsoft Docs
description: Dowiedz się, jak używać ról wbudowanych i tworzyć role niestandardowe w usłudze Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ef71591d6d5a26aa737db4e7cb547c8b2c39d92a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812178"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Jak używać kontroli dostępu opartej na rolach w usłudze Azure API Management

Usługa Azure API Management korzysta z kontroli dostępu na podstawie ról (RBAC) platformy Azure, aby umożliwić precyzyjne zarządzanie dostępem dla usług i jednostek usługi API Management (na przykład interfejsów API i zasad). Ten artykuł zawiera omówienie ról wbudowanych i niestandardowych w API Management. Aby uzyskać więcej informacji na temat zarządzania dostępem w Azure Portal, zobacz Wprowadzenie do zarządzania [dostępem w Azure Portal](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Wbudowane role

API Management obecnie udostępnia trzy wbudowane role i doda jeszcze dwie role w najbliższej przyszłości. Te role można przypisać w różnych zakresach, w tym w subskrypcji, grupie zasobów i poszczególnych API Management wystąpienia. Jeśli na przykład przypiszesz użytkownikowi rolę "Czytelnik usługi API Management Service" na poziomie grupy zasobów, użytkownik będzie mieć dostęp do odczytu do wszystkich wystąpień API Management wewnątrz grupy zasobów. 

W poniższej tabeli przedstawiono krótkie opisy ról wbudowanych. Te role można przypisywać przy użyciu interfejsu Azure Portal lub innych narzędzi, takich jak program Azure [PowerShell,](../role-based-access-control/role-assignments-powershell.md)interfejs [wiersza polecenia](../role-based-access-control/role-assignments-cli.md)platformy Azure i interfejs [API REST.](../role-based-access-control/role-assignments-rest.md) Aby uzyskać szczegółowe informacje na temat przypisywania wbudowanych ról, zobacz Przypisywanie ról platformy Azure w celu zarządzania [dostępem do zasobów subskrypcji platformy Azure.](../role-based-access-control/role-assignments-portal.md)

| Rola          | Dostęp do<sup>odczytu [1]</sup> | Dostęp do<sup>zapisu [2]</sup> | Tworzenie, usuwanie, skalowanie, sieć VPN i konfiguracja domeny niestandardowej | Dostęp do portalu starszego wydawcy | Opis
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management współautora usługi | ✓ | ✓ | ✓ | ✓ | Superuzyter. Ma pełny dostęp CRUD do API Management i jednostek (na przykład interfejsów API i zasad). Ma dostęp do portalu starszego wydawcy. |
| API Management Service Reader | ✓ | | || Ma dostęp tylko do odczytu do API Management i jednostek. |
| API Management Service Operator | ✓ | | ✓ | | Może zarządzać API Management, ale nie jednostkami.|
| API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Może zarządzać API Management, ale nie usługami.|
| API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Może zarządzać portalem deweloperów. Dostęp tylko do odczytu do usług i jednostek.|

<sup>[1] Dostęp do odczytu do API Management i jednostek (na przykład interfejsów API i zasad).</sup>

<sup>[2] Dostęp do zapisu dla API Management i jednostek z wyjątkiem następujących operacji: tworzenie, usuwanie i skalowanie wystąpienia; Konfiguracja sieci VPN; i konfiguracja domeny niestandardowej.</sup>

<sup>\* Rola Edytora usług będzie dostępna po migracji całego interfejsu użytkownika administratora z istniejącego portalu wydawcy do Azure Portal. Rola Menedżera zawartości będzie dostępna po refaktoryzacji portalu wydawcy, aby zawierał tylko funkcje związane z zarządzaniem portalem dla deweloperów.</sup>  

## <a name="custom-roles"></a>Role niestandardowe

Jeśli żadna z wbudowanych ról nie spełnia Określonych potrzeb, można utworzyć role niestandardowe w celu zapewnienia bardziej szczegółowego zarządzania dostępem API Management jednostkami. Można na przykład utworzyć rolę niestandardową, która ma dostęp tylko do odczytu do usługi API Management, ale ma dostęp do zapisu tylko do jednego określonego interfejsu API. Aby dowiedzieć się więcej na temat ról niestandardowych, zobacz [Role niestandardowe w kontroli RBAC na platformie Azure.](../role-based-access-control/custom-roles.md) 

> [!NOTE]
> Aby można było zobaczyć wystąpienie API Management w Azure Portal, rola niestandardowa musi zawierać ```Microsoft.ApiManagement/service/read``` akcję.

Podczas tworzenia roli niestandardowej łatwiej jest rozpocząć od jednej z wbudowanych ról. Edytuj atrybuty, aby dodać **akcje**, **NotActions** lub **AssignableScopes**, a następnie zapisz zmiany jako nową rolę. Poniższy przykład rozpoczyna się od roli "API Management Service Reader" i tworzy rolę niestandardową o nazwie "Edytor interfejsu API kalkulatora". Rolę niestandardową można przypisać do określonego interfejsu API. W związku z tym ta rola ma dostęp tylko do tego interfejsu API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Artykuł [Azure Resource Manager operacji dostawcy zasobów](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) zawiera listę uprawnień, które można przyznać na API Management poziomie.

## <a name="video"></a>Wideo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na Role-Based Access Control platformie Azure, zobacz następujące artykuły:
  * [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
  * [Przypisywanie ról platformy Azure w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](../role-based-access-control/role-assignments-portal.md)
  * [Role niestandardowe w kontroli RBAC na platformie Azure](../role-based-access-control/custom-roles.md)
  * [Operacje związane z dostawcami zasobów w usłudze Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
