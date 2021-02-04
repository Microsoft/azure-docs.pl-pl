---
title: Wyświetlanie listy kontenerów interfejsu API SQL dostępnych w Azure Cosmos DB przy użyciu interfejsu API REST
description: Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich kontenerach SQL Azure Cosmos DB w ramach określonej bazy danych. Pomaga to w scenariuszu, w którym kontener został przypadkowo usunięty.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: bba00b10d1a24dc29c1e986de1f0144c00350f5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537428"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Wyświetlanie listy kontenerów interfejsu API SQL dostępnych w Azure Cosmos DB przy użyciu interfejsu API REST

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokaż źródło zdarzeń wszystkich mutacji wykonanych na wszystkich kontenerach SQL Azure Cosmos DB w ramach określonej bazy danych. Pomaga to w scenariuszu, w którym kontener został przypadkowo usunięty. Ten interfejs API wymaga `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` uprawnień

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Z opcjonalnymi parametrami:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | W | Wymagany | Typ | Opis |
| --- | --- | --- | --- | --- |
| **Identyfikator wystąpienia** | path | True |ciąg| Identyfikator GUID obiektu instanceId konta bazy danych dostępnych. |
| **przeniesienie** | path | True | ciąg| Azure Cosmos DB region, z odstępami między wyrazami i każdym słowem. |
| **subscriptionId** | path | True | ciąg| Identyfikator subskrypcji docelowej. |
| **wersja interfejsu API** | query | True | ciąg | Wersja interfejsu API do użycia dla tej operacji. |
| **restorableSqlDatabaseRid** | query | |ciąg| Identyfikator zasobu bazy danych SQL. |

## <a name="responses"></a>Odpowiedzi

| Nazwa | Typ | Opis |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| Operacja została ukończona pomyślnie. |
| Inne kody stanu | [DefaultErrorResponse](#defaulterrorresponse)| Odpowiedź na błąd z opisem przyczyny niepowodzenia operacji. |

## <a name="examples"></a>Przykłady

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Przykładowe żądanie**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Przykładowa odpowiedź**

Kod stanu: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definicje

| Definicja | Opis | | --- || --- | | [Kontener](#container) | Azure Cosmos DB obiektu zasobów kontenera SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Odpowiedź na błąd z usługi. | | [Zwrócono](#errorresponse) | Odpowiedź na błąd. | | [OperationType](#operationtype) | Wyliczenie, aby wskazać typ operacji zdarzenia. | | [Zasób](#resource) | Zasób Azure Cosmos DB zdarzenia kontenera SQL | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Zdarzenie kontenera Azure Cosmos DB SQL | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Właściwości Azure Cosmos DB zdarzenia kontenera SQL | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | Odpowiedź na listę operacji, która zawiera zdarzenia kontenera SQL i ich właściwości. |

### <a name="container"></a><a id="container"></a>Kontener

Azure Cosmos DB obiektu zasobów kontenera SQL

| **Nazwa**  |  **Typ**  |  **Opis** | | --- || --- | ---| | _etag | ciąg | Wygenerowana przez system właściwość reprezentująca element ETag zasobu wymagany do optymistycznej kontroli współbieżności. | | _rid | ciąg | Właściwość wygenerowana przez system. Unikatowy identyfikator. | | _self | ciąg | Wygenerowana przez system właściwość, która określa ścieżkę adresowaną do zasobu kontenera. | | _ts | | Wygenerowana przez system właściwość, która oznacza ostatnią zaktualizowaną sygnaturę czasową zasobu. | | ID | ciąg | Nazwa Azure Cosmos DB kontenera SQL |

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
| Utwórz |ciąg|zdarzenie tworzenia kontenera|
| Usuń |ciąg|zdarzenie usuwania kontenera|
| Zamień |ciąg|zdarzenie modyfikacji kontenera|
| SystemOperation |ciąg|zdarzenie modyfikacji kontenera wyzwolone przez system. To zdarzenie nie jest inicjowane przez użytkownika|

### <a name="resource"></a><a id="resource"></a>Zasób

Zasób zdarzenia kontenera Azure Cosmos DB SQL

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| _rid |ciąg| Właściwość wygenerowana przez system. Unikatowy identyfikator. |
| kontener |[Kontener](#container)| Azure Cosmos DB obiektu zasobów kontenera SQL |
| eventTimestamp |ciąg| Godzina, o której nastąpiło zdarzenie tego kontenera. |
| operationType |[OperationType](#operationtype)| Typ operacji dla tego zdarzenia kontenera. |
| ownerId |ciąg| Nazwa kontenera SQL. |
| ownerResourceId |ciąg| Identyfikator zasobu kontenera SQL. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Zdarzenie kontenera Azure Cosmos DB SQL

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | ---
| ID (Identyfikator) |ciąg| Unikatowy identyfikator zasobu Azure Resource Manager. |
| name |ciąg| Nazwa zasobu Azure Resource Manager. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| Właściwości zdarzenia kontenera SQL. |
| typ |ciąg| Typ zasobu platformy Azure. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Właściwości zdarzenia kontenera SQL Azure Cosmos DB

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| zasób |[Zasób](#resource)| Zasób zdarzenia kontenera Azure Cosmos DB SQL |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

Odpowiedź na listę operacji, która zawiera zdarzenia kontenera SQL i ich właściwości.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| wartość |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Lista zdarzeń kontenera SQL i ich właściwości. |

## <a name="next-steps"></a>Następne kroki

* [Utwórz listę baz danych dostępnych](restorable-mongodb-databases-list.md)  w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu interfejsu API REST.
* [Model zasobów](continuous-backup-restore-resource-model.md) trybu ciągłej kopii zapasowej.