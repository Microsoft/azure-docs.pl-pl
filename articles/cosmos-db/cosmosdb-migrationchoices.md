---
title: Opcje migracji Cosmos DB
description: W tym dokumencie opisano różne opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: a1b8ddba84920d8d3b6871ab404081d3b24c72e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261974"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB

Dane z różnych źródeł danych można ładować do Azure Cosmos DB. Ponadto, ponieważ Azure Cosmos DB obsługuje wiele interfejsów API, obiekty docelowe mogą być dowolnymi z istniejących interfejsów API. Aby można było obsługiwać ścieżki migracji z różnych źródeł do różnych Azure Cosmos DB interfejsów API, istnieje wiele rozwiązań, które zapewniają wyspecjalizowaną obsługę dla każdej ścieżki migracji. Ten dokument zawiera listę dostępnych rozwiązań oraz opis ich zalet i ograniczeń.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Czynniki wpływające na wybór narzędzia migracji

Wybór narzędzia migracji zależy od następujących czynników:
* **Migracja**w trybie online i offline: wiele narzędzi migracji udostępnia ścieżkę do przeprowadzenia jednorazowej migracji. Oznacza to, że aplikacje uzyskujące dostęp do bazy danych mogą być wykorzystane przez pewien czas przestoju. Niektóre rozwiązania migracji umożliwiają przeprowadzenie migracji na żywo, w której jest skonfigurowany potok replikacji między źródłem a obiektem docelowym.

* **Źródło danych**: istniejące dane mogą znajdować się w różnych źródłach danych, takich jak Oracle DB2, DataStax Cassanda, Azure SQL Database, PostgreSQL itd. Dane mogą również znajdować się w istniejącym koncie Azure Cosmos DB i zamiar migracji może zmienić model danych lub ponownie podzielić dane w kontener z innym kluczem partycji.

* **Azure Cosmos DB API**: w przypadku interfejsu API SQL w programie Azure Cosmos DB istnieją różne narzędzia opracowane przez zespół Azure Cosmos DB, który pomaga w różnych scenariuszach migracji. Wszystkie inne interfejsy API mają własny, wyspecjalizowany zestaw narzędzi opracowanych i konserwowanych przez społeczność. Ponieważ Azure Cosmos DB obsługuje te interfejsy API na poziomie protokołu przewodowego, narzędzia te powinny być wykonywane w trakcie migracji danych do Azure Cosmos DB. Mogą jednak wymagać niestandardowej obsługi dla ograniczania przepustowości, ponieważ ta koncepcja jest specyficzna dla Azure Cosmos DB.

* **Rozmiar danych**: większość narzędzi do migracji działa bardzo dobrze w przypadku mniejszych zestawów danych. Gdy zestaw danych przekracza kilka setek gigabajtów, Opcje narzędzi migracji są ograniczone. 

* **Oczekiwany czas trwania migracji**: migracje można skonfigurować tak, aby odbywały się w powolnym, przyrostowym tempie, które zużywa mniejszą przepływność, lub zużywa całą przepustowość zainicjowaną w docelowym kontenerze Azure Cosmos DB i dokończyć migrację w krótszym czasie.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB — interfejs SQL API
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia do rozważenia**|
|---------|---------|---------|
|W trybie offline|[Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Nieodpowiednie dla dużych zestawów danych|
|W trybie offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych <br/>&bull;Odpowiednie dla dużych zestawów danych <br/>&bull;Brak punktów kontrolnych — oznacza to, że w przypadku wystąpienia problemu w trakcie migracji należy ponownie uruchomić cały proces migracji.<br/>&bull;Brak kolejki utraconych wiadomości — oznacza to, że kilka błędnych plików może zatrzymać cały proces migracji.|
|W trybie offline|[Łącznik Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych <br/>&bull;Odpowiednie dla dużych zestawów danych <br/>&bull;Potrzebuje niestandardowej konfiguracji platformy Spark <br/>&bull;Platforma Spark jest wrażliwa na niespójności schematu i może to być problem podczas migracji |
|W trybie offline|[Narzędzie niestandardowe z Cosmos DB zbiorczego wykonawcy biblioteki](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Zawiera punkty kontrolne, funkcje obsługi utraconych wiadomości, które zwiększają odporność migracji <br/>&bull;Odpowiednie dla bardzo dużych zestawów danych (10 TB +)  <br/>&bull;Wymaga konfiguracji niestandardowej tego narzędzia działającego jako App Service |
|Online|[Funkcje Cosmos DB i interfejs API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Łatwa konfiguracja <br/>&bull;Działa tylko wtedy, gdy źródłem jest kontener Azure Cosmos DB <br/>&bull;Nieodpowiednie dla dużych zestawów danych <br/>&bull;Nie przechwytuje usunięć z kontenera źródłowego |
|Online|[Niestandardowa usługa migracji przy użyciu ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Zawiera śledzenie postępu <br/>&bull;Działa tylko wtedy, gdy źródłem jest kontener Azure Cosmos DB <br/>&bull;Działa również w przypadku większych zestawów danych <br/>&bull;Wymaga od użytkownika skonfigurowania App Service, aby hostować procesor źródła zmian <br/>&bull;Nie przechwytuje usunięć z kontenera źródłowego|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Działa z wieloma różnymi źródłami, takimi jak Oracle, DB2, SQL Server <br/>&bull;Łatwe tworzenie potoków ETL i udostępnia pulpit nawigacyjny do monitorowania <br/>&bull;Obsługuje większe zestawy danych <br/>&bull;Ponieważ jest to narzędzie innej firmy, należy je zakupić w portalu Marketplace i zainstalować w środowisku użytkownika|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB interfejs API Mongo
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia do rozważenia**|
|---------|---------|---------|
|W trybie offline|[Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Nieodpowiednie dla dużych zestawów danych|
|W trybie offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Łatwa konfiguracja i obsługa wielu źródeł <br/>&bull;Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych <br/>&bull;Odpowiednie dla dużych zestawów danych <br/>&bull;Brak punktów kontrolnych oznacza, że każdy problem w trakcie migracji będzie wymagał ponownego uruchomienia całego procesu migracji<br/>&bull;Brak kolejki utraconych wiadomości oznacza, że kilka błędnych plików może zatrzymać cały proces migracji <br/>&bull;Potrzebuje niestandardowego kodu, aby zwiększyć przepływność odczytu dla niektórych źródeł danych|
|W trybie offline|[Istniejące narzędzia Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Łatwa konfiguracja i integracja <br/>&bull;Wymaga obsługi niestandardowej dla ograniczania przepustowości|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych <br/>&bull;Odpowiednie dla dużych zestawów danych i należy zachować ostrożność replikowania zmian na żywo <br/>&bull;Działa tylko z innymi źródłami MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>Interfejs API Cassandra usługi Azure Cosmos DB
|**Typ migracji**|**Rozwiązanie**|**Zagadnienia do rozważenia**|
|---------|---------|---------|
|W trybie offline|[cqlsh — polecenie kopiowania](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Łatwa konfiguracja <br/>&bull;Nieodpowiednie dla dużych zestawów danych <br/>&bull;Działa tylko wtedy, gdy źródłem jest tabela Cassandra|
|W trybie offline|[Kopiuj tabelę za pomocą platformy Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Może korzystać z możliwości platformy Spark do zrównoleglanie transformacji i pozyskiwania <br/>&bull;Wymaga konfiguracji z niestandardowymi zasadami ponawiania w celu obsługi przepustnic|
|Online|[Striim (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Działa z wieloma różnymi źródłami, takimi jak Oracle, DB2, SQL Server <br/>&bull;Łatwe tworzenie potoków ETL i udostępnia pulpit nawigacyjny do monitorowania <br/>&bull;Obsługuje większe zestawy danych <br/>&bull;Ponieważ jest to narzędzie innej firmy, należy je zakupić w portalu Marketplace i zainstalować w środowisku użytkownika|
|Online|[Blitzz (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Obsługuje większe zestawy danych <br/>&bull;Ponieważ jest to narzędzie innej firmy, należy je zakupić w portalu Marketplace i zainstalować w środowisku użytkownika|

## <a name="other-apis"></a>Inne interfejsy API
W przypadku interfejsów API innych niż interfejs API SQL, interfejs API Mongo i interfejs API Cassandra są dostępne różne narzędzia obsługiwane przez każdy z istniejących ekosystemów interfejsu API. 

**Interfejs API tabel** 
* [Narzędzie do migracji danych](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Interfejs API języka Gremlin**
* [Biblioteka wykonawców zbiorczych wykresu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej, pobierając przykładowe aplikacje zużywające zbiorczą bibliotekę wykonawczą w programie [.NET i środowisku](bulk-executor-dot-net.md) [Java](bulk-executor-java.md). 
* Biblioteka wykonawców zbiorczych jest zintegrowana z łącznikiem Cosmos DB Spark, aby dowiedzieć się więcej, zobacz Azure Cosmos DB artykuł dotyczący [łącznika Spark](spark-connector.md) .  
* Skontaktuj się z zespołem produktu Azure Cosmos DB, otwierając bilet pomocy technicznej w ramach typu problemu "ogólny poradnik" i "duże (TB +) migracje", aby uzyskać dodatkową pomoc dotyczącą migracji z dużą skalą.
