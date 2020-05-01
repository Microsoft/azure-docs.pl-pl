---
title: Tworzenie Azure Cosmos DB i zarządzanie nimi przy użyciu programu PowerShell
description: Za pomocą programu Azure PowerShell Zarządzaj kontami, bazami danych, kontenerami i przepływem pracy platformy Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/29/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: d4473bbfe10fa2d0fc87eed7889a3e06af650b5b
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592149"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Zarządzanie Azure Cosmos DB zasobami interfejsu API SQL przy użyciu programu PowerShell

W poniższym przewodniku opisano sposób użycia programu PowerShell do tworzenia skryptów i automatyzowania zarządzania zasobami usługi Azure Cosmos DB, w tym kontem, bazą danych, kontenerem i przepływnością.

> [!NOTE]
> Przykłady w tym artykule używają poleceń cmdlet programu [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management. Te polecenia cmdlet są nadal w wersji zapoznawczej i mogą ulec zmianie, zanim staną się ogólnie dostępne. Na stronie Dokumentacja interfejsu API [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) można znaleźć wszystkie aktualizacje poleceń.

W przypadku zarządzania różnymi platformami Azure Cosmos DB można użyć poleceń cmdlet `Az` i programu `Az.CosmosDB` PowerShell dla [wielu platform](https://docs.microsoft.com/powershell/scripting/install/installing-powershell), a także [interfejsu][rp-rest-api]wiersza polecenia [platformy Azure](manage-with-cli.md)lub [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Getting Started

Postępuj zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell][powershell-install-configure] , aby zainstalować i zalogować się do konta platformy Azure w programie PowerShell.

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

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Utwórz konto usługi Azure Cosmos

To polecenie tworzy konto bazy danych Azure Cosmos DB z [wieloma regionami][distribute-data-globally], [automatycznym trybem failover](how-to-manage-database-account.md#automatic-failover) i ograniczonymi [zasadami spójności](consistency-levels.md).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`Grupa zasobów platformy Azure, w której ma zostać wdrożone konto Cosmos. Musi już istnieć.
* `$locations`Regiony konta bazy danych, począwszy od regionu zapisu i uporządkowane według priorytetu trybu failover.
* `$accountName`Nazwa konta usługi Azure Cosmos. Musi być unikatowa, mała, zawierać tylko znaki alfanumeryczne i znak "-" oraz długość od 3 do 31 znaków.
* `$apiKind`Typ konta Cosmos do utworzenia. Aby uzyskać więcej informacji, zobacz [interfejsy API w Cosmos DB](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis).
* `$consistencyPolicy`, `$maxStalenessInterval`i `$maxStalenessPrefix` domyślny poziom spójności oraz ustawienia konta usługi Azure Cosmos. Aby uzyskać więcej informacji, zobacz [poziomy spójności w Azure Cosmos DB](consistency-levels.md).

Konta usługi Azure Cosmos można skonfigurować za pomocą zapory IP, punktów końcowych usługi Virtual Network i prywatnych punktów końcowych. Aby uzyskać informacje dotyczące sposobu konfigurowania zapory protokołu IP dla Azure Cosmos DB, zobacz [Konfigurowanie zapory IP](how-to-configure-firewall.md). Aby uzyskać informacje na temat włączania punktów końcowych usługi dla Azure Cosmos DB, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md). Aby uzyskać informacje na temat włączania prywatnych punktów końcowych dla Azure Cosmos DB, zobacz [Konfigurowanie dostępu z prywatnych punktów końcowych](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Wyświetl listę wszystkich kont usługi Azure Cosmos w grupie zasobów

To polecenie wyświetla listę wszystkich kont usługi Azure Cosmos w grupie zasobów.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>Pobierz właściwości konta usługi Azure Cosmos

To polecenie umożliwia uzyskanie właściwości istniejącego konta usługi Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Aktualizowanie konta usługi Azure Cosmos

To polecenie umożliwia zaktualizowanie właściwości konta bazy danych Azure Cosmos DB. Dostępne są następujące właściwości:

* Dodawanie lub usuwanie regionów
* Zmiana domyślnych zasad spójności
* Zmienianie filtru zakresu adresów IP
* Zmiana konfiguracji Virtual Network
* Włączanie wielu wzorców

> [!NOTE]
> Nie można jednocześnie dodawać lub usuwać regionów (`locations`) oraz zmieniać innych właściwości dla konta usługi Azure Cosmos. Modyfikowanie regionów musi być wykonywane jako oddzielna operacja od innych zmian w ramach konta.
> [!NOTE]
> To polecenie pozwala dodawać i usuwać regiony, ale nie pozwala na modyfikowanie priorytetów trybu failover ani wyzwalanie ręcznej pracy awaryjnej. Zobacz [Modyfikowanie priorytetu trybu failover](#modify-failover-priority) i [wyzwalanie ręcznego przełączania do trybu failover](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) {
    $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ }
}

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObjects2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) {
    $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ }
}

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObjects3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Włącz wiele regionów zapisu dla konta usługi Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-master
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a>Usuwanie konta usługi Azure Cosmos

To polecenie usuwa istniejące konto usługi Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Aktualizowanie tagów konta usługi Azure Cosmos

To polecenie ustawia [Tagi zasobów platformy Azure][azure-resource-tags] dla konta usługi Azure Cosmos. Tagi można ustawić zarówno podczas tworzenia konta `New-AzCosmosDBAccount` , jak i w przypadku aktualizacji konta przy użyciu `Update-AzCosmosDBAccount`.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Wyświetl listę kluczy konta

Podczas tworzenia konta usługi Azure Cosmos Usługa generuje dwa główne klucze dostępu, których można użyć do uwierzytelniania podczas uzyskiwania dostępu do konta usługi Azure Cosmos. Klucze tylko do odczytu do uwierzytelniania operacji tylko do odczytu są również generowane.
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

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Wyświetlanie listy parametrów połączenia

Następujące polecenie pobiera parametry połączenia w celu połączenia aplikacji z kontem Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Ponowne generowanie kluczy konta

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

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Włącz automatyczną pracę w trybie failover

Następujące polecenie ustawia konto Cosmos DB w celu automatycznego przełączenia w tryb failover do jego regionu pomocniczego, jeśli region podstawowy staje się niedostępny.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-master - cannot have both automatic
# failover and multi-master on an account
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

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Modyfikowanie priorytetu trybu failover

W przypadku kont skonfigurowanych z automatycznym trybem failover można zmienić kolejność, w jakiej Cosmos replikę pomocniczą na podstawową, gdyby podstawowy stał się niedostępny.

W poniższym przykładzie przyjęto założenie, że bieżącym priorytetem `East US 2 = 1`trybu `South Central US = 2`failover jest `West US 2 = 0`,,. Polecenie zmieni ten stan na `West US 2 = 0`, `South Central US = 1`,. `East US 2 = 2`

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

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Wyzwalanie ręcznego przełączania do trybu failover

W przypadku kont skonfigurowanych z ręcznym trybem failover można przełączyć wszystkie repliki pomocnicze na podstawowe przez zmianę `failoverPriority=0`na. Ta operacja może służyć do inicjowania testowania odzyskiwania po awarii w celu zaplanowania odzyskiwania po awarii.

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

## <a name="azure-cosmos-db-database"></a>Baza danych Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać bazą danych Azure Cosmos DB, w tym:

* [Tworzenie bazy danych Azure Cosmos DB](#create-db)
* [Tworzenie bazy danych Azure Cosmos DB z udostępnioną przepływność](#create-db-ru)
* [Uzyskiwanie przepływności bazy danych Azure Cosmos DB](#get-db-ru)
* [Wyświetlanie listy wszystkich Azure Cosmos DB baz danych na koncie](#list-db)
* [Pobieranie pojedynczej bazy danych Azure Cosmos DB](#get-db)
* [Usuwanie bazy danych Azure Cosmos DB](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Tworzenie bazy danych Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
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

Set-AzCosmosDBSqlDatabase `
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

## <a name="azure-cosmos-db-container"></a>Kontener Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać kontenerem Azure Cosmos DB, w tym:

* [Tworzenie kontenera Azure Cosmos DB](#create-container)
* [Tworzenie kontenera Azure Cosmos DB z dużym kluczem partycji](#create-container-big-pk)
* [Uzyskiwanie przepływności kontenera Azure Cosmos DB](#get-container-ru)
* [Tworzenie kontenera Azure Cosmos DB z indeksem niestandardowym](#create-container-custom-index)
* [Tworzenie kontenera Azure Cosmos DB z wyłączonym indeksem](#create-container-no-index)
* [Tworzenie kontenera Azure Cosmos DB z unikatowym kluczem i czasem wygaśnięcia](#create-container-unique-key-ttl)
* [Tworzenie kontenera Azure Cosmos DB z rozwiązywaniem konfliktów](#create-container-lww)
* [Wyświetl listę wszystkich kontenerów Azure Cosmos DB w bazie danych](#list-containers)
* [Pobieranie jednego kontenera Azure Cosmos DB w bazie danych](#get-container)
* [Usuwanie kontenera Azure Cosmos DB](#delete-container)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Tworzenie kontenera Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Tworzenie kontenera Azure Cosmos DB o dużym rozmiarze klucza partycji

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
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

Set-AzCosmosDBSqlContainer `
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

Set-AzCosmosDBSqlContainer `
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

Set-AzCosmosDBSqlContainer `
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

Aby zapisać wszystkie konflikty w ConflictsFeed i obsłudze oddzielnie, `-Type "Custom" -Path ""`Przekaż.

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

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Aby utworzyć zasady rozwiązywania konfliktów w celu użycia procedury składowanej, `New-AzCosmosDBSqlConflictResolutionPolicy` Wywołaj i `-Type` Przekaż `-ConflictResolutionProcedure`parametry i.

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

Set-AzCosmosDBSqlContainer `
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

## <a name="next-steps"></a>Następne kroki

* [Wszystkie przykłady programu PowerShell](powershell-samples.md)
* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Tworzenie kontenera Azure Cosmos DB](how-to-create-container.md)
* [Skonfiguruj czas wygaśnięcia w Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
