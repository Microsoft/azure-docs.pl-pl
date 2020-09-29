---
title: Inspekcja na koncie magazynu za siecią wirtualną i zaporą
description: Konfigurowanie inspekcji do zapisywania zdarzeń bazy danych na koncie magazynu za siecią wirtualną i zaporą
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 74926411b659cf5973b03b2caca58d7666803f9c
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444527"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Inspekcja zapisu na koncie magazynu za siecią wirtualną i zaporą
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Inspekcja dla [Azure SQL Database](sql-database-paas-overview.md) i [usługi Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) obsługuje zapisywanie zdarzeń bazy danych na [koncie usługi Azure Storage](../../storage/common/storage-account-overview.md) za siecią wirtualną i zaporą.

W tym artykule opisano dwa sposoby konfigurowania Azure SQL Database i konta usługi Azure Storage dla tej opcji. Pierwszy używa Azure Portal, drugi używa REST.

## <a name="background"></a>Tło

[Usługa azure Virtual Network (VNET)](../../virtual-network/virtual-networks-overview.md) to podstawowy blok konstrukcyjny dla sieci prywatnej na platformie Azure. Sieć wirtualna umożliwia bezpieczne komunikowanie się ze sobą za pomocą Internetu i sieci lokalnych w wielu typach zasobów platformy Azure, takich jak Azure Virtual Machines (VM). Sieć wirtualna jest podobna do tradycyjnej sieci w Twoim centrum danych, ale oferuje dodatkowe korzyści wynikające z infrastruktury platformy Azure, takiej jak skalowanie, dostępność i izolacja.

Aby dowiedzieć się więcej na temat koncepcji sieci wirtualnej, najlepszych rozwiązań i wielu innych, zobacz [co to jest usługa Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

Aby dowiedzieć się więcej na temat tworzenia sieci wirtualnej, zobacz [Szybki Start: tworzenie sieci wirtualnej przy użyciu Azure Portal](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić inspekcję zapisu na koncie magazynu za siecią wirtualną lub zaporą, wymagane są następujące wymagania wstępne:

> [!div class="checklist"]
>
> * Konto magazynu ogólnego przeznaczenia w wersji 2. Jeśli masz konto magazynu ogólnego przeznaczenia w wersji 1 lub BLOB, [Przeprowadź uaktualnienie do konta magazynu ogólnego przeznaczenia w wersji 2](../../storage/common/storage-account-upgrade.md). Aby uzyskać więcej informacji, zobacz [typy kont magazynu](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Konto magazynu musi znajdować się w tej samej subskrypcji i w tej samej lokalizacji co [serwer logiczny programu SQL Server](logical-servers.md).
> * Wymagane jest konto usługi Azure Storage `Allow trusted Microsoft services to access this storage account` . Ustaw tę wartość na **zaporach konta magazynu i w sieciach wirtualnych**.
> * Musisz mieć `Microsoft.Authorization/roleAssignments/write` uprawnienie do wybranego konta magazynu. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurowanie w witrynie Azure Portal

Połącz się z [Azure Portalą](https://portal.azure.com) z subskrypcją. Przejdź do grupy zasobów i serwera.

1. Kliknij pozycję **Inspekcja** pod nagłówkiem zabezpieczenia. Wybierz pozycję **włączone**.

2. Wybierz pozycję **Magazyn**. Wybierz konto magazynu, w którym będą zapisywane dzienniki. Konto magazynu musi być zgodne z wymaganiami wymienionymi w sekcji [wymagania wstępne](#prerequisites).

3. Otwórz **szczegóły magazynu**

  > [!NOTE]
  > Jeśli wybrane konto magazynu znajduje się za siecią wirtualną, zostanie wyświetlony następujący komunikat:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Jeśli ten komunikat nie jest wyświetlany, konto magazynu nie należy do sieci wirtualnej.

4. Wybierz liczbę dni okresu przechowywania. Następnie kliknij przycisk **OK**. Dzienniki starsze niż okres przechowywania są usuwane.

5. Wybierz pozycję **Zapisz** w ustawieniach inspekcji.

Inspekcja została pomyślnie skonfigurowana do zapisu na koncie magazynu za siecią wirtualną lub zaporą.

## <a name="configure-with-rest-commands"></a>Konfigurowanie przy użyciu poleceń REST

Alternatywą dla korzystania z Azure Portal może być użycie poleceń REST w celu skonfigurowania inspekcji do zapisywania zdarzeń bazy danych na koncie magazynu za siecią wirtualną i zaporą.

Przykładowe skrypty w tej sekcji wymagają aktualizacji skryptu przed jego uruchomieniem. Zastąp następujące wartości w skryptach:

|Wartość przykładowa|Przykładowy opis|
|:-----|:-----|
|`<subscriptionId>`| Identyfikator subskrypcji platformy Azure|
|`<resource group>`| Grupa zasobów|
|`<logical SQL server>`| Nazwa serwera|
|`<administrator login>`| Konto administratora |
|`<complex password>`| Złożone hasło dla konta administratora|

Aby skonfigurować inspekcję SQL do zapisywania zdarzeń na koncie magazynu za siecią wirtualną lub zaporą:

1. Zarejestruj serwer w usłudze Azure Active Directory (Azure AD). Użyj programu PowerShell lub interfejsu API REST.

   **Program PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**interfejs API REST**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Przykładowe żądanie

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Treść żądania

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do konta magazynu. Znajdź **Access Control (IAM)**, a następnie kliknij pozycję **Dodaj przypisanie roli**. Przypisywanie **danych obiektów blob magazynu współautor** roli platformy Azure do serwera, na którym znajduje się baza danych, która została zarejestrowana w Azure Active Directory (Azure AD), jak w poprzednim kroku.

   > [!NOTE]
   > Tylko członkowie z uprawnieniami właściciela mogą wykonać ten krok. W przypadku różnych wbudowanych ról platformy Azure Zapoznaj się z [wbudowanymi rolami platformy Azure](../../role-based-access-control/built-in-roles.md).

3. Skonfiguruj [zasady inspekcji obiektów BLOB serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)bez określania *storageAccountAccessKey*:

   Przykładowe żądanie

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   Treść żądania

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager

Inspekcję można skonfigurować do zapisywania zdarzeń bazy danych na koncie magazynu za siecią wirtualną i zaporą przy użyciu szablonu [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , jak pokazano w następującym przykładzie:

> [!IMPORTANT]
> Aby można było używać konta magazynu za siecią wirtualną i zaporą, należy ustawić parametr **isStorageBehindVnet** na wartość true.

- [Wdrożenie serwera SQL platformy Azure z włączoną inspekcją w celu zapisania dzienników inspekcji do magazynu obiektów BLOB](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> Połączony przykład znajduje się w zewnętrznym repozytorium publicznym i ma charakter "AS IS", bez rękojmi i nie jest obsługiwany w ramach żadnego programu lub usługi pomocy technicznej firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* [Użyj programu PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej, a następnie regułę sieci wirtualnej dla Azure SQL Database.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Reguły Virtual Network: operacje przy użyciu interfejsów API REST](/rest/api/sql/virtualnetworkrules)
* [Korzystanie z punktów końcowych usługi sieci wirtualnej i reguł dla serwerów](vnet-service-endpoint-rule-overview.md)
