---
title: Zarządzanie zasobami interfejsu API Azure Cosmos DB Core (SQL) przy użyciu programu PowerShell
description: Zarządzanie zasobami interfejsu API Azure Cosmos DB Core (SQL) przy użyciu programu PowerShell.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333447"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Zarządzanie zasobami interfejsu API Azure Cosmos DB Core (SQL) przy użyciu programu PowerShell
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W poniższym przewodniku opisano sposób użycia programu PowerShell do tworzenia skryptów i automatyzowania zarządzania zasobami interfejsu API Azure Cosmos DB Core (SQL), w tym konta Cosmos, bazy danych, kontenera i przepływności. Dla poleceń cmdlet programu PowerShell dla innych interfejsów API zobacz [przykłady programu PowerShell dla Cassandra](powershell-samples-cassandra.md), przykłady programu PowerShell dla [interfejsu API MongoDB](powershell-samples-mongodb.md), [przykłady programu PowerShell dla usługi Gremlin](powershell-samples-gremlin.md), [przykłady dla programu](powershell-samples-table.md) PowerShell dla tabeli

> [!NOTE]
> Przykłady w tym artykule używają poleceń cmdlet programu [AZ. CosmosDB](/powershell/module/az.cosmosdb) Management. Najnowsze zmiany można znaleźć na stronie Dokumentacja interfejsu API [AZ. CosmosDB](/powershell/module/az.cosmosdb) .

W przypadku zarządzania różnymi platformami Azure Cosmos DB można użyć `Az` poleceń cmdlet i programu `Az.CosmosDB` [PowerShell dla wielu platform](/powershell/scripting/install/installing-powershell), a także [interfejsu][rp-rest-api] [wiersza polecenia platformy Azure](manage-with-cli.md)lub [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Wprowadzenie

Postępuj zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell][powershell-install-configure] , aby zainstalować i zalogować się do konta platformy Azure w programie PowerShell.

> [!IMPORTANT]
> Nie można zmienić nazw zasobów Azure Cosmos DB, ponieważ to narusza sposób, w jaki Azure Resource Manager współpracuje z identyfikatorami URI zasobów.

## <a name="azure-cosmos-accounts"></a>Konta usługi Azure Cosmos

W poniższych sekcjach pokazano, jak zarządzać kontem usługi Azure Cosmos, w tym:

* [tworzenie konta usługi Azure Cosmos](#create-account)
* [Aktualizowanie konta usługi Azure Cosmos](#update-account)
* [Wyświetlanie listy wszystkich kont usługi Azure Cosmos w ramach subskrypcji](#list-accounts)
* [Pobierz konto usługi Azure Cosmos](#get-account)
* [Usuwanie konta usługi Azure Cosmos](#delete-account)
* [Aktualizowanie tagów dla konta usługi Azure Cosmos](#update-tags)
* [Lista kluczy dla konta usługi Azure Cosmos](#list-keys)
* [Ponowne generowanie kluczy dla konta usługi Azure Cosmos](#regenerate-keys)
* [Wyświetlanie listy parametrów połączenia dla konta usługi Azure Cosmos](#list-connection-strings)
* [Modyfikowanie priorytetu trybu failover dla konta usługi Azure Cosmos](#modify-failover-priority)
* [Wyzwalanie ręcznego przełączania do trybu failover dla konta usługi Azure Cosmos](#trigger-manual-failover)
* [Wyświetlanie listy blokad zasobów na koncie Azure Cosmos DB](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Utwórz konto usługi Azure Cosmos

To polecenie tworzy konto bazy danych Azure Cosmos DB z [wieloma regionami][distribute-data-globally], [automatycznym trybem failover](how-to-manage-database-account.md#automatic-failover) i ograniczonymi [zasadami spójności](consistency-levels.md).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` Grupa zasobów platformy Azure, w której ma zostać wdrożone konto Cosmos. Musi już istnieć.
* `$locations` Regiony konta bazy danych, region z `FailoverPriority 0` jest regionem zapisu.
* `$accountName` Nazwa konta usługi Azure Cosmos. Musi być unikatowa, mała, zawierać tylko znaki alfanumeryczne i znak "-" oraz długość od 3 do 31 znaków.
* `$apiKind` Typ konta Cosmos do utworzenia. Aby uzyskać więcej informacji, zobacz [interfejsy API w Cosmos DB](introduction.md#simplified-application-development).
* `$consistencyPolicy`, `$maxStalenessInterval` i `$maxStalenessPrefix` domyślny poziom spójności oraz ustawienia konta usługi Azure Cosmos. Aby uzyskać więcej informacji, zobacz [poziomy spójności w Azure Cosmos DB](consistency-levels.md).

Konta usługi Azure Cosmos można skonfigurować za pomocą zapory IP, punktów końcowych usługi Virtual Network i prywatnych punktów końcowych. Aby uzyskać informacje dotyczące sposobu konfigurowania zapory protokołu IP dla Azure Cosmos DB, zobacz [Konfigurowanie zapory IP](how-to-configure-firewall.md). Aby uzyskać informacje na temat włączania punktów końcowych usługi dla Azure Cosmos DB, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md). Aby uzyskać informacje na temat włączania prywatnych punktów końcowych dla Azure Cosmos DB, zobacz [Konfigurowanie dostępu z prywatnych punktów końcowych](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Wyświetl listę wszystkich kont usługi Azure Cosmos w grupie zasobów

To polecenie wyświetla listę wszystkich kont usługi Azure Cosmos w grupie zasobów.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Pobierz właściwości konta usługi Azure Cosmos

To polecenie umożliwia uzyskanie właściwości istniejącego konta usługi Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Aktualizowanie konta usługi Azure Cosmos

To polecenie umożliwia zaktualizowanie właściwości konta bazy danych Azure Cosmos DB. Dostępne są następujące właściwości:

* Dodawanie lub usuwanie regionów
* Zmiana domyślnych zasad spójności
* Zmienianie filtru zakresu adresów IP
* Zmiana konfiguracji Virtual Network
* Włączanie zapisu wieloregionowego

> [!NOTE]
> Nie można jednocześnie dodawać lub usuwać regionów ( `locations` ) oraz zmieniać innych właściwości dla konta usługi Azure Cosmos. Modyfikowanie regionów musi być wykonywane jako oddzielna operacja od innych zmian w ramach konta.
> [!NOTE]
> To polecenie pozwala dodawać i usuwać regiony, ale nie pozwala na modyfikowanie priorytetów trybu failover ani wyzwalanie ręcznej pracy awaryjnej. Zobacz [Modyfikowanie priorytetu trybu failover](#modify-failover-priority) i [wyzwalanie ręcznego przełączania do trybu failover](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Włącz wiele regionów zapisu dla konta usługi Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Usuwanie konta usługi Azure Cosmos

To polecenie usuwa istniejące konto usługi Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Aktualizowanie tagów konta usługi Azure Cosmos

To polecenie ustawia [Tagi zasobów platformy Azure][azure-resource-tags] dla konta usługi Azure Cosmos. Tagi można ustawić zarówno podczas tworzenia konta `New-AzCosmosDBAccount` , jak i w przypadku aktualizacji konta przy użyciu `Update-AzCosmosDBAccount` .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Wyświetl listę kluczy konta

Podczas tworzenia konta usługi Azure Cosmos Usługa generuje dwa podstawowe klucze dostępu, których można użyć do uwierzytelniania podczas uzyskiwania dostępu do konta usługi Azure Cosmos. Klucze tylko do odczytu do uwierzytelniania operacji tylko do odczytu są również generowane.
Dostarczając dwa klucze dostępu, Azure Cosmos DB umożliwia ponowne generowanie i obracanie jednego klucza w czasie bez przerw na koncie usługi Azure Cosmos.
Konta Cosmos DB mają dwa klucze do odczytu i zapisu (podstawowe i pomocnicze) oraz dwie klucze tylko do odczytu (podstawowe i pomocnicze).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Wyświetlanie listy parametrów połączenia

Następujące polecenie pobiera parametry połączenia w celu połączenia aplikacji z kontem Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Ponowne generowanie kluczy konta

Aby zapewnić bezpieczeństwo połączeń, należy okresowo generować klucze dostępu do konta usługi Azure Cosmos. Podstawowy i pomocniczy klucz dostępu są przypisane do konta. Pozwala to klientom na zachowanie dostępu podczas ponownego generowania jednego klucza w danym momencie.
Istnieją cztery typy kluczy dla konta usługi Azure Cosmos (podstawowa, pomocnicza, PrimaryReadonly i SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Włącz automatyczną pracę w trybie failover

Następujące polecenie ustawia konto Cosmos DB w celu automatycznego przełączenia w tryb failover do jego regionu pomocniczego, jeśli region podstawowy staje się niedostępny.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Modyfikowanie priorytetu trybu failover

W przypadku kont skonfigurowanych z automatycznym trybem failover można zmienić kolejność, w jakiej Cosmos replikę pomocniczą na podstawową, gdyby podstawowy stał się niedostępny.

W poniższym przykładzie przyjęto założenie, że bieżącym priorytetem trybu failover jest `West US 2 = 0` , `East US 2 = 1` , `South Central US = 2` . Polecenie zmieni ten stan na `West US 2 = 0` , `South Central US = 1` , `East US 2 = 2` .

> [!CAUTION]
> Zmiana lokalizacji `failoverPriority=0` spowoduje wyzwolenie ręcznej pracy awaryjnej dla konta usługi Azure Cosmos. Wszystkie inne zmiany priorytetów nie będą wyzwalać trybu failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Wyzwalanie ręcznego przełączania do trybu failover

W przypadku kont skonfigurowanych z ręcznym trybem failover można przełączyć wszystkie repliki pomocnicze na podstawowe przez zmianę na `failoverPriority=0` . Ta operacja może służyć do inicjowania testowania odzyskiwania po awarii w celu zaplanowania odzyskiwania po awarii.

W poniższym przykładzie Załóżmy, że konto ma bieżący priorytet trybu failover `West US 2 = 0` i `East US 2 = 1` i przerzuca regiony.

> [!CAUTION]
> Zmiana `locationName` dla `failoverPriority=0` zostanie wyzwolona ręczny tryb failover dla konta usługi Azure Cosmos. Jakakolwiek inna zmiana priorytetu nie spowoduje wyzwolenia trybu failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Wyświetlanie listy blokad zasobów na koncie Azure Cosmos DB

Blokadę zasobów można umieścić na Azure Cosmos DB zasobów, w tym baz danych i kolekcji. W poniższym przykładzie pokazano, jak wyświetlić listę wszystkich blokad zasobów platformy Azure na koncie Azure Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Baza danych Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać bazą danych Azure Cosmos DB, w tym:

* [Tworzenie bazy danych usługi Azure Cosmos DB](#create-db)
* [Tworzenie bazy danych Azure Cosmos DB z udostępnioną przepływność](#create-db-ru)
* [Uzyskiwanie przepływności bazy danych Azure Cosmos DB](#get-db-ru)
* [Migrowanie przepływności bazy danych do automatycznego skalowania](#migrate-db-ru)
* [Wyświetlanie listy wszystkich Azure Cosmos DB baz danych na koncie](#list-db)
* [Pobieranie pojedynczej bazy danych Azure Cosmos DB](#get-db)
* [Usuwanie bazy danych Azure Cosmos DB](#delete-db)
* [Utwórz blokadę zasobów na Azure Cosmos DB bazie danych, aby zapobiec usunięciu](#create-db-lock)
* [Usuwanie blokady zasobu w bazie danych Azure Cosmos DB](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Tworzenie bazy danych usługi Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Tworzenie bazy danych Azure Cosmos DB z udostępnioną przepływność

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Uzyskiwanie przepływności bazy danych Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Migrowanie przepływności bazy danych do automatycznego skalowania

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Pobierz wszystkie bazy danych Azure Cosmos DB na koncie

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Pobieranie pojedynczej bazy danych Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Usuwanie bazy danych Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Utwórz blokadę zasobów na Azure Cosmos DB bazie danych, aby zapobiec usunięciu

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Usuwanie blokady zasobu w bazie danych Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Kontener Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać kontenerem Azure Cosmos DB, w tym:

* [Tworzenie kontenera Azure Cosmos DB](#create-container)
* [Tworzenie kontenera Azure Cosmos DB przy użyciu funkcji automatycznego skalowania](#create-container-autoscale)
* [Tworzenie kontenera Azure Cosmos DB z dużym kluczem partycji](#create-container-big-pk)
* [Uzyskiwanie przepływności kontenera Azure Cosmos DB](#get-container-ru)
* [Migrowanie przepływności kontenera do automatycznego skalowania](#migrate-container-ru)
* [Tworzenie kontenera Azure Cosmos DB z indeksem niestandardowym](#create-container-custom-index)
* [Tworzenie kontenera Azure Cosmos DB z wyłączonym indeksem](#create-container-no-index)
* [Tworzenie kontenera Azure Cosmos DB z unikatowym kluczem i czasem wygaśnięcia](#create-container-unique-key-ttl)
* [Tworzenie kontenera Azure Cosmos DB z rozwiązywaniem konfliktów](#create-container-lww)
* [Wyświetl listę wszystkich kontenerów Azure Cosmos DB w bazie danych](#list-containers)
* [Pobieranie jednego kontenera Azure Cosmos DB w bazie danych](#get-container)
* [Usuwanie kontenera Azure Cosmos DB](#delete-container)
* [Utwórz blokadę zasobów dla kontenera Azure Cosmos DB, aby zapobiec usunięciu](#create-container-lock)
* [Usuwanie blokady zasobu w kontenerze Azure Cosmos DB](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Tworzenie kontenera Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Tworzenie kontenera Azure Cosmos DB przy użyciu funkcji automatycznego skalowania

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Tworzenie kontenera Azure Cosmos DB o dużym rozmiarze klucza partycji

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Uzyskiwanie przepływności kontenera Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Migrowanie przepływności kontenera do automatycznego skalowania

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Tworzenie kontenera Azure Cosmos DB przy użyciu niestandardowych zasad indeksu

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Tworzenie kontenera Azure Cosmos DB z wyłączonym indeksem

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Tworzenie kontenera Azure Cosmos DB z unikatowymi zasadami kluczy i czasem wygaśnięcia

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Tworzenie kontenera Azure Cosmos DB z rozwiązywaniem konfliktów

Aby zapisać wszystkie konflikty w ConflictsFeed i obsłudze oddzielnie, Przekaż `-Type "Custom" -Path ""` .

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Aby utworzyć zasady rozwiązywania konfliktów w celu użycia procedury składowanej, wywołaj `New-AzCosmosDBSqlConflictResolutionPolicy` i przekaż parametry `-Type` i `-ConflictResolutionProcedure` .

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Wyświetl listę wszystkich kontenerów Azure Cosmos DB w bazie danych

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Pobieranie jednego kontenera Azure Cosmos DB w bazie danych

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Usuwanie kontenera Azure Cosmos DB

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Utwórz blokadę zasobów dla kontenera Azure Cosmos DB, aby zapobiec usunięciu

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Usuwanie blokady zasobu w kontenerze Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Następne kroki

* [Wszystkie przykłady programu PowerShell](powershell-samples.md)
* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Tworzenie kontenera Azure Cosmos DB](how-to-create-container.md)
* [Skonfiguruj czas wygaśnięcia w Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/