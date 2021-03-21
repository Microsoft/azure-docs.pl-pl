---
title: Model zasobów dla funkcji przywracania do punktu w czasie Azure Cosmos DB.
description: W tym artykule przedstawiono model zasobów dla funkcji przywracania do punktu w czasie w usłudze Azure Cosmos DB. Omówiono w nim parametry, które obsługują ciągłą kopię zapasową, i zasoby, które można przywrócić za pomocą interfejsu API usługi Azure Cosmos DB dla kont SQL i MongoDB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: edf60a5c454d34a2424ef7981b02952ffbfd3bde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097231"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Model zasobów dla funkcji przywracania do momentu Azure Cosmos DB (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono model zasobów dla funkcji przywracania do punktu w czasie Azure Cosmos DB (wersja zapoznawcza). Omówiono w nim parametry, które obsługują ciągłą kopię zapasową, i zasoby, które można przywrócić za pomocą interfejsu API usługi Azure Cosmos DB dla kont SQL i MongoDB.

## <a name="database-accounts-resource-model"></a>Model zasobów konta bazy danych

Model zasobów konta bazy danych jest aktualizowany za pomocą kilku dodatkowych właściwości w celu obsługi nowych scenariuszy przywracania. Te właściwości to **BackupPolicy, createmode i RestoreParameters.**

### <a name="backuppolicy"></a>BackupPolicy

Nowa właściwość w zasadach tworzenia kopii zapasowych na poziomie konta o nazwie `Type` w obszarze `backuppolicy` parametr umożliwia ciągłą wykonywanie kopii zapasowych oraz funkcje przywracania do określonego momentu. Ten tryb jest nazywany **ciągłą kopią zapasową**. W publicznej wersji zapoznawczej ten tryb można ustawić tylko podczas tworzenia konta. Po jego włączeniu wszystkie kontenery i bazy danych utworzone w ramach tego konta będą mieć domyślnie włączone funkcje przywracania ciągłej kopii zapasowej oraz przywracanie do punktu w czasie.

> [!NOTE]
> Obecnie funkcja przywracania do punktu w czasie jest w publicznej wersji zapoznawczej i jest dostępna dla Azure Cosmos DB API for MongoDB i kont SQL. Po utworzeniu konta z trybem ciągłym nie można przełączyć go do trybu okresowego.

### <a name="createmode"></a>Createmode

Ta właściwość wskazuje, w jaki sposób konto zostało utworzone. Możliwe wartości to *default* i *Restore*. Aby wykonać przywracanie, należy ustawić tę wartość na *Przywróć* i podać odpowiednie wartości we `RestoreParameters` właściwości.

### <a name="restoreparameters"></a>RestoreParameters

`RestoreParameters`Zasób zawiera szczegóły operacji przywracania, w tym identyfikator konta, czas przywrócenia oraz zasoby, które należy przywrócić.

|Nazwa właściwości |Opis  |
|---------|---------|
|przechowajmode  | Tryb przywracania powinien być *PointInTime* |
|restoreSource   |  Identyfikator instanceId konta źródłowego, z którego zostanie zainicjowane przywracanie.       |
|restoreTimestampInUtc  | Punkt w czasie w formacie UTC, do którego ma zostać przywrócone konto. |
|databasesToRestore   | Lista `DatabaseRestoreSource` obiektów, aby określić, które bazy danych i kontenery mają zostać przywrócone. Jeśli ta wartość jest pusta, całe konto zostanie przywrócone.   |

**DatabaseRestoreResource** — każdy zasób reprezentuje pojedynczą bazę danych i wszystkie kolekcje w tej bazie danych.

|Nazwa właściwości |Opis  |
|---------|---------|
|Bazy | Nazwa bazy danych |
| CollectionName| Lista kontenerów w ramach tej bazy danych |

### <a name="sample-resource"></a>Zasób przykładowy

Poniższy kod JSON jest przykładowym zasobem konta bazy danych z włączoną funkcją ciągłej kopii zapasowej:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Zasoby dostępnych

Dostępny jest zestaw nowych zasobów i interfejsów API, które ułatwiają odnajdywanie krytycznych informacji o zasobach, które można przywrócić, lokalizacji, z których można je przywrócić, oraz sygnatur czasowych wykonywania operacji na tych zasobach.

> [!NOTE]
> Wszystkie interfejsy API używane do wyliczania tych zasobów wymagają następujących uprawnień:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Konto bazy danych dostępnych

Ten zasób zawiera wystąpienie konta bazy danych, które można przywrócić. Konto bazy danych może być kontem usuniętym lub na żywo. Zawiera informacje, które umożliwiają znalezienie konta źródłowej bazy danych, które ma zostać przywrócone.

|Nazwa właściwości |Opis  |
|---------|---------|
| ID (Identyfikator) | Unikatowy identyfikator zasobu. |
| accountName | Nazwa globalnego konta bazy danych. |
| creationTime | Godzina w formacie UTC utworzenia konta.  |
| deletionTime | Czas w formacie UTC, gdy konto zostało usunięte.  Ta wartość jest pusta, jeśli konto jest aktywne. |
| apiType | Typ interfejsu API konta Azure Cosmos DB. |
| restorableLocations | Lista lokalizacji, w których istniało konto. |
| restorableLocations: LocationName | Nazwa regionu konta regionalnego. |
| restorableLocations: regionalDatabaseAccountInstanceI | Identyfikator GUID konta regionalnego. |
| restorableLocations: creationTime | Godzina w formacie UTC utworzenia konta regionalnego.|
| restorableLocations: deletionTime | Czas (UTC), po usunięciu konta regionalnego. Ta wartość jest pusta, jeśli konto regionalne jest aktywne.|

Aby uzyskać listę wszystkich kont dostępnych, zobacz artykuł [dostępnych Database accounts-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorabledatabaseaccounts/list) lub [dostępnych accounts Database-list by location](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorabledatabaseaccounts/listbylocation) .

### <a name="restorable-sql-database"></a>Baza danych SQL dostępnych

Każdy zasób zawiera informacje o zdarzeniu mutacji, takie jak tworzenie i usuwanie, które wystąpiły w SQL Database. Te informacje mogą pomóc w scenariuszach, w których baza danych została przypadkowo usunięta, i jeśli trzeba się dowiedzieć, kiedy to zdarzenie wystąpi.

|Nazwa właściwości |Opis  |
|---------|---------|
| eventTimestamp | Godzina w formacie UTC utworzenia lub usunięcia bazy danych. |
| ownerId | Nazwa bazy danych SQL. |
| ownerResourceId | Identyfikator zasobu bazy danych SQL|
| operationType | Typ operacji dla tego zdarzenia bazy danych. Oto możliwe wartości:<br/><ul><li>Tworzenie: zdarzenie tworzenia bazy danych</li><li>Usuń: zdarzenie usunięcia bazy danych</li><li>Zastąp: zdarzenie modyfikacji bazy danych</li><li>SystemOperation: zdarzenie modyfikacji bazy danych wyzwolone przez system. To zdarzenie nie jest inicjowane przez użytkownika</li></ul> |
| database |Właściwości bazy danych SQL w czasie zdarzenia|

Aby uzyskać listę wszystkich mutacji bazy danych, zobacz artykuł [dostępnych SQL Databases-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqldatabases/list) .

### <a name="restorable-sql-container"></a>Dostępnych kontener SQL

Każdy zasób zawiera informacje o zdarzeniu mutacji, takie jak tworzenie i usuwanie, które wystąpiło w kontenerze SQL. Te informacje mogą pomóc w scenariuszach, w których kontener został zmodyfikowany lub usunięty, i jeśli trzeba się dowiedzieć, kiedy wystąpił ten zdarzenie.

|Nazwa właściwości |Opis  |
|---------|---------|
| eventTimestamp    | Godzina w formacie UTC wystąpienia tego zdarzenia kontenera.|
| ownerId| Nazwa kontenera SQL.|
| ownerResourceId   | Identyfikator zasobu kontenera SQL.|
| operationType | Typ operacji dla tego zdarzenia kontenera. Oto możliwe wartości: <br/><ul><li>Tworzenie: zdarzenie tworzenia kontenera</li><li>Usuń: zdarzenie usunięcia kontenera</li><li>Zastąp: zdarzenie modyfikacji kontenera</li><li>SystemOperation: zdarzenie modyfikacji kontenera wyzwolone przez system. To zdarzenie nie jest inicjowane przez użytkownika</li></ul> |
| kontener | Właściwości kontenera SQL w momencie zdarzenia.|

