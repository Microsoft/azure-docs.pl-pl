---
title: Wprowadzenie do interfejsu API Cassandra usługi Azure Cosmos DB
description: Dowiedz się, jak można użyć Azure Cosmos DB do istniejących aplikacji "Unieś-and-Shift" i utworzyć nowe aplikacje za pomocą sterowników Cassandra i CQL
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 05/21/2019
ms.openlocfilehash: 70dbceb51ed030124d1b793d77c6bc287da91065
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "81687641"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Wprowadzenie do interfejsu API Cassandra usługi Azure Cosmos DB

Interfejs API Cassandra usługi Azure Cosmos DB może być używany jako magazyn danych dla aplikacji napisanych dla na potrzeby usługi [Apache Cassandra](https://cassandra.apache.org). Oznacza to, że używając istniejących [sterowników Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) zgodnych z językiem CQL w wersji 4, aplikacja napisana dla platformy Cassandra może teraz komunikować się z interfejsem API Cassandra usługi Azure Cosmos DB. W wielu przypadkach można przełączać się za pomocą platformy Apache Cassandra w celu użycia interfejs API Cassandra Azure Cosmos DB, zmieniając tylko parametry połączenia. 

Interfejs API Cassandra umożliwia korzystanie z danych przechowywanych w Azure Cosmos DB za pomocą Cassandra Query Language (CQL), narzędzi opartych na Cassandra (takich jak cqlsh) i Cassandra sterowników klientów, które są już znane.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Jakie są zalety korzystania z interfejsu API Apache Cassandra dla usługi Azure Cosmos DB?

**Brak zarządzania operacjami**: jako usługa w pełni zarządzana w chmurze interfejs API Cassandra usługi Azure Cosmos DB usuwa nadmiarowość związaną z zarządzaniem wieloma ustawieniami systemu operacyjnego, maszynami JVM i plikami yaml, ich monitorowaniem oraz interakcjami. Usługa Azure Cosmos DB umożliwia monitorowanie przepływności, opóźnienia, magazynu, dostępności oraz alertów z możliwością konfigurowania.

**Standard typu open source**: Mimo że w pełni zarządzana usługa interfejs API Cassandra nadal obsługuje dużą powierzchnię natywnego protokołu platformy [Apache Cassandra](cassandra-support.md), co pozwala na tworzenie aplikacji na szeroko używanym i w chmurze niezależny od Open Source.

**Zarządzanie wydajnością**: usługa Azure Cosmos DB oferuje operacje odczytu i zapisu o gwarantowanych małych opóźnieniach na poziomie 99. percentyla zapisane w umowie SLA. Użytkownicy nie muszą już martwić się o nakłady operacyjne, aby zapewnić wysoką wydajność i małe opóźnienia odczytów i zapisów. Oznacza to, że użytkownicy nie muszą radzić sobie z ręcznym planowaniem kompaktowania, zarządzaniem reliktami, konfigurowaniem filtrów Blooma oraz replik. Usługa Azure Cosmos DB powoduje usunięcie narzutu związanego z zarządzaniem tymi zagadnieniami i pozwala skupić się na logice aplikacji.

**Możliwość używania istniejącego kodu i narzędzi**: usługa Azure Cosmos DB udostępnia zgodność na poziomie protokołu przewodowego z istniejącymi zestawami SDK i narzędziami platformy Cassandra. Dzięki tej zgodności można używać istniejącej bazy kodu z interfejsem API Cassandra usługi Azure Cosmos DB z prostymi zmianami.

**Elastyczność przepływności i magazynu**: usługa Azure Cosmos DB oferuje gwarantowaną przepływność we wszystkich regionach i możliwość skalowania aprowizowanej przepływności za pomocą witryny Azure Portal, programu PowerShell lub operacji interfejsu wiersza polecenia. W miarę potrzeb można [elastycznie skalować](manage-scale-cassandra.md) magazyn i przepływność dla tabel.

**Dystrybucja globalna i dostępność**: usługa Azure Cosmos DB zapewnia możliwość globalnej dystrybucji danych we wszystkich regionach platformy Azure z lokalnym przekazywaniem danych przy jednoczesnym zapewnieniu dostępu do danych z małymi opóźnieniami i wysokiej dostępności. Usługa Azure Cosmos DB zapewnia dostępność przez 99,99% czasu w obrębie regionu oraz dostępność odczytu i zapisu przez 99,999% czasu w wielu regionach bez nadmiarowych operacji. Dowiedz się więcej z artykułu [Globalna dystrybucja danych](distribute-data-globally.md). 

**Wybór spójności**: usługa Azure Cosmos DB oferuje możliwość wyboru spośród pięciu dobrze zdefiniowanych poziomów spójności w celu osiągnięcia optymalnego kompromisu między spójnością a wydajnością. Te poziomy spójności to: silne, powiązana nieaktualność, sesja, spójny prefiks i ostateczne. Te dokładnie zdefiniowane, praktyczne i intuicyjne poziomy spójności umożliwiają deweloperom dokonywanie precyzyjnych kompromisów między spójnością, dostępnością i opóźnieniem. Dowiedz się więcej w artykule o [poziomach spójności](consistency-levels.md). 

**Klasa korporacyjna**: usługa Azure Cosmos DB udostępnia [certyfikaty zgodności](https://www.microsoft.com/trustcenter), aby zapewnić użytkownikom możliwość bezpiecznego korzystania z platformy. Usługa Azure Cosmos DB oferuje również szyfrowanie danych magazynowanych i w ruchu, zaporę adresów IP oraz dzienniki inspekcji na potrzeby działań płaszczyzny sterowania.

Źródło **zdarzeń**: interfejs API Cassandra zapewnia dostęp do dziennika zmian trwałych, [źródła zmian](cassandra-change-feed.md), które może ułatwić źródło zdarzeń bezpośrednio z bazy danych. W programie Apache Cassandra jedynym odpowiednikiem jest funkcja przechwytywania zmian danych, która jest tylko mechanizmem do flagi określonych tabel do archiwizacji, a także do odrzucania zapisów w tych tabelach po osiągnięciu konfigurowalnego rozmiaru na dysku na potrzeby dziennika przechwytywania zmian (te funkcje są nadmiarowe w Cosmos DB, ponieważ odpowiednie aspekty są automatycznie regulowane).

## <a name="next-steps"></a>Następne kroki

* Możesz szybko rozpocząć od kompilowania następujących aplikacji specyficznych dla języka do tworzenia i zarządzania danymi interfejsu API Cassandra:
  - [Aplikacja platformy Node.js](create-cassandra-nodejs.md)
  - [Aplikacja platformy .NET](create-cassandra-dotnet.md)
  - [Aplikacja w języku Python](create-cassandra-python.md)

* Wprowadzenie do [tworzenia konta interfejs API Cassandra, bazy danych i tabeli](create-cassandra-api-account-java.md) przy użyciu aplikacji Java.

* [Ładowanie przykładowych danych do tabeli interfejsu API Cassandra](cassandra-api-load-data.md) przy użyciu aplikacji w języku Java.

* [Wykonywanie zapytań dotyczących danych z poziomu konta interfejsu API Cassandra](cassandra-api-query-data.md) przy użyciu aplikacji w języku Java.

* Aby dowiedzieć się więcej o funkcjach platformy Apache Cassandra obsługiwanych przez interfejs API Cassandra usługi Azure Cosmos DB, zobacz artykuł [obsługa rozwiązania Cassandra](cassandra-support.md).

* Przeczytaj [Często zadawane pytania](cassandra-faq.md).
