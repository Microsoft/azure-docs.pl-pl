---
title: Wyświetlanie listy kont bazy danych dostępnych przy Azure Cosmos DB użyciu interfejsu API REST
description: Wyświetla listę wszystkich kont bazy danych dostępnych Azure Cosmos DB dostępnych w ramach subskrypcji.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 71aafc756e5291e148c3b162f8946544b6e3c2d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537581"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Wyświetlanie listy kont bazy danych dostępnych przy Azure Cosmos DB użyciu interfejsu API REST

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wyświetla listę wszystkich kont bazy danych dostępnych Azure Cosmos DB dostępnych w ramach subskrypcji. To wywołanie wymaga `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` uprawnień.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | W | Wymagany | Typ | Opis |
| --- | --- | --- | --- | --- |
| **subscriptionId** | path | True | ciąg| Identyfikator subskrypcji docelowej. |
| **wersja interfejsu API** | query | True | ciąg | Wersja interfejsu API do użycia dla tej operacji. |

## <a name="responses"></a>Odpowiedzi

| Nazwa | Typ | Opis |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| Operacja została ukończona pomyślnie. |
| Inne kody stanu | [DefaultErrorResponse](#defaulterrorresponse)| Odpowiedź na błąd z opisem przyczyny niepowodzenia operacji. |

## <a name="examples"></a>Przykłady

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Przykładowe żądanie**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Przykładowa odpowiedź**

Kod stanu: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definicje

|Definicja  | Opis|
| --- | --- |
| [ApiType](#apitype) | Wyliczenie, aby wskazać typ interfejsu API konta bazy danych dostępnych. |
| [DefaultErrorResponse](#defaulterrorresponse) | Odpowiedź na błąd z usługi. |
| [Zwrócono](#errorresponse) | Odpowiedź na błąd. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Konto bazy danych dostępnych Azure Cosmos DB. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | Odpowiedź na listę operacji, która zawiera konta bazy danych dostępnych i ich właściwości. |
| [RestorableLocationResource](#restorablelocationresource) | Właściwości konta dostępnych regionalnego. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Wyliczenie, aby wskazać typ interfejsu API konta bazy danych dostępnych.

| **Nazwa** | **Typ** |
| --- | --- |
| Cassandra |ciąg|
| Gremlin |ciąg|
| GremlinV2 |ciąg|
| MongoDB |ciąg|
| Sql |ciąg|
| Tabela |ciąg|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Konto bazy danych dostępnych Azure Cosmos DB.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| ID (Identyfikator) |ciąg| Unikatowy identyfikator zasobu Azure Resource Manager. |
| location |ciąg| Lokalizacja grupy zasobów, do której należy zasób. |
| name |ciąg| Nazwa zasobu Menedżer zasobów. |
| Właściwości. AccountName |ciąg| Nazwa globalnego konta bazy danych |
| Właściwości. apiType |[ApiType](#apitype)| Typ interfejsu API konta bazy danych dostępnych. |
| Właściwości. creationTime |ciąg| Godzina utworzenia konta bazy danych dostępnych (format ISO-8601). |
| Właściwości. deletionTime |ciąg| Godzina usunięcia konta bazy danych dostępnych (format ISO-8601). |
| Właściwości. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Lista regionów, z których można przywrócić konto bazy danych. |
| typ |ciąg| Typ zasobu platformy Azure. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

Odpowiedź na listę operacji, która zawiera konta bazy danych dostępnych i ich właściwości.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| wartość |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Lista kont bazy danych dostępnych i ich właściwości. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Właściwości konta dostępnych regionalnego.

| **Nazwa** | **Typ** | **Opis** |
| --- | --- | --- |
| creationTime |ciąg| Godzina utworzenia konta bazy danych dostępnych (format ISO-8601). |
| deletionTime |ciąg| Godzina usunięcia konta bazy danych dostępnych (format ISO-8601). |
| Lokalizacja |ciąg| Lokalizacja konta dostępnych regionalnego. |
| regionalDatabaseAccountInstanceId |ciąg| Identyfikator wystąpienia konta dostępnych regionalnego. |

## <a name="next-steps"></a>Następne kroki

* [Konta bazy danych dostępnych — lista według lokalizacji.](restorable-database-accounts-list-by-location.md)
* [Model zasobów](continuous-backup-restore-resource-model.md) trybu ciągłej kopii zapasowej.