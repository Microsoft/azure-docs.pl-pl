---
title: Wprowadzenie do usługi Azure Cosmos DB
description: Zapoznaj się z informacjami na temat usługi Azure Cosmos DB. Ta dostępna w skali światowej, wielomodelowa baza danych zapewnia małe opóźnienia, elastyczną skalowalność i wysoką dostępność oraz oferuje natywną obsługę danych NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: cd6bbe963f0cee89c188053d0770816fdabc1b93
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490394"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB — Zapraszamy!

Współczesne aplikacje muszą zapewniać szybki czas reakcji i zawsze działać w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Aplikacje muszą reagować w czasie rzeczywistym na duże zmiany użycia w godzinach szczytu, przechowywać coraz większe ilości danych i udostępniać je użytkownikom w ciągu kilku milisekund.

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Poprzez kliknięcie jednego przycisku usługa Cosmos DB umożliwia elastyczne i niezależne skalowanie przepływności i magazynu w dowolnej liczbie regionów świadczenia platformy Azure na całym świecie. Można elastycznie skalować przepływność i magazyn oraz korzystać z szybkiego, jednocyfrowego dostępu do danych za pomocą ulubionego interfejsu API, w tym: SQL, MongoDB, Cassandra, tabele lub Gremlin. Cosmos DB oferuje kompleksowe [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) (umowy SLA) w zakresie przepływności, opóźnień, dostępności i gwarancji spójności, a także inne oferty usługi bazy danych.

