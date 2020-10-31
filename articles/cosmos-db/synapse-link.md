---
title: Link usługi Azure Synapse dla Azure Cosmos DB, korzyści i kiedy należy z niego korzystać
description: Dowiedz się więcej o usłudze Azure Synapse link dla Azure Cosmos DB. Link Synapse umożliwia uruchamianie analizy prawie w czasie rzeczywistym (HTAP) przy użyciu usługi Azure Synapse Analytics przez dane operacyjne w Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: d25cd7418ec34b8c4a4a53dba4892e85ed1a66d5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098080"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db-preview"></a>Co to jest link usługi Azure Synapse dla Azure Cosmos DB (wersja zapoznawcza)?
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)][!INCLUDE[appliesto-mongodb-apis](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]
> Link usługi Azure Synapse dla Azure Cosmos DB jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Link Synapse platformy Azure dla Azure Cosmos DB to natywna w chmurze funkcja przetwarzania transakcyjnego i analitycznego (HTAP), która pozwala uruchamiać analizę niemal w czasie rzeczywistym za pośrednictwem danych operacyjnych w Azure Cosmos DB. Usługa Azure Synapse Link umożliwia ścisłą integrację między usługami Azure Cosmos DB i Azure Synapse Analytics.

Korzystając z [Azure Cosmos DB analitycznego](analytical-store-introduction.md)magazynu, w pełni izolowany magazyn kolumn, usługa Azure Synapse Link umożliwia analizowanie w [usłudze Azure Synapse analiz](../synapse-analytics/overview-what-is.md) na podstawie danych operacyjnych w odpowiedniej skali. Analitycy biznesowi, inżynierowie danych i analityki danych mogą teraz korzystać z Synapse Spark lub Synapse SQL zamiennie do uruchamiania niemal w czasie rzeczywistym potoków analizy biznesowej, analizy i uczenia maszynowego. Można to osiągnąć bez wpływu na wydajność transakcyjnych obciążeń w usłudze Azure Cosmos DB. 

Na poniższej ilustracji przedstawiono integrację usługi Azure Synapse z usługami Azure Cosmos DB i Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Diagram architektury dla integracji usługi Azure Synapse Analytics z usługą Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Korzysta

Aby analizować duże operacyjne zestawy danych przy jednoczesnym zminimalizowaniu wpływu obciążeń transakcyjnych na wydajność, tradycyjnie dane operacyjne w Azure Cosmos DB są wyodrębniane i przetwarzane przy użyciu potoków wyodrębniania transformacji i ładowania (ETL). Potoki ETL wymagają wielu warstw przenoszenia danych, co znacznie zwiększa złożoność operacyjną i wpływa na wydajność Twoich obciążeń transakcyjnych. Zwiększa również opóźnienie analizy danych operacyjnych od momentu pochodzenia.

W porównaniu do tradycyjnych rozwiązań opartych na usłudze ETL link usługi Azure Synapse dla Azure Cosmos DB oferuje kilka korzyści, takich jak:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Zmniejszona złożoność bez zadań ETL do zarządzania

Link usługi Azure Synapse umożliwia bezpośredni dostęp do magazynu analitycznego Azure Cosmos DB przy użyciu usługi Azure Synapse Analytics bez złożonych ruchów danych. Wszystkie aktualizacje danych operacyjnych są widoczne w magazynie analitycznym niemal w czasie rzeczywistym bez zadań ETL lub zmiany źródła. Można uruchomić dużą skalę analizy w porównaniu z magazynem analitycznym, z Synapse Analytics, bez konieczności przekształceń danych.

### <a name="near-real-time-insights-into-your-operational-data"></a>Wgląd w dane operacyjne niemal w czasie rzeczywistym

Teraz możesz uzyskać szczegółowe informacje na temat danych operacyjnych niemal w czasie rzeczywistym przy użyciu linku usługi Azure Synapse. Systemy oparte na usłudze ETL mają większe opóźnienia do analizowania danych operacyjnych, z powodu wielu warstw potrzebnych do wyodrębniania, przekształcania i ładowania danych operacyjnych. Dzięki integracji natywnej Azure Cosmos DB z magazynem analitycznym przy użyciu usługi Azure Synapse Analytics można analizować dane operacyjne niemal w czasie rzeczywistym, włączając nowe scenariusze biznesowe. 

### <a name="no-impact-on-operational-workloads"></a>Brak wpływu na obciążenia operacyjne

Za pomocą linku Synapse platformy Azure można uruchamiać zapytania analityczne względem magazynu analitycznego Azure Cosmos DB (oddzielnego magazynu kolumn), podczas gdy operacje transakcyjne są przetwarzane przy użyciu alokowanej przepływności dla transakcyjnego obciążenia (magazyn transakcyjny oparty na wierszach).  Obciążenie analityczne jest obsługiwane niezależnie od transakcyjnego ruchu obciążenia bez zużywania przepustowości dla danych operacyjnych.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Optymalizacja pod kątem obciążeń analizy na dużą skalę

Azure Cosmos DB magazyn analityczny jest zoptymalizowany pod kątem zapewnienia skalowalności, elastyczności i wydajności obciążeń analitycznych bez żadnej zależności od czasu wykonywania obliczeń. Technologia magazynowania jest samozarządzana w celu zoptymalizowania obciążeń analitycznych. Dzięki wbudowanej obsłudze usługi Azure Synapse Analytics dostęp do tej warstwy magazynowania zapewnia prostotę i wysoką wydajność.

### <a name="cost-effective"></a>Niskie koszty

Za pomocą linku Azure Synapse można uzyskać zoptymalizowane pod kątem kosztów, w pełni zarządzane rozwiązanie do analizy operacyjnej. Eliminuje to dodatkowe warstwy magazynu i obliczenia wymagane w tradycyjnych potokach ETL do analizowania danych operacyjnych. 

Azure Cosmos DB magazyn analityczny jest zgodny z modelem cen opartym na zużyciu, który jest oparty na magazynowaniu danych i operacjach analitycznych odczytu/zapisu oraz wykonywanych zapytań. Nie wymaga to obsługi żadnej przepływności, ponieważ jest to aktualne w przypadku obciążeń transakcyjnych. Uzyskiwanie dostępu do danych przy użyciu wysoce elastycznych aparatów obliczeniowych z usługi Azure Synapse Analytics sprawia, że całkowity koszt magazynu i wydajności obliczeniowej jest bardzo wydajny.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analiza dla lokalnie dostępnych, globalnie dystrybuowanych, wieloregionowych zapisów

Można efektywnie uruchamiać zapytania analityczne względem najbliższej kopii regionalnej danych w Azure Cosmos DB. Azure Cosmos DB zapewnia najnowocześniejszą funkcję do uruchamiania dystrybuowanych globalnie obciążeń analitycznych oraz obciążeń transakcyjnych w sposób aktywny.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Włącz scenariusze HTAP dla danych operacyjnych

Link Synapse łączy się ze sobą Azure Cosmos DB magazyn analityczny dzięki obsłudze środowiska uruchomieniowego usługi Azure Synapse Analytics. Ta Integracja umożliwia tworzenie rozwiązań natywnych HTAP w chmurze (hybrydowe przetwarzanie transakcyjne/analityczne), które generują szczegółowe informacje na podstawie aktualizacji danych operacyjnych w czasie rzeczywistym w dużych zestawach. Odblokowuje nowe scenariusze biznesowe, aby zgłaszać alerty w oparciu o trendy na żywo, kompilować pulpity nawigacyjne niemal w czasie rzeczywistym i środowiska biznesowe na podstawie zachowania użytkowników.

### <a name="azure-cosmos-db-analytical-store"></a>Magazyn analityczny Azure Cosmos DB

Magazyn analityczny Azure Cosmos DB to ukierunkowana na kolumny reprezentacja danych operacyjnych w Azure Cosmos DB. Ten magazyn analityczny jest odpowiedni do szybkiego i ekonomicznego wykonywania zapytań dotyczących dużych zestawów danych operacyjnych bez kopiowania danych i wpływu na wydajność obciążeń transakcyjnych.

Magazyn analityczny automatycznie wybiera operacje wstawiania o wysokiej częstotliwości, aktualizacje, usunięcia w ramach obciążeń transakcyjnych w czasie niemal rzeczywistym, jako w pełni zarządzanej funkcji ("Automatyczna synchronizacja") Azure Cosmos DB. Nie jest wymagane żadne Źródło zmian ani ETL. 

Jeśli masz konto Azure Cosmos DB dystrybuowane globalnie, po włączeniu magazynu analitycznego dla kontenera będzie ono dostępne we wszystkich regionach dla tego konta. Aby uzyskać więcej informacji na temat magazynu analitycznego, zobacz artykuł [Omówienie magazynu analitycznego Azure Cosmos DB](analytical-store-introduction.md) .

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integracja z usługą Azure Synapse Analytics

Za pomocą linku Synapse możesz teraz łączyć się bezpośrednio z kontenerami Azure Cosmos DB z usługi Azure Synapse Analytics i uzyskiwać dostęp do magazynu analitycznego bez oddzielnych łączników. Usługa Azure Synapse Analytics obecnie obsługuje link Synapse z [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) i [Synapse SQL Server](../synapse-analytics/sql/on-demand-workspace-overview.md).

Możesz wykonywać zapytania dotyczące danych z Azure Cosmos DBego magazynu analitycznego jednocześnie, z międzyoperacyjności w różnych porach przebiegu analizy obsługiwanych przez usługę Azure Synapse Analytics. Do analizowania danych operacyjnych nie są wymagane żadne dodatkowe przekształcenia danych. Możesz wykonywać zapytania i analizować dane z magazynu analitycznego przy użyciu:

* Synapse Apache Spark z pełną obsługą Scala, Python, SparkSQL i C#. Synapse Spark to centralne scenariusze dotyczące inżynierii danych i analizy danych

* Program SQL Server w języku T-SQL i obsługa znanych narzędzi analizy biznesowej (na przykład Power BI Premium itd.)

> [!NOTE]
> Usługa Azure Synapse Analytics umożliwia dostęp do magazynów analitycznych i transakcyjnych w kontenerze Azure Cosmos DB. Jeśli jednak chcesz uruchamiać analizę na dużą skalę lub skanować dane operacyjne, zalecamy użycie magazynu analitycznego, aby uniknąć wpływu na wydajność transakcji transakcyjnych.

> [!NOTE]
> Możesz uruchomić analizę z małymi opóźnieniami w regionie świadczenia usługi Azure, łącząc kontener Azure Cosmos DB z Synapse środowiska uruchomieniowego w tym regionie.

Ta Integracja umożliwia korzystanie z następujących scenariuszy HTAP dla różnych użytkowników:

* Inżynier analizy biznesowej, który chce modelować i publikować raport Power BI, aby uzyskać dostęp do danych operacyjnych na żywo w Azure Cosmos DB bezpośrednio za pomocą Synapse SQL.

* Analityk danych, który chce uzyskiwać wgląd w dane operacyjne w kontenerze Azure Cosmos DB, wykonując zapytania o Synapse SQL, Odczytaj dane w odpowiedniej skali i Połącz te wyniki z innymi źródłami danych.

* Analityk danych, który chce używać Synapse Spark, aby znaleźć funkcję ulepszania modelu i uczenia tego modelu bez złożonych zadań inżynieryjnych. Mogą również zapisywać wyniki wnioskowania dotyczącego modelu w Azure Cosmos DB na potrzeby oceny danych w czasie rzeczywistym za pomocą platformy Spark Synapse.

* Inżynier ds. danych, który chce udostępnić dane klientom, tworząc tabele SQL lub Spark za pośrednictwem kontenerów Azure Cosmos DB bez ręcznej procesów ETL.

Aby uzyskać więcej informacji o obsłudze środowiska uruchomieniowego usługi Azure Synapse Analytics dla Azure Cosmos DB, zobacz [Azure Synapse Analytics for Cosmos DB support](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Kiedy używać linku usługi Azure Synapse dla Azure Cosmos DB?

Łącze Synapse jest zalecane w następujących przypadkach:

* Jeśli jesteś Azure Cosmos DB klientem i chcesz uruchamiać analizy, analizy biznesowej i uczenia maszynowego na danych operacyjnych. W takich przypadkach łącze Synapse zapewnia bardziej zintegrowane środowisko analityczne bez wpływu na przepływność zainicjowaną przez magazyn transakcyjny. Przykład:

  * Jeśli używasz analizy lub analizy biznesowej Azure Cosmos DB danych operacyjnych bezpośrednio przy użyciu osobnych łączników, lub

  * W przypadku uruchamiania procesów ETL w celu wyodrębnienia danych operacyjnych do oddzielnego systemu analitycznego.
 
W takich przypadkach łącze Synapse zapewnia bardziej zintegrowane środowisko analityczne bez wpływu na przepływność zainicjowaną przez magazyn transakcyjny.

Łącze Synapse nie jest zalecane, jeśli szukasz tradycyjnych wymagań magazynu danych, takich jak wysoka współbieżność, zarządzanie obciążeniami i trwałość agregacji w wielu źródłach danych. Aby uzyskać więcej informacji, zobacz [typowe scenariusze, które mogą być obsługiwane za pomocą linku usługi Azure Synapse dla Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Ograniczenia

* Obecnie usługa Azure Synapse Link dla usługi Azure Cosmos DB jest obsługiwana w przypadku interfejsu API SQL oraz interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Nie jest obsługiwana w przypadku interfejsu API Gremlin i interfejsu API tabel. Pomoc techniczna dla interfejs API Cassandra jest w prywatnej wersji zapoznawczej. Aby uzyskać więcej informacji, skontaktuj się z [zespołem usługi Azure Synapse](mailto:cosmosdbsynapselink@microsoft.com).  

* Obecnie magazyn analityczny można włączyć tylko dla nowych kontenerów. Aby używać magazynu analitycznego dla istniejących kontenerów, Migruj dane z istniejących kontenerów do nowych kontenerów za pomocą [narzędzi do migracji Azure Cosmos DB](cosmosdb-migrationchoices.md). Link Synapse można włączyć na nowych i istniejących kontach Azure Cosmos DB.

* W przypadku kontenerów z włączonym magazynem analitycznym automatyczne tworzenie kopii zapasowych i przywracanie danych w magazynie analitycznym nie jest obecnie obsługiwane. Gdy łącze Synapse jest włączone na koncie bazy danych, Azure Cosmos DB będzie nadal automatycznie [tworzyć kopie zapasowe](./online-backup-and-restore.md) danych w magazynie transakcyjnym (tylko) kontenerów z zaplanowanym interwałem tworzenia kopii zapasowych, tak jak zawsze. Należy pamiętać, że gdy kontener z włączonym magazynem analitycznym zostanie przywrócony do nowego konta, kontener zostanie przywrócony tylko z magazynem transakcyjnym i nie włączono magazynu analitycznego. 

* Uzyskiwanie dostępu do Sklepu Azure Cosmos DB Analytics z zainicjowaną obsługą SQL Synapse nie jest obecnie dostępne.

## <a name="pricing"></a>Cennik

Model rozliczeń łącza usługi Azure Synapse obejmuje koszty związane z korzystaniem z magazynu analitycznego Azure Cosmos DB i środowiska uruchomieniowego Synapse. Aby dowiedzieć się więcej, zapoznaj się z artykułami dotyczącymi [cen sklepu analitycznego Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) i [cennika usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz następujące dokumenty:

* [Omówienie magazynu analitycznego usługi Azure Cosmos DB](analytical-store-introduction.md)

* [Rozpoczynanie pracy z usługą Azure Synapse Link dla usługi Azure Cosmos DB](configure-synapse-link.md)
 
* [Co jest obsługiwane w środowisku uruchomieniowym usługi Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Często zadawane pytania dotyczące usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Przypadki użycia usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-use-cases.md)