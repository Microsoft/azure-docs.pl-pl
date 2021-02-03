---
title: Skonfiguruj uprawnienia do przywracania konta Azure Cosmos DB.
description: Dowiedz się, jak odizolować i ograniczyć uprawnienia do przywracania dla konta ciągłej kopii zapasowej do określonej roli lub podmiotu zabezpieczeń. Pokazano, jak przypisać wbudowaną rolę przy użyciu Azure Portal, interfejsu wiersza polecenia lub zdefiniować rolę niestandardową.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 3614a85a6df2e793a73a2609d6f5762e4dc873fb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527623"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Zarządzaj uprawnieniami do przywracania konta Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB umożliwia izolowanie i ograniczanie uprawnień przywracania dla konta ciągłej kopii zapasowej do określonej roli lub podmiotu zabezpieczeń. Właściciel konta może wyzwolić przywracanie i przypisać rolę do innych podmiotów zabezpieczeń, aby wykonać operację przywracania. Te uprawnienia można stosować w zakresie subskrypcji lub dokładniej w zakresie konta źródłowego, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lista ról wymaganych do wykonania operacji przywracania." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Zakres to zestaw zasobów, do których masz dostęp, aby dowiedzieć się więcej o zakresach, zobacz dokumentację [usługi Azure RBAC](../role-based-access-control/scope-overview.md) . W Azure Cosmos DB odpowiednie zakresy to źródłowa subskrypcja i konto bazy danych dla większości przypadków użycia. Podmiot zabezpieczeń wykonujący akcje przywracania powinien mieć uprawnienia do zapisu w docelowej grupie zasobów.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Przypisywanie ról do przywracania za pomocą Azure Portal

Aby można było wykonać przywracanie, użytkownik lub podmiot zabezpieczeń muszą mieć uprawnienia do przywracania (czyli uprawnienia do przywracania/działania) i uprawnienia do aprowizacji nowego konta (czyli uprawnienia do zapisu).  Aby przyznać te uprawnienia, właściciel może przypisać role "CosmosRestoreOperator" i "operator Cosmos DB" wbudowane w rolach do podmiotu zabezpieczeń.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)

1. Przejdź do subskrypcji i przejdź do karty **Kontrola dostępu (IAM)** i wybierz pozycję **Dodaj**  >  **Dodaj przypisanie roli**

1. W okienku **Dodaj przypisanie roli** w polu **rola** wybierz pozycję rola **CosmosRestoreOperator** . Wybierz **użytkownika, grupę lub jednostkę usługi** dla pola **Przypisz dostęp do** i wyszukaj nazwę użytkownika lub identyfikator e-mail, jak pokazano na poniższej ilustracji:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Przypisywanie ról operatorów CosmosRestoreOperator i Cosmos DB." border="true":::

1. Wybierz pozycję **Zapisz** , aby udzielić uprawnienia "Przywracanie/akcja".

1. Powtórz krok 3 z rolą **operatora Cosmos DB** , aby udzielić uprawnienia do zapisu. Przypisując tę rolę z Azure Portal przyznaje uprawnienia do przywracania całej subskrypcji.

## <a name="permission-scopes"></a>Zakresy uprawnień

|Zakres  |Przykład  |
|---------|---------|
|Subskrypcja | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Grupa zasobów | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Zasób konta CosmosDB dostępnych | /subscriptions/00000000-0000-0000-0000-000000000000/Providers/Microsoft.DocumentDB/Locations/zachodnie stany USA/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

Zasób konta dostępnych można wyodrębnić z danych wyjściowych `az cosmosdb restorable-database-account list --name <accountname>` polecenia w interfejsie CLI lub w poleceniu `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` cmdlet w programie PowerShell. Atrybut Name w danych wyjściowych reprezentuje wartość "instanceID" konta dostępnych. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [programu PowerShell](continuous-backup-restore-powershell.md) lub [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md) .

## <a name="permissions"></a>Uprawnienia

Następujące uprawnienia są wymagane do wykonywania różnych działań związanych z przywracaniem dla kont trybu ciągłej kopii zapasowej:

|Uprawnienie  |Wpływ  |Zakres minimalny  |Zakres maksymalny  |
|---------|---------|---------|---------|
|Microsoft. resources/Deployments/Validate/Action, Microsoft. resources/Deployments/Write | Te uprawnienia są wymagane do wdrożenia szablonu ARM w celu utworzenia przywróconego konta. Aby ustawić tę rolę, zobacz przykładowe uprawnienia [RestorableAction]() poniżej. | Nie dotyczy | Nie dotyczy  |
|Microsoft.DocumentDB/databaseAccounts/zapis | To uprawnienie jest wymagane do przywrócenia konta do grupy zasobów | Grupa zasobów, w ramach której zostało utworzone przywrócone konto. | Subskrypcja, w ramach której zostało utworzone przywrócone konto |
|Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/Restore/Action |To uprawnienie jest wymagane w zakresie konta źródłowej bazy danych dostępnych, aby umożliwić wykonywanie na nim akcji przywracania.  | Zasób "RestorableDatabaseAccount" należący do przywracanego konta źródłowego. Ta wartość jest również określona przez właściwość "ID" zasobu konta bazy danych dostępnych. Przykładem konta dostępnych jest `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>` | Subskrypcja zawierająca konto bazy danych dostępnych. Nie można wybrać grupy zasobów jako zakresu.  |
|Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/Read |To uprawnienie jest wymagane w zakresie konta źródłowej bazy danych dostępnych w celu wyświetlenia listy kont bazy danych, które mogą zostać przywrócone.  | Zasób "RestorableDatabaseAccount" należący do przywracanego konta źródłowego. Ta wartość jest również określona przez właściwość "ID" zasobu konta bazy danych dostępnych. Przykładem konta dostępnych jest `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| Subskrypcja zawierająca konto bazy danych dostępnych. Nie można wybrać grupy zasobów jako zakresu.  |
|Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/*/Read | To uprawnienie jest wymagane w zakresie konta źródłowego dostępnych, aby umożliwić odczytywanie zasobów dostępnych, takich jak lista baz danych i kontenerów dla konta dostępnych.  | Zasób "RestorableDatabaseAccount" należący do przywracanego konta źródłowego. Ta wartość jest również określona przez właściwość "ID" zasobu konta bazy danych dostępnych. Przykładem konta dostępnych jest `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| Subskrypcja zawierająca konto bazy danych dostępnych. Nie można wybrać grupy zasobów jako zakresu. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Scenariusze przypisywania ról interfejsu wiersza polecenia platformy Azure do przywrócenia w różnych zakresach

Role z uprawnieniem można przypisać do różnych zakresów w celu uzyskania szczegółowej kontroli nad tym, kto może wykonać operację przywracania w ramach subskrypcji lub danego konta.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Przypisywanie możliwości przywracania z dowolnego konta dostępnych w ramach subskrypcji

Przypisywanie `CosmosRestoreOperator` wbudowanej roli na poziomie subskrypcji

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Przypisywanie możliwości przywracania z określonego konta

* Przypisz akcję zapisu użytkownika do konkretnej grupy zasobów. Ta akcja jest wymagana do utworzenia nowego konta w grupie zasobów.

* Przypisz wbudowaną rolę "CosmosRestoreOperator" do określonego konta bazy danych dostępnych, które ma zostać przywrócone. W poniższym poleceniu zakres dla elementu "RestorableDatabaseAccount" jest pobierany z właściwości "ID" w danych wyjściowych `az cosmosdb restorable-database-account` (jeśli jest używany interfejs wiersza polecenia) lub  `Get-AzCosmosDBRestorableDatabaseAccount` (jeśli jest używany program PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Przypisz możliwość przywracania z dowolnego konta źródłowego w grupie zasobów.
Ta operacja nie jest obecnie obsługiwana.

## <a name="custom-role-creation-for-restore-action-with-cli"></a>Tworzenie roli niestandardowej dla akcji przywracania za pomocą interfejsu wiersza polecenia

Właściciel subskrypcji może zapewnić uprawnienie do przywracania wszelkich innych tożsamości usługi Azure AD. Uprawnienie do przywracania opiera się na akcji: "Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/Restore/Action" i powinna być uwzględniona w ich uprawnienia do przywracania. Istnieje wbudowana rola o nazwie "CosmosRestoreOperator", która ma tę rolę. Możesz przypisać uprawnienie przy użyciu tej wbudowanej roli lub utworzyć rolę niestandardową.

RestorableAction poniżej reprezentuje rolę niestandardową. Musisz jawnie utworzyć tę rolę. Poniższy szablon JSON tworzy rolę niestandardową "RestorableAction" z uprawnieniem przywracanie:

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