Możesz bezpłatnie [Wypróbować Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure, bezpłatnie i zobowiązań lub korzystać z [bezpłatnej warstwy Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) , aby uzyskać konto z pierwszym 400 ru/s i 5 GB wolnego miejsca w magazynie.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB to dostępna w skali światowej usługa bazy danych firmy Microsoft z elastycznym skalowaniem, gwarantowanymi małymi opóźnieniami, pięcioma modelami spójności oraz kompleksowymi, gwarantowanymi umowami SLA" border="false":::

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="turnkey-global-distribution"></a>Gotowa do użycia dystrybucja globalna

Usługa Cosmos DB umożliwia tworzenie bardzo dynamicznych i wysoce dostępnych aplikacji na całym świecie. Cosmos DB w sposób przezroczysty replikuje dane, wszędzie tam, gdzie się znajdują użytkownicy, dzięki czemu użytkownicy mogą korzystać z repliki danych znajdujących się najbliżej nich.

Usługa Cosmos DB umożliwia dodawanie i usuwanie regionów platformy Azure do konta usługi Cosmos w dowolnym momencie, wystarczy kliknąć przycisk. Cosmos DB będzie bezproblemowo replikować dane do wszystkich regionów skojarzonych z kontem usługi Cosmos, gdy aplikacja nadal będzie wysoce dostępna, dzięki możliwościom *wielomultihostingunym* . Aby uzyskać więcej informacji, zobacz artykuł dotyczący [globalnej dystrybucji](distribute-data-globally.md).

### <a name="always-on"></a>Zawsze włączone

Dzięki głębokiej integracji z infrastrukturą platformy Azure i [przezroczystej replikacji wieloregionowej](global-dist-under-the-hood.md), Cosmos DB zapewnia [wysoką dostępność na 99,999%](high-availability.md) dla operacji odczytu i zapisu. Usługa Cosmos DB zapewnia również możliwość programowego (lub za pomocą portalu) wywoływania regionalnego trybu failover konta usługi Cosmos. Ta funkcja pomaga zapewnić, że aplikacja została zaprojektowana w celu przełączenia w tryb failover w przypadku awarii regionalnej.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastyczne skalowanie przepływności i magazynu na całym świecie

Zaprojektowana z przezroczystym podziałem na partycje i wieloregionową replikacją zapisu, Cosmos DB oferuje niespotykaną elastyczną skalowalność dla operacji zapisu i odczytu na całym świecie. Można elastycznie skalować w górę od tysięcy do setek milionów żądań na sekundę na całym świecie przy użyciu jednego wywołania interfejsu API i płacić tylko za przepływność i magazyn, które są potrzebne. Ta funkcja pomaga radzić sobie z niespodziewanymi wzrostami w obciążeniach bez konieczności nadmiernej aprowizacji dla szczytu. Aby uzyskać więcej informacji, zobacz [partycjonowanie w Cosmos DB](partitioning-overview.md), [zainicjowana przepływność na kontenerach i bazach danych](set-throughput.md)i [globalnie skalowanie przepływności](./request-units.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Gwarantowane małe opóźnienia na poziomie 99. percentyla, na całym świecie

Przy użyciu usługi Cosmos DB można tworzyć szybko reagujące aplikacje skalowane na całym świecie. Za pomocą nowej metody wieloregionowego protokołu replikacji zapisu i [aparatu bazy danych z nieoptymalnym zatrzaskiem i zoptymalizowanym pod kątem pisania](index-policy.md)Cosmos dB gwarantuje, że są one mniejsze niż 10 ms dla obydwu, odczyty (indeksowane) i operacje zapisu w 99 percentylu na całym świecie. Ta funkcja umożliwia stałe pozyskiwanie danych i wyjątkowo szybkie żądania do natychmiast reagujących aplikacji.

### <a name="precisely-defined-multiple-consistency-choices"></a>Wiele dokładnie zdefiniowanych opcji spójności

Podczas kompilowania aplikacji rozproszonych globalnie w Cosmos DB nie trzeba już podejmować wyjątkowo [kompromisów między spójnością, dostępnością, opóźnieniami i przepływności](./consistency-levels.md). Wieloregionowa Usługa replikacji zapisu w systemie Cosmos DB została starannie zaprojektowana w celu zaoferowania [pięciu dobrze zdefiniowanych opcji spójności](consistency-levels.md), silnej nieprawidłowej nieprawidłowej, bezwzględnej,  -  *strong* *spójnego prefiksu*i *ostateczności* — dla intuicyjnego modelu programowania z małymi opóźnieniami i wysoką dostępnością dla aplikacji rozproszonej globalnie. *bounded staleness* *session*

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

Przechowywanie schematu i indeksów bazy danych w programie — synchronizacja ze schematem aplikacji jest szczególnie bolesnym dla aplikacji rozproszonych globalnie. W przypadku Cosmos DB nie trzeba obsługiwać zarządzania schematem ani indeksem. Aparat bazy danych jest całkowicie niezależny od schematów.  Ponieważ nie jest wymagane zarządzanie schematami i indeksem, nie trzeba również martwić się o przestoje aplikacji podczas migrowania schematów. Usługa Cosmos DB [automatycznie indeksuje wszystkie dane](index-policy.md) i szybko obsługuje zapytania.

### <a name="battle-tested-database-service"></a>Gruntownie przetestowana usługa bazy danych

Cosmos DB to fundamentalna usługa na platformie Azure. Prawie dekada Cosmos DB była używana przez wiele produktów firmy Microsoft dla aplikacji o znaczeniu strategicznym na skalę globalną, w tym Skype, Xbox, Microsoft 365, Azure i wielu innych. Obecnie Cosmos DB jest jednym z najszybszych rosnących usług na platformie Azure, używanych przez wielu klientów zewnętrznych i aplikacji o znaczeniu strategicznym, które wymagają elastycznej skali, gotowe globalnej dystrybucji, wieloregionowej replikacji zapisów w celu uzyskania małych opóźnień i wysokiej dostępności zarówno operacji odczytu, jak i zapisu.

### <a name="ubiquitous-regional-presence"></a>Szeroka dostępność w regionach

Usługa Cosmos DB jest dostępna we wszystkich regionach świadczenia usługi Azure na całym świecie, w tym w odniesieniu do 54 + regionów w chmurze publicznej, [Azure Chiny 21Vianet](https://www.azure.cn/en-us/), Azure (Niemcy), Azure Government i Azure Government dla działu obrony (DoD). Zobacz [wysoką dostępność dzięki Azure Cosmos DB](high-availability.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Gwarancja bezpieczeństwa i przeznaczenie dla przedsiębiorstw

Cosmos DB jest certyfikowany dla [szerokiej gamy standardów zgodności](compliance.md). Ponadto wszystkie dane w usłudze Cosmos DB są szyfrowane podczas magazynowania i przesyłania. Usługa Cosmos DB zapewnia autoryzację na poziomie wiersza i przestrzeganie rygorystycznych standardów zabezpieczeń.

### <a name="significant-tco-savings"></a>Znaczne oszczędności całkowitego kosztu posiadania

Ponieważ Cosmos DB to w pełni zarządzana usługa, nie trzeba już zarządzać i korzystać z złożonych wdrożeń i uaktualnień obejmującej wiele centrów danych, płacisz za pomoc techniczną, Licencjonowanie lub operacje ani nie trzeba udostępniać bazy danych dla szczytowego obciążenia. Aby uzyskać więcej informacji, zobacz [Optymalizacja kosztów za pomocą Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Wiodące w branży kompleksowe umowy SLA

Usługa Cosmos DB jest pierwszą i jedyną usługą, która oferuje [wiodące w branży kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zapewniające wysoką dostępność przez 99,999% czasu, opóźnienie odczytu i zapisu w 99. percentylu, gwarantowaną przepływność i spójność.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globalnie dystrybuowana analiza operacyjna i AI z natywnie wbudowaną Apache Spark

Możesz uruchomić projekt [Spark](spark-connector.md) bezpośrednio na danych przechowywanych w usłudze Cosmos DB. Ta funkcja umożliwia przeprowadzenie analizy operacyjnej o małych opóźnieniach w skali globalnej bez wywierania wpływu na obciążenia transakcyjne działające bezpośrednio w odniesieniu do usługi Cosmos DB. Aby uzyskać więcej informacji, zobacz [globalnie dystrybuowane analizy operacyjne](./synapse-link.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Opracowywanie aplikacji na Cosmos DB przy użyciu popularnych interfejsów API oprogramowania typu Open Source (OSS)

Cosmos DB oferuje interfejsy API do pracy z danymi przechowywanymi w bazie danych Cosmos. Domyślnie [można użyć języka SQL](./sql-query-getting-started.md) (podstawowego interfejsu API) do wykonywania zapytań dotyczących bazy danych Cosmos. Cosmos DB również implementuje interfejsy API dla [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) i [Azure Table Storage](table-introduction.md). Możesz wskazać sterowniki klienta (i narzędzia) dla najczęściej używanych NoSQL (np. MongoDB, Cassandra, Gremlin) bezpośrednio do swojej bazy danych programu Cosmos. Dzięki obsłudze protokołów sieci powszechnie używanych interfejsów API NoSQL Cosmos DB umożliwia:

* Łatwe migrowanie aplikacji do usługi Cosmos DB przy jednoczesnym zachowaniu znacznej części logiki aplikacji.
* Zapewnienie, że aplikacja będzie nadal przenośna i niezależna od dostawcy i chmury.
* Korzystaj z w pełni zarządzanej usługi w chmurze, która zapewnia wiodące w branży umowy SLA, finansowo NoSQLe dla wspólnych interfejsów API. 
* Elastyczne skalowanie aprowizowanej przepływności i magazynu dla baz danych zgodnie z potrzebami i płatność tylko za przepływność i magazyn, które są potrzebne. Prowadzi to do znacznych oszczędności kosztów.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Rozwiązania, które korzystają z zalet usługi Azure Cosmos DB

Wszystkie [aplikacje sieci Web, mobilne, gry i IoT](use-cases.md) , które wymagają obsługi dużych ilości danych, odczytów i zapisów w [skali globalnej](distribute-data-globally.md) z niemal rzeczywistym czasem odpowiedzi dla różnych danych, będą korzystać Cosmos DB z [gwarantowanej wysokiej dostępności](https://azure.microsoft.com/support/legal/sla/cosmos-db/), wysokiej przepustowości, małych opóźnień i możliwość dostosowania spójności. Dowiedz się, jak można używać usługi Azure Cosmos DB do kompilowania rozwiązań z zakresu [IoT i telematyki](use-cases.md#iot-and-telematics), [sprzedaży i marketingu](use-cases.md#retail-and-marketing), [gier](use-cases.md#gaming) oraz [aplikacji mobilnych i internetowych](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat podstawowych pojęć dotyczących Cosmos DB [gotowe globalna dystrybucja](distribute-data-globally.md) i [partycjonowania](partitioning-overview.md) oraz [przepływności aprowizacji](request-units.md).

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)