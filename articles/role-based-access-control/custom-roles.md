---
title: Role niestandardowe platformy Azure — RBAC na platformie Azure
description: Dowiedz się, jak tworzyć role niestandardowe platformy Azure przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby precyzyjnego zarządzania dostępem do zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5030fb50313e1db2173990c55930c22fdf58f559
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734794"
---
# <a name="azure-custom-roles"></a>Role niestandardowe platformy Azure

> [!IMPORTANT]
> Dodawanie grupy zarządzania do `AssignableScopes` programu jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne role niestandardowe. Podobnie jak wbudowane role można przypisywać niestandardowe role do użytkowników, grup i jednostek usługi w ramach grupy zarządzania, subskrypcji i zakresów grup zasobów.

Role niestandardowe mogą być współużytkowane przez subskrypcje, które ufają temu samemu katalogowi usługi Azure AD. Obowiązuje limit **5 000** ról niestandardowych dla katalogu. (W przypadku platformy Azure (Niemcy i Azure Chiny 21Vianet limit wynosi 2 000 ról niestandardowych). Role niestandardowe można tworzyć przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.

## <a name="custom-role-example"></a>Przykład roli niestandardowej

Poniżej pokazano, jak wygląda rola niestandardowa, jak wyświetlana w formacie JSON. Ta rola niestandardowa może służyć do monitorowania i ponownego uruchamiania maszyn wirtualnych.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Gdy tworzysz rolę niestandardową, zostanie ona wyświetlona w Azure Portal z pomarańczowym ikoną zasobu.

![Ikona roli niestandardowej](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Procedura tworzenia roli niestandardowej

1. Zdecyduj, w jaki sposób chcesz utworzyć rolę niestandardową

    Role niestandardowe można tworzyć przy użyciu [Azure Portal](custom-roles-portal.md), [Azure PowerShell](custom-roles-powershell.md), interfejsu [wiersza polecenia platformy Azure](custom-roles-cli.md)lub [interfejsu API REST](custom-roles-rest.md).

1. Ustalanie potrzebnych uprawnień

    Podczas tworzenia roli niestandardowej należy znać operacje dostawcy zasobów, które są dostępne do definiowania uprawnień. Aby wyświetlić listę operacji, zapoznaj się z tematem [operacje dostawcy zasobów Azure Resource Manager](resource-provider-operations.md). Operacje zostaną dodane do właściwości `Actions` lub `NotActions` [definicji roli](role-definitions.md). Jeśli masz operacje na danych, dodasz je do właściwości `DataActions` lub. `NotDataActions`

1. Utwórz rolę niestandardową

    Zazwyczaj należy zacząć od istniejącej wbudowanej roli, a następnie zmodyfikować ją na potrzeby Twoich potrzeb. Następnie użyj polecenia [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) lub [AZ role Definition Create](/cli/azure/role/definition#az-role-definition-create) , aby utworzyć rolę niestandardową. Aby utworzyć rolę niestandardową `Microsoft.Authorization/roleDefinitions/write` , musisz mieć uprawnienie wszystkie `AssignableScopes`, takie jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).

1. Testowanie roli niestandardowej

    Gdy masz rolę niestandardową, musisz ją przetestować, aby upewnić się, że działa zgodnie z oczekiwaniami. Aby później wprowadzić zmiany, możesz zaktualizować rolę niestandardową.

Aby zapoznać się z samouczkiem krok po kroku na temat tworzenia roli niestandardowej, zobacz [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell](tutorial-custom-role-powershell.md) lub [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Właściwości roli niestandardowej

Rola niestandardowa ma następujące właściwości.

| Właściwość | Wymagany | Typ | Opis |
| --- | --- | --- | --- |
| `Name` | Tak | String | Nazwa wyświetlana roli niestandardowej. Chociaż definicja roli jest grupą zarządzania lub zasobem na poziomie subskrypcji, definicja roli może być używana w wielu subskrypcjach, które współużytkują ten sam katalog usługi Azure AD. Ta nazwa wyświetlana musi być unikatowa w zakresie katalogu usługi Azure AD. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id` | Tak | String | Unikatowy identyfikator roli niestandardowej. W przypadku Azure PowerShell i interfejsu wiersza polecenia platformy Azure ten identyfikator jest generowany automatycznie podczas tworzenia nowej roli. |
| `IsCustom` | Tak | String | Wskazuje, czy jest to rola niestandardowa. Ustaw na `true` dla ról niestandardowych. |
| `Description` | Tak | String | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 1024. |
| `Actions` | Tak | Ciąg [] | Tablica ciągów, która określa operacje zarządzania, które mogą być wykonywane przez rolę. Aby uzyskać więcej informacji, zobacz [Akcje](role-definitions.md#actions). |
| `NotActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje zarządzania, które są wykluczone z dozwolonej `Actions`wartości. Aby uzyskać więcej informacji, zobacz [Nonaruszone](role-definitions.md#notactions). |
| `DataActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje na danych, które mogą być wykonywane na danych w tym obiekcie. Jeśli utworzysz rolę niestandardową z `DataActions`, ta rola nie może zostać przypisana w zakresie grupy zarządzania. Aby uzyskać więcej informacji, zobacz temat [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje na danych, które są wykluczone z dozwolonej `DataActions`wartości. Aby uzyskać więcej informacji, zobacz [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Tak | Ciąg [] | Tablica ciągów, która określa zakresy, które rola niestandardowa jest dostępna do przypisania. W `AssignableScopes` roli niestandardowej można zdefiniować tylko jedną grupę zarządzania. Dodawanie grupy zarządzania do `AssignableScopes` programu jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową

Podobnie jak wbudowane role, `AssignableScopes` Właściwość określa zakresy, które rola jest dostępna do przypisania. `AssignableScopes` Właściwość roli niestandardowej kontroluje również, kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Utwórz/Usuń rolę niestandardową | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym udzielono tej operacji na wszystkich `AssignableScopes` roli niestandardowej, mogą tworzyć (lub usuwać) role niestandardowe do użycia w tych zakresach. Na przykład [właściciele](built-in-roles.md#owner) i [Administratorzy dostępu użytkowników do](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Aktualizacja roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym udzielono tej operacji na wszystkich `AssignableScopes` roli niestandardowej, mogą aktualizować role niestandardowe w tych zakresach. Na przykład [właściciele](built-in-roles.md#owner) i [Administratorzy dostępu użytkowników do](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Wyświetlanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/read` | Użytkownicy, którym udzielono tej operacji w zakresie, mogą wyświetlać role niestandardowe, które są dostępne do przypisania w tym zakresie. Wszystkie wbudowane role umożliwiają przypisywanie ról niestandardowych. |

## <a name="custom-role-limits"></a>Limity ról niestandardowych

Na poniższej liście opisano limity ról niestandardowych.

- Każdy katalog może mieć maksymalnie **5000** ról niestandardowych.
- Platformy Azure (Niemcy i Azure Chiny) mogą mieć do 2000 ról niestandardowych dla każdego katalogu.
- Nie można ustawić `AssignableScopes` zakresu głównego (`"/"`).
- W `AssignableScopes` roli niestandardowej można zdefiniować tylko jedną grupę zarządzania. Dodawanie grupy zarządzania do `AssignableScopes` programu jest obecnie w wersji zapoznawczej.
- Ról niestandardowych z `DataActions` nie można przypisać do zakresu grupy zarządzania.
- Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie możliwym do przypisania definicji roli.

Aby uzyskać więcej informacji na temat ról niestandardowych i grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Następne kroki
- [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure Portal](custom-roles-portal.md)
- [Informacje o definicjach ról platformy Azure](role-definitions.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
