---
title: Opcje migracji Cosmos DB
description: W tym dokumencie opisano różne opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 8721c0eb728f568521e86baecb658dc9c869a7f6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097587"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dane z różnych źródeł danych można ładować do Azure Cosmos DB. Ponieważ Azure Cosmos DB obsługuje wiele interfejsów API, obiekty docelowe mogą być dowolnymi z istniejących interfejsów API. Poniżej przedstawiono kilka scenariuszy, w których dane są migrowane do Azure Cosmos DB:

* Przenieś dane z jednego kontenera usługi Azure Cosmos do innego kontenera w tej samej bazie danych lub w różnych bazach danych.
* Przeniesienie danych między dedykowanymi kontenerami do udostępnionych kontenerów baz danych.
* Przenieś dane z konta usługi Azure Cosmos znajdującego się w Region1 na inne konto platformy Azure Cosmos w tym samym lub innym regionie.
* Przenieś dane ze źródła, takiego jak Azure Blob Storage, plik JSON, baza danych Oracle, Couchbase, DynamoDB do Azure Cosmos DB.

Aby można było obsługiwać ścieżki migracji z różnych źródeł do różnych Azure Cosmos DB interfejsów API, istnieje wiele rozwiązań, które zapewniają wyspecjalizowaną obsługę dla każdej ścieżki migracji. Ten dokument zawiera listę dostępnych rozwiązań oraz opis ich zalet i ograniczeń.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Czynniki wpływające na wybór narzędzia migracji

Wybór narzędzia migracji zależy od następujących czynników:

* **Migracja online a offline** : Wiele narzędzi migracji udostępnia ścieżkę do przeprowadzenia tylko jednej migracji. Oznacza to, że aplikacje uzyskujące dostęp do bazy danych mogą mieć przestój. Niektóre rozwiązania migracji umożliwiają przeprowadzenie migracji na żywo, w której potok replikacji jest skonfigurowany między źródłem a obiektem docelowym.

* **Źródło danych** : istniejące dane mogą znajdować się w różnych źródłach danych, takich jak Oracle DB2, DataStax Cassanda, Azure SQL Database, PostgreSQL itd. Dane mogą również znajdować się w istniejącym koncie Azure Cosmos DB i zamiar migracji może zmienić model danych lub ponownie podzielić dane w kontener z innym kluczem partycji.

* **Interfejs API usługi Azure Cosmos DB** : W przypadku interfejsu SQL API w usłudze Azure Cosmos DB istnieje wiele narzędzi opracowanych przez zespół usługi Azure Cosmos DB, które pomagają w różnych scenariuszach migracji. Wszystkie inne interfejsy API mają własny, wyspecjalizowany zestaw narzędzi opracowanych i utrzymywanych przez społeczność. Ponieważ usługa Azure Cosmos DB obsługuje te interfejsy API na poziomie protokołu przewodowego, narzędzia te powinny działać w niezmienionej postaci w trakcie migracji danych do usługi Azure Cosmos DB. Mogą jednak wymagać niestandardowej obsługi w przypadku ograniczania przepustowości, ponieważ to pojęcie jest specyficzne dla usługi Azure Cosmos DB.

* **Rozmiar danych** : Większość narzędzi do migracji działa bardzo dobrze w przypadku mniejszych zestawów danych. Gdy zestaw danych przekracza kilkaset gigabajtów, opcje narzędzi migracji są ograniczone. 

* **Oczekiwany czas trwania migracji** : Migracje można skonfigurować tak, aby odbywały się w powolnym, przyrostowym tempie z użyciem mniejszej przepływności lub całej przepływności aprowizowanej w docelowym kontenerze usługi Azure Cosmos DB i dokończyć migrację w krótszym czasie.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB — interfejs SQL API

|Typ migracji|Rozwiązanie|Obsługiwane źródła|Obsługiwane elementy docelowe|Zagadnienia do rozważenia|
|---------|---------|---------|---------|---------|
|Tryb offline|[Narzędzie do migracji danych](import-data.md)| &bull;Pliki JSON/CSV<br/>&bull;Azure Cosmos DB — interfejs SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob Storage|&bull;Azure Cosmos DB — interfejs SQL API<br/>&bull;Interfejs API tabel Azure Cosmos DB<br/>&bull;Pliki JSON |&bull; Łatwa konfiguracja i obsługa wielu źródeł. <br/>&bull; Nieodpowiedni dla dużych zestawów danych.|
|Tryb offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Pliki JSON/CSV<br/>&bull;Azure Cosmos DB — interfejs SQL API<br/>&bull;Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/> <br/>Zapoznaj się z artykułem [Azure Data Factory](../data-factory/connector-overview.md) w innych obsługiwanych źródłach.|&bull;Azure Cosmos DB — interfejs SQL API<br/>&bull;Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB<br/>&bull;Pliki JSON <br/><br/> Zapoznaj się z artykułem [Azure Data Factory](../data-factory/connector-overview.md) innymi obsługiwanymi obiektami docelowymi. |&bull; Łatwa konfiguracja i obsługa wielu źródeł.<br/>&bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych. <br/>&bull; Odpowiednie dla dużych zestawów danych. <br/>&bull; Brak punktów kontrolnych — oznacza to, że w przypadku wystąpienia problemu w trakcie migracji należy ponownie uruchomić cały proces migracji.<br/>&bull; Brak kolejki utraconych wiadomości — oznacza to, że kilka błędnych plików może zatrzymać cały proces migracji.|
|Tryb offline|[Łącznik Azure Cosmos DB Spark](spark-connector.md)|Azure Cosmos DB interfejs API SQL. <br/><br/>Można używać innych źródeł z dodatkowymi łącznikami z ekosystemu Spark.| Azure Cosmos DB interfejs API SQL. <br/><br/>Można używać innych obiektów docelowych z dodatkowymi łącznikami z ekosystemu Spark.| &bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych. <br/>&bull; Odpowiednie dla dużych zestawów danych. <br/>&bull; Potrzebuje niestandardowej konfiguracji platformy Spark. <br/>&bull; Platforma Spark jest wrażliwa na niespójności schematu i może to być problem podczas migracji. |
|Tryb offline|[Narzędzie niestandardowe z Cosmos DB zbiorczego wykonawcy biblioteki](migrate-cosmosdb-data.md)| Źródło zależy od niestandardowego kodu | Azure Cosmos DB — interfejs SQL API| &bull; Oferuje punkty kontrolne, funkcje obsługi utraconych wiadomości, które zwiększają odporność migracji. <br/>&bull; Odpowiednie dla bardzo dużych zestawów danych (10 TB +).  <br/>&bull; Wymaga konfiguracji niestandardowej tego narzędzia działającego jako App Service. |
|Tryb online|[Funkcje Cosmos DB i interfejs API ChangeFeed](change-feed-functions.md)| Azure Cosmos DB — interfejs SQL API | Azure Cosmos DB — interfejs SQL API| &bull; Łatwa konfiguracja. <br/>&bull; Działa tylko wtedy, gdy źródłem jest kontener Azure Cosmos DB. <br/>&bull; Nieodpowiedni dla dużych zestawów danych. <br/>&bull; Nie przechwytuje usunięć z kontenera źródłowego. |
|Tryb online|[Niestandardowa usługa migracji przy użyciu ChangeFeed](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB — interfejs SQL API | Azure Cosmos DB — interfejs SQL API| &bull; Zapewnia śledzenie postępu. <br/>&bull; Działa tylko wtedy, gdy źródłem jest kontener Azure Cosmos DB. <br/>&bull; Działa również w przypadku większych zestawów danych.<br/>&bull; Wymaga od użytkownika skonfigurowania App Service, aby hostować procesor źródła zmian. <br/>&bull; Nie przechwytuje usunięć z kontenera źródłowego.|
|Tryb online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Zapoznaj się z [witryną sieci Web Striim](https://www.striim.com/sources-and-targets/) dla innych obsługiwanych źródeł. |&bull;Azure Cosmos DB — interfejs SQL API <br/>&bull; Azure Cosmos DB interfejs API Cassandra<br/><br/> Zapoznaj się z [witryną sieci Web Striim](https://www.striim.com/sources-and-targets/) dla innych obsługiwanych elementów docelowych. | &bull; Działa z wieloma różnymi źródłami, takimi jak Oracle, DB2, SQL Server.<br/>&bull; Łatwe tworzenie potoków ETL i udostępnia pulpit nawigacyjny do monitorowania. <br/>&bull; Obsługuje większe zestawy danych. <br/>&bull; Ponieważ jest to narzędzie innej firmy, należy je zakupić w portalu Marketplace i zainstalować w środowisku użytkownika.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB interfejs API Mongo

|Typ migracji|Rozwiązanie|Obsługiwane źródła|Obsługiwane elementy docelowe|Zagadnienia do rozważenia|
|---------|---------|---------|---------|---------|
|Tryb online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB |&bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych. <br/>&bull; Odpowiednie dla dużych zestawów danych i należy zachować ostrożność replikowania zmian na żywo. <br/>&bull; Działa tylko z innymi źródłami MongoDB.|
|Tryb offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB| &bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych. <br/>&bull; Odpowiednie dla dużych zestawów danych i należy zachować ostrożność replikowania zmian na żywo. <br/>&bull; Działa tylko z innymi źródłami MongoDB.|
|Tryb offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Pliki JSON/CSV<br/>&bull;Azure Cosmos DB — interfejs SQL API<br/>&bull;Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/><br/> Zapoznaj się z artykułem [Azure Data Factory](../data-factory/connector-overview.md) w innych obsługiwanych źródłach. | &bull;Azure Cosmos DB — interfejs SQL API<br/>&bull;Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB <br/>&bull; Pliki JSON <br/><br/> Zapoznaj się z artykułem [Azure Data Factory](../data-factory/connector-overview.md) innymi obsługiwanymi obiektami docelowymi.| &bull; Łatwa konfiguracja i obsługa wielu źródeł. <br/>&bull; Korzysta z Azure Cosmos DB biblioteki wykonawców zbiorczych. <br/>&bull; Odpowiednie dla dużych zestawów danych. <br/>&bull; Brak punktów kontrolnych oznacza, że każdy problem w trakcie migracji będzie wymagał ponownego uruchomienia całego procesu migracji.<br/>&bull; Brak kolejki utraconych wiadomości oznacza, że kilka błędnych plików może zatrzymać cały proces migracji. <br/>&bull; Potrzebuje niestandardowego kodu, aby zwiększyć przepływność odczytu dla niektórych źródeł danych.|
|Tryb offline|[Istniejące narzędzia Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB| &bull; Łatwa konfiguracja i integracja. <br/>&bull; Wymaga obsługi niestandardowej dla ograniczania przepustowości.|

## <a name="azure-cosmos-db-cassandra-api"></a>Interfejs API Cassandra usługi Azure Cosmos DB

|Typ migracji|Rozwiązanie|Obsługiwane źródła|Obsługiwane elementy docelowe|Zagadnienia do rozważenia|
|---------|---------|---------|---------|---------|
|Tryb offline|[cqlsh — polecenie kopiowania](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|Pliki CSV | Interfejs API Cassandra usługi Azure Cosmos DB| &bull; Łatwa konfiguracja. <br/>&bull; Nieodpowiedni dla dużych zestawów danych. <br/>&bull; Działa tylko wtedy, gdy źródłem jest tabela Cassandra.|
|Tryb offline|[Kopiuj tabelę za pomocą platformy Spark](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;Interfejs API Cassandra usługi Azure Cosmos DB| Interfejs API Cassandra usługi Azure Cosmos DB | &bull; Może korzystać z możliwości platformy Spark do zrównoleglanie transformacji i pozyskiwania. <br/>&bull; Wymaga konfiguracji z niestandardowymi zasadami ponawiania, aby obsłużyć ograniczenia.|
|Tryb online|[Striim (z Oracle DB/Apache Cassandra)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Zapoznaj się z [witryną sieci Web Striim](https://www.striim.com/sources-and-targets/) dla innych obsługiwanych źródeł.|&bull;Azure Cosmos DB — interfejs SQL API<br/>&bull;Interfejs API Cassandra usługi Azure Cosmos DB <br/><br/> Zapoznaj się z [witryną sieci Web Striim](https://www.striim.com/sources-and-targets/) dla innych obsługiwanych elementów docelowych.| &bull; Działa z wieloma różnymi źródłami, takimi jak Oracle, DB2, SQL Server. <br/>&bull; Łatwe tworzenie potoków ETL i udostępnia pulpit nawigacyjny do monitorowania. <br/>&bull; Obsługuje większe zestawy danych. <br/>&bull; Ponieważ jest to narzędzie innej firmy, należy je zakupić w portalu Marketplace i zainstalować w środowisku użytkownika.|
|Tryb online|[Blitzz (z Oracle DB/Apache Cassandra)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Zapoznaj się z [witryną sieci Web Blitzz](https://www.blitzz.io/) dla innych obsługiwanych źródeł. |Interfejs API Cassandra Azure Cosmos DB. <br/><br/>Zapoznaj się z [witryną sieci Web Blitzz](https://www.blitzz.io/) dla innych obsługiwanych elementów docelowych. | &bull; Obsługuje większe zestawy danych. <br/>&bull; Ponieważ jest to narzędzie innej firmy, należy je zakupić w portalu Marketplace i zainstalować w środowisku użytkownika.|

## <a name="other-apis"></a>Inne interfejsy API

W przypadku interfejsów API innych niż interfejs API SQL, interfejs API Mongo i interfejs API Cassandra są dostępne różne narzędzia obsługiwane przez każdy z istniejących ekosystemów interfejsu API. 

**Interfejs API tabel** 

* [Narzędzie do migracji danych](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Interfejs API języka Gremlin**

* [Biblioteka wykonawców zbiorczych wykresu](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej, pobierając przykładowe aplikacje zużywające zbiorczą bibliotekę wykonawczą w programie [.NET i środowisku](bulk-executor-dot-net.md) [Java](bulk-executor-java.md). 
* Biblioteka wykonawców zbiorczych jest zintegrowana z łącznikiem Cosmos DB Spark, aby dowiedzieć się więcej, zobacz Azure Cosmos DB artykuł dotyczący [łącznika Spark](spark-connector.md) .  
* Skontaktuj się z zespołem produktu Azure Cosmos DB, otwierając bilet pomocy technicznej w ramach typu problemu "ogólny poradnik" i "duże (TB +) migracje", aby uzyskać dodatkową pomoc dotyczącą migracji z dużą skalą.
