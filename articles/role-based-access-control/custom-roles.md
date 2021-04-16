---
title: Role niestandardowe platformy Azure — RBAC platformy Azure
description: Dowiedz się, jak tworzyć role niestandardowe platformy Azure za pomocą kontroli dostępu opartej na rolach (RBAC) platformy Azure w celu precyzyjnego zarządzania dostępem do zasobów platformy Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 9779c2a269902d856d1639ce78028d0e658656bb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479835"
---
# <a name="azure-custom-roles"></a>Role niestandardowe platformy Azure

> [!IMPORTANT]
> Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie dostępne w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli role [wbudowane platformy Azure](built-in-roles.md) nie spełniają określonych potrzeb organizacji, możesz utworzyć własne role niestandardowe. Podobnie jak role wbudowane, role niestandardowe można przypisywać użytkownikom, grupom i jednostkom usługi w grupach zarządzania (tylko w wersji zapoznawczej), subskrypcjach i zakresach grupy zasobów.

Role niestandardowe mogą być współdzielone między subskrypcjami, które ufają tym samym katalogowi usługi Azure AD. Istnieje limit **5000 ról niestandardowych** na katalog. (W przypadku platformy Azure (Niemcy) Azure (Chiny) — 21Vianet limit wynosi 2000 ról niestandardowych). Role niestandardowe można tworzyć przy użyciu interfejsu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

## <a name="steps-to-create-a-custom-role"></a>Kroki tworzenia roli niestandardowej

Poniżej znajdują się podstawowe kroki tworzenia roli niestandardowej.

1. Określ wymagane uprawnienia.

    Podczas tworzenia roli niestandardowej musisz znać operacje, które są dostępne do definiowania uprawnień. Zazwyczaj rozpoczyna się od istniejącej wbudowanej roli, a następnie modyfikuje ją zgodnie z potrzebami. Dodasz operacje do właściwości `Actions` lub `NotActions` definicji [roli](role-definitions.md). Jeśli masz operacje na danych, dodasz je do `DataActions` właściwości `NotDataActions` lub .

    Aby uzyskać więcej informacji, zobacz następną sekcję [Jak określić potrzebne uprawnienia.](#how-to-determine-the-permissions-you-need)

1. Zdecyduj, jak chcesz utworzyć rolę niestandardową.

    Role niestandardowe można tworzyć przy [użyciu Azure Portal](custom-roles-portal.md), [Azure PowerShell,](custom-roles-powershell.md) [interfejsu](custom-roles-cli.md)wiersza polecenia platformy Azure lub interfejsu [API REST.](custom-roles-rest.md)

1. Utwórz rolę niestandardową.

    Najprostszym sposobem jest użycie Azure Portal. Aby uzyskać instrukcje dotyczące sposobu tworzenia roli niestandardowej przy użyciu Azure Portal, zobacz Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu [Azure Portal](custom-roles-portal.md).

1. Przetestuj rolę niestandardową.

    Gdy masz już rolę niestandardową, musisz ją przetestować, aby sprawdzić, czy działa ona zgodnie z oczekiwaniami. Jeśli musisz później wprowadzić zmiany, możesz zaktualizować rolę niestandardową.

## <a name="how-to-determine-the-permissions-you-need"></a>Jak określić potrzebne uprawnienia

Platforma Azure ma tysiące uprawnień, które możesz potencjalnie uwzględnić w swojej roli niestandardowej. Oto kilka metod, które mogą pomóc w ustaleniu uprawnień, które chcesz dodać do roli niestandardowej:

- Przyjrzyj się istniejącym [wbudowanym rolam](built-in-roles.md).

    Możesz zmodyfikować istniejącą rolę lub połączyć uprawnienia używane w wielu rolach.

- Lista usług platformy Azure, do których chcesz udzielić dostępu.

- Określ dostawców [zasobów mapowych na usługi platformy Azure.](../azure-resource-manager/management/azure-services-resource-providers.md)

    Usługi platformy Azure uwidoczniają swoje funkcje i uprawnienia za [pośrednictwem dostawców zasobów.](../azure-resource-manager/management/overview.md) Na przykład dostawca zasobów Microsoft.Compute dostarcza zasoby maszyny wirtualnej, a dostawca zasobów Microsoft.Billing dostarcza zasoby subskrypcji i rozliczeń. Znajomość dostawców zasobów może ułatwić zawężenie i określenie uprawnień potrzebnych dla roli niestandardowej.

    Podczas tworzenia roli niestandardowej przy użyciu Azure Portal można również określić dostawców zasobów, wyszukując słowa kluczowe. Ta funkcja wyszukiwania została opisana w te [tematu Create or update Azure custom roles using the Azure Portal](custom-roles-portal.md#step-4-permissions)( Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure Portal .

    ![Okienko Dodawanie uprawnień z dostawcą zasobów](./media/custom-roles-portal/add-permissions-provider.png)

- Przeszukaj [dostępne uprawnienia,](resource-provider-operations.md) aby znaleźć uprawnienia, które chcesz uwzględnić.

    Podczas tworzenia roli niestandardowej przy użyciu Azure Portal można wyszukiwać uprawnienia według słowa kluczowego. Możesz na przykład wyszukać maszynę *wirtualną lub* *uprawnienia do* rozliczeń. Możesz również pobrać wszystkie uprawnienia jako plik CSV, a następnie przeszukać ten plik. Ta funkcja wyszukiwania została opisana w te [tematu Create or update Azure custom roles using the Azure Portal](custom-roles-portal.md#step-4-permissions)(Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure Portal .

    ![Dodawanie listy uprawnień](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Przykład roli niestandardowej

Poniżej pokazano, jak wygląda rola niestandardowa wyświetlana przy użyciu Azure PowerShell w formacie JSON. Ta rola niestandardowa może służyć do monitorowania i ponownego uruchamiania maszyn wirtualnych.

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

Po utworzeniu roli niestandardowej zostanie ona wyświetlona w Azure Portal z pomarańczową ikoną zasobu.

![Ikona roli niestandardowej](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Właściwości roli niestandardowej

W poniższej tabeli opisano znaczenie właściwości roli niestandardowej.

| Właściwość | Wymagany | Typ | Opis |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Tak | Ciąg | Nazwa wyświetlana roli niestandardowej. Chociaż definicja roli jest grupą zarządzania lub zasobem na poziomie subskrypcji, definicja roli może być używana w wielu subskrypcjach, które współużytkuje ten sam katalog usługi Azure AD. Ta nazwa wyświetlana musi być unikatowa w zakresie katalogu usługi Azure AD. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id`</br>`name` | Tak | Ciąg | Unikatowy identyfikator roli niestandardowej. W Azure PowerShell i interfejsu wiersza polecenia platformy Azure ten identyfikator jest generowany automatycznie podczas tworzenia nowej roli. |
| `IsCustom`</br>`roleType` | Tak | Ciąg | Wskazuje, czy jest to rola niestandardowa. Ustaw na `true` lub `CustomRole` dla ról niestandardowych. Ustaw na `false` lub `BuiltInRole` dla ról wbudowanych. |
| `Description`</br>`description` | Tak | Ciąg | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 1024. |
| `Actions`</br>`actions` | Tak | String[] | Tablica ciągów określająca operacje zarządzania, które mogą być wykonywane przez rolę. Aby uzyskać więcej informacji, zobacz [Akcje](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | Nie | String[] | Tablica ciągów określająca operacje zarządzania, które są wykluczone z `Actions` dozwolonego . Aby uzyskać więcej informacji, zobacz [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Nie | String[] | Tablica ciągów określająca operacje na danych, które rola może wykonywać na danych w tym obiekcie. W przypadku tworzenia roli niestandardowej za pomocą funkcji nie można przypisać tej roli `DataActions` w zakresie grupy zarządzania. Aby uzyskać więcej informacji, zobacz [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | Nie | String[] | Tablica ciągów określająca operacje na danych, które są wykluczone z `DataActions` dozwolonego . Aby uzyskać więcej informacji, zobacz [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Tak | String[] | Tablica ciągów określająca zakresy, które rola niestandardowa jest dostępna do przypisania. W roli niestandardowej można zdefiniować tylko jedną `AssignableScopes` grupę zarządzania. Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Uprawnienia z symbolami wieloznacznymi

`Actions`, `NotActions` , i obsługują symbole `DataActions` `NotDataActions` wieloznaczne ( `*` ) w celu definiowania uprawnień. Symbol wieloznaczny ( `*` ) rozszerza uprawnienia do wszystkiego, co pasuje do ciągu akcji, który podaniem. Załóżmy na przykład, że chcesz dodać wszystkie uprawnienia związane z Azure Cost Management eksportami. Możesz dodać wszystkie te ciągi akcji:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Zamiast dodawać wszystkie te ciągi, można po prostu dodać ciąg z symbolami wieloznacznymi. Na przykład następujący ciąg z symbolami wieloznacznymi jest odpowiednikiem pięciu poprzednich ciągów. Obejmuje to również wszelkie przyszłe uprawnienia eksportu, które mogą zostać dodane.

```
Microsoft.CostManagement/exports/*
```

W ciągu może być również wiele symboli wieloznacznych. Na przykład następujący ciąg reprezentuje wszystkie uprawnienia zapytania dla Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową

Podobnie jak role wbudowane, właściwość `AssignableScopes` określa zakresy, które rola jest dostępna do przypisania. Właściwość roli niestandardowej kontroluje również, kto może `AssignableScopes` tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Tworzenie/usuwanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którzy mają przyznaną tę operację dla wszystkich ról niestandardowych, mogą tworzyć (lub usuwać) role niestandardowe do użycia `AssignableScopes` w tych zakresach. Na przykład [właściciele i](built-in-roles.md#owner) [administratorzy dostępu użytkowników](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Aktualizacja roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którzy mają przyznaną tę operację we wszystkich `AssignableScopes` rolach niestandardowych, mogą aktualizować role niestandardowe w tych zakresach. Na przykład [właściciele i](built-in-roles.md#owner) [administratorzy dostępu użytkowników](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Wyświetlanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/read` | Użytkownicy, którzy mają przyznaną tę operację w zakresie, mogą wyświetlać role niestandardowe, które są dostępne do przypisania w tym zakresie. Wszystkie role wbudowane umożliwiają udostępnianie ról niestandardowych do przypisania. |

## <a name="custom-role-limits"></a>Limity ról niestandardowych

Na poniższej liście opisano limity ról niestandardowych.

- Każdy katalog może mieć maksymalnie **5000** ról niestandardowych.
- Platforma Azure w Niemczech Azure (Chiny) — 21Vianet może mieć do 2000 ról niestandardowych dla każdego katalogu.
- Nie można `AssignableScopes` ustawić zakresu głównego ( `"/"` ).
- W programie nie można używać symboli wieloznacznych ( `*` `AssignableScopes` ). To ograniczenie z symbolami wieloznacznych pomaga zapewnić, że użytkownik nie może potencjalnie uzyskać dostępu do zakresu, aktualizując definicję roli.
- W roli niestandardowej można zdefiniować tylko jedną `AssignableScopes` grupę zarządzania. Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie dostępne w wersji zapoznawczej.
- Ról niestandardowych `DataActions` z programem nie można przypisać w zakresie grupy zarządzania.
- Azure Resource Manager nie weryfikuje istnienia grupy zarządzania w zakresie, który można przypisać definicji roli.

Aby uzyskać więcej informacji na temat ról niestandardowych i grup zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="input-and-output-formats"></a>Formaty danych wejściowych i wyjściowych

Aby utworzyć rolę niestandardową przy użyciu wiersza polecenia, zazwyczaj używa się danych JSON do określenia właściwości roli niestandardowej. W zależności od narzędzi, których używasz, formaty danych wejściowych i wyjściowych będą wyglądać nieco inaczej. W tej sekcji wymieniono formaty danych wejściowych i wyjściowych w zależności od narzędzia.

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

Aby zaktualizować rolę niestandardową przy Azure PowerShell, należy podać następujące dane wejściowe. Zwróć `Id` uwagę, że właściwość została dodana. 

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

Poniżej przedstawiono przykładowe dane wyjściowe po liście roli niestandardowej przy użyciu polecenia Azure PowerShell polecenia [ConvertTo-Json.](/powershell/module/microsoft.powershell.utility/convertto-json) 

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

Aby utworzyć lub zaktualizować rolę niestandardową przy użyciu interfejsu wiersza polecenia platformy Azure, należy podać następujące dane wejściowe. Ten format jest taki sam podczas tworzenia roli niestandardowej przy użyciu Azure PowerShell.

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

Poniżej przedstawiono przykładowe dane wyjściowe po liście roli niestandardowej przy użyciu interfejsu wiersza polecenia platformy Azure.

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

Aby utworzyć lub zaktualizować rolę niestandardową przy użyciu interfejsu API REST, należy podać następujące dane wejściowe. Ten format jest ten sam, który jest generowany podczas tworzenia roli niestandardowej przy użyciu Azure Portal.

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

Poniżej przedstawiono przykładowe dane wyjściowe po liście roli niestandardowej przy użyciu interfejsu API REST.

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
- [Opis definicji ról platformy Azure](role-definitions.md)
- [Rozwiązywanie problemów z kontroli RBAC na platformie Azure](troubleshooting.md)
