---
title: Skonfiguruj uprawnienia do przywracania konta Azure Cosmos DB.
description: Dowiedz się, jak odizolować i ograniczyć uprawnienia do przywracania dla konta ciągłej kopii zapasowej do określonej roli lub podmiotu zabezpieczeń. Pokazano, jak przypisać wbudowaną rolę przy użyciu Azure Portal, interfejsu wiersza polecenia lub zdefiniować rolę niestandardową.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b3ce2c195dc2fa3dd703306e731aa5b807b78b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100648607"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Zarządzaj uprawnieniami do przywracania konta Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB pozwala odizolować i ograniczyć uprawnienia przywracania do ciągłego tworzenia kopii zapasowych (wersja zapoznawcza) do określonej roli lub podmiotu zabezpieczeń. Właściciel konta może wyzwolić przywracanie i przypisać rolę do innych podmiotów zabezpieczeń, aby wykonać operację przywracania. Te uprawnienia można stosować w zakresie subskrypcji lub dokładniej w zakresie konta źródłowego, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lista ról wymaganych do wykonania operacji przywracania." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Zakres to zestaw zasobów, do których masz dostęp, aby dowiedzieć się więcej o zakresach, zobacz dokumentację [usługi Azure RBAC](../role-based-access-control/scope-overview.md) . W Azure Cosmos DB odpowiednie zakresy to źródłowa subskrypcja i konto bazy danych dla większości przypadków użycia. Podmiot zabezpieczeń wykonujący akcje przywracania powinien mieć uprawnienia do zapisu w docelowej grupie zasobów.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Przypisywanie ról do przywracania za pomocą Azure Portal

Aby wykonać przywracanie, użytkownik lub podmiot zabezpieczeń musi mieć uprawnienia do przywracania (czyli uprawnienia do *przywracania/działania* ) i uprawnienia do aprowizacji nowego konta (z uprawnieniem do *zapisu* ).  Aby przyznać te uprawnienia, właściciel może przypisać `CosmosRestoreOperator` `Cosmos DB Operator` role i wbudowane do podmiotu zabezpieczeń.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)

1. Przejdź do subskrypcji i przejdź do karty **Kontrola dostępu (IAM)** i wybierz pozycję **Dodaj**  >  **Dodaj przypisanie roli**

1. W okienku **Dodaj przypisanie roli** w polu **rola** wybierz pozycję rola **CosmosRestoreOperator** . Wybierz **użytkownika, grupę lub jednostkę usługi** dla pola **Przypisz dostęp do** i wyszukaj nazwę użytkownika lub identyfikator e-mail, jak pokazano na poniższej ilustracji:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Przypisywanie ról operatorów CosmosRestoreOperator i Cosmos DB." border="true":::

1. Wybierz pozycję **Zapisz** , aby przyznać uprawnienia *przywracanie/akcja* .

1. Powtórz krok 3 z rolą **operatora Cosmos DB** , aby udzielić uprawnienia do zapisu. Przypisując tę rolę z Azure Portal przyznaje uprawnienia do przywracania całej subskrypcji.

## <a name="permission-scopes"></a>Zakresy uprawnień

|Zakres  |Przykład  |
|---------|---------|
|Subskrypcja | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Grupa zasobów | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Zasób konta CosmosDB dostępnych | /subscriptions/00000000-0000-0000-0000-000000000000/Providers/Microsoft.DocumentDB/Locations/zachodnie stany USA/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

Zasób konta dostępnych można wyodrębnić z danych wyjściowych `az cosmosdb restorable-database-account list --name <accountname>` polecenia w interfejsie CLI lub w poleceniu `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` cmdlet w programie PowerShell. Atrybut Name w danych wyjściowych reprezentuje `instanceID` konto dostępnych. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [programu PowerShell](continuous-backup-restore-powershell.md) lub [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md) .

## <a name="permissions"></a>Uprawnienia

Następujące uprawnienia są wymagane do wykonywania różnych działań związanych z przywracaniem dla kont trybu ciągłej kopii zapasowej:

|Uprawnienie  |Wpływ  |Zakres minimalny  |Zakres maksymalny  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Te uprawnienia są wymagane do wdrożenia szablonu ARM w celu utworzenia przywróconego konta. Aby ustawić tę rolę, zobacz przykładowe uprawnienia [RestorableAction](#custom-restorable-action) poniżej. | Nie dotyczy | Nie dotyczy  |
|`Microsoft.DocumentDB/databaseAccounts/write` | To uprawnienie jest wymagane do przywrócenia konta do grupy zasobów | Grupa zasobów, w ramach której zostało utworzone przywrócone konto. | Subskrypcja, w ramach której zostało utworzone przywrócone konto |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |To uprawnienie jest wymagane w zakresie konta źródłowej bazy danych dostępnych, aby umożliwić wykonywanie na nim akcji przywracania.  | Zasób *RestorableDatabaseAccount* należący do przywracanego konta źródłowego. Ta wartość jest również określona przez `ID` Właściwość zasobu konta bazy danych dostępnych. Przykładem konta dostępnych jest */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>* | Subskrypcja zawierająca konto bazy danych dostępnych. Nie można wybrać grupy zasobów jako zakresu.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |To uprawnienie jest wymagane w zakresie konta źródłowej bazy danych dostępnych w celu wyświetlenia listy kont bazy danych, które mogą zostać przywrócone.  | Zasób *RestorableDatabaseAccount* należący do przywracanego konta źródłowego. Ta wartość jest również określona przez `ID` Właściwość zasobu konta bazy danych dostępnych. Przykładem konta dostępnych jest */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| Subskrypcja zawierająca konto bazy danych dostępnych. Nie można wybrać grupy zasobów jako zakresu.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | To uprawnienie jest wymagane w zakresie konta źródłowego dostępnych, aby umożliwić odczytywanie zasobów dostępnych, takich jak lista baz danych i kontenerów dla konta dostępnych.  | Zasób *RestorableDatabaseAccount* należący do przywracanego konta źródłowego. Ta wartość jest również określona przez `ID` Właściwość zasobu konta bazy danych dostępnych. Przykładem konta dostępnych jest */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| Subskrypcja zawierająca konto bazy danych dostępnych. Nie można wybrać grupy zasobów jako zakresu. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Scenariusze przypisywania ról interfejsu wiersza polecenia platformy Azure do przywrócenia w różnych zakresach

Role z uprawnieniem można przypisać do różnych zakresów w celu uzyskania szczegółowej kontroli nad tym, kto może wykonać operację przywracania w ramach subskrypcji lub danego konta.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Przypisywanie możliwości przywracania z dowolnego konta dostępnych w ramach subskrypcji

Przypisywanie `CosmosRestoreOperator` wbudowanej roli na poziomie subskrypcji

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Przypisywanie możliwości przywracania z określonego konta

* Przypisz akcję zapisu użytkownika do konkretnej grupy zasobów. Ta akcja jest wymagana do utworzenia nowego konta w grupie zasobów.

* Przypisz wbudowaną rolę *CosmosRestoreOperator* do określonego konta bazy danych dostępnych, które ma zostać przywrócone. W poniższym poleceniu zakres dla *RestorableDatabaseAccount* jest pobierany z `ID` właściwości w danych wyjściowych `az cosmosdb restorable-database-account` (jeśli jest używany interfejs wiersza polecenia) lub  `Get-AzCosmosDBRestorableDatabaseAccount` (w przypadku korzystania z programu PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Przypisz możliwość przywracania z dowolnego konta źródłowego w grupie zasobów.
Ta operacja nie jest obecnie obsługiwana.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>Tworzenie roli niestandardowej dla akcji przywracania za pomocą interfejsu wiersza polecenia

Właściciel subskrypcji może zapewnić uprawnienie do przywracania wszelkich innych tożsamości usługi Azure AD. Uprawnienie przywracanie jest oparte na akcji: `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` i powinno być dołączone do ich uprawnień przywracania. Istnieje wbudowana rola o nazwie *CosmosRestoreOperator* , która ma tę rolę. Możesz przypisać uprawnienie przy użyciu tej wbudowanej roli lub utworzyć rolę niestandardową.

RestorableAction poniżej reprezentuje rolę niestandardową. Musisz jawnie utworzyć tę rolę. Poniższy szablon JSON tworzy rolę niestandardową *RestorableAction* z uprawnieniami do przywracania:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Następnie użyj następującego polecenia wdrażania szablonu, aby utworzyć rolę z uprawnieniem przywracanie przy użyciu szablonu usługi ARM:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Model zasobów trybu ciągłej kopii zapasowej](continuous-backup-restore-resource-model.md)
