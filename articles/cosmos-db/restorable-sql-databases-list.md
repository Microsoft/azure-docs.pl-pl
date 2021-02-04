---
title: Wyświetlanie listy baz danych interfejsu API SQL dostępnych w Azure Cosmos DB przy użyciu interfejsu API REST
description: Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich Azure Cosmos DB baz danych SQL w ramach konta dostępnych. Pomaga to w scenariuszu, w którym przypadkowo usunięto bazę danych w celu uzyskania czasu usuwania.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539505"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Wyświetlanie listy baz danych interfejsu API SQL dostępnych w Azure Cosmos DB przy użyciu interfejsu API REST

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich Azure Cosmos DB baz danych SQL w ramach konta dostępnych. Pomaga to w scenariuszu, w którym przypadkowo usunięto bazę danych w celu uzyskania czasu usuwania. Ten interfejs API wymaga `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` uprawnień

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | W | Wymagany | Typ | Opis |
| --- | --- | --- | --- | --- |
| **Identyfikator wystąpienia** | path | True |ciąg| Identyfikator GUID obiektu instanceId konta bazy danych dostępnych. |
| **przeniesienie** | path | True | ciąg| Azure Cosmos DB region, z odstępami między wyrazami i każdym słowem. |
| **subscriptionId** | path | True | ciąg| Identyfikator subskrypcji docelowej. |
| **wersja interfejsu API** | query | True | ciąg | Wersja interfejsu API do użycia dla tej operacji. |

## <a name="responses"></a>Odpowiedzi

| Nazwa | Typ | Opis |
| --- | --- | --- |
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| Operacja została ukończona pomyślnie. |
| Inne kody stanu | [DefaultErrorResponse](#defaulterrorresponse)| Odpowiedź na błąd z opisem przyczyny niepowodzenia operacji. |

## <a name="examples"></a>Przykłady

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Przykładowe żądanie**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Przykładowa odpowiedź**

Kod stanu: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definicje

| Definicja | Opis | | --- || --- | | [Baza danych](#database) | Azure Cosmos DB obiektu zasobów bazy danych SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Odpowiedź na błąd z usługi. | | [Zwrócono](#errorresponse) | Odpowiedź na błąd. | | [OperationType](#operationtype) | Wyliczenie, aby wskazać typ operacji zdarzenia. | | [Zasób](#resource) | Zasób Azure Cosmos DB zdarzenia bazy danych SQL | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Zdarzenie Azure Cosmos DB bazy danych SQL | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Właściwości zdarzenia Azure Cosmos DB SQL Database | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | Odpowiedź na listę operacji, która zawiera zdarzenia bazy danych SQL i ich właściwości. |

### <a name="database"></a><a id="database"></a>Database

Azure Cosmos DB obiektu zasobów bazy danych SQL

| **Nazwa**  |  **Typ**  |  **Opis** | | --- || --- | ---| | _colls | ciąg | Wygenerowana przez system właściwość, która określa ścieżkę adresowaną do zasobu kolekcji. | | _etag | ciąg | Wygenerowana przez system właściwość reprezentująca element ETag zasobu wymagany do optymistycznej kontroli współbieżności. | | _rid | ciąg | Właściwość wygenerowana przez system. Unikatowy identyfikator. | | _self | ciąg | Wygenerowana przez system właściwość, która określa ścieżkę adresowaną do zasobu bazy danych. | | _ts | | Wygenerowana przez system właściwość, która oznacza ostatnią zaktualizowaną sygnaturę czasową zasobu. | | _users | ciąg | Wygenerowana przez system właściwość, która określa ścieżkę adresowaną do zasobu użytkownika. | | ID | ciąg | Nazwa bazy danych SQL Azure Cosmos DB |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Odpowiedź na błąd z usługi.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| error | [Zwrócono](#errorresponse)| Odpowiedź na błąd. |

### <a name="errorresponse"></a><a id="errorresponse"></a>Zwrócono

Odpowiedź na błąd.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| kod |ciąg| Kod błędu. |
| message |ciąg| Komunikat o błędzie informujący o tym, dlaczego operacja nie powiodła się. |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Wyliczenie, aby wskazać typ operacji zdarzenia.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| Utwórz |ciąg|zdarzenie tworzenia bazy danych|
| Usuń |ciąg|zdarzenie usunięcia bazy danych|
| Zamień |ciąg|zdarzenie modyfikacji bazy danych|
| SystemOperation |ciąg|zdarzenie modyfikacji bazy danych wyzwolone przez system. To zdarzenie nie jest inicjowane przez użytkownika|

### <a name="resource"></a><a id="resource"></a>Zasób

Zasób zdarzenia Azure Cosmos DB SQL Database

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| _rid |ciąg| Właściwość wygenerowana przez system. Unikatowy identyfikator. |
| database |[Database](#database) (Baza danych)| Azure Cosmos DB obiektu zasobów bazy danych SQL |
| eventTimestamp |ciąg| Godzina wystąpienia tego zdarzenia bazy danych. |
| operationType |[OperationType](#operationtype)| Typ operacji dla tego zdarzenia bazy danych. |
| ownerId |ciąg| Nazwa bazy danych SQL. |
| ownerResourceId |ciąg| Identyfikator zasobu bazy danych SQL. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Zdarzenie Azure Cosmos DB bazy danych SQL

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| ID (Identyfikator) |ciąg| Unikatowy identyfikator zasobu Azure Resource Manager. |
| name |ciąg| Nazwa zasobu Azure Resource Manager. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| Właściwości zdarzenia bazy danych SQL. |
| typ |ciąg| Typ zasobu platformy Azure. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Właściwości zdarzenia Azure Cosmos DB SQL Database

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| zasób |[Zasób](#resource)| Zasób zdarzenia Azure Cosmos DB SQL Database |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

Odpowiedź na listę operacji, która zawiera zdarzenia bazy danych SQL i ich właściwości.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| wartość |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Lista zdarzeń bazy danych SQL i ich właściwości. |

## <a name="next-steps"></a>Następne kroki

* [Utwórz listę kontenerów dostępnych](restorable-sql-containers-list.md) w Azure Cosmos DB interfejsie API SQL przy użyciu interfejsu API REST.
* [Model zasobów](continuous-backup-restore-resource-model.md) trybu ciągłej kopii zapasowej.