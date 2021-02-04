---
title: Wyświetlanie listy kolekcji dostępnych w interfejsie API usługi Azure Cosmos DB MongoDB — interfejs API REST
description: Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich Azure Cosmos DBych kolekcjach MongoDB w ramach określonej bazy danych. Pomaga to w scenariuszu, w którym kontener został przypadkowo usunięty.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537547"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Utwórz listę dostępnych kolekcji w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu interfejsu API REST

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich przeAzure Cosmos DBch interfejsu API dla kolekcji MongoDB w ramach określonej bazy danych. Pomaga to w scenariuszu, w którym kontener został przypadkowo usunięty. Ten interfejs API wymaga `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` uprawnień

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Z opcjonalnymi parametrami:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | W | Wymagany | Typ | Opis |
| --- | --- | --- | --- | --- |
| **Identyfikator wystąpienia** | path | True |ciąg| Identyfikator GUID obiektu instanceId konta bazy danych dostępnych. |
| **przeniesienie** | path | True | ciąg| Azure Cosmos DB region, z odstępami między wyrazami i każdym słowem. |
| **subscriptionId** | path | True | ciąg| Identyfikator subskrypcji docelowej. |
| **wersja interfejsu API** | query | True | ciąg | Wersja interfejsu API do użycia dla tej operacji. |
| **restorableMongodbDatabaseRid** | query | |ciąg| Identyfikator zasobu Azure Cosmos DB API dla bazy danych MongoDB. |

## <a name="responses"></a>Odpowiedzi

| Nazwa | Typ | Opis |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| Operacja została ukończona pomyślnie. |
| Inne kody stanu | [DefaultErrorResponse](#defaulterrorresponse)| Odpowiedź na błąd z opisem przyczyny niepowodzenia operacji.|

## <a name="examples"></a>Przykłady

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Przykładowe żądanie**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Przykładowa odpowiedź**

Kod stanu: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definicje

|Definicja  | Opis|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Odpowiedź na błąd z usługi. |
| [Zwrócono](#errorresponse) | Odpowiedź na błąd. |
| [OperationType](#operationtype) | Wyliczenie, aby wskazać typ operacji zdarzenia. |
| [Zasób](#resource) | Zasób Azure Cosmos DB interfejsu API dla zdarzenia zbierania MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Azure Cosmos DB interfejs API dla zdarzenia zbierania MongoDB |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | Właściwości Azure Cosmos DB interfejsu API dla zdarzenia zbierania MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | Odpowiedź na listę operacji, która zawiera zdarzenia kolekcji i ich właściwości. |

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
| Utwórz |ciąg|zdarzenie tworzenia kolekcji|
| Usuń |ciąg|zdarzenie usuwania kolekcji|
| Zamień |ciąg|zdarzenie modyfikacji kolekcji|

### <a name="resource"></a><a id="resource"></a>Zasób

Zasób Azure Cosmos DB zdarzenia zbierania MongoDB

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| _rid |ciąg| Właściwość wygenerowana przez system. Unikatowy identyfikator. |
| eventTimestamp |ciąg| Godzina, o której nastąpiło zdarzenie zbierania danych. |
| operationType |[OperationType](#operationtype)|  Typ operacji dla tego zdarzenia zbierania danych. |
| ownerId |ciąg| Nazwa kolekcji MongoDB.|
| ownerResourceId |ciąg| Identyfikator zasobu kolekcji MongoDB. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Azure Cosmos DB zdarzenie zbierania MongoDB

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| ID (Identyfikator) |ciąg| Unikatowy identyfikator zasobu Azure Resource Manager. |
| name |ciąg| Nazwa zasobu Menedżer zasobów. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| Właściwości zdarzenia kolekcji. |
| typ |ciąg| Typ zasobu platformy Azure. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Właściwości Azure Cosmos DB zdarzenia zbierania MongoDB

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| zasób | [Zasób](#resource)| Zasób Azure Cosmos DB interfejsu API dla zdarzenia zbierania MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

Odpowiedź na listę operacji, która zawiera zdarzenia kolekcji i ich właściwości.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| wartość |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Lista Azure Cosmos DB interfejsu API dla zdarzeń zbierania MongoDB oraz ich właściwości. |

## <a name="next-steps"></a>Następne kroki

* [Utwórz listę baz danych dostępnych](restorable-mongodb-databases-list.md)  w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu interfejsu API REST.
* [Model zasobów](continuous-backup-restore-resource-model.md) trybu ciągłej kopii zapasowej.