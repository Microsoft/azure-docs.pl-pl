---
title: Wprowadzenie do interfejsu API Cassandra usługi Azure Cosmos DB
description: Dowiedz się, jak za pomocą usługi Azure Cosmos DB do "lift-and-shift" istniejących aplikacji i tworzenia nowych aplikacji przy użyciu sterowników Cassandra i CQL
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 05/21/2019
ms.openlocfilehash: 70dbceb51ed030124d1b793d77c6bc287da91065
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687641"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Wprowadzenie do interfejsu API Cassandra usługi Azure Cosmos DB

Interfejs API Cassandra usługi Azure Cosmos DB może być używany jako magazyn danych dla aplikacji napisanych dla na potrzeby usługi [Apache Cassandra](https://cassandra.apache.org). Oznacza to, że używając istniejących [sterowników Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) zgodnych z językiem CQL w wersji 4, aplikacja napisana dla platformy Cassandra może teraz komunikować się z interfejsem API Cassandra usługi Azure Cosmos DB. W wielu przypadkach można przełączyć się z przy użyciu Apache Cassandra do korzystania z interfejsu API Cassandra usługi Azure Cosmos DB, po prostu zmieniając ciąg połączenia. 

Interfejs API Cassandra umożliwia interakcję z danymi przechowywanymi w usłudze Azure Cosmos DB przy użyciu języka cassandra query language (CQL), narzędzi opartych na kasandra (takich jak cqlsh) i sterowników klienta Cassandra, które są już znane.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Jakie są zalety korzystania z interfejsu API Apache Cassandra dla usługi Azure Cosmos DB?

**Brak zarządzania operacjami**: jako usługa w pełni zarządzana w chmurze interfejs API Cassandra usługi Azure Cosmos DB usuwa nadmiarowość związaną z zarządzaniem wieloma ustawieniami systemu operacyjnego, maszynami JVM i plikami yaml, ich monitorowaniem oraz interakcjami. Usługa Azure Cosmos DB umożliwia monitorowanie przepływności, opóźnienia, magazynu, dostępności oraz alertów z możliwością konfigurowania.

**Standard open source:** Pomimo tego, że jest w pełni zarządzaną usługą, API Cassandra nadal obsługuje dużą powierzchnię macierzystego [protokołu przewodowego Apache Cassandra,](cassandra-support.md)co pozwala na tworzenie aplikacji na szeroko stosowanym i niezależnym od chmury standardzie open source.

**Zarządzanie wydajnością**: usługa Azure Cosmos DB oferuje operacje odczytu i zapisu o gwarantowanych małych opóźnieniach na poziomie 99. percentyla zapisane w umowie SLA. Użytkownicy nie muszą już martwić się o nakłady operacyjne, aby zapewnić wysoką wydajność i małe opóźnienia odczytów i zapisów. Oznacza to, że użytkownicy nie muszą radzić sobie z ręcznym planowaniem kompaktowania, zarządzaniem reliktami, konfigurowaniem filtrów Blooma oraz replik. Usługa Azure Cosmos DB powoduje usunięcie narzutu związanego z zarządzaniem tymi zagadnieniami i pozwala skupić się na logice aplikacji.

**Możliwość używania istniejącego kodu i narzędzi**: usługa Azure Cosmos DB udostępnia zgodność na poziomie protokołu przewodowego z istniejącymi zestawami SDK i narzędziami platformy Cassandra. Dzięki tej zgodności można używać istniejącej bazy kodu z interfejsem API Cassandra usługi Azure Cosmos DB z prostymi zmianami.

**Elastyczność przepływności i magazynu**: usługa Azure Cosmos DB oferuje gwarantowaną przepływność we wszystkich regionach i możliwość skalowania aprowizowanej przepływności za pomocą witryny Azure Portal, programu PowerShell lub operacji interfejsu wiersza polecenia. Można [elastycznie skalować](manage-scale-cassandra.md) magazyn i przepływność dla tabel w razie potrzeby z przewidywalną wydajnością.

**Dystrybucja globalna i dostępność**: usługa Azure Cosmos DB zapewnia możliwość globalnej dystrybucji danych we wszystkich regionach platformy Azure z lokalnym przekazywaniem danych przy jednoczesnym zapewnieniu dostępu do danych z małymi opóźnieniami i wysokiej dostępności. Usługa Azure Cosmos DB zapewnia dostępność przez 99,99% czasu w obrębie regionu oraz dostępność odczytu i zapisu przez 99,999% czasu w wielu regionach bez nadmiarowych operacji. Dowiedz się więcej z artykułu [Globalna dystrybucja danych](distribute-data-globally.md). 

**Wybór spójności**: usługa Azure Cosmos DB oferuje możliwość wyboru spośród pięciu dobrze zdefiniowanych poziomów spójności w celu osiągnięcia optymalnego kompromisu między spójnością a wydajnością. Te poziomy spójności to: silne, powiązana nieaktualność, sesja, spójny prefiks i ostateczne. Te dokładnie zdefiniowane, praktyczne i intuicyjne poziomy spójności umożliwiają deweloperom dokonywanie precyzyjnych kompromisów między spójnością, dostępnością i opóźnieniem. Dowiedz się więcej w artykule o [poziomach spójności](consistency-levels.md). 

**Klasa korporacyjna**: usługa Azure Cosmos DB udostępnia [certyfikaty zgodności](https://www.microsoft.com/trustcenter), aby zapewnić użytkownikom możliwość bezpiecznego korzystania z platformy. Usługa Azure Cosmos DB oferuje również szyfrowanie danych magazynowanych i w ruchu, zaporę adresów IP oraz dzienniki inspekcji na potrzeby działań płaszczyzny sterowania.

**Pozyskiwanie zdarzeń:** Api Cassandra zapewnia dostęp do dziennika zmian trwałych, [źródło danych zmiany](cassandra-change-feed.md), co może ułatwić pozyskiwanie zdarzeń bezpośrednio z bazy danych. W Apache Cassandra jedynym odpowiednikiem jest przechwytywanie danych zmian (CDC), który jest tylko mechanizmem do oznaczania określonych tabel do archiwizacji, a także odrzucania zapisów do tych tabel po osiągnięciu konfigurowalnego rozmiaru na dysku dla dziennika CDC (te możliwości są zbędne w usłudze Cosmos DB, ponieważ odpowiednie aspekty są automatycznie regulowane).

## <a name="next-steps"></a>Następne kroki

* Możesz szybko rozpocząć od kompilowania następujących aplikacji specyficznych dla języka do tworzenia i zarządzania danymi interfejsu API Cassandra:
  - [Aplikacja platformy Node.js](create-cassandra-nodejs.md)
  - [Aplikacja platformy .NET](create-cassandra-dotnet.md)
  - [Aplikacja w języku Python](create-cassandra-python.md)

* Wprowadzenie do [tworzenia konta interfejsu API Cassandra, bazy danych i tabeli](create-cassandra-api-account-java.md) przy użyciu aplikacji Java.

* [Ładowanie przykładowych danych do tabeli interfejsu API Cassandra](cassandra-api-load-data.md) przy użyciu aplikacji w języku Java.

* [Wykonywanie zapytań dotyczących danych z poziomu konta interfejsu API Cassandra](cassandra-api-query-data.md) przy użyciu aplikacji w języku Java.

* Aby dowiedzieć się więcej o funkcjach platformy Apache Cassandra obsługiwanych przez interfejs API Cassandra usługi Azure Cosmos DB, zobacz artykuł [obsługa rozwiązania Cassandra](cassandra-support.md).

* Przeczytaj [Często zadawane pytania](cassandra-faq.md).
