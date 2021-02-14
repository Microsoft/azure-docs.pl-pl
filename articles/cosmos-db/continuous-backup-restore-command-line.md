---
title: Użyj interfejsu wiersza polecenia platformy Azure, aby skonfigurować ciągłą kopię zapasową i przywracanie do punktu w czasie w Azure Cosmos DB.
description: Dowiedz się, jak udostępnić konto z ciągłymi kopiami zapasowymi i przywracać dane przy użyciu interfejsu wiersza polecenia platformy Azure.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377333"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Konfigurowanie i zarządzanie ciągłymi kopiami zapasowymi oraz przywracanie do punktu w czasie (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkcja przywracania do punktu w czasie Azure Cosmos DB (wersja zapoznawcza) ułatwia odzyskanie od przypadkowej zmiany w kontenerze, przywrócenie usuniętego konta, bazy danych lub kontenera lub przywrócenie do dowolnego regionu (w którym istnieją kopie zapasowe). Tryb ciągłej kopii zapasowej umożliwia przywracanie w dowolnym momencie w ciągu ostatnich 30 dni.

W tym artykule opisano sposób aprowizacji konta z ciągłymi kopiami zapasowymi i przywracaniem danych przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="install-azure-cli"></a><a id="install"></a>Instalowanie interfejsu wiersza polecenia platformy Azure

1. Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure

   * Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub nowszą niż 2.17.1.
   * Jeśli już zainstalowano interfejs wiersza polecenia, uruchom `az upgrade` polecenie w celu zaktualizowania do najnowszej wersji. To polecenie będzie działało tylko z wersją interfejsu wiersza polecenia wyższą niż 2,11. Jeśli masz wcześniejszą wersję, użyj powyższego linku, aby zainstalować najnowszą wersję.

1. Zainstaluj `cosmosdb-preview` rozszerzenie interfejsu wiersza polecenia.

   * Polecenia przywracania do punktu w czasie są dostępne w obszarze `cosmosdb-preview` rozszerzenie.
   * To rozszerzenie można zainstalować, uruchamiając następujące polecenie: `az extension add --name cosmosdb-preview`
   * Aby odinstalować to rozszerzenie, należy uruchomić następujące polecenie: `az extension remove --name cosmosdb-preview`

1. Zaloguj się i wybierz swoją subskrypcję

   * Zaloguj się do konta platformy Azure przy użyciu `az login` polecenia.
   * Wybierz wymaganą subskrypcję za pomocą `az account set -s <subscriptionguid>` polecenia.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Inicjowanie obsługi administracyjnej konta interfejsu API SQL przy użyciu ciągłej kopii zapasowej

Aby zapewnić obsługę administracyjną konta interfejsu API SQL przy użyciu ciągłej kopii zapasowej, `--backup-policy-type Continuous` należy przekazać dodatkowy argument wraz z zwykłym poleceniem aprowizacji. Poniższe polecenie jest przykładem konta zapisu w jednym regionie o nazwie `pitracct2` z zasadami ciągłego tworzenia kopii zapasowych utworzonych w regionie *zachodnie stany USA* w obszarze *mojagz* grupy zasobów:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Udostępnianie interfejsu API Azure Cosmos DB dla konta MongoDB z ciągłą kopią zapasową

Następujące polecenie pokazuje przykład jednego regionu konta zapisu o nazwie `pitracct3` z zasadami ciągłego tworzenia kopii zapasowych utworzony region *zachodnie stany USA* w obszarze *mojagz* grupy zasobów:

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Wyzwalanie operacji przywracania przy użyciu interfejsu wiersza polecenia

Najprostszym sposobem wyzwalania przywracania jest wygenerowanie polecenia Restore z nazwą konta docelowego, kontem źródłowym, lokalizacją, grupą zasobów, sygnaturą czasową (w formacie UTC) i opcjonalnie nazwą bazy danych i kontenera. Poniżej przedstawiono kilka przykładów wyzwalania operacji przywracania:

1. Utwórz nowe konto Azure Cosmos DB, przywracając je z istniejącego konta.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Utwórz nowe konto Azure Cosmos DB przez przywrócenie tylko wybranych baz danych i kontenerów z istniejącego konta bazy danych.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Wyliczanie zasobów dostępnych dla interfejsu API SQL

Opisane poniżej polecenia wyliczania ułatwiają odnajdywanie zasobów dostępnych do przywrócenia w różnych sygnaturach czasowych. Ponadto udostępniają one również Źródło najważniejszych zdarzeń na koncie dostępnych, bazie danych i zasobach kontenerów.

**Wyświetl listę wszystkich kont, które mogą zostać przywrócone w bieżącej subskrypcji**

Uruchom następujące polecenie interfejsu wiersza polecenia, aby wyświetlić listę wszystkich kont, które mogą zostać przywrócone w bieżącej subskrypcji

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

Odpowiedź dotyczy wszystkich kont bazy danych (zarówno aktywnych, jak i usuniętych), które mogą zostać przywrócone, oraz regionów, z których mogą zostać przywrócone:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Podobnie jak w `CreationTime` `DeletionTime` przypadku konta, istnieje `CreationTime` również `DeletionTime` region lub. Te czasy umożliwiają wybranie odpowiedniego regionu i prawidłowego zakresu czasu do przywrócenia w danym regionie.

**Wyświetl listę wszystkich wersji bazy danych na aktywnym koncie bazy danych**

Lista wszystkich wersji baz danych umożliwia wybranie odpowiedniej bazy danych w scenariuszu, w którym rzeczywisty czas istnienia bazy danych jest nieznany.

Uruchom następujące polecenie interfejsu wiersza polecenia, aby wyświetlić listę wszystkich wersji baz danych. To polecenie działa tylko z kontami na żywo. `instanceId` `location` Parametry i są uzyskiwane z `name` `location` właściwości i w odpowiedzi `az cosmosdb restorable-database-account list` polecenia. Atrybut instanceId jest również właściwością konta źródłowej bazy danych, które jest przywracane:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

To dane wyjściowe polecenia są teraz wyświetlane, gdy baza danych została utworzona i usunięta.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Wyświetl listę wszystkich wersji kontenerów SQL bazy danych na aktywnym koncie bazy danych**

Użyj poniższego polecenia, aby wyświetlić listę wszystkich wersji kontenerów SQL. To polecenie działa tylko z kontami na żywo. `databaseRid`Parametr jest `ResourceId` bazą danych, którą chcesz przywrócić. Jest to wartość `ownerResourceid` atrybutu znalezionego w odpowiedzi `az cosmosdb sql restorable-database list` polecenia.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Dane wyjściowe polecenia zawierają listę operacji wykonanych na wszystkich kontenerach w tej bazie danych:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Znajdowanie baz danych lub kontenerów, które można przywrócić w dowolnym znaczniku czasu**

Użyj poniższego polecenia, aby pobrać listę baz danych lub kontenerów, które można przywrócić w dowolnym znaczniku czasu. To polecenie działa tylko z kontami na żywo.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Wyliczanie zasobów dostępnych dla konta interfejsu API MongoDB

Opisane poniżej polecenia wyliczania ułatwiają odnajdywanie zasobów dostępnych do przywrócenia w różnych sygnaturach czasowych. Ponadto udostępniają one również Źródło najważniejszych zdarzeń na koncie dostępnych, bazie danych i zasobach kontenerów. Podobnie jak w przypadku interfejsu API SQL, można użyć `az cosmosdb` polecenia, ale z `mongodb` parametrem as zamiast `sql` . Te polecenia działają tylko w przypadku kont na żywo.

**Wyświetlanie listy wszystkich wersji baz danych MongoDB na żywo konta bazy danych**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Wyświetl listę wszystkich wersji kolekcji MongoDB bazy danych na aktywnym koncie bazy danych**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Wyświetl listę wszystkich zasobów konta bazy danych MongoDB, które są dostępne do przywrócenia w danym znaczniku czasu i w danym regionie**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Model zasobów trybu ciągłej kopii zapasowej](continuous-backup-restore-resource-model.md)
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.
