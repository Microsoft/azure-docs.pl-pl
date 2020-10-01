---
title: Opis zakresu kontroli RBAC platformy Azure
description: Dowiedz się więcej o zakresie kontroli dostępu opartej na rolach (Azure RBAC) i określeniu zakresu dla zasobu.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: 80a05fd389ecaf504f58d12bf8efe87debd4ab86
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604396"
---
# <a name="understand-scope-for-azure-rbac"></a>Opis zakresu kontroli RBAC platformy Azure

*Zakres* to zbiór zasobów, do których odnosi się dostęp. Podczas przypisywania roli ważne jest zrozumienie zakresu, aby można było udzielić podmiotu zabezpieczeń tylko tego, co naprawdę potrzebuje. Ograniczając zakres, można ograniczyć zasoby, które są zagrożone w przypadku naruszenia zabezpieczeń podmiotu zabezpieczeń.

## <a name="scope-levels"></a>Poziomy zakresu

Na platformie Azure można określić zakres na czterech poziomach: [Grupa zarządzania](../governance/management-groups/overview.md), subskrypcja, [Grupa zasobów](../azure-resource-manager/management/overview.md#resource-groups)i zasób. Zakresy mają strukturę opartą na relacji nadrzędny-podrzędny. Każdy poziom hierarchii sprawia, że zakres jest bardziej szczegółowy. Role można przypisywać na dowolnym z tych poziomów zakresu. Wybrany poziom określa, jak szeroko ma być stosowana rola. Niższe poziomy dziedziczą uprawnienia roli z wyższego poziomu. 

![Zakres w przypisaniu roli](./media/scope-overview/rbac-scope-no-label.png)

## <a name="scope-format"></a>Format zakresu

W przypadku dodawania przypisań ról przy użyciu wiersza polecenia należy określić zakres. W przypadku narzędzi wiersza polecenia Scope jest potencjalnie długim ciągiem, który identyfikuje dokładny zakres przypisania roli. W Azure Portal ten zakres jest zwykle wymieniony jako *Identyfikator zasobu*.

Zakres składa się z serii identyfikatorów oddzielonych znakiem ukośnika (/). Ten ciąg można traktować jako wyrażenie następującej hierarchii, gdzie tekst bez symboli zastępczych ( `{}` ) jest stałymi identyfikatorami:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` jest IDENTYFIKATORem subskrypcji do użycia (identyfikator GUID).
- `{resourcesGroupName}` jest nazwą zawierającej grupę zasobów.
- `{providerName}` jest nazwą [dostawcy zasobów](../azure-resource-manager/management/azure-services-resource-providers.md) obsługującego zasób, `{resourceType}` a następnie `{resourceSubType*}` zidentyfikuje dalsze poziomy w ramach tego dostawcy zasobów.
- `{resourceName}` jest ostatnią częścią ciągu, który identyfikuje określony zasób.

Grupy zarządzania są poziomami powyżej subskrypcji i mają szerszy (najmniej szczegółowy) zakres. Przypisania ról na tym poziomie mają zastosowanie do subskrypcji w grupie zarządzania. Zakres dla grupy zarządzania ma następujący format:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Przykłady zakresu

> [!div class="mx-tableFixed"]
> | Zakres | Przykład |
> | --- | --- |
> | Grupa zarządzania | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Subskrypcja | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Grupa zasobów | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Zasób | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Jak określić zakres dla zasobu

Można stosunkowo łatwo określić zakres dla grupy zarządzania, subskrypcji lub grupy zasobów. Wystarczy znać nazwę i Identyfikator subskrypcji. Jednak określenie zakresu dla zasobu zajmuje trochę więcej pracy. Oto kilka sposobów określania zakresu dla zasobu.

- W Azure Portal Otwórz zasób, a następnie Przyjrzyj się właściwościom. Zasób powinien wyświetlić **Identyfikator zasobu** , w którym można określić zakres. Na przykład poniżej przedstawiono identyfikatory zasobów dla konta magazynu.

    ![Identyfikatory zasobów dla konta magazynu w Azure Portal](./media/scope-overview/scope-resource-id.png)

- Innym sposobem jest użycie Azure Portal do tymczasowego przypisania roli do zakresu zasobów, a następnie użycie [Azure PowerShell](role-assignments-list-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](role-assignments-list-cli.md) w celu wyświetlenia listy przypisania roli. W danych wyjściowych zakres będzie wyświetlany jako właściwość.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Następne kroki

- [Procedura dodawania przypisania roli](role-assignments-steps.md)
- [Dostawcy zasobów dla usług platformy Azure](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)
