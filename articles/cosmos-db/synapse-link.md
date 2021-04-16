---
title: Azure Synapse Link Azure Cosmos DB, korzyści i zastosowania
description: Dowiedz się więcej Azure Synapse linku Azure Cosmos DB. Synapse Link umożliwia uruchamianie analizy niemal w czasie rzeczywistym (HTAP) przy użyciu Azure Synapse Analytics danych operacyjnych w Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 123c443e1afaf8eaded7021b963b68b3d8a8f554
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483762"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Co to jest link usługa Azure Synapse Link dla usługi Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link for Azure Cosmos DB to natywna dla chmury funkcja hybrydowego przetwarzania transakcyjnego i analitycznego (HTAP), która umożliwia uruchamianie analizy niemal w czasie rzeczywistym danych operacyjnych w Azure Cosmos DB. Azure Synapse Link tworzy ścisłą, bezproblemową integrację między Azure Cosmos DB i Azure Synapse Analytics.

Korzystając Azure Cosmos DB magazynu [analitycznego](analytical-store-introduction.md), w pełni izolowanego magazynu kolumn, usługa Azure Synapse Link nie umożliwia [](../synapse-analytics/overview-what-is.md) analizy wyodrębniania, przekształcania i ładowania (ETL) w Azure Synapse Analytics względem danych operacyjnych na dużą skalę. Analitycy biznesowi, inżynierowie danych i analitycy danych mogą teraz używać usługi Synapse Spark lub Synapse SQL zamiennie, aby uruchamiać potoki analizy biznesowej, analizy i uczenia maszynowego niemal w czasie rzeczywistym. Można to osiągnąć bez wpływu na wydajność transakcyjnych obciążeń w usłudze Azure Cosmos DB. 

Na poniższej ilustracji przedstawiono integrację Azure Synapse Link z Azure Cosmos DB i Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Diagram architektury na Azure Synapse Analytics integracji z Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Korzyści

Aby analizować duże operacyjne zestawy danych przy jednoczesnym zminimalizowaniu wpływu na wydajność obciążeń transakcyjnych o krytycznym znaczeniu, tradycyjnie dane operacyjne w programie Azure Cosmos DB są wyodrębnione i przetwarzane przez potoki wyodrębniania, przekształcania i ładowania (ETL, Extract-Transform-Load). Potoki ETL wymagają wielu warstw ruchu danych, co znacznie zwiększa złożoność operacyjną i wpływa na wydajność obciążeń transakcyjnych. Zwiększa to również opóźnienie analizowania danych operacyjnych od czasu pochodzenia.

W porównaniu z tradycyjnymi rozwiązaniami opartymi na ETL, Azure Synapse Link for Azure Cosmos DB oferuje kilka zalet, takich jak:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Ograniczona złożoność dzięki użyciu zadań ETL do zarządzania

Azure Synapse Link umożliwia bezpośredni dostęp do Azure Cosmos DB analitycznych przy użyciu Azure Synapse Analytics bez złożonego ruchu danych. Wszelkie aktualizacje danych operacyjnych są widoczne w magazynie analitycznym niemal w czasie rzeczywistym bez zadań ETL ani zestawienia zmian. Analizę na dużą skalę można uruchamiać w magazynie analitycznym z Azure Synapse Analytics, bez konieczności dodatkowego przekształcania danych.

### <a name="near-real-time-insights-into-your-operational-data"></a>Wgląd w dane operacyjne niemal w czasie rzeczywistym

Teraz możesz uzyskać szczegółowe informacje na temat danych operacyjnych niemal w czasie rzeczywistym przy użyciu usługi Azure Synapse Link. Systemy oparte na ETL mają zwykle większe opóźnienie podczas analizowania danych operacyjnych ze względu na wiele warstw potrzebnych do wyodrębniania, przekształcania i ładowania danych operacyjnych. Dzięki natywnej integracji Azure Cosmos DB magazynu analitycznego z Azure Synapse Analytics można analizować dane operacyjne w czasie niemal rzeczywistym, umożliwiając nowe scenariusze biznesowe. 

### <a name="no-impact-on-operational-workloads"></a>Brak wpływu na obciążenia operacyjne

Za pomocą usługi Azure Synapse Link można uruchamiać zapytania analityczne względem magazynu analitycznego usługi Azure Cosmos DB (oddzielnego magazynu kolumn), podczas gdy operacje transakcyjne są przetwarzane przy użyciu aprowizowanej przepływności dla obciążenia transakcyjnego (magazynu transakcyjnego opartego na wierszach).  Obciążenie analityczne jest obsługiwane niezależnie od transakcyjnego ruchu obciążenia bez zużywania żadnej przepływności aprowizowanej dla danych operacyjnych.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Zoptymalizowany pod kątem obciążeń analitycznych na dużą skalę

Azure Cosmos DB magazyn analityczny jest zoptymalizowany pod kątem zapewnienia skalowalności, elastyczności i wydajności dla obciążeń analitycznych bez żadnych zależności od czasów wykonywania obliczeń. Technologia magazynu jest samodzielnie zarządzana w celu optymalizacji obciążeń analitycznych. Dzięki wbudowanej obsłudze Azure Synapse Analytics dostęp do tej warstwy magazynu zapewnia prostotę i wysoką wydajność.

### <a name="cost-effective"></a>Niskie koszty

Usługa Azure Synapse Link umożliwia uzyskiwanie zoptymalizowanego pod kątem kosztów, w pełni zarządzanego rozwiązania do analizy operacyjnej. Eliminuje to dodatkowe warstwy magazynu i mocy obliczeniowej wymagane w tradycyjnych potokach ETL do analizowania danych operacyjnych. 

Azure Cosmos DB magazynu analitycznego jest zgodny z modelem cenowym opartym na zużyciu, który jest oparty na magazynie danych oraz wykonywanych analitycznych operacjach odczytu/zapisu i zapytaniach. Nie wymaga aprowizować żadnej przepływności, tak jak obecnie w przypadku obciążeń transakcyjnych. Uzyskiwanie dostępu do danych przy użyciu wysoce elastycznych aparatów obliczeniowych z Azure Synapse Analytics sprawia, że całkowity koszt działania magazynu i zasobów obliczeniowych jest bardzo wydajny.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analiza lokalnie dostępnych, globalnie rozproszonych magazynów w wielu regionach

Zapytania analityczne można skutecznie uruchamiać względem najbliższej regionalnej kopii danych w Azure Cosmos DB. Azure Cosmos DB zapewnia najnowocześniejsze możliwości uruchamiania globalnie rozproszonych obciążeń analitycznych wraz z obciążeniami transakcyjnych w sposób aktywny-aktywny.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Włączanie scenariuszy HTAP dla danych operacyjnych

Synapse Link łączy Azure Cosmos DB magazynu analitycznego z Azure Synapse środowiska uruchomieniowego analizy. Ta integracja umożliwia tworzenie natywnych dla chmury rozwiązań HTAP (hybrydowego przetwarzania transakcyjnego/analitycznego), które generują szczegółowe informacje na podstawie aktualizacji danych operacyjnych w czasie rzeczywistym w dużych zestawach danych. Odblokowuje nowe scenariusze biznesowe, aby zgłaszać alerty na podstawie trendów na żywo, tworzyć pulpity nawigacyjne niemal w czasie rzeczywistym i środowisko biznesowe na podstawie zachowania użytkownika.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB magazynu analitycznego

Azure Cosmos DB analityczny to zorientowana na kolumny reprezentacja danych operacyjnych w Azure Cosmos DB. Ten magazyn analityczny jest odpowiedni do szybkiego i ekonomicznego wykonywania zapytań dotyczących dużych zestawów danych operacyjnych bez kopiowania danych i wpływu na wydajność obciążeń transakcyjnych.

Magazyn analityczny automatycznie pobiera wstawianie, aktualizacje i usuwanie z dużą częstotliwością w obciążeniach transakcyjnych niemal w czasie rzeczywistym jako w pełni zarządzaną funkcję ("automatyczna synchronizacja") Azure Cosmos DB. Nie jest wymagany żaden kanał zmian ani ETL. 

Jeśli masz globalnie dystrybuowane konto Azure Cosmos DB, po włączeniu magazynu analitycznego dla kontenera będzie on dostępny we wszystkich regionach dla tego konta. Aby uzyskać więcej informacji na temat magazynu analitycznego, zobacz [Azure Cosmos DB omówienie magazynu analitycznego.](analytical-store-introduction.md)

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integracja z Azure Synapse Analytics

Dzięki Synapse Link można teraz bezpośrednio łączyć się z kontenerami usługi Azure Cosmos DB z Azure Synapse Analytics i uzyskać dostęp do magazynu analitycznego bez oddzielnych łączników. Azure Synapse Analytics obecnie obsługuje Synapse Link z [pulą SQL Apache Spark Synapse i](../synapse-analytics/spark/apache-spark-concepts.md) [bez serwera.](../synapse-analytics/sql/on-demand-workspace-overview.md)

Zapytania o dane z magazynu Azure Cosmos DB analitycznego można wielokrotnie Azure Synapse Analytics. Do analizowania danych operacyjnych nie są wymagane żadne dodatkowe przekształcenia danych. Zapytania dotyczące danych magazynu analitycznego i ich analizowanie można analizować przy użyciu:

* Synapse Apache Spark z pełną obsługą języka Scala, Python, SparkSQL i C#. Synapse Spark jest centralnym elementem scenariuszy inżynierii danych i nauki o danych

* Bez serverless SQL pool with T-SQL language and support for familiar BI tools (for example, Power BI Premium, etc.)

> [!NOTE]
> Z Azure Synapse Analytics można uzyskać dostęp do magazynów analitycznych i transakcyjnych w kontenerze Azure Cosmos DB danych. Jeśli jednak chcesz uruchamiać analizy na dużą skalę lub skanowania danych operacyjnych, zalecamy użycie magazynu analitycznego, aby uniknąć wpływu na wydajność obciążeń transakcyjnych.

> [!NOTE]
> Analizę z małym opóźnieniem można uruchomić w regionie świadczenia usługi Azure, łącząc kontener usługi Azure Cosmos DB z środowiskiem uruchomieniowym usługi Synapse w tym regionie.

Ta integracja umożliwia korzystanie z następujących scenariuszy HTAP dla różnych użytkowników:

* Inżynier ds. bi, który chce modelować i publikować raport Power BI, aby uzyskać dostęp do danych operacyjnych na żywo w Azure Cosmos DB bezpośrednio za pośrednictwem Synapse SQL.

* Analityk danych, który chce uzyskać szczegółowe informacje z danych operacyjnych w kontenerze usługi Azure Cosmos DB, odpytując je za pomocą usługi Synapse SQL, odczytać dane na dużą skalę i połączyć te wyniki z innymi źródłami danych.

* Analityk danych, który chce używać usługi Synapse Spark, aby znaleźć funkcję ulepszać swój model i trenować go bez wykonywania złożonych operacji inżynierii danych. Mogą również zapisywać wyniki wnioskowania po modelu w celu Azure Cosmos DB oceniania danych w czasie rzeczywistym za pośrednictwem platformy Spark Synapse.

* Inżynier danych, który chce, aby dane były dostępne dla użytkowników, tworząc tabele SQL lub Spark Azure Cosmos DB kontenerach bez ręcznych procesów ETL.

Aby uzyskać więcej informacji na Azure Synapse Analytics środowiska uruchomieniowego dla Azure Cosmos DB, zobacz Azure Synapse Analytics, aby [uzyskać Cosmos DB pomocy technicznej.](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Kiedy należy używać Azure Synapse Link dla Azure Cosmos DB?

Synapse Link zalecane w następujących przypadkach:

* Jeśli jesteś klientem Azure Cosmos DB i chcesz uruchamiać analizy, analizy biznesowe i uczenie maszynowe na danych operacyjnych. W takich Synapse Link zapewnia bardziej zintegrowane środowisko analizy bez wpływu na przepływność aprowizowanej magazynu transakcyjnego. Na przykład:

  * Jeśli obecnie uruchamiasz analizę lub analizę bi na Azure Cosmos DB danych operacyjnych przy użyciu oddzielnych łączników lub

  * Jeśli uruchamiasz procesy ETL w celu wyodrębnienia danych operacyjnych do oddzielnego systemu analitycznego.
 
W takich Synapse Link zapewnia bardziej zintegrowane środowisko analizy bez wpływu na aprowizowanie przepływności magazynu transakcyjnego.

Synapse Link nie jest zalecane, jeśli szukasz tradycyjnych wymagań magazynu danych, takich jak wysoka współbieżność, zarządzanie obciążeniami i trwałość agregacji w wielu źródłach danych. Aby uzyskać więcej informacji, zobacz [typowe scenariusze, które mogą być obsługiwane za](synapse-link-use-cases.md)pomocą Azure Synapse Link dla Azure Cosmos DB .

## <a name="limitations"></a>Ograniczenia

* Usługa Azure Synapse Link dla usługi Azure Cosmos DB jest obsługiwana w przypadku interfejsu API SQL oraz interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Nie jest ona obsługiwana w przypadku interfejsów API, interfejsów API interfejs API Cassandra i interfejs API tabel.

* Magazyn analityczny można włączyć tylko dla nowych kontenerów. Aby użyć magazynu analitycznego dla istniejących kontenerów, przemigruj dane z istniejących kontenerów do nowych kontenerów przy [użyciu Azure Cosmos DB migracji.](cosmosdb-migrationchoices.md) Możesz włączyć obsługę Synapse Link nowych i istniejących Azure Cosmos DB kont.

* W przypadku kontenerów z włączonym magazynem analitycznym automatyczne tworzenie kopii zapasowych i przywracanie danych w magazynie analitycznym nie jest obecnie obsługiwane. Gdy Synapse Link na koncie bazy danych, program Azure Cosmos DB będzie [](./online-backup-and-restore.md) nadal automatycznie tworzyć kopie zapasowe danych w magazynie transakcyjnym (tylko) kontenerów w zaplanowanych interwałach tworzenia kopii zapasowych, jak zawsze. Należy pamiętać, że po przywróceniu kontenera z włączonym magazynem analitycznym do nowego konta kontener zostanie przywrócony tylko z magazynem transakcyjnym i bez włączonego magazynu analitycznego.

* Dostęp do magazynu Azure Cosmos DB analytics za pomocą Synapse SQL jest obecnie niedostępny.

## <a name="security"></a>Zabezpieczenia

Synapse Link umożliwia uruchamianie analiz niemal w czasie rzeczywistym dotyczących danych o kluczowym znaczeniu w Azure Cosmos DB. Ważne jest, aby mieć pewność, że krytyczne dane biznesowe są bezpiecznie przechowywane zarówno w magazynach transakcyjnych, jak i analitycznych. Azure Synapse Link for Azure Cosmos DB została zaprojektowana w celu spełnienia tych wymagań dotyczących zabezpieczeń za pomocą następujących funkcji:

* **Izolacja sieci przy użyciu prywatnych punktów końcowych** — możesz niezależnie kontrolować dostęp sieciowy do danych w magazynach transakcyjnych i analitycznych. Izolacja sieci odbywa się przy użyciu oddzielnych zarządzanych prywatnych punktów końcowych dla każdego magazynu w zarządzanych sieciach wirtualnych w Azure Synapse roboczych. Aby dowiedzieć się więcej, zobacz artykuł [Konfigurowanie prywatnych punktów końcowych dla magazynu analitycznego.](analytical-store-private-endpoints.md)

* **Szyfrowanie danych za pomocą kluczy** zarządzanych przez klienta — możesz bezproblemowo szyfrować dane w magazynach transakcyjnych i analitycznych przy użyciu tych samych kluczy zarządzanych przez klienta w sposób automatyczny i przezroczysty. Aby dowiedzieć się więcej, zobacz artykuł [Konfigurowanie kluczy zarządzanych przez](how-to-setup-cmk.md) klienta.

* **Bezpieczne zarządzanie kluczami** — uzyskiwanie dostępu do danych w magazynie analitycznym z bezserowych pul SQL usług Synapse Spark i Synapse wymaga zarządzania kluczami Azure Cosmos DB w ramach Synapse Analytics roboczych. Zamiast używać kluczy kont Azure Cosmos DB w tekście w zadaniach Platformy Spark lub skryptach SQL, usługa Azure Synapse Link zapewnia bezpieczniejsze możliwości.

  * W przypadku korzystania z bez serwera pul SQL usługi Synapse można odpytować magazyn analityczny usługi Azure Cosmos DB, wstępnie tworząc poświadczenia SQL przechowujące klucze kont i odwołując się do nich w `OPENROWSET` funkcji . Aby dowiedzieć się więcej, zobacz [artykuł Query with a serverless SQL pool (Wykonywanie](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) zapytań przy użyciu bez serwera puli SQL) Azure Synapse Link.

  * W przypadku korzystania z usługi Synapse Spark klucze kont można przechowywać w połączonych obiektach usługi, które wskazują bazę Azure Cosmos DB i odwołują się do tego w konfiguracji platformy Spark w czasie wykonywania. Aby dowiedzieć się więcej, zobacz [Kopiowanie danych do dedykowanej puli SQL przy użyciu Apache Spark](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) artykułu.


## <a name="pricing"></a>Ceny

Model rozliczeń usługi Azure Synapse Link obejmuje koszty ponoszone przy użyciu magazynu Azure Cosmos DB analitycznego i środowiska uruchomieniowego usługi Synapse. Aby dowiedzieć się więcej, zobacz [cennik Azure Cosmos DB magazynu analitycznego](analytical-store-introduction.md#analytical-store-pricing) i [Azure Synapse Analytics cennik.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz następujące dokumenty:

* [Azure Cosmos DB magazynu analitycznego — omówienie](analytical-store-introduction.md)

* [Rozpoczynanie pracy z usługą Azure Synapse Link dla usługi Azure Cosmos DB](configure-synapse-link.md)
 
* [Co jest obsługiwane w środowisku uruchomieniowym usługi Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Często zadawane pytania dotyczące usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Przypadki użycia usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-use-cases.md)
