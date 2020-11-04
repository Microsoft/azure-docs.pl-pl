---
title: Przypadki użycia analiz w czasie rzeczywistym za pomocą linku usługi Azure Synapse dla Azure Cosmos DB
description: Dowiedz się, w jaki sposób usługa Azure Synapse link dla Azure Cosmos DB jest używana w analizie łańcucha dostaw, prognozowanie, raportowanie, personalizacji w czasie rzeczywistym i konserwacja predykcyjna IOT.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: 7621a19b510d302454465f9fcbacec27a14dddf9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340179"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Usługa Azure Synapse Link dla usługi Azure Cosmos DB: Przypadki użycia analizy prawie w czasie rzeczywistym
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Link Azure Synapse](synapse-link.md) dla Azure Cosmos DB to funkcja natywnego hybrydowego przetwarzania transakcyjnego i analitycznego (HTAP) w chmurze, która umożliwia uruchamianie analizy niemal w czasie rzeczywistym za pośrednictwem danych operacyjnych. Link Synapse tworzy ścisłą integrację między usługami Azure Cosmos DB i Azure Synapse Analytics.

Chcesz wiedzieć można zrozumieć, jakie przypadki użycia w branży mogą korzystać z tej natywnej możliwości HTAP w chmurze na potrzeby analiz niemal w czasie rzeczywistym za pośrednictwem danych operacyjnych. Poniżej przedstawiono trzy typowe przypadki użycia dla Azure Cosmos DB usługi Azure Synapse:

* Analiza łańcucha dostaw, prognozowanie & raportowanie
* Personalizacja w czasie rzeczywistym
* Konserwacja predykcyjna, wykrywanie anomalii w scenariuszach IOT

> [!NOTE]
> Link Synapse platformy Azure dla Azure Cosmos DB jest celem scenariusza, w którym zespoły korporacyjne chcą uruchamiać analizę niemal w czasie rzeczywistym. Te analizy są uruchamiane bez przejmowania danych operacyjnych wygenerowanych w ramach transakcyjnych aplikacji opartych na Azure Cosmos DB. Nie zastępuje to potrzeby osobnego magazynu danych, jeśli istnieją tradycyjne wymagania dotyczące magazynu danych, takie jak zarządzanie obciążeniami, duże współbieżność i agregacje trwałości w wielu źródłach danych.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Analiza łańcucha dostaw, prognozowanie & raportowanie

Badania badawcze pokazują, że osadzanie analizy danych Big Data w operacjach łańcucha dostaw prowadzi do ulepszeń czasu dostaw między cyklami i wydajności łańcucha dostaw.

Producenci są dołączani do natywnych technologii w chmurze w celu wyróżnienia ograniczeń starszych systemów planowania zasobów przedsiębiorstwa (ERP) i zarządzania łańcuchem dostaw (SCM). W przypadku łańcuchów dostaw generują coraz większe ilości danych operacyjnych co minutę (zamówienie, przesyłanie, dane transakcji), producenci potrzebują operacyjnej bazy danych. Ta operacyjna baza danych powinna być skalowana w celu obsługi woluminów danych, a także na platformie analitycznej, aby uzyskać dostęp do poziomu analizy kontekstowej w czasie rzeczywistym.

W poniższej architekturze przedstawiono możliwości korzystania z Azure Cosmos DB jako natywnej, operacyjnej bazy danych i Synapse w chmurze w analizie łańcucha dostaw:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Link Synapse platformy Azure dla Azure Cosmos DB w analizie łańcucha dostaw " border="false":::

W oparciu o poprzednią architekturę można osiągnąć następujące przypadki użycia z linkiem Synapse Azure Cosmos DB:

* **Przygotuj potok & uczenie predykcyjne:** Generowanie szczegółowych informacji na temat danych operacyjnych w łańcuchu dostaw przy użyciu funkcji Uczenie maszynowe. W ten sposób można obniżyć zapasy, koszty operacji i skrócić czasy przesyłania zamówienia do klientów.

  Link Synapse umożliwia analizowanie zmian danych operacyjnych w Azure Cosmos DB bez żadnych ręcznych procesów ETL. Pozwala to zaoszczędzić od dodatkowych kosztów, opóźnień i złożoności operacyjnej. Link Synapse umożliwia inżynierom danych i analitykom danych tworzenie niezawodnych potoków predykcyjnych:

  * Wykonaj zapytania dotyczące danych operacyjnych z magazynu analitycznego Azure Cosmos DB, wykorzystując natywną integrację z pulami Apache Spark w usłudze Azure Synapse Analytics. Możesz wykonywać zapytania dotyczące danych w interaktywnym notesie lub zaplanowanych zadaniach zdalnych bez złożonych zadań inżynieryjnych.

  * Kompiluj Machine Learning (ML) modele z algorytmami Spark ML i integracją z platformą Azure ML w usłudze Azure Synapse Analytics.

  * Zapisuj wyniki po wnioskach o modelu do Azure Cosmos DB na potrzeby oceny działania niemal w czasie rzeczywistym.

* **Raportowanie operacyjne:** Zespoły łańcucha dostaw potrzebują elastycznych i niestandardowych raportów w czasie rzeczywistym i dokładnych danych operacyjnych. Te raporty są wymagane do uzyskania widoku migawki dotyczącego wydajności łańcucha dostaw, zyskowności i produktywności. Pozwala ona analitykom danych i innym najważniejszym udziałowcom stale przeanalizować działalność i identyfikować obszary, które można dostosować w celu zmniejszenia kosztów operacyjnych. 

  Link Synapse dla Azure Cosmos DB włącza bogate scenariusze analizy biznesowej (BI)/Reporting:

  * Wykonywanie zapytań dotyczących danych operacyjnych z magazynu analitycznego Azure Cosmos DB przy użyciu natywnej integracji z programem Synapse SQL Server i pełnym wyrazistości języka T-SQL.

  * Modelowanie i publikowanie pulpitów nawigacyjnych usługi Power BI za pośrednictwem Azure Cosmos DB za pośrednictwem programu Synapse SQL Server w przypadku znanych narzędzi analizy biznesowej. Na przykład Azure Analysis Services, Power BI Premium itd.

Poniżej przedstawiono kilka wskazówek dotyczących integracji danych na potrzeby usługi Batch & przesyłania strumieniowego danych w Azure Cosmos DB:

* **Integracja danych wsadowych & aranżacji:** W przypadku łańcuchów dostaw zapewniających bardziej skomplikowaną platformę danych łańcucha dostaw należy zintegrować z różnymi źródłami danych i formatami. Usługa Azure Synapse jest wbudowana w taki sam aparat integracji danych i środowisko jak Azure Data Factory. Ta Integracja umożliwia inżynierom danych tworzenie rozbudowanych potoków danych bez oddzielnego aparatu aranżacji:

  * Przenieś dane z 85 + obsługiwane źródła danych, aby [Azure Cosmos DB z Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Pisz nieodpłatne potoki ETL do Azure Cosmos DB, w tym [mapowania relacyjne i hierarchiczne oraz mapowanie przepływów danych](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Przetwarzanie &j integracji danych przesyłanych strumieniowo:** Wraz ze wzrostem zawartości przemysłowej (czujników śledzenia zasobów z "piętra do sklepu", połączonych flot logistycznych itp.) istnieje możliwość rozłożenia danych w czasie rzeczywistym, które muszą być zintegrowane z tradycyjnym wolnym przeniesieniem danych do generowania szczegółowych informacji. Azure Stream Analytics to zalecana usługa do przesyłania strumieniowego ETL i przetwarzania na platformie Azure z [szeroką gamę scenariuszy](../stream-analytics/streaming-technologies.md). Azure Stream Analytics obsługuje [Azure Cosmos dB jako natywny obiekt ujścia danych](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Personalizacja w czasie rzeczywistym

Sprzedawcy detaliczni muszą tworzyć bezpieczne i skalowalne rozwiązania do obsługi handlu elektronicznego, które spełniają wymagania klientów i firmy. Te rozwiązania z zakresu handlu elektronicznego muszą angażować klientów za pośrednictwem dostosowanych produktów i ofert, szybko i bezpiecznie przetwarzać transakcje oraz skupić się na realizacji usługi klienta. Azure Cosmos DB wraz z najnowszym linkiem Synapse dla Azure Cosmos DB umożliwia detalistom generowanie spersonalizowanych zaleceń dla klientów w czasie rzeczywistym. Używają one ustawień spójności o małym opóźnieniu i możliwość dostosowania na potrzeby natychmiastowego wglądu w dane, jak pokazano w następującej architekturze:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Link usługi Azure Synapse dla Azure Cosmos DB w trybie personalizacji w czasie rzeczywistym" border="false":::

Azure Cosmos DB Synapse:

* **Przygotuj potok & uczenie predykcyjne:** Możesz generować szczegółowe informacje o danych operacyjnych w ramach jednostek gospodarczych lub segmentów klientów przy użyciu modeli Synapse Spark i Machine Learning. Pozwala to na spersonalizowane dostarczanie do docelowych segmentów klientów, predykcyjne środowiska użytkowników końcowych i ukierunkowane marketingowe w celu dopasowania do wymagań użytkowników końcowych.

## <a name="iot-predictive-maintenance"></a>Konserwacja predykcyjna IOT

Innowacje przemysłowe mają znacząco zmniejszone przestoje maszyn i zwiększoną ogólną wydajność we wszystkich dziedzinach branżowych. Jednym z tych innowacji jest analiza konserwacji predykcyjnej dla maszyn na granicy chmury.

Poniżej przedstawiono architekturę wykorzystującą natywne możliwości HTAP w chmurze w usłudze Azure Synapse link Azure Cosmos DB w ramach konserwacji predykcyjnej IoT:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Link Synapse na platformie Azure dla Azure Cosmos DB w usłudze IOT — konserwacja predykcyjna" border="false" :::

Link Synapse dla przypadków użycia Azure Cosmos DB:

* **Przygotuj potok & uczenie predykcyjne:** Historyczne dane operacyjne z czujników urządzeń IoT mogą służyć do uczenia modeli predykcyjnych, takich jak wykrywacze anomalii. Te detektory anomalii są następnie wdrażane z powrotem do krawędzi na potrzeby monitorowania w czasie rzeczywistym. Taka pętla składa umożliwia ciągłe przeszkolenie modeli predykcyjnych.

* **Raportowanie operacyjne:** Wraz ze wzrostem inicjatyw cyfrowych sznurów firmowych firmy zbierają ogromną ilość danych operacyjnych z dużej liczby czujników w celu utworzenia kopii cyfrowej każdej maszyny. Te dane mogą mieć świadomość trendów nad danymi historycznymi, a nie tylko w przypadku aplikacji w czasie rzeczywistym za pośrednictwem najnowszych danych.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Przykładowy scenariusz: HTAP dla Azure Cosmos DB

W przypadku niemal dekady Azure Cosmos DB był używany przez tysiące klientów dla aplikacji o znaczeniu strategicznym, które wymagają elastycznej skali, globalnej dystrybucji gotowe, wieloregionowej replikacji zapisu w przypadku małych opóźnień i wysokiej dostępności obydwu odczytów & zapisów w ich transakcyjnych obciążeniach.
 
Na poniższej liście przedstawiono omówienie różnych wzorców obciążeń, które są obsługiwane w przypadku danych operacyjnych przy użyciu Azure Cosmos DB:

* Aplikacje w czasie rzeczywistym & usług
* Przetwarzanie strumienia zdarzeń
* Pulpity nawigacyjne analizy biznesowej
* Analiza danych big data
* Uczenie maszynowe

Usługa Azure Synapse Link umożliwia Azure Cosmos DB nie tylko w przypadku obciążeń transakcyjnych, ale również umożliwia wykonywanie obciążeń analitycznych niemal w czasie rzeczywistym za pośrednictwem historycznych danych operacyjnych. Ma to miejsce bez wymagań ETL i gwarantowanej izolacji wydajności z obciążeń transakcyjnych.

Na poniższej ilustracji przedstawiono wzorce obciążeń przy użyciu Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="link Azure Synapse dla wzorców obciążenia Azure Cosmos DB" border="false":::

Poinformuj nas o tym, że CompanyXYZ firmy handlu elektronicznego z operacjami globalnymi w 20 krajach/regionach, aby zilustrować korzyści wynikające z wyboru Azure Cosmos DB jako pojedynczej bazy danych w czasie rzeczywistym, co pozwala na globalne i analityczne wymagania dotyczące platformy zarządzania zapasami.

* Podstawowa firma CompanyXYZ zależy od systemu zarządzania spisem, dlatego dostępność & niezawodność to wymagania podstawowe filar. Zalety korzystania z Azure Cosmos DB:

  * Dzięki głębokiej integracji z infrastrukturą platformy Azure i niewidocznym wieloregionowym zapisem, replikacja globalna Azure Cosmos DB zapewnia wiodącą w branży [99,999% wysoką dostępność](high-availability.md) na poziomie regionalnym.

* Partnerzy łańcucha dostaw w CompanyXYZ mogą znajdować się w różnych lokalizacjach geograficznych, ale mogą mieć jeden widok spisu produktów na całym świecie w celu obsługi ich operacji lokalnych. Obejmuje to konieczność odczytywania aktualizacji dokonanych przez innych partnerów łańcucha dostaw w czasie rzeczywistym. Jak również można wprowadzać aktualizacje bez obaw o konflikty z innymi partnerami przy dużej przepływności. Zalety korzystania z Azure Cosmos DB:

  * W przypadku unikatowego wieloregionowego zapisu protokołu replikacji i niezawolnych, zoptymalizowanych pod kątem zapisu magazyn transakcyjny, Azure Cosmos DB gwarantuje mniej niż 10 ms opóźnień zarówno dla indeksowanych odczytów, jak i zapisów w 99 percentylu globalnie.

  * Wysoką przepływność w przypadku przesyłania strumieniowego danych w [czasie rzeczywistym](index-policy.md) w magazynie transakcyjnym w usłudze Batch, w której przesyłane są dane &.

  * Azure Cosmos DB magazyn transakcyjny oferuje trzy więcej opcji niż dwa skrajnie silnych i ostatecznych poziomów spójności w celu osiągnięcia [kompromisów dotyczących dostępności i wydajności](./consistency-levels.md) najbliżej potrzeb firmy.

* Partnerzy łańcucha dostaw CompanyXYZ mają wysoce fluktuacje wzorców ruchu od setek do milionów żądań/s, a tym samym platformę zarządzania zapasami muszą zająć się nieoczekiwanym burstiness w ruchu.  Zalety korzystania z Azure Cosmos DB:

  * Magazyn transakcyjny Azure Cosmos DB obsługuje elastyczną skalowalność magazynu i przepływność przy użyciu partycjonowania poziomego. Kontenery i bazy danych skonfigurowane w trybie autopilotażu mogą automatycznie i natychmiast skalować zainicjowaną przepływność na podstawie potrzeb aplikacji, bez wywierania wpływu na dostępność, opóźnienie, przepływność lub wydajność na całym świecie.

* CompanyXYZ musi nawiązywać bezpieczną platformę analityczną do przechowywania historycznych danych spisu systemu, aby umożliwić analizę i wgląd w szczegółowe dane dotyczące partnera łańcucha dostaw, jednostek i funkcji firmy. Platforma analityczna musi umożliwiać współpracę w systemie, tradycyjne przypadki użycia analizy biznesowej/raportowania, Zaawansowane przypadki użycia analizy i inteligentne rozwiązania, które są dostępne w ramach operacyjnych danych spisu. Zalety używania linku Synapse Azure Cosmos DB:

  * Korzystając z [Azure Cosmos DBego magazynu analitycznego](analytical-store-introduction.md), w pełni izolowany magazyn kolumn, link Synapse umożliwia analizę typu extract-transform-load (ETL) w [usłudze Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) względem globalnie dystrybuowanych danych operacyjnych na dużą skalę.  Analitycy biznesowi, inżynierowie danych i analityki danych mogą teraz korzystać z Synapse Spark lub Synapse SQL w sposób międzyoperacyjny, aby przeprowadzać niemal w czasie rzeczywistym potoki analizy biznesowej, analizy i uczenia maszynowego bez wpływu na wydajność transakcyjnych obciążeń w Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [zalety linku Synapse w Azure Cosmos DB](synapse-link.md) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz następujące dokumenty:

* [Link Synapse platformy Azure dla Azure Cosmos DB](synapse-link.md) 

* [Magazyn analityczny usługi Azure Cosmos DB](analytical-store-introduction.md)

* [Praca z linkiem usługi Azure Synapse dla Azure Cosmos DB](configure-synapse-link.md)

* [Często zadawane pytania dotyczące usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Apache Spark w usłudze Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Obsługa środowiska wykonawczego bezserwerowego programu SQL Server w usłudze Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)