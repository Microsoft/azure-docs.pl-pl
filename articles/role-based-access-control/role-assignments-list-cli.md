---
title: Lista przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure — Azure RBAC
description: Dowiedz się, do jakich zasobów użytkownicy, grupy, jednostki usługi lub tożsamości zarządzane mają dostęp przy użyciu interfejsu wiersza polecenia platformy Azure i kontroli dostępu na podstawie ról (RBAC) platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2d30571b68ba7e38e9960d1e434cf7844f6be852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780105"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Lista przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] W tym artykule opisano sposób listy przypisań ról przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!NOTE]
> Jeśli Twoja organizacja ma funkcje zarządzania zewnętrznego dla dostawcy usług, który korzysta z zarządzania zasobami delegowanymi na platformie [Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)przypisania ról autoryzowane przez tego dostawcę usług nie będą wyświetlane w tym miejscu.

## <a name="prerequisites"></a>Wymagania wstępne

- [Powłoka Bash w programie Azure Cloud Shell](../cloud-shell/overview.md) lub interfejsie [wiersza polecenia platformy Azure](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról dla określonego użytkownika, użyj [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee {assignee}
```

Domyślnie będą wyświetlane tylko przypisania ról dla bieżącej subskrypcji. Aby wyświetlić przypisania ról dla bieżącej subskrypcji i poniżej, dodaj `--all` parametr . Aby wyświetlić dziedziczone przypisania ról, dodaj `--include-inherited` parametr .

Poniższy przykład zawiera listę przypisań ról, które są przypisane bezpośrednio do użytkownika *contoso.com patlong: \@*

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Wyświetlanie listy przypisań ról dla grupy zasobów

Aby wyświetlić listę przypisań ról, które istnieją w zakresie grupy zasobów, użyj [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

Poniższy przykład zawiera listę przypisań ról dla *grupy zasobów sales:*

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Tworzenie listy przypisań ról dla subskrypcji

Aby wyświetlić listę wszystkich przypisań ról w zakresie subskrypcji, użyj [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list). Aby uzyskać identyfikator subskrypcji, możesz go  znaleźć w bloku Subskrypcje w skrypcie Azure Portal lub użyć [az account list](/cli/azure/account#az_account_list).

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Przykład:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Lista przypisań ról dla grupy zarządzania

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zarządzania, użyj [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list). Aby uzyskać identyfikator grupy zarządzania, można  go znaleźć w bloku Grupy zarządzania w grupę Azure Portal lub użyć listy [az account management-group .](/cli/azure/account/management-group#az_account_management_group_list)

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Przykład:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Lista przypisań ról dla tożsamości zarządzanej

1. Pobierz identyfikator podmiotu zabezpieczeń tożsamości zarządzanej przypisanej przez system lub przez użytkownika.

    Aby uzyskać identyfikator podmiotu zabezpieczeń tożsamości zarządzanej przypisanej przez użytkownika, możesz użyć az [ad sp list](/cli/azure/ad/sp#az_ad_sp_list) lub az identity [list](/cli/azure/identity#az_identity_list).

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Aby uzyskać identyfikator podmiotu zabezpieczeń tożsamości zarządzanej przypisanej przez system, możesz użyć [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Aby wyświetlić listę przypisań ról, użyj [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list).

    Domyślnie będą wyświetlane tylko przypisania ról dla bieżącej subskrypcji. Aby wyświetlić przypisania ról dla bieżącej subskrypcji i poniżej, dodaj `--all` parametr . Aby wyświetlić dziedziczone przypisania ról, dodaj `--include-inherited` parametr .

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md)