---
title: Role niestandardowe platformy Azure — RBAC na platformie Azure
description: Dowiedz się, jak tworzyć role niestandardowe platformy Azure przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby precyzyjnego zarządzania dostępem do zasobów platformy Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: eddbd9cb695f3ff7eabd9f2549d0a868d8826eb9
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369127"
---
# <a name="azure-custom-roles"></a>Role niestandardowe platformy Azure

> [!IMPORTANT]
> Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne role niestandardowe. Podobnie jak wbudowane role można przypisywać niestandardowe role do użytkowników, grup i jednostek usługi w ramach grupy zarządzania, subskrypcji i zakresów grup zasobów.

Role niestandardowe mogą być współużytkowane przez subskrypcje, które ufają temu samemu katalogowi usługi Azure AD. Obowiązuje limit **5 000** ról niestandardowych dla katalogu. (W przypadku platformy Azure (Niemcy i Azure Chiny 21Vianet limit wynosi 2 000 ról niestandardowych). Role niestandardowe można tworzyć przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.

## <a name="steps-to-create-a-custom-role"></a>Procedura tworzenia roli niestandardowej

Poniżej przedstawiono podstawowe kroki tworzenia roli niestandardowej.

1. Określ wymagane uprawnienia.

    Podczas tworzenia roli niestandardowej należy znać operacje, które są dostępne do definiowania uprawnień. Zazwyczaj należy zacząć od istniejącej wbudowanej roli, a następnie zmodyfikować ją na potrzeby Twoich potrzeb. Operacje zostaną dodane do `Actions` `NotActions` właściwości lub [definicji roli](role-definitions.md). Jeśli masz operacje na danych, dodasz je do `DataActions` `NotDataActions` właściwości lub.

    Aby uzyskać więcej informacji, zobacz następną sekcję [jak określić wymagane uprawnienia](#how-to-determine-the-permissions-you-need).

1. Zdecyduj, w jaki sposób chcesz utworzyć rolę niestandardową.

    Role niestandardowe można tworzyć przy użyciu [Azure Portal](custom-roles-portal.md), [Azure PowerShell](custom-roles-powershell.md), interfejsu [wiersza polecenia platformy Azure](custom-roles-cli.md)lub [interfejsu API REST](custom-roles-rest.md).

1. Utwórz rolę niestandardową.

    Najprostszym sposobem jest użycie Azure Portal. Aby uzyskać instrukcje dotyczące sposobu tworzenia roli niestandardowej przy użyciu Azure Portal, zobacz [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure Portal](custom-roles-portal.md).

1. Przetestuj rolę niestandardową.

    Gdy masz rolę niestandardową, musisz ją przetestować, aby upewnić się, że działa zgodnie z oczekiwaniami. Aby później wprowadzić zmiany, możesz zaktualizować rolę niestandardową.

## <a name="how-to-determine-the-permissions-you-need"></a>Jak określić wymagane uprawnienia

Platforma Azure ma tysiące uprawnień, które można dołączyć do roli niestandardowej. Poniżej przedstawiono niektóre metody, które mogą pomóc w ustaleniu uprawnień, które mają zostać dodane do roli niestandardowej:

- Zapoznaj się z istniejącymi [wbudowanymi rolami](built-in-roles.md).

    Może zajść potrzeba zmodyfikowania istniejącej roli lub połączenia uprawnień używanych w wielu rolach.

- Utwórz listę usług platformy Azure, do których chcesz udzielić dostępu.

- Określ [dostawców zasobów mapowanych na usługi platformy Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    Usługi platformy Azure uwidaczniają swoje funkcje i uprawnienia za pomocą [dostawców zasobów](../azure-resource-manager/management/overview.md). Na przykład dostawca zasobów Microsoft. COMPUTE dostarcza zasoby maszyn wirtualnych i dostawcę zasobów Microsoft. rozliczeń dostarcza subskrypcję i zasoby rozliczeń. Znajomość dostawców zasobów może pomóc w zawężaniu i ustaleniu uprawnień potrzebnych dla roli niestandardowej.

    Podczas tworzenia roli niestandardowej przy użyciu Azure Portal można także określić dostawców zasobów, wyszukując słowa kluczowe. Ta funkcja wyszukiwania została opisana w temacie [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Dodawanie okienka uprawnień przy użyciu dostawcy zasobów](./media/custom-roles-portal/add-permissions-provider.png)

- Wyszukaj uprawnienia [dostępne](resource-provider-operations.md) , aby znaleźć uprawnienia, które chcesz dołączyć.

    Podczas tworzenia roli niestandardowej przy użyciu Azure Portal można wyszukać uprawnienia według słowa kluczowego. Można na przykład wyszukać *maszynę wirtualną* lub uprawnienia do *rozliczeń* . Możesz również pobrać wszystkie uprawnienia jako plik CSV, a następnie przeszukać ten plik. Ta funkcja wyszukiwania została opisana w temacie [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Dodaj listę uprawnień](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Przykład roli niestandardowej

Poniżej pokazano, jak wygląda rola niestandardowa, jak wyświetlana przy użyciu Azure PowerShell w formacie JSON. Ta rola niestandardowa może służyć do monitorowania i ponownego uruchamiania maszyn wirtualnych.

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

Poniżej przedstawiono tę samą rolę niestandardową, która jest wyświetlana przy użyciu interfejsu wiersza polecenia platformy Azure.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Gdy tworzysz rolę niestandardową, zostanie ona wyświetlona w Azure Portal z pomarańczowym ikoną zasobu.

![Ikona roli niestandardowej](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Właściwości roli niestandardowej

W poniższej tabeli opisano znaczenie właściwości roli niestandardowej.

| Właściwość | Wymagany | Typ | Opis |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Tak | Ciąg | Nazwa wyświetlana roli niestandardowej. Chociaż definicja roli jest grupą zarządzania lub zasobem na poziomie subskrypcji, definicja roli może być używana w wielu subskrypcjach, które współużytkują ten sam katalog usługi Azure AD. Ta nazwa wyświetlana musi być unikatowa w zakresie katalogu usługi Azure AD. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id`</br>`name` | Tak | Ciąg | Unikatowy identyfikator roli niestandardowej. W przypadku Azure PowerShell i interfejsu wiersza polecenia platformy Azure ten identyfikator jest generowany automatycznie podczas tworzenia nowej roli. |
| `IsCustom`</br>`roleType` | Tak | Ciąg | Wskazuje, czy jest to rola niestandardowa. Ustaw na `true` lub `CustomRole` dla ról niestandardowych. Ustaw dla `false` `BuiltInRole` ról wbudowanych lub. |
| `Description`</br>`description` | Tak | Ciąg | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 1024. |
| `Actions`</br>`actions` | Tak | Ciąg [] | Tablica ciągów, która określa operacje zarządzania, które mogą być wykonywane przez rolę. Aby uzyskać więcej informacji, zobacz [Akcje](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje zarządzania, które są wykluczone z dozwolonej wartości `Actions` . Aby uzyskać więcej informacji, zobacz [Nonaruszone](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje na danych, które mogą być wykonywane na danych w tym obiekcie. Jeśli utworzysz rolę niestandardową z `DataActions` , ta rola nie może zostać przypisana w zakresie grupy zarządzania. Aby uzyskać więcej informacji, zobacz temat [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje na danych, które są wykluczone z dozwolonej wartości `DataActions` . Aby uzyskać więcej informacji, zobacz [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Tak | Ciąg [] | Tablica ciągów, która określa zakresy, które rola niestandardowa jest dostępna do przypisania. W roli niestandardowej można zdefiniować tylko jedną grupę zarządzania `AssignableScopes` . Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Uprawnienia symboli wieloznacznych

`Actions`, `NotActions` , `DataActions` i `NotDataActions` obsługują symbole wieloznaczne ( `*` ) do definiowania uprawnień. Symbol wieloznaczny ( `*` ) rozszerza uprawnienia do wszystkich elementów, które pasują do podanego ciągu akcji. Załóżmy na przykład, że chcesz dodać wszystkie uprawnienia związane z Azure Cost Management i eksportami. Można dodać wszystkie te ciągi akcji:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Zamiast dodawać wszystkie te ciągi, można po prostu dodać symbol wieloznaczny. Na przykład następujący ciąg symboli wieloznacznych jest odpowiednikiem pięciu poprzednich ciągów. Obejmuje to również wszelkie przyszłe uprawnienia eksportu, które mogą zostać dodane.

```
Microsoft.CostManagement/exports/*
```

Można też użyć wielu symboli wieloznacznych w ciągu. Na przykład następujący ciąg reprezentuje wszystkie uprawnienia zapytania dla Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową

Podobnie jak wbudowane role, `AssignableScopes` Właściwość określa zakresy, które rola jest dostępna do przypisania. `AssignableScopes`Właściwość roli niestandardowej kontroluje również, kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Utwórz/Usuń rolę niestandardową | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym udzielono tej operacji na wszystkich `AssignableScopes` roli niestandardowej, mogą tworzyć (lub usuwać) role niestandardowe do użycia w tych zakresach. Na przykład [właściciele](built-in-roles.md#owner) i [Administratorzy dostępu użytkowników do](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Aktualizacja roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym udzielono tej operacji na wszystkich `AssignableScopes` roli niestandardowej, mogą aktualizować role niestandardowe w tych zakresach. Na przykład [właściciele](built-in-roles.md#owner) i [Administratorzy dostępu użytkowników do](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Wyświetlanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/read` | Użytkownicy, którym udzielono tej operacji w zakresie, mogą wyświetlać role niestandardowe, które są dostępne do przypisania w tym zakresie. Wszystkie role wbudowane umożliwiają udostępnianie ról niestandardowych do przypisania. |

## <a name="custom-role-limits"></a>Limity ról niestandardowych

Na poniższej liście opisano limity ról niestandardowych.

- Każdy katalog może mieć maksymalnie **5000** ról niestandardowych.
- Platformy Azure (Niemcy i Azure Chiny) mogą mieć do 2000 ról niestandardowych dla każdego katalogu.
- Nie można ustawić `AssignableScopes` zakresu głównego ( `"/"` ).
- W roli niestandardowej można zdefiniować tylko jedną grupę zarządzania `AssignableScopes` . Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie w wersji zapoznawczej.
- Ról niestandardowych z `DataActions` nie można przypisać do zakresu grupy zarządzania.
- Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie możliwym do przypisania definicji roli.

Aby uzyskać więcej informacji na temat ról niestandardowych i grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Formaty danych wejściowych i wyjściowych

Aby utworzyć rolę niestandardową przy użyciu wiersza polecenia, zazwyczaj używany jest kod JSON do określenia właściwości dla roli niestandardowej. W zależności od używanych narzędzi Formatowanie danych wejściowych i wyjściowych będzie wyglądać nieco inaczej. Ta sekcja zawiera listę formatów wejściowych i wyjściowych w zależności od narzędzia.

### <a name="azure-powershell"></a>Azure PowerShell

Aby utworzyć rolę niestandardową przy użyciu Azure PowerShell, należy podać następujące dane wejściowe.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Aby zaktualizować rolę niestandardową przy użyciu Azure PowerShell, należy podać następujące dane wejściowe. Należy zauważyć, że `Id` Właściwość została dodana. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Poniżej przedstawiono przykład danych wyjściowych podczas wyświetlania listy ról niestandardowych przy użyciu Azure PowerShell i polecenia [ConvertTo-JSON](/powershell/module/microsoft.powershell.utility/convertto-json) . 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć lub zaktualizować rolę niestandardową przy użyciu interfejsu wiersza polecenia platformy Azure, musisz podać następujące dane wejściowe. Ten format jest ten sam format podczas tworzenia roli niestandardowej przy użyciu Azure PowerShell.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Poniżej przedstawiono przykład danych wyjściowych podczas wyświetlania listy ról niestandardowych przy użyciu interfejsu wiersza polecenia platformy Azure.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>Interfejs API REST

Aby utworzyć lub zaktualizować rolę niestandardową przy użyciu interfejsu API REST, należy podać następujące dane wejściowe. Ten format jest tym samym formatem, który jest generowany podczas tworzenia roli niestandardowej przy użyciu Azure Portal.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Poniżej przedstawiono przykład danych wyjściowych podczas wyświetlania listy ról niestandardowych przy użyciu interfejsu API REST.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Informacje o definicjach ról platformy Azure](role-definitions.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