Aby uzyskać listę wszystkich mutacji kontenerów w ramach tej samej bazy danych, zobacz [dostępnych SQL Containers-list —](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqlcontainers/list) artykuł.

### <a name="restorable-sql-resources"></a>Dostępnych zasoby SQL

Każdy zasób reprezentuje pojedynczą bazę danych i wszystkie kontenery w tej bazie danych.

|Nazwa właściwości |Opis  |
|---------|---------|
| Bazy  | Nazwa bazy danych SQL.
| CollectionName   | Lista kontenerów SQL w tej bazie danych.|

Aby uzyskać listę danych SQL i pola kombi kontenera istniejących na koncie dla danego znacznika czasu i lokalizacji, zobacz artykuł [dostępnych SQL Resources-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqlresources/list) .

### <a name="restorable-mongodb-database"></a>Baza danych dostępnych MongoDB

Każdy zasób zawiera informacje o zdarzeniu mutacji, takie jak tworzenie i usuwanie, które wystąpiły w bazie danych MongoDB. Te informacje mogą pomóc w scenariuszu, w którym baza danych została przypadkowo usunięta, a użytkownik musi dowiedzieć się, kiedy wystąpiło to zdarzenie.

|Nazwa właściwości |Opis  |
|---------|---------|
|eventTimestamp| Godzina w formacie UTC wystąpienia tego zdarzenia bazy danych.|
| ownerId| Nazwa bazy danych MongoDB. |
| ownerResourceId   | Identyfikator zasobu bazy danych MongoDB. |
| operationType |   Typ operacji dla tego zdarzenia bazy danych. Oto możliwe wartości:<br/><ul><li> Tworzenie: zdarzenie tworzenia bazy danych</li><li> Usuń: zdarzenie usunięcia bazy danych</li><li> Zastąp: zdarzenie modyfikacji bazy danych</li><li> SystemOperation: zdarzenie modyfikacji bazy danych wyzwolone przez system. To zdarzenie nie jest inicjowane przez użytkownika </li></ul> |

Aby uzyskać listę wszystkich mutacji bazy danych, zobacz artykuł [dostępnych MongoDB Databases-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbdatabases/list) .

### <a name="restorable-mongodb-collection"></a>Dostępnych MongoDB, kolekcja

Każdy zasób zawiera informacje o zdarzeniu mutacji, takie jak tworzenie i usuwanie, które wystąpiły w kolekcji MongoDB. Te informacje mogą pomóc w scenariuszach, w których kolekcja została zmodyfikowana lub usunięta, a użytkownik musi dowiedzieć się, kiedy to zdarzenie wystąpi.

|Nazwa właściwości |Opis  |
|---------|---------|
| eventTimestamp |Godzina w formacie UTC wystąpienia tego zdarzenia zbierania danych. |
| ownerId| Nazwa kolekcji MongoDB. |
| ownerResourceId   | Identyfikator zasobu kolekcji MongoDB. |
| operationType |Typ operacji dla tego zdarzenia zbierania danych. Oto możliwe wartości:<br/><ul><li>Utwórz: zdarzenie tworzenia kolekcji</li><li>Usuń: zdarzenie usunięcia kolekcji</li><li>Zastąp: zdarzenie modyfikacji kolekcji</li><li>SystemOperation: zdarzenie modyfikacji kolekcji wyzwolone przez system. To zdarzenie nie jest inicjowane przez użytkownika</li></ul> |

Aby uzyskać listę wszystkich mutacji kontenerów w ramach tej samej bazy danych, zobacz artykuł [dostępnych MongoDB Collections-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbcollections/list) .

### <a name="restorable-mongodb-resources"></a>Zasoby dostępnych MongoDB

Każdy zasób reprezentuje pojedynczą bazę danych i wszystkie kolekcje w tej bazie danych.

|Nazwa właściwości |Opis  |
|---------|---------|
| Bazy  |Nazwa bazy danych MongoDB. |
| CollectionName | Lista kolekcji MongoDB w tej bazie danych. |

Aby uzyskać listę wszystkich MongoDB baz danych i kolekcji, które istnieją na koncie pod daną sygnaturą czasową i lokalizację, zobacz artykuł [dostępnych MongoDB Resources-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbresources/list) .

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.
