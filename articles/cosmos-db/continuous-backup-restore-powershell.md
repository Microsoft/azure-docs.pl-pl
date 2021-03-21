---
title: Użyj Azure PowerShell, aby skonfigurować ciągłą kopię zapasową i przywracanie do punktu w czasie w programie Azure Cosmos DB.
description: Dowiedz się, jak udostępnić konto z ciągłą kopią zapasową i przywrócić dane przy użyciu Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381838"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>Konfigurowanie i zarządzanie ciągłymi kopiami zapasowymi oraz przywracanie do punktu w czasie (wersja zapoznawcza) — Używanie Azure PowerShell
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkcja przywracania do punktu w czasie Azure Cosmos DB (wersja zapoznawcza) ułatwia odzyskanie od przypadkowej zmiany w kontenerze, przywrócenie usuniętego konta, bazy danych lub kontenera lub przywrócenie do dowolnego regionu (w którym istnieją kopie zapasowe). Tryb ciągłej kopii zapasowej umożliwia przywracanie w dowolnym momencie w ciągu ostatnich 30 dni.

W tym artykule opisano sposób aprowizacji konta z ciągłymi kopiami zapasowymi i przywracania danych przy użyciu Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Instalowanie programu Azure PowerShell

1. Uruchom następujące polecenie z Azure PowerShell, aby zainstalować `Az.CosmosDB` moduł wersji zapoznawczej, który zawiera polecenia związane z przywracaniem do punktu w czasie:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Po zainstalowaniu modułów Zaloguj się do platformy Azure przy użyciu polecenia

   ```azurepowershell
   Connect-AzAccount
   ```

1. Wybierz określoną subskrypcję za pomocą następującego polecenia:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Inicjowanie obsługi administracyjnej konta interfejsu API SQL przy użyciu ciągłej kopii zapasowej

Aby udostępnić konto przy użyciu ciągłej kopii zapasowej, Dodaj argument `-BackupPolicyType Continuous` wraz z poleceniem regularnego inicjowania obsługi.

Poniższe polecenie cmdlet to przykład konta zapisu z jednym regionem `pitracct2` z zasadami ciągłego tworzenia kopii zapasowych utworzonych w regionie *zachodnie stany USA* w obszarze *mojagz* grupy zasobów:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Inicjowanie obsługi administracyjnej konta interfejsu API MongoDB przy użyciu ciągłej kopii zapasowej

Następujące polecenie cmdlet jest przykładem ciągłego tworzenia kopii zapasowych *pitracct2* utworzonych w regionie *zachodnie stany USA* w obszarze Grupa zasobów *mojagz* :

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Wyzwalanie operacji przywracania

Poniższe polecenie cmdlet służy do wyzwalania operacji przywracania za pomocą polecenia Restore przy użyciu konta docelowego, konta źródłowego, lokalizacji, grupy zasobów i sygnatury czasowej:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Przykład 1:** Przywracanie całego konta:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Przykład 2:** Przywracanie określonych kolekcji i baz danych. W tym przykładzie przywraca kolekcje myCol2 z myDB1 i całej bazy danych myDB2, która zawiera wszystkie kontenery znajdujące się w nim.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Wyliczanie zasobów dostępnych dla interfejsu API SQL

Polecenia cmdlet do wyliczania ułatwiają odnajdywanie zasobów dostępnych do przywrócenia w różnych sygnaturach czasowych. Ponadto udostępniają one również Źródło najważniejszych zdarzeń na koncie dostępnych, bazie danych i zasobach kontenerów.

**Wyświetl listę wszystkich kont, które mogą zostać przywrócone w bieżącej subskrypcji**

Uruchom `Get-AzCosmosDBRestorableDatabaseAccount` polecenie programu PowerShell, aby wyświetlić listę wszystkich kont, które mogą zostać przywrócone w bieżącej subskrypcji.

Odpowiedź dotyczy wszystkich kont bazy danych (zarówno aktywnych, jak i usuniętych), które mogą zostać przywrócone, oraz regionów, z których mogą zostać przywrócone.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Podobnie jak w `CreationTime` `DeletionTime` przypadku konta, istnieje `CreationTime` również `DeletionTime` region lub. Te czasy umożliwiają wybranie odpowiedniego regionu i prawidłowego zakresu czasu do przywrócenia w danym regionie.

**Wyświetlanie listy wszystkich wersji baz danych SQL na żywo konta bazy danych**

Lista wszystkich wersji baz danych umożliwia wybranie odpowiedniej bazy danych w scenariuszu, w którym rzeczywisty czas istnienia bazy danych jest nieznany.

Uruchom następujące polecenie programu PowerShell, aby wyświetlić listę wszystkich wersji baz danych. To polecenie działa tylko z kontami na żywo. `DatabaseAccountInstanceId` `LocationName` Parametry i są uzyskiwane z `name` `location` właściwości i w odpowiedzi `Get-AzCosmosDBRestorableDatabaseAccount` polecenia cmdlet. Ten `DatabaseAccountInstanceId` atrybut odnosi się do `instanceId` Właściwości przywracanego konta źródłowej bazy danych:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Wyświetl listę wszystkich wersji kontenerów SQL bazy danych na aktywnym koncie bazy danych.**

Użyj poniższego polecenia, aby wyświetlić listę wszystkich wersji kontenerów SQL. To polecenie działa tylko z kontami na żywo. `DatabaseRid`Parametr jest `ResourceId` bazą danych, którą chcesz przywrócić. Jest to wartość `ownerResourceid` atrybutu znalezionego w odpowiedzi `Get-AzCosmosdbSqlRestorableDatabase` polecenia cmdlet. Odpowiedź zawiera również listę operacji wykonanych na wszystkich kontenerach w tej bazie danych.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Znajdowanie baz danych lub kontenerów, które można przywrócić w dowolnym znaczniku czasu**

Użyj poniższego polecenia, aby pobrać listę baz danych lub kontenerów, które można przywrócić w dowolnym znaczniku czasu. To polecenie działa tylko z kontami na żywo.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Wyliczanie zasobów dostępnych dla MongoDB

Opisane poniżej polecenia wyliczania ułatwiają odnajdywanie zasobów dostępnych do przywrócenia w różnych sygnaturach czasowych. Ponadto udostępniają one również Źródło najważniejszych zdarzeń na koncie dostępnych, bazie danych i zasobach kontenerów. Te polecenia działają tylko dla kont na żywo i są podobne do poleceń interfejsu API języka SQL, ale przy użyciu `MongoDB` nazwy polecenia zamiast `sql` .

**Wyświetlanie listy wszystkich wersji baz danych MongoDB na żywo konta bazy danych**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Wyświetl listę wszystkich wersji kolekcji MongoDB bazy danych na aktywnym koncie bazy danych**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Wyświetl listę wszystkich zasobów konta bazy danych MongoDB, które są dostępne do przywrócenia w danym znaczniku czasu i w danym regionie**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [interfejsu wiersza polecenia platformy Azure](continuous-backup-restore-command-line.md), [Menedżer zasobów](continuous-backup-restore-template.md)lub [Azure Portal](continuous-backup-restore-portal.md).
* [Model zasobów trybu ciągłej kopii zapasowej](continuous-backup-restore-resource-model.md)
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.
