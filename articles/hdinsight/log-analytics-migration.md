---
title: Migrowanie danych usługi Log Analytics na Azure HDInsight
description: Dowiedz się więcej o zmianach w Azure Monitor i najlepszych rozwiązaniach dotyczących korzystania z nowych tabel.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741808"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Przewodnik migracji usługi Log Analytics Azure HDInsight klastrów

 Azure HDInsight to gotowa do użycia w przedsiębiorstwie usługa klastra zarządzanego. Ta usługa uruchamia platformy analizy typu open source, takie jak Apache Spark, Hadoop, HBase i Kafka na platformie Azure. Azure HDInsight została zintegrowana z innymi usługami platformy Azure, aby umożliwić klientom lepsze zarządzanie aplikacjami do analizy danych big data.

Usługa Log Analytics udostępnia narzędzie w Azure Portal do edytowania i uruchamiania zapytań dziennika. Zapytania pochodzą z danych zebranych przez Azure Monitor i interakcyjnie analizują wyniki. Klienci mogą używać zapytań usługi Log Analytics do pobierania rekordów, które spełniają określone kryteria. Mogą również używać zapytań do identyfikowania trendów, analizowania wzorców i zapewnienia wglądu w dane.

Azure HDInsight włączono integrację z usługą Log Analytics w 2017 r. Klienci usługi HDInsight szybko przyjmowali tę funkcję do monitorowania klastrów usługi HDInsight i wykonywania zapytań o dzienniki w klastrach. W czasie, gdy wdrożenie tej funkcji wzrosło, klienci podali opinię na temat integracji:

- Klienci nie mogą zdecydować, które dzienniki mają być przechowywane, a przechowywanie wszystkich dzienników może stać się kosztowne.
- Bieżące dzienniki schematów usługi HDInsight nie są zgodne ze spójnymi konwencjami nazewnictwa, a niektóre tabele powtarzają się.
- Klienci chcą, aby dostępny w tym miejscu pulpit nawigacyjny umożliwia łatwe monitorowanie wskaźników KPI klastrów usługi HDInsight.
- Klienci muszą przejść do usługi Log Analytics, aby uruchamiać proste zapytania.

## <a name="solution-overview"></a>Omówienie rozwiązania

Biorąc pod uwagę opinie klientów, zespół Azure HDInsight zainwestował w integrację z Azure Monitor. Ta integracja umożliwia:

- Nowy zestaw tabel w obszarze roboczym usługi Log Analytics klientów. Nowe tabele są dostarczane za pośrednictwem nowego potoku usługi Log Analytics.
- Wyższa niezawodność
- Szybsze dostarczanie dzienników
- Grupowanie tabel oparte na zasobach i zapytania domyślne

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Zalety nowej integracji Azure Monitor integracji

Ten dokument zawiera opis zmian w integracji Azure Monitor i najlepsze rozwiązania dotyczące korzystania z nowych tabel.

**Przeprojektowane schematy:** Formatowanie schematu dla nowej Azure Monitor jest lepiej zorganizowane i łatwe do zrozumienia. Istnieje dwie trzecie mniej schematów do usunięcia jak najwięcej niejednoznaczności w starszych schematach, jak to możliwe.

**Rejestrowanie selektywne (dostępne wkrótce):** za pośrednictwem usługi Log Analytics są dostępne dzienniki i metryki. Aby pomóc Ci zaoszczędzić na kosztach monitorowania, udostępniamy nową funkcję selektywnego rejestrowania. Ta funkcja umożliwia włączanie i wyłączanie różnych dzienników i źródeł metryk. Dzięki tej funkcji będziesz płacić tylko za to, czego używasz.

**Integracja portalu klastra dzienników:** **okienko Dzienniki** jest nowe w portalu klastra usługi HDInsight. Każda osoba mająca dostęp do klastra może przejść do tego okienka, aby wysłać zapytanie do dowolnej tabeli, do których zasób klastra wysyła rekordy. Użytkownicy nie muszą już mieć dostępu do obszaru roboczego usługi Log Analytics, aby wyświetlić rekordy dla określonego zasobu klastra.

