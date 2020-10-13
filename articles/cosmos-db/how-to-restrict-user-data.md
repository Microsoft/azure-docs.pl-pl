---
title: Ograniczanie dostępu użytkowników do operacji na danych tylko przy użyciu Azure Cosmos DB
description: Dowiedz się, jak ograniczyć dostęp do operacji na danych tylko przy użyciu Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 44a62643c459fb61e7a2a95c2a9dd55ea4f19111
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570649"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Ograniczanie dostępu użytkowników do operacji na danych w Azure Cosmos DB

W Azure Cosmos DB istnieją dwa sposoby uwierzytelniania interakcji z usługą bazy danych:

- Używanie tożsamości Azure Active Directory podczas korzystania z Azure Portal,
- Używanie [kluczy](secure-access-to-data.md#primary-keys) Azure Cosmos DB lub [tokenów zasobów](secure-access-to-data.md#resource-tokens) podczas wystawiania wywołań z interfejsów API i zestawów SDK.

Każda metoda uwierzytelniania zapewnia dostęp do różnych zestawów operacji, z pewnym nakładaniem:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Podział operacji na typ uwierzytelniania" border="false":::

W niektórych scenariuszach może być konieczne ograniczenie niektórych użytkowników organizacji do wykonywania operacji na danych (to znaczy żądania CRUD i zapytania). Jest to zazwyczaj przypadek dla deweloperów, którzy nie muszą tworzyć ani usuwać zasobów, ani zmieniać zainicjowanej przepływności kontenerów, nad którymi pracują.

Dostęp można ograniczyć, wykonując następujące czynności:
1. Tworzenie niestandardowej roli Azure Active Directory dla użytkowników, dla których chcesz ograniczyć dostęp. Niestandardowa rola Active Directory powinna mieć szczegółowy poziom dostępu do operacji przy użyciu [akcji szczegółowych](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)Azure Cosmos DB.
1. Niezezwalanie na wykonywanie operacji innych niż dane z kluczami. Można to osiągnąć, ograniczając te operacje tylko do Azure Resource Manager wywołań.

W następnych sekcjach tego artykułu pokazano, jak wykonać te kroki.

> [!NOTE]
> Aby można było wykonać polecenia w kolejnych sekcjach, należy zainstalować Azure PowerShell module 3.0.0 lub nowszym, a także [rolę właściciela platformy Azure](../role-based-access-control/built-in-roles.md#owner) w subskrypcji, którą próbujesz zmodyfikować.

W skryptach programu PowerShell w kolejnych sekcjach Zastąp następujące symbole zastępcze wartościami charakterystycznymi dla danego środowiska:
- `$MySubscriptionId` — Identyfikator subskrypcji zawierający konto usługi Azure Cosmos, w którym chcesz ograniczyć uprawnienia. Przykład: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` — Grupa zasobów zawierająca konto usługi Azure Cosmos. Przykład: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` — Nazwa konta usługi Azure Cosmos. Przykład: `mycosmosdbsaccount`.
- `$MyUserName` -Identyfikator logowania ( username@domain ) użytkownika, dla którego chcesz ograniczyć dostęp. Przykład: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Wybierz subskrypcję platformy Azure

Polecenia Azure PowerShell wymagają logowania i wybrania subskrypcji w celu wykonania poleceń:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Utwórz niestandardową rolę Azure Active Directory

Poniższy skrypt tworzy przypisanie roli Azure Active Directory z dostępem "klucz Only" dla kont usługi Azure Cosmos. Rola jest oparta na [rolach niestandardowych platformy Azure](../role-based-access-control/custom-roles.md) i [szczegółowych akcjach dla Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Te role i akcje są częścią `Microsoft.DocumentDB` przestrzeni nazw Azure Active Directory.

1. Najpierw Utwórz dokument JSON o nazwie `AzureCosmosKeyOnlyAccess.json` z następującą zawartością:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Uruchom następujące polecenia, aby utworzyć przypisanie roli i przypisać je do użytkownika:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Nie Zezwalaj na wykonywanie operacji innych niż dane

Następujące polecenia umożliwiają usunięcie możliwości korzystania z kluczy w celu:
- Tworzenie, modyfikowanie lub usuwanie zasobów
- Aktualizowanie ustawień kontenera (w tym zasad indeksowania, przepływności itp.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat kontroli dostępu opartej na rolach Cosmos DB](role-based-access-control.md)
- Zapoznaj się z omówieniem [bezpiecznego dostępu do danych w Cosmos DB](secure-access-to-data.md)
