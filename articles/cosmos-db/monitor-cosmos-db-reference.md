---
title: Informacje o monitorowaniu Azure Cosmos DB danych | Microsoft Docs
description: Ważne materiały referencyjne potrzebne podczas monitorowania dzienników i metryk w programie Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/07/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 5f542b35110a6d967640ad91faead75f6cc0e0c2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593296"
---
# <a name="monitoring-azure-cosmos-db-data-reference"></a>Informacje o monitorowaniu Azure Cosmos DB danych

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W tym artykule zawarto dokumentację dotyczącą danych dziennika i metryk zebranych w celu analizy wydajności i dostępności usługi Azure Cosmos DB. Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania Azure Cosmos DB, zobacz artykuł [monitorowanie Azure Cosmos DB](monitor-cosmos-db.md) .

## <a name="metrics"></a>Metryki

Wszystkie metryki odpowiadające Azure Cosmos DB są przechowywane w przestrzeni nazw **Cosmos DB metrykach standardowych**. Aby uzyskać listę wszystkich Azure Monitor metryki pomocy technicznej (w tym Azure Cosmos DB), zobacz temat [Azure monitor Supported Metrics](../azure-monitor/essentials/metrics-supported.md). W tej sekcji wymieniono wszystkie automatycznie zbierane metryki platformy zebrane dla Azure Cosmos DB.  

### <a name="request-metrics"></a>Metryki żądań

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji) |Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---| ---|
| TotalRequests (całkowita liczba żądań) | Liczba (liczba) | Liczba wykonanych żądań| DatabaseName, CollectionName, region, StatusCode| Wszystko | TotalRequests, http 2xx, http 3xx, http 400, HTTP 401, wewnętrzny błąd serwera, Usługa niedostępna, żądania ograniczone, średnia liczba żądań na sekundę | Służy do monitorowania żądań według kodu stanu, kontenera z dokładnością do minuty. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji Count na minutę i Podziel ją na 60. |
| MetadataRequests (żądania metadanych) |Liczba (liczba) | Liczba żądań metadanych. Azure Cosmos DB utrzymuje kontener metadanych systemu dla każdego konta, który umożliwia Wyliczenie kolekcji, baz danych itp., a ich konfiguracje, bez opłat. | DatabaseName, CollectionName, region, StatusCode| Wszystko| |Służy do monitorowania ograniczania przepustowości ze względu na żądania metadanych.|
| MongoRequests (żądania Mongo) | Liczba (liczba) | Liczba wykonanych żądań Mongo | DatabaseName, CollectionName, region, CommandName, ErrorCode| Wszystko |Częstotliwość żądań zapytań Mongo, szybkość żądania aktualizacji Mongo, częstotliwość żądań usunięcia Mongo, Mongo wstawiania liczby żądań, Mongo| Służy do monitorowania błędów żądania Mongo, użycia na typ polecenia. |

### <a name="request-unit-metrics"></a>Metryki jednostki żądania

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo żądania) | Liczba (łącznie) |Wykorzystane jednostki żądania Mongo| DatabaseName, CollectionName, region, CommandName, ErrorCode| Wszystko |Mongo naliczanie żądań zapytań, opłata za żądanie aktualizacji Mongo, opłata za żądanie usunięcia żądania, Mongo, opłata za żądanie| Służy do monitorowania Mongo zasobów jednostek RU w ciągu minuty.|
| TotalRequestUnits (łączna liczba jednostek żądań)| Liczba (łącznie) | Wykorzystane jednostki żądania| DatabaseName, CollectionName, region, StatusCode |Wszystko| TotalRequestUnits| Służy do monitorowania całkowitego użycia RU z dokładnością do minuty. Aby obliczyć średnią liczbę zużytych jednostek RU na sekundę, użyj łącznej agregacji na minutę i Podziel ją na 60.|
| ProvisionedThroughput (zainicjowana przepływność)| Liczba (maksimum) |Elastyczna przepływność na poziomie szczegółowości kontenera| DatabaseName, ContainerName| 5 mln| | Służy do monitorowania zainicjowanej przepływności na kontener.|

### <a name="storage-metrics"></a>Metryki magazynu

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (dostępny magazyn) |Bajty (łącznie) | Łączna ilość dostępnego magazynu zgłoszona z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 mln| Dostępny magazyn| Służy do monitorowania dostępnej pojemności magazynu (dotyczy tylko stałych kolekcji magazynów) minimalny stopień szczegółowości powinien wynosić 5 minut.| 
| Datausage (użycie danych) |Bajty (łącznie) |Całkowite użycie danych zgłoszone z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 mln |Rozmiar danych | Służy do monitorowania łącznego użycia danych w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut.|
| IndexUsage (użycie indeksu) | Bajty (łącznie) |Całkowite użycie indeksów zgłoszone w 5-minutowych stopnia szczegółowości na region| DatabaseName, CollectionName, region| 5 mln| Rozmiar indeksu| Służy do monitorowania łącznego użycia danych w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut. |
| DocumentQuota (przydział dokumentu) | Bajty (łącznie) | Łączny przydział magazynu zgłoszony z dokładnością do 5 minut na region.| DatabaseName, CollectionName, region| 5 mln |Pojemność magazynu| Służy do monitorowania łącznego limitu przydziału w kontenerze i regionie. minimalny stopień szczegółowości powinien wynosić 5 minut.|
| DocumentCount (liczba dokumentów) | Liczba (łącznie) |Łączna liczba dokumentów zgłoszona z dokładnością do 5 minut na region| DatabaseName, CollectionName, region| 5 mln |Liczba dokumentów|Używane do monitorowania liczby dokumentów w kontenerze i regionie, minimalny stopień szczegółowości powinien wynosić 5 minut.|

### <a name="latency-metrics"></a>Metryki opóźnienia

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Użycie |
|---|---|---|---| ---| ---|
| ReplicationLatency (opóźnienie replikacji)| Milisekundy (minimum, maksimum, średnia) | Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną| SourceRegion, TargetRegion| Wszystko | Służy do monitorowania opóźnienia replikacji poziomie P99 między dowolnymi dwoma regionami dla konta z replikacją geograficzną. |
| Opóźnienie po stronie serwera| Milisekundy (średnia) | Czas przetwarzania żądania przez serwer. | CollectionName, Connectionmode, DatabaseName, OperationType, PublicAPIType, region | Wszystko | Służy do monitorowania opóźnienia żądania na serwerze Azure Cosmos DB. |

### <a name="availability-metrics"></a>Metryki dostępności

|Metryka (nazwa wyświetlana metryki) |Jednostka (typ agregacji)|Opis| Stopień szczegółowości czasu| Mapowanie starszej metryki | Użycie |
|---|---|---|---| ---| ---|
| Serviceavailability (dostępność usługi)| Procent (minimum, maksimum) | Dostępność żądań konta z dokładnością do 1 godziny| 1 godz | Dostępność usługi | Reprezentuje procent całkowitej liczby żądań zakończonych niepowodzenie. Żądanie jest uznawane za niepowodzenie z powodu błędu systemu, jeśli kod stanu to 410, 500 lub 503 używany do monitorowania dostępności konta na poziomie szczegółowości godzin. |

### <a name="cassandra-api-metrics"></a>Metryki interfejs API Cassandra

|Metryka (nazwa wyświetlana metryki)|Jednostka (typ agregacji)|Opis|Wymiary| Stopień szczegółowości czasu| Użycie |
|---|---|---|---| ---| ---|
| CassandraRequests (żądania Cassandra) | Liczba (liczba) | Liczba wykonanych żądań interfejs API Cassandra| DatabaseName, CollectionName, ErrorCode, region, OperationType, ResourceType| Wszystko| Służy do monitorowania żądań Cassandra z dokładnością do minuty. Aby uzyskać średnią liczbę żądań na sekundę, użyj agregacji Count na minutę i Podziel ją na 60.|
| CassandraRequestCharges (Cassandra żądania) | Count (sum, min, Max, AVG) | Jednostki żądań używane przez interfejs API Cassandra | DatabaseName, CollectionName, region, OperationType, ResourceType| Wszystko| Służy do monitorowania jednostek ru używanych na minutę przez konto interfejs API Cassandra.|
| CassandraConnectionClosures (zamknięcia połączeń Cassandra) |Liczba (liczba) |Liczba zamkniętych połączeń Cassandra| ClosureReason, region| Wszystko | Służy do monitorowania łączności między klientami i interfejs API Cassandra Azure Cosmos DB.|

Aby uzyskać więcej informacji, zobacz listę [wszystkich metryk platformy obsługiwanych w Azure monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="resource-logs"></a>Dzienniki zasobów

W poniższej tabeli wymieniono właściwości dzienników zasobów w Azure Cosmos DB. Dzienniki zasobów są zbierane do dzienników Azure Monitor i usługi Azure Storage. W Azure Monitor dzienniki są zbierane w tabeli **AzureDiagnostics** pod nazwą dostawcy zasobów * * `MICROSOFT.DOCUMENTDB` .

| Pole lub właściwość usługi Azure Storage | Właściwość dzienników Azure Monitor | Opis |
| --- | --- | --- |
| **pierwszym** | **TimeGenerated** | Data i godzina (UTC), gdy wystąpiła operacja. |
| **Identyfikator** | **Zasób** | Konto Azure Cosmos DB, dla którego są włączone dzienniki.|
| **kategorii** | **Kategoria** | W przypadku Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests**, **CassandraRequests**, **GremlinRequests** są dostępne typy dzienników. |
| **operationName** | **OperationName** | Nazwa operacji. Nazwa operacji może być,,,,,,,,,,,  `Create` `Update` `Read` `ReadFeed` `Delete` `Replace` `Execute` `SqlQuery` `Query` `JSQuery` `Head` `HeadFeed` lub `Upsert` .   |
| **aœciwoœci** | n/d | Zawartość tego pola jest opisana w następujących wierszach. |
| **activityId** | **activityId_g** | Unikatowy identyfikator GUID dla zarejestrowanej operacji. |
| **userAgent** | **userAgent_s** | Ciąg określający agenta użytkownika klienta, z którego wysłano żądanie. Format agenta użytkownika to `{user agent name}/{version}` .|
| **requestResourceType** | **requestResourceType_s** | Typ zasobu, do którego uzyskuje się dostęp. Ta wartość może być bazą danych, kontenerem, dokumentem, załącznikiem, użytkownikiem, uprawnieniem, procedurą składowaną, wyzwalaczem, funkcją zdefiniowaną przez użytkownika lub ofertą. |
| **statusCode** | **statusCode_s** | Stan odpowiedzi operacji. |
| **requestResourceId** | **ResourceId** | Identyfikator zasobu, który odnosi się do żądania. W zależności od wykonanej operacji ta wartość może wskazywać na `databaseRid` , `collectionRid` , lub `documentRid` .|
| **clientIpAddress** | **clientIpAddress_s** | Adres IP klienta. |
| **requestCharge** | **requestCharge_s** | Liczba jednostek RU/s, które są używane przez operację |
| **collectionRid** | **collectionId_s** | Unikatowy identyfikator kolekcji.|
| **trwania** | **duration_d** | Czas trwania operacji (w milisekundach). |
| **requestLength** | **requestLength_s** | Długość żądania w bajtach. |
| **responseLength** | **responseLength_s** | Długość odpowiedzi (w bajtach).|
| **resourceTokenPermissionId** | **resourceTokenPermissionId_s** | Ta właściwość wskazuje identyfikator uprawnienia tokenu zasobu, który został określony. Aby dowiedzieć się więcej o uprawnieniach, zobacz artykuł [bezpieczny dostęp do danych](./secure-access-to-data.md#permissions) . |
| **resourceTokenPermissionMode** | **resourceTokenPermissionMode_s** | Ta właściwość wskazuje tryb uprawnień, który został ustawiony podczas tworzenia tokenu zasobu. Tryb uprawnień może mieć wartości, takie jak "All" lub "read". Aby dowiedzieć się więcej o uprawnieniach, zobacz artykuł [bezpieczny dostęp do danych](./secure-access-to-data.md#permissions) . |
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ta wartość nie jest pusta, gdy [tokeny zasobów](./secure-access-to-data.md#resource-tokens) są używane do uwierzytelniania. Wartość wskazuje identyfikator zasobu użytkownika. |
| **responseLength** | **responseLength_s** | Długość odpowiedzi (w bajtach).|

Aby zapoznać się z listą wszystkich kategorii dzienników Azure Monitor i linków do skojarzonych schematów, zobacz [Azure monitor dzienniki kategorii i schematów](../azure-monitor/essentials/resource-logs-schema.md). 

## <a name="azure-monitor-logs-tables"></a>Tabele dzienników Azure Monitor

Azure Cosmos DB używa tabel Kusto z dzienników Azure Monitor. Za pomocą usługi log Analytics można badać te tabele. Aby zapoznać się z listą Kusto Bales, zobacz artykuł [Informacje o tabelach dzienników Azure monitor](/azure/azure-monitor/reference/tables/tables-resourcetype#azure-cosmos-db) .

## <a name="see-also"></a>Zobacz też

- Aby uzyskać opis Azure Cosmos DB monitorowania, zobacz [Azure Cosmos DB monitorowania](monitor-cosmos-db.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) .