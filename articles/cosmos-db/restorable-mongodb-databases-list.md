---
title: Wyświetlanie listy baz danych dostępnych w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu interfejsu API REST
description: Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich Azure Cosmos DBych bazach danych MongoDB na koncie dostępnych. Pomaga to w scenariuszu, w którym przypadkowo usunięto bazę danych w celu uzyskania czasu usuwania.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 64288f67728e59c32c662a6640d60daf52c53fe1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527926"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Wyświetlanie listy baz danych dostępnych w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu interfejsu API REST

Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich Azure Cosmos DBych bazach danych MongoDB na koncie dostępnych. Pomaga to w scenariuszu, w którym przypadkowo usunięto bazę danych w celu uzyskania czasu usuwania. Ten interfejs API wymaga `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` uprawnień

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| Operacja została ukończona pomyślnie. |
| Inne kody stanu | [DefaultErrorResponse](#defaulterrorresponse)| Odpowiedź na błąd z opisem przyczyny niepowodzenia operacji.|

## <a name="examples"></a>Przykłady

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Przykładowe żądanie**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Przykładowa odpowiedź**

Kod stanu: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definicje

| Definicja | Opis | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Odpowiedź na błąd z usługi. | | [Zwrócono](#errorresponse) | Odpowiedź na błąd. | | [OperationType](#operationtype) | Wyliczenie, aby wskazać typ operacji zdarzenia. | | [Zasób](#resource) | Zasób Azure Cosmos DB API dla zdarzenia bazy danych MongoDB | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Interfejs API Azure Cosmos DB dla zdarzenia bazy danych MongoDB | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | Właściwości Azure Cosmos DB API dla zdarzenia bazy danych MongoDB | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | Odpowiedź na listę operacji, która zawiera Azure Cosmos DB interfejs API dla zdarzeń bazy danych MongoDB i ich właściwości. |

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

### <a name="resource"></a><a id="resource"></a>Zasób

Zasób Azure Cosmos DB API dla zdarzenia bazy danych MongoDB

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| _rid |ciąg| Właściwość wygenerowana przez system. Unikatowy identyfikator. |
| eventTimestamp |ciąg| Godzina wystąpienia tego zdarzenia bazy danych. |
| operationType |[OperationType](#operationtype)| Typ operacji dla tego zdarzenia bazy danych.  |
| ownerId |ciąg| Nazwa Azure Cosmos DB API dla bazy danych MongoDB.|
| ownerResourceId |ciąg| Identyfikator zasobu Azure Cosmos DB API dla bazy danych MongoDB. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Azure Cosmos DB interfejs API dla zdarzenia bazy danych MongoDB

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| ID (Identyfikator) |ciąg| Unikatowy identyfikator zasobu Azure Resource Manager. |
| name |ciąg| Nazwa zasobu Menedżer zasobów. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| Właściwości Azure Cosmos DB API dla zdarzenia bazy danych MongoDB. |
| typ |ciąg| Typ zasobu platformy Azure. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

Właściwości Azure Cosmos DB API dla zdarzenia bazy danych MongoDB

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| zasób |[Zasób](#resource)| Zasób Azure Cosmos DB API dla zdarzenia bazy danych MongoDB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

Odpowiedź na listę operacji, która zawiera zdarzenia bazy danych i ich właściwości.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| wartość |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Lista zdarzeń bazy danych i ich właściwości. |

## <a name="next-steps"></a>Następne kroki

* [Utwórz listę zasobów dostępnych](restorable-mongodb-resources-list.md)  w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu interfejsu API REST.
* [Utwórz listę dostępnych kolekcji](restorable-mongodb-collections-list.md)  w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu interfejsu API REST.
* [Model zasobów](continuous-backup-restore-resource-model.md) trybu ciągłej kopii zapasowej.