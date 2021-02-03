---
title: Wyświetlanie listy dostępnych zasobów interfejsu API SQL w Azure Cosmos DB przy użyciu interfejsu API REST
description: Zwróć listę danych i pola kombi kontenera, które istnieją na koncie pod daną sygnaturą czasową i lokalizacją. Pomaga to w scenariuszach do sprawdzania, jakie zasoby istnieją w danej sygnatury czasowej i lokalizacji.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dbee87098dcc712669c332deac656cf5656ef4c4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527514"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Wyświetlanie listy dostępnych zasobów interfejsu API SQL w Azure Cosmos DB przy użyciu interfejsu API REST

Zwróć listę danych i pola kombi kontenera, które istnieją na koncie pod daną sygnaturą czasową i lokalizacją. Pomaga to w scenariuszach do sprawdzania, jakie zasoby istnieją w danej sygnatury czasowej i lokalizacji. Ten interfejs API wymaga `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` uprawnień.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Z opcjonalnymi parametrami:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | W | Wymagany | Typ | Opis |
| --- | --- | --- | --- | --- |
| **Identyfikator wystąpienia** | path | True |ciąg| Identyfikator GUID obiektu instanceId konta bazy danych dostępnych. |
| **przeniesienie** | path | True | ciąg| Azure Cosmos DB region, z odstępami między wyrazami i każdym słowem. |
| **subscriptionId** | path | True | ciąg| Identyfikator subskrypcji docelowej. |
| **wersja interfejsu API** | query | True | ciąg | Wersja interfejsu API do użycia dla tej operacji. |
| **restoreLocation** | query | |ciąg| Lokalizacja, w której znajdują się zasoby dostępnych. |
| **restoreTimestampInUtc** | query | |ciąg| Sygnatura czasowa w przypadku istnienia zasobów dostępnych. |

## <a name="responses"></a>Odpowiedzi

| Nazwa | Typ | Opis |
| --- | --- | --- |
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| Operacja została ukończona pomyślnie. |
| Inne kody stanu | [DefaultErrorResponse](#defaulterrorresponse)| Odpowiedź na błąd z opisem przyczyny niepowodzenia operacji. |

## <a name="examples"></a>Przykłady

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Przykładowe żądanie**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Przykładowa odpowiedź**

Kod stanu: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definicje

| Definicja | Opis | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Określone bazy danych do przywrócenia. | | [DefaultErrorResponse](#defaulterrorresponse) | Odpowiedź na błąd z usługi. | | [Zwrócono](#errorresponse) | Odpowiedź na błąd. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | Odpowiedź na listę operacji, która zawiera zasoby SQL dostępnych. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Określone bazy danych do przywrócenia.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| CollectionName |ciąg []| Nazwy kolekcji dostępne do przywrócenia. |
| Bazy |ciąg| Nazwa bazy danych, która jest dostępna do przywracania. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

Odpowiedź na listę operacji, która zawiera zasoby SQL dostępnych.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| wartość |[DatabaseRestoreResource](#databaserestoreresource)[]| Lista dostępnych zasobów SQL, w tym nazw baz danych i kolekcji. |

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie listy baz danych dostępnych](restorable-sql-databases-list.md) w interfejsie API usługi Azure Cosmos SQL przy użyciu interfejsu API REST.
* [Model zasobów](continuous-backup-restore-resource-model.md) trybu ciągłej kopii zapasowej.