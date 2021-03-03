---
title: Kroki poprzedzające migrację migracji danych do interfejsu API Azure Cosmos DB MongoDB
description: Ten dokument zawiera omówienie wymagań wstępnych dotyczących migracji danych z programu MongoDB do Cosmos DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: ced795385fdf00e706ea897db80f558b513a9f9d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656962"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kroki poprzedzające migrację dla migracji danych z MongoDB do interfejsu API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Przed przeprowadzeniem migracji danych z MongoDB (lokalnie lub w chmurze) do interfejsu API Azure Cosmos DB dla MongoDB, należy:

1. [Zapoznaj się z najważniejszymi zagadnieniami dotyczącymi używania interfejsu API Azure Cosmos DB dla MongoDB](#considerations)
2. [Wybierz opcję migracji danych](#options)
3. [Oszacowanie przepływności wymaganej dla obciążeń](#estimate-throughput)
4. [Wybierz optymalny klucz partycji dla danych](#partitioning)
5. [Informacje na temat zasad indeksowania, które można ustawić dla danych](#indexing)

Jeśli zostały już wykonane powyższe wymagania wstępne dotyczące migracji, można [migrować dane MongoDB do interfejsu API Azure Cosmos DB MongoDB przy użyciu Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md). Ponadto jeśli nie utworzono konta, możesz przejrzeć dowolne [Przewodniki Szybki Start](create-mongodb-dotnet.md) pokazujące kroki tworzenia konta.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Zagadnienia dotyczące korzystania z interfejsu API Azure Cosmos DB dla MongoDB

Poniżej przedstawiono konkretne charakterystyki dotyczące interfejsu API Azure Cosmos DB MongoDB:

- **Model pojemności**: wydajność bazy danych na Azure Cosmos DB jest oparta na modelu przepływności. Ten model jest oparty na [jednostkach żądań na sekundę](request-units.md), które są jednostką, która reprezentuje liczbę operacji bazy danych, które mogą być wykonywane w odniesieniu do kolekcji na sekundę. Tę pojemność można przydzielić na [poziomie bazy danych lub kolekcji](set-throughput.md)i można ją zainicjować w modelu alokacji lub przy użyciu [alokowanej przepływności skalowania automatycznego](provision-throughput-autoscale.md).

- **Jednostki żądania**: Każda operacja bazy danych ma koszt powiązanych jednostek żądań (jednostek ru) w Azure Cosmos DB. Po wykonaniu ta wartość jest odejmowana od dostępnego poziomu jednostki żądania w danej sekundzie. Jeśli żądanie wymaga więcej jednostek ru niż aktualnie przydzielono RU/s, dostępne są dwie opcje rozwiązania problemu — zwiększenie ilości jednostek ru lub poczekanie na następną sekundę, a następnie ponów próbę wykonania operacji.

- **Elastyczna pojemność**: pojemność danej kolekcji lub bazy danych może się zmienić w dowolnym momencie. Pozwala to na elastyczną adaptację bazy danych do wymagań dotyczących przepływności w obciążeniu.

- **Automatyczna fragmentowania**: Azure Cosmos DB udostępnia system automatycznego partycjonowania, który wymaga tylko fragmentu (lub klucza partycji). [Mechanizm automatycznej partycjonowania](partitioning-overview.md) jest współużytkowany przez wszystkie Azure Cosmos DB interfejsy API i umożliwia bezproblemowe przekazywanie danych i skalowanie w poziomie.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opcje migracji Azure Cosmos DB interfejsu API dla MongoDB

[Azure Database Migration Service interfejsu API Azure Cosmos DB dla MongoDB](../dms/tutorial-mongodb-cosmos-db.md) zapewnia mechanizm, który upraszcza migrację danych, zapewniając w pełni zarządzaną platformę hostingu, opcje monitorowania migracji i automatyczną obsługę ograniczania. Pełna lista opcji jest następująca:

|**Typ migracji**|**Rozwiązanie**|**Zagadnienia do rozważenia**|
|---------|---------|---------|
|Tryb online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych <br/>&bull; Odpowiednie dla dużych zestawów danych i należy zachować ostrożność replikowania zmian na żywo <br/>&bull; Działa tylko z innymi źródłami MongoDB|
|Tryb offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych <br/>&bull; Odpowiednie dla dużych zestawów danych i należy zachować ostrożność replikowania zmian na żywo <br/>&bull; Działa tylko z innymi źródłami MongoDB|
|Tryb offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych <br/>&bull; Odpowiednie dla dużych zestawów danych <br/>&bull; Brak punktów kontrolnych oznacza, że każdy problem w trakcie migracji będzie wymagał ponownego uruchomienia całego procesu migracji<br/>&bull; Brak kolejki utraconych wiadomości oznacza, że kilka błędnych plików może zatrzymać cały proces migracji <br/>&bull; Potrzebuje niestandardowego kodu, aby zwiększyć przepływność odczytu dla niektórych źródeł danych|
|Tryb offline|[Istniejące narzędzia Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Łatwa konfiguracja i integracja <br/>&bull; Wymaga obsługi niestandardowej dla ograniczania przepustowości|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> Oszacowanie potrzeby przepływności dla obciążeń

W Azure Cosmos DB przepływność jest inicjowana z wyprzedzeniem i mierzona w jednostkach żądań (RU) na sekundę. W przeciwieństwie do maszyn wirtualnych lub serwerów lokalnych, jednostek ru można łatwo skalować w górę i w dół w dowolnym momencie. Możesz natychmiast zmienić liczbę zainicjowanych jednostek ru. Aby uzyskać więcej informacji, zobacz [jednostki żądań w Azure Cosmos DB](request-units.md).

Możesz użyć [kalkulatora pojemności Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) , aby określić liczbę jednostek żądania na podstawie konfiguracji konta bazy danych, ilości danych, rozmiaru dokumentu oraz wymaganych odczytów i zapisów na sekundę.

Poniżej przedstawiono kluczowe czynniki wpływające na liczbę wymaganych jednostek ru:
- **Rozmiar dokumentu**: w miarę wzrostu rozmiaru elementu/dokumentu, liczba jednostek ru zużytych do odczytu lub zapisu elementu/dokumentu również rośnie.

- **Liczba właściwości dokumentu**: liczba jednostek ru użytych do utworzenia lub zaktualizowania dokumentu jest powiązana z liczbą, złożonością i długością właściwości. Można zmniejszyć użycie jednostek żądania dla operacji zapisu, [ograniczając liczbę indeksowanych właściwości](mongodb-indexing.md).

- **Wzorce zapytań**: złożoność zapytania wpływa na liczbę jednostek żądań używanych przez zapytanie. 

Najlepszym sposobem na zrozumienie kosztów zapytań jest użycie przykładowych danych w Azure Cosmos DB [i uruchomienie przykładowych zapytań z powłoki MongoDB](connect-mongodb-account.md) przy użyciu `getLastRequestStastistics` polecenia w celu uzyskania opłaty za żądanie, która będzie wyprowadzać liczbę użytych jednostek ru:

`db.runCommand({getLastRequestStatistics: 1})`

To polecenie spowoduje wyjście dokumentu JSON podobnego do poniższego:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Możesz również użyć [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) , aby zrozumieć częstotliwość i wzorce zapytań wykonywanych względem Azure Cosmos DB. Wyniki dzienników diagnostycznych mogą być wysyłane do konta magazynu, wystąpienia EventHub lub [log Analytics platformy Azure](../azure-monitor/logs/log-analytics-tutorial.md).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Wybierz klucz partycji
Partycjonowanie, znane także jako fragmentowania, to kluczowy punkt rozważania przed migrowaniem danych. Azure Cosmos DB używa w pełni zarządzanego partycjonowania, aby zwiększyć pojemność bazy danych w celu spełnienia wymagań dotyczących magazynu i przepływności. Ta funkcja nie wymaga hostingu ani konfiguracji serwerów routingu.   

W podobny sposób funkcja partycjonowania automatycznie dodaje pojemność i ponownie równoważy dane. Aby uzyskać szczegółowe informacje i zalecenia dotyczące wybierania odpowiedniego klucza partycji dla danych, zobacz [artykuł Wybieranie klucza partycji](partitioning-overview.md#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indeksowanie danych

Interfejs API Azure Cosmos DB dla serwera MongoDB w wersji 3,6 lub nowszej automatycznie indeksuje `_id` tylko pole. Nie można porzucić tego pola. Automatycznie Wymusza unikatowość `_id` pola na klucz fragmentu. Aby zindeksować dodatkowe pola, należy zastosować [polecenia MongoDB index-Management](mongodb-indexing.md). Te domyślne zasady indeksowania różnią się od interfejsu API Azure Cosmos DB SQL, który domyślnie indeksuje wszystkie pola.

Możliwości indeksowania zapewniane przez Azure Cosmos DB obejmują dodawanie indeksów złożonych, unikatowych indeksów i indeksów czasu wygaśnięcia (TTL). Interfejs zarządzania indeksami jest mapowany na `createIndex()` polecenie. Dowiedz się więcej na temat [indeksowania w Azure Cosmos DB API for MongoDB](mongodb-indexing.md).

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatycznie migruje kolekcje MongoDB z unikatowymi indeksami. Jednak przed migracją należy utworzyć unikatowe indeksy. Azure Cosmos DB nie obsługuje tworzenia unikatowych indeksów, gdy w kolekcjach znajdują się już dane. Aby uzyskać więcej informacji, zobacz [unikalne klucze w Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Następne kroki
* [Przeprowadź migrację danych MongoDB do Cosmos DB przy użyciu Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Obsługa przepływności w kontenerach i bazach danych platformy Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Dystrybucja globalna w Azure Cosmos DB](distribute-data-globally.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