**Integracja portalu klastra szczegółowych informacji:** okienko **Szczegółowe informacje** jest również nowe w portalu klastra usługi HDInsight. Po włączeniu nowej integracji Azure Monitor klastra możesz  wybrać okienko Szczegółowe informacje. Zostanie automatycznie wypełniony pulpit nawigacyjny dzienników i metryk specyficznych dla typu klastra. Te pulpity nawigacyjne zostały odświeżone w poprzednich rozwiązaniach platformy Azure. Zapewniają one szczegółowe informacje na temat wydajności i kondycji klastra.

**Szczegółowe informacje na dużą skalę:** nowego skoroszytu usługi **Insights** na dużą skalę w portalu **usługi Azure Monitor** można użyć do monitorowania kondycji i wydajności klastrów w różnych subskrypcjach.

## <a name="customer-scenarios"></a>Scenariusze dla klientów

W poniższych sekcjach opisano, jak klienci mogą korzystać z nowej Azure Monitor w różnych scenariuszach. W [sekcji Activate a new Azure Monitor integration](#activate-a-new-azure-monitor-integration) (Aktywowanie nowej integracji aplikacji) opisano sposób aktywowania nowej integracji Azure Monitor integracji. Sekcja [Migrating from Azure Monitor Classic to the new Azure Monitor Integration (Migrowanie](#migrate-to-the-new-azure-monitor-integration) z wersji klasycznej do nowej integracji Azure Monitor) zawiera dodatkowe informacje dla użytkowników, którzy są zależni od Azure Monitor integracji.

> [!NOTE]
> Tylko klastry utworzone pod koniec września 2020 r. i później kwalifikują się do nowej integracji monitorowania platformy Azure.

## <a name="activate-a-new-azure-monitor-integration"></a>Aktywowanie nowej integracji Azure Monitor integracji 

> [!NOTE]
> Przed włączeniem nowej integracji musisz mieć utworzony obszar roboczy usługi Log Analytics w subskrypcji, do których masz dostęp. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego usługi Log Analytics, zobacz Tworzenie obszaru roboczego usługi [Log Analytics w Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Aktywuj nową integrację, przechodząc do strony portalu klastra i przewijając menu po lewej stronie, aż do sekcji **Monitorowanie.** W sekcji **Monitorowanie** wybierz pozycję **Monitorowanie integracji.** Następnie wybierz pozycję **Włącz** i możesz wybrać obszar roboczy usługi Log Analytics, do którego mają być wysyłane dzienniki. Po **wybraniu** obszaru roboczego wybierz pozycję Zapisz. 

### <a name="access-the-new-tables"></a>Uzyskiwanie dostępu do nowych tabel

Istnieją dwa sposoby uzyskiwania dostępu do nowych tabel. 

### <a name="approach-1"></a>Podejście 1:
Pierwszym sposobem uzyskania dostępu do nowych tabel jest obszar roboczy usługi Log Analytics. 

1. Przejdź do obszaru roboczego usługi Log Analytics wybranego po włączeniu integracji. 
2. Przewiń w dół menu po lewej stronie ekranu i wybierz pozycję **Dzienniki.** W edytorze zapytań dzienników zostanie wyświetlona lista wszystkich tabel w obszarze roboczym. 
3. Jeśli tabele są pogrupowane według **rozwiązania**, nowe tabele HDI znajdują się w sekcji **Zarządzanie dziennikami.** 
4. W przypadku grupowania tabel według **typu zasobu** tabele znajdują się w sekcji Klastry usługi **HDInsight,** jak pokazano na poniższej ilustracji. 

> [!NOTE]
> W tym procesie opisano sposób, w jaki uzyskiwano dostęp do dzienników w starej integracji. Wymaga to, aby użytkownik miał dostęp do obszaru roboczego.

### <a name="approach-2"></a>Podejście 2.

Drugi sposób uzyskiwania dostępu do nowych tabel jest za pośrednictwem dostępu do portalu klastra.
 
1. Przejdź do strony portalu klastra i przewiń menu po lewej stronie w dół do sekcji **Monitorowanie.** W tej sekcji zobaczysz okienko **Dzienniki.** 
2. Wybierz **pozycję Dzienniki.** Zostanie wyświetlony edytor zapytań Dzienniki. Edytor zawiera wszystkie dzienniki skojarzone z zasobem klastra. Dzienniki zostały wysłane do obszaru roboczego usługi Log Analytics po włączeniu integracji. Te dzienniki zapewniają dostęp oparty na zasobach (RBAC). Dzięki kontroli RBAC użytkownicy, którzy mają dostęp do klastra, ale nie do obszaru roboczego, mogą zobaczyć dzienniki skojarzone z klastrem.

Dla porównania na poniższych zrzutach ekranu przedstawiono starszy widok obszaru roboczego integracji i nowy widok obszaru roboczego integracji:

**Widok starszej wersji obszaru roboczego integracji**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Zrzut ekranu przedstawiający widok starszej wersji obszaru roboczego integracji." border="false":::

**Nowy widok obszaru roboczego integracji**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Zrzut ekranu przedstawiający nowy widok obszaru roboczego integracji." border="false":::

### <a name="use-the-new-tables"></a>Korzystanie z nowych tabel

Te integracje mogą ułatwić korzystanie z nowych tabel:

#### <a name="default-queries-to-use-with-new-tables"></a>Zapytania domyślne do użycia z nowymi tabelami

W edytorze zapytań dzienników ustaw przełącznik na wartość **Zapytania** nad listą tabel. Upewnij się, że zapytania  są grupowane według typu zasobu i że nie ma ustawionego filtru dla typu zasobu innego niż **klastry usługi HDInsight.** Na poniższej ilustracji pokazano, jak wyglądają wyniki pogrupowane według **typu** zasobu i przefiltrowane dla **klastrów usługi HDInsight.** Po prostu wybierz jeden z nich i pojawi się on w edytorze zapytań Dzienniki. Pamiętaj, aby przeczytać komentarze zawarte w zapytaniach, ponieważ niektóre wymagają wprowadzenia pewnych informacji, takich jak nazwa klastra, aby zapytanie zostało pomyślnie uruchomione.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Zrzut ekranu przedstawiający domyślny typ zasobu pogrupowanych wyników zapytania." border="true":::


#### <a name="create-your-own-queries"></a>Tworzenie własnych zapytań

Możesz wprowadzić własne zapytania w edytorze zapytań Dzienników. Zapytania używane w starych tabelach nie będą prawidłowe w nowych tabelach, ponieważ wiele nowych tabel ma nowe, udoskonalone schematy. Zapytania domyślne są doskonałymi odwołaniami do kształtowania zapytań w nowych tabelach.

#### <a name="insights"></a>Insights

Szczegółowe informacje to pulpity nawigacyjne wizualizacji specyficzne dla klastra wykonane przy użyciu [skoroszytów platformy Azure.](../azure-monitor/platform/workbooks-overview.md) Te pulpity nawigacyjne zapewniają szczegółowe wykresy i wizualizacje dotyczące sposobu działania klastra. Pulpity nawigacyjne zawierają sekcje dla każdego typu klastra, yarn, metryk systemowych i dzienników składników. Dostęp do pulpitu nawigacyjnego klastra można uzyskać, odwiedzając stronę klastra w  portalu, przewijając w dół do sekcji Monitorowanie i wybierając **okienko Szczegółowe** informacje. Pulpit nawigacyjny jest ładowany automatycznie, jeśli włączono nową integrację. Załaduj wykresy w ciągu kilku sekund podczas wykonywania zapytań w dziennikach.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Zrzut ekranu przedstawiający pulpit nawigacyjny wizualizacji.":::

#### <a name="custom-azure-workbooks"></a>Niestandardowe skoroszyty platformy Azure

Możesz tworzyć własne skoroszyty platformy Azure z niestandardowymi grafami i wizualizacjami. Na stronie portalu klastra przewiń w  dół do sekcji Monitorowanie i wybierz okienko **Skoroszyty** w menu po lewej stronie. Możesz rozpocząć korzystanie z pustego szablonu lub użyć jednego z szablonów w sekcji **Klastry usługi HDInsight.** Dla każdego typu klastra istnieje szablon. Szablony są przydatne, jeśli chcesz zapisać określone dostosowania, których nie zapewniają domyślne funkcje HDInsight Insights. Jeśli uważasz, że coś brakuje, możesz wysyłać żądania dotyczące nowych funkcji usługi HDInsight Insights.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Skoroszyty na dużą skalę dla nowych Azure Monitor integracji

Użyj naszego nowego skoroszytu na dużą skalę, aby uzyskać środowisko monitorowania wielu klastrów dla klastrów. Nasz skoroszyt na dużą skalę pokazuje, które klastry mają włączony potok monitorowania. Skoroszyt zapewnia również prosty sposób sprawdzania kondycji wielu klastrów jednocześnie. Aby wyświetlić ten skoroszyt:

1. Przejdź do **strony Azure Monitor** z Azure Portal głównej
2. Na stronie **Azure Monitor** wybierz pozycję **Centrum szczegółowych** informacji w **sekcji Szczegółowe** informacje.
3. Wybierz **pozycję Klastry usługi HDInsight** w **sekcji Analiza.**

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Zrzut ekranu przedstawiający skoroszyt w dużej skali." border="false":::

#### <a name="alerts"></a>Alerty

Alerty niestandardowe można dodawać do klastrów i obszarów roboczych w edytorze zapytań dziennika. Przejdź do edytora zapytań dzienników, wybierając okienko **Dzienniki** w portalu klastra lub obszaru roboczego. Uruchom zapytanie, a następnie wybierz pozycję **Nowa reguła alertu,** jak pokazano na poniższym zrzucie ekranu. Aby uzyskać więcej informacji, przeczytaj o [konfigurowaniu alertów](../azure-monitor/platform/alerts-log.md).

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Zrzut ekranu przedstawiający nowy alert reguły." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Migrowanie do nowej Azure Monitor integracji

Jeśli korzystasz z integracji klasycznej Azure Monitor Azure Monitor, po przełączeniu się do nowej integracji tabel należy wprowadzić pewne zmiany w nowych formatach tabel.

Aby włączyć nową Azure Monitor, wykonaj kroki opisane w sekcji [Aktywowanie](#activate-a-new-azure-monitor-integration) nowej Azure Monitor integracji.

### <a name="run-queries-in-log-analytics"></a>Uruchamianie zapytań w u użytkownikach usługi Log Analytics

Ponieważ nowy format tabeli różni się od poprzedniego, zapytania muszą zostać ponownie przepracowane, aby można było korzystać z naszych nowych tabel. Po włączeniu nowej integracji Azure Monitor można przeglądać tabele i schematy w celu zidentyfikowania pól używanych w starych zapytaniach.

Oferujemy tabelę [mapowania między](#appendix-table-mapping) starą tabelą a nową tabelą, aby ułatwić szybkie znajdowanie nowych pól, których należy użyć do migrowania pulpitów nawigacyjnych i zapytań.

**Zapytania domyślne:** Utworzono zapytania domyślne, które pokazują, jak używać nowych tabel w typowych sytuacjach. Zapytania domyślne pokazują również, jakie informacje są dostępne w poszczególnych tabelach. Dostęp do zapytań domyślnych można uzyskać, korzystając z instrukcji w sekcji [Domyślne zapytania](#default-queries-to-use-with-new-tables) do użycia z nowymi tabelami w tym artykule.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Aktualizowanie pulpitów nawigacyjnych dla klastrów usługi HDInsight

Jeśli masz wiele pulpitów nawigacyjnych do monitorowania klastrów usługi HDInsight, po włączeniu nowej integracji z usługą Azure Monitor należy dostosować zapytanie dotyczące tabeli. Nazwa tabeli lub nazwa pola może ulec zmianie w nowej integracji, ale uwzględniane są wszystkie informacje zawarte w starej integracji.

Zapoznaj się z [tabelą mapowania](#appendix-table-mapping) między starą tabelą/schematem a nową tabelą/schematem, aby zaktualizować zapytanie za pulpitami nawigacyjnymi.

#### <a name="out-of-box-dashboards"></a>Out-of-box dashboards 

Ulepszyliśmy również pulpity nawigacyjne na poziomie klastra. W prawym górnym rogu każdego grafu znajduje się przycisk umożliwiający wyświetlanie bazowego zapytania, które generuje informacje. Wykres to doskonały sposób na zapoznanie się ze sposobem efektywnego zapytania o nowe tabele. Aby uzyskać dostęp do pulpitów nawigacyjnych, możesz uzyskać dostęp, [](#insights) korzystając z instrukcji w sekcjach Szczegółowe informacje i W skali dla nowych Azure Monitor [integracji.](#at-scale-workbooks-for-new-azure-monitor-integrations)

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>Korzystanie z pulpitu nawigacyjnego monitorowania w skali usługi HDInsight

Jeśli korzystasz z pulpitu nawigacyjnego monitorowania usługi HDInsight, takiego jak HDInsight Spark Monitoring i HDInsight Interactive Monitoring, pracujemy nad zapewnieniem tych samych możliwości w witrynie Azure Monitor Portal.

Zobaczysz, że istnieje opcja klastrów usługi HDInsight w Azure Monitor.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Zrzut ekranu przedstawiający opcję HDInsight w Azure Monitor." border="false":::

Centrum Azure Monitor Portal zapewnia możliwość monitorowania wielu klastrów usługi HDInsight w jednym miejscu. Organizujemy klastry na podstawie typu obciążenia, aby można było zobaczyć typy takie jak Spark, HBase i Hive. Zamiast korzystać z wielu pulpitów nawigacyjnych, możesz teraz monitorować wszystkie klastry usługi HDInsight w tym widoku.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [sekcje Szczegółowe informacje](#insights) i Skoroszyty na dużą skalę dla nowych [Azure Monitor](#at-scale-workbooks-for-new-azure-monitor-integrations) integracji w tym artykule.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Włącz obie integracje, aby przyspieszyć migrację

Zarówno klasyczną, jak i nową integrację usługi Azure Monitor można aktywować jednocześnie w klastrze, który kwalifikuje się do obu integracji, aby szybko przeprowadzić migrację do nowej Azure Monitor klastra. Nowa integracja jest dostępna dla wszystkich klastrów utworzonych po połowie września 2020 r.

W ten sposób można łatwo wykonać porównanie obok siebie dla zapytań, których używasz.

### <a name="enabling-the-classic-integration"></a>Włączanie integracji klasycznej

Jeśli używasz klastra utworzonego po połowie września 2020 r., nowe środowisko portalu zostanie wyświetlony w portalu klastra. Aby włączyć nowy potok, możesz wykonać kroki opisane w sekcji [Aktywowanie](#activate-a-new-azure-monitor-integration) nowej Azure Monitor integracji. Aby aktywować integrację klasyczną w tym klastrze, przejdź do strony portalu klastra. Wybierz okienko **Monitorowanie integracji** w **sekcji Monitorowanie** w menu po lewej stronie strony portalu klastra. Wybierz **pozycję Skonfiguruj Azure Monitor integracji klastrów usługi HDInsight (wersja klasyczna).** Zostanie wyświetlony kontekst boczny z przełącznikiem umożliwiającym włączenie i wyłączenie klasycznej integracji monitorowania platformy Azure. 

> [!NOTE]
> Żadne dzienniki ani metryki z integracji klasycznej nie będą dostępne za pośrednictwem dzienników i strony szczegółowych informacji w portalu klastra. W tych lokalizacjach będą obecne tylko nowe dzienniki integracji i metryki.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Zrzut ekranu przedstawiający link dostępu do integracji klasycznej." border="false":::

Tworzenie nowych klastrów przy użyciu Azure Monitor integracji nie jest dostępne po 1 lipca 2021 r.

## <a name="release-and-support-timeline"></a>Oś czasu wydania i pomocy technicznej

- Klasyczna integracja monitorowania platformy Azure będzie niedostępna po 15 października 2021 r. Po tej dacie nie można włączyć klasycznej integracji monitorowania platformy Azure.
- Istniejące klasyczne integracje monitorowania platformy Azure będą nadal działać. Klasyczna integracja monitorowania platformy Azure będzie ograniczona. 
  - Problemy zostaną zbadane po przesłaniu biletu pomocy technicznej przez klientów.
  - Jeśli rozwiązanie wymaga zmiany obrazu, klienci powinni przejść do nowej integracji.
  - Nie będziemy poprawiać klasycznych klastrów integracji monitorowania platformy Azure z wyjątkiem krytycznych problemów z zabezpieczeniami.

## <a name="appendix-table-mapping"></a>Dodatek: Mapowanie tabel

Na poniższych wykresach przedstawiono mapowania tabel z klasycznej integracji monitorowania platformy Azure na nową. W **kolumnie Obciążenie** opisano, z którym obciążeniem jest skojarzona każda tabela. Wiersz **Nowa tabela** zawiera nazwę nowej tabeli. W **wierszu** Opis opisano typ dzienników/metryk, które będą dostępne w tej tabeli. Wiersz **Stara tabela** to lista wszystkich tabel z klasycznej integracji Azure Monitor, których dane będą teraz obecne w tabeli wymienionej w **wierszu Nowa** tabela.

> [!NOTE]
> Niektóre tabele są nowe i nie są oparte na starych tabelach.

## <a name="general-workload-tables"></a>Ogólne tabele obciążeń

| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Opis:** Ta tabela zawiera metryki systemowe zebrane z systemu Ambari. Metryki pochodzą teraz z każdego węzła w klastrze (z wyjątkiem węzłów krawędzi), a nie tylko z dwóch węzłów głównych. Każda metryka jest teraz kolumną, a każda metryka jest raportowa raz dla każdego rekordu.</li><li>Stara **tabela:** metrics \_ cpu nice \_ \_ cl, metrics \_ cpu system \_ \_ cl, metrics \_ cpu user \_ \_ cl, metrics \_ memory cache \_ \_ CL, metrics \_ memory swap \_ CL, metrics memory total \_ \_ \_ \_ CLmetrics \_ memory buffer \_ \_ CL, metrics \_ load \_ 1min \_ CL, metrics \_ load cpu \_ CL, metrics load nodes \_ \_ \_ \_ CL, metrics \_ load \_ procs \_ CL, metrics network in \_ \_ \_ CL, metrics \_ network out \_ \_ CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Opis:** Ta tabela zawiera alerty klastra Systemu Ambari z każdego węzła w klastrze (z wyjątkiem węzłów brzegowych). Każdy alert jest rekordem w tej tabeli.</li><li>**Stara tabela:** alerty klastra \_ metryk \_ \_ CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Opis:** ta tabela zawiera rekordy z dzienników inspekcji i uwierzytelniania systemu Ambari.</li><li>**Stara tabela:** log \_ ambari \_ audit \_ CL, log \_ auth \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Opis:** Ta tabela zawiera wszystkie rekordy z dziennika inspekcji ranger dla klastrów ESP.</li><li>**Stara tabela:** dzienniki inspekcji \_ ranger \_ \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Opis:** Ta tabela zawiera informacje o inspekcji węzłów bramy. Jest on w tym samym formacie co tabela w kolumnie Stare tabele. **Nadal znajduje się w sekcji Dzienniki niestandardowe.**</li><li>**Stara tabela:** brama \_ dzienników \_ Audit \_ CL</li></ul>|

## <a name="spark-workload"></a>Obciążenie platformy Spark

> [!NOTE]
> Tabele związane z aplikacją Platformy Spark zostały zastąpione 11 nowymi tabelami platformy Spark (począwszy od hdInsightSpark*), które zawierają bardziej szczegółowe informacje o obciążeniach platformy Spark.


| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Opis:** Ta tabela zawiera wszystkie dzienniki związane z usługą Spark i jej powiązanym składnikiem: Livy i Jupyter.</li><li>**Stara tabela:** log \_ livy, \_ CL, log \_ jupyter \_ CL, log \_ spark \_ CL, log \_ sparkappsexecutors \_ CL, log \_ sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach dla aplikacji platformy Spark, w tym czas przesyłania i ukończenia, identyfikator aplikacji i nazwa aplikacji. Jest to przydatne do śledzenia, kiedy aplikacje zostały uruchomione i ukończone. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach związanych z menedżerem bloków platformy Spark. Zawiera informacje, takie jak użycie pamięci funkcji wykonawczej.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach związane ze środowiskiem wykonywanym przez aplikację w programie, w tym z trybem wdrażania platformy Spark, wzorcem i informacjami o wykonawcy.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniu użycia funkcji wykonywania platformy Spark przez aplikację.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach, które nie mieszczą się w żadnej innej tabeli platformy Spark. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zadaniach Platformy Spark, w tym ich czas rozpoczęcia i zakończenia, wyniki i skojarzone etapy.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach zapytań Spark SQL, w tym informacje o ich planie i opisie oraz godziny rozpoczęcia i zakończenia.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach dla etapów platformy Spark, w tym czasy rozpoczęcia i zakończenia, stan niepowodzenia i szczegółowe informacje o wykonaniu.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Opis:** Ta tabela zawiera metryki wydajności dla etapów i zadań.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach dla zadań platformy Spark, w tym czas rozpoczęcia i ukończenia, skojarzone etapy, stan wykonywania i typ zadania.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Opis:** Ta tabela zawiera informacje o zdarzeniach dla notesów Jupyter Notebook.</li></ul>|

## <a name="hadoopyarn-workload"></a>Obciążenie Hadoop/YARN

| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Opis:** Ta tabela zawiera metryki JMX ze struktury Hadoop i YARN. Zawiera ona wszystkie te same metryki JMX co stare tabele dzienników niestandardowych oraz więcej metryk, które uznaliśmy za ważne. Dodaliśmy metryki Serwer osi czasu, Menedżer węzłów i Serwer historii zadań. Zawiera jedną metrykę na rekord.</li><li>Stara **tabela:** \_ metrics resourcemanager \_ clustermetrics \_ CL, metrics \_ resourcemanager \_ jvm \_ CL, \_ \_ metrics resourcemanager queue \_ root \_ CL, metrics \_ resourcemanager queue root \_ \_ \_ joblauncher \_ CL, metrics \_ resourcemanager queue root \_ CL, metrics resourcemanager queue root \_ \_ \_ CL, \_ metrics resourcemanager \_ queue root \_ \_ thriftsvr \_ CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Opis:** Ta tabela zawiera wszystkie dzienniki wygenerowane na podstawie platform Hadoop i YARN.</li><li>**Stara tabela:** log \_ mrjobsummary \_ CL, log \_ resourcemanager \_ CL, log \_ timelineserver \_ CL, log \_ nodemanager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Obciążenie Hive/LLAP 

| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Opis:** Ta tabela zawiera metryki JMX ze struktury Hive i LLAP. Zawiera ona wszystkie te same metryki JMX co stare tabele dzienników niestandardowych. Zawiera jedną metrykę na rekord.</li><li>Stara **tabela:** \_ \_ llap metrics hiveserver2 \_ CL, llap \_ metrics \_ hs2 \_ \_ metrics subsystemllap \_ metrics \_ jvm \_ CL, llap \_ metrics \_ llap \_ daemon \_ info \_ CL, llap \_ metrics \_ metrics a \_ allocator info \_ \_ CL, llap \_ metrics \_ deamon \_ jvm \_ CL, llap \_ metrics \_ io \_ CL, llap \_ metrics \_ executor \_ metrics \_ CL, llap \_ metrics \_ metricssystem \_ stats \_ CL, llap \_ metrics cache \_ \_ CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Opis:** Ta tabela zawiera dzienniki generowane przez hive, llap i ich powiązane składniki: WebHCat i Zeppelin.</li><li>**Stara tabela:** log \_ hivemetastore \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log \_ \_ zeppelin zeppelin \_ CL</li></ul>|


## <a name="kafka-workload"></a>Obciążenie platformy Kafka

| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Opis:** Ta tabela zawiera metryki JMX z platformy Kafka. Zawiera ona wszystkie te same metryki JMX co stare tabele dzienników niestandardowych oraz więcej metryk, które uznaliśmy za ważne. Zawiera jedną metrykę na rekord.</li><li>**Stara tabela:** metrics \_ kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Opis:** Ta tabela zawiera wszystkie dzienniki wygenerowane przez brokerów platformy Kafka.</li><li>**Stara tabela:** log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>Obciążenie bazy danych HBase

| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Opis:** Ta tabela zawiera metryki JMX z bazy danych HBase. Zawiera te same metryki JMX z tabel wymienionych w kolumnie Stary schemat. W przeciwieństwie do starych tabel każdy wiersz zawiera jedną metrykę.</li><li>**Stara tabela:** metrics \_ regionserver \_ CL, \_ \_ metrics regionserver wal \_ CL, metrics \_ regionserver \_ ipc \_ CL, \_ metrics regionserver \_ os \_ CL, metrics \_ regionserver \_ replication \_ CL, metrics \_ restserver \_ CL, metrics \_ restserver \_ jvm \_ CL, metrics \_ \_ hmaster assignmentmanager \_ CL, \_ \_ metrics hmaster ipc \_ CL, \_ metrics hmaser \_ os \_ CL, metrics \_ \_ hmaster balancer \_ CL, metrics \_ hmaster \_ jvm \_ CL, metrics \_ hmaster \_ CL,metrics \_ hmaster \_ fs \_ CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Opis:** Ta tabela zawiera dzienniki bazy danych HBase i powiązane z nią składniki: Phoenix i HDFS.</li><li>**Stara tabela:** log \_ regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamenode \_ CL, log \_ garbage collector \_ \_ CL</li></ul>|

## <a name="storm-workload"></a>Obciążenie Storm

| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Opis:** Ta tabela zawiera te same metryki JMX co tabele w sekcji Stare tabele. Jego wiersze zawierają jedną metrykę na rekord.</li><li>**Stara tabela:** metrics \_ stormnimbus \_ CL, \_ metrics stormsupervisor \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Opis:** Ta tabela zawiera metryki poziomu topologii z usługi Storm. Jest to taki sam kształt jak tabela wymieniona w sekcji Stare tabele.</li><li>**Stara tabela:** metrics \_ stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Opis:** Ta tabela zawiera wszystkie dzienniki wygenerowane przez system Storm.</li><li>**Stara tabela:** log \_ supervisor \_ CL, log \_ nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Obciążenie Oozie

| Nowa tabela | Szczegóły |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Opis:** Ta tabela zawiera wszystkie dzienniki wygenerowane na podstawie struktury Oozie.</li><li>**Stara tabela:** Log \_ oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Następne kroki
[Wykonywanie zapytań Azure Monitor w celu monitorowania klastrów usługi HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
