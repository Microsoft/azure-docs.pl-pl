---
title: Zarządzanie zasobami interfejsu API Azure Cosmos DB Core (SQL) przy użyciu interfejsu wiersza polecenia platformy Azure
description: Zarządzaj zasobami interfejsu API Azure Cosmos DB Core (SQL) za pomocą interfejsu wiersza polecenia platformy Azure.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: b13f5bfffced9afd80663d606e30e028e52643ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563842"
---
# <a name="manage-azure-cosmos-core-sql-api-resources-using-azure-cli"></a>Zarządzanie zasobami interfejsu API platformy Azure Cosmos Core (SQL) przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W poniższym przewodniku opisano typowe polecenia służące do automatyzowania zarządzania kontami, bazami danych i kontenerami usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure. W [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb)są dostępne strony referencyjne dla wszystkich poleceń CLI Azure Cosmos DB. Więcej przykładów można znaleźć w przykładach [interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md), w tym tworzenia i zarządzania kontami Cosmos DB, bazami danych i kontenerami dla MongoDB, Gremlin, Cassandra i interfejs API tabel.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.12.1 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

Przykłady interfejsu wiersza polecenia platformy Azure dla innych interfejsów API, zobacz przykłady interfejsu wiersza polecenia [dla Cassandra](cli-samples-cassandra.md), [przykłady interfejsu wiersza polecenia dla MongoDB API](cli-samples-mongodb.md), przykłady interfejsu wiersza polecenia [dla Gremlin](cli-samples-gremlin.md), [przykłady interfejsu wiersza polecenia dla tabeli](cli-samples-table.md)

> [!IMPORTANT]
> Nie można zmienić nazw zasobów Azure Cosmos DB, ponieważ to narusza sposób, w jaki Azure Resource Manager współpracuje z identyfikatorami URI zasobów.

## <a name="azure-cosmos-accounts"></a>Konta usługi Azure Cosmos

W poniższych sekcjach pokazano, jak zarządzać kontem usługi Azure Cosmos, w tym:

* [tworzenie konta usługi Azure Cosmos](#create-an-azure-cosmos-db-account)
* [Add or remove regions](#add-or-remove-regions) (Dodawanie lub usuwanie regionów)
* [Włącz zapisywanie w ramach wieloregionu](#enable-multiple-write-regions)
* [Ustawianie priorytetu trybu failover dla regionu](#set-failover-priority)
* [Włącz automatyczną pracę w trybie failover](#enable-automatic-failover)
* [Wyzwalanie ręcznego przełączania do trybu failover](#trigger-manual-failover)
* [Wyświetl listę kluczy konta](#list-account-keys)
* [Wyświetlanie listy kluczy konta tylko do odczytu](#list-read-only-account-keys)
* [Wyświetlanie listy parametrów połączenia](#list-connection-strings)
* [Ponownie Wygeneruj klucz konta](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Utwórz konto Azure Cosmos DB przy użyciu interfejsu API SQL, spójność sesji w regionach zachodnie stany USA 2 i Wschodnie stany USA 2:

> [!IMPORTANT]
> Nazwa konta usługi Azure Cosmos musi zawierać małe litery i mniej niż 44 znaków.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>Add or remove regions (Dodawanie lub usuwanie regionów)

Utwórz konto usługi Azure Cosmos z dwoma regionami, Dodaj region i Usuń region.

> [!NOTE]
> Nie można jednocześnie dodawać i usuwać regionów `locations` oraz zmieniać innych właściwości dla konta usługi Azure Cosmos. Modyfikowanie regionów musi być wykonywane jako oddzielna operacja niż jakakolwiek inna zmiana w zasobie konta.
> [!NOTE]
> To polecenie pozwala dodawać i usuwać regiony, ale nie pozwala na modyfikowanie priorytetów trybu failover ani wyzwalanie ręcznej pracy awaryjnej. Zobacz [Ustawianie priorytetu trybu failover](#set-failover-priority) i [wyzwalanie ręcznego przełączania do trybu failover](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>Włącz wiele regionów zapisu

Włączanie zapisu wieloregionowego dla konta Cosmos

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>Ustawianie priorytetu trybu failover

Ustawianie priorytetu trybu failover dla konta usługi Azure Cosmos skonfigurowanego do automatycznego przełączania do trybu failover

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a>Włącz automatyczną pracę w trybie failover

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>Wyzwalanie ręcznego przełączania do trybu failover

> [!CAUTION]
> Zmiana regionu o priorytecie = 0 spowoduje wyzwolenie ręcznego przejścia w tryb failover dla konta usługi Azure Cosmos. Jakakolwiek inna zmiana priorytetu nie spowoduje wyzwolenia trybu failover.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> Wyświetl listę wszystkich kluczy konta

Pobierz wszystkie klucze dla konta Cosmos.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>Wyświetlanie listy kluczy konta tylko do odczytu

Pobierz klucze tylko do odczytu dla konta Cosmos.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>Wyświetlanie listy parametrów połączenia

Pobierz parametry połączenia dla konta Cosmos.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>Ponownie Wygeneruj klucz konta

Wygeneruj ponownie nowy klucz dla konta Cosmos.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Baza danych Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać bazą danych Azure Cosmos DB, w tym:

* [Tworzenie bazy danych](#create-a-database)
* [Tworzenie bazy danych z udostępnioną przepływność](#create-a-database-with-shared-throughput)
* [Migrowanie bazy danych do automatycznego skalowania przepływności](#migrate-a-database-to-autoscale-throughput)
* [Zmień przepływność bazy danych](#change-database-throughput)
* [Uniemożliwiaj usunięcie bazy danych](#prevent-a-database-from-being-deleted)

### <a name="create-a-database"></a>Tworzenie bazy danych

Utwórz bazę danych Cosmos.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>Tworzenie bazy danych z udostępnioną przepływność

Utwórz bazę danych Cosmos z udostępnioną przepływność.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="migrate-a-database-to-autoscale-throughput"></a>Migrowanie bazy danych do automatycznego skalowania przepływności

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

# Migrate to autoscale throughput
az cosmosdb sql database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="change-database-throughput"></a>Zmień przepływność bazy danych

Zwiększ przepływność bazy danych Cosmos o 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="prevent-a-database-from-being-deleted"></a>Uniemożliwiaj usunięcie bazy danych

Umieść blokadę usuwania zasobów platformy Azure w bazie danych, aby zapobiec jej usunięciu. Ta funkcja wymaga zablokowania konta Cosmos z poziomu zestawów SDK płaszczyzny danych. Aby dowiedzieć się więcej, zobacz temat [zapobieganie zmianom z zestawów SDK](role-based-access-control.md#prevent-sdk-changes). Blokady zasobów platformy Azure mogą również uniemożliwić zmianę zasobu przez określenie `ReadOnly` typu blokady. W przypadku bazy danych Cosmos można jej użyć, aby uniemożliwić zmianę przepływności.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Kontener Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać kontenerem Azure Cosmos DB, w tym:

* [Tworzenie kontenera](#create-a-container)
* [Tworzenie kontenera z funkcją automatycznego skalowania](#create-a-container-with-autoscale)
* [Tworzenie kontenera z włączonym czasem TTL](#create-a-container-with-ttl)
* [Tworzenie kontenera przy użyciu niestandardowych zasad indeksu](#create-a-container-with-a-custom-index-policy)
* [Zmień przepływność kontenera](#change-container-throughput)
* [Migrowanie kontenera do automatycznego skalowania przepływności](#migrate-a-container-to-autoscale-throughput)
* [Zapobiegaj usunięciu kontenera](#prevent-a-container-from-being-deleted)

### <a name="create-a-container"></a>Tworzenie kontenera

Utwórz kontener Cosmos z domyślnymi zasadami indeksu, kluczem partycji i RU/s z 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>Tworzenie kontenera z funkcją automatycznego skalowania

Utwórz kontener Cosmos z domyślnymi zasadami indeksu, kluczem partycji i automatycznym skalowaniem RU/s z 4000.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>Tworzenie kontenera z czasem wygaśnięcia

Utwórz kontener Cosmos z włączonym czasem TTL.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>Tworzenie kontenera przy użyciu niestandardowych zasad indeksu

Utwórz kontener Cosmos z niestandardowymi zasadami indeksu, indeksem przestrzennym, indeksem złożonym, kluczem partycji i RU/s z 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>Zmień przepływność kontenera

Zwiększ przepływność kontenera Cosmos o 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="migrate-a-container-to-autoscale-throughput"></a>Migrowanie kontenera do automatycznego skalowania przepływności

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

# Migrate to autoscale throughput
az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="prevent-a-container-from-being-deleted"></a>Zapobiegaj usunięciu kontenera

Umieść blokadę usuwania zasobów platformy Azure w kontenerze, aby zapobiec jej usunięciu. Ta funkcja wymaga zablokowania konta Cosmos z poziomu zestawów SDK płaszczyzny danych. Aby dowiedzieć się więcej, zobacz temat [zapobieganie zmianom z zestawów SDK](role-based-access-control.md#prevent-sdk-changes). Blokady zasobów platformy Azure mogą również uniemożliwić zmianę zasobu przez określenie `ReadOnly` typu blokady. W przypadku kontenera Cosmos można go użyć, aby uniemożliwić zmianę przepływności lub inną właściwość.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* [Dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb)
* [Dodatkowe przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md)