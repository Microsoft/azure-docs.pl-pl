---
title: Metryki w Azure Monitor | Microsoft Docs
description: Opisuje metryki w Azure Monitor, które stanowią dane lekkiego monitorowania, które mogą obsługiwać niemal scenariusze w czasie rzeczywistym.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8ecfd74a4d486a83add490501c2f7af4a4003b85
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700978"
---
# <a name="azure-monitor-metrics-overview"></a>Przegląd metryk Azure Monitor
Metryki Azure Monitor są funkcją Azure Monitor, która zbiera dane liczbowe z [monitorowanych zasobów](../monitor-reference.md) do bazy danych szeregów czasowych. Metryki to wartości liczbowe, które są zbierane w regularnych odstępach czasu i opisują kilka aspektów systemu w danym momencie. Metryki w Azure Monitor są lekkie i mogą obsługiwać niemal scenariusze w czasie rzeczywistym, dzięki czemu są szczególnie przydatne w przypadku alertów i szybkiego wykrywania problemów. Możesz przeanalizować je interaktywnie przy użyciu Eksploratora metryk, otrzymywać powiadomienia o alertach, gdy wartość przekroczy próg lub Wizualizuj je w skoroszycie lub pulpicie nawigacyjnym.


> [!NOTE]
> Azure Monitor metryki to jedna połowa platformy danych obsługującej Azure Monitor. Druga z nich to [Azure monitor dzienniki](../logs/data-platform-logs.md) , które gromadzą i organizują dane dziennika i wydajności oraz umożliwiają analizowanie ich przy użyciu zaawansowanego języka zapytań. Metryki są bardziej uproszczone niż dane w Azure Monitor dziennikach i mogą obsługiwać niemal scenariusze w czasie rzeczywistym, dzięki czemu są szczególnie przydatne w przypadku alertów i szybkiego wykrywania problemów. Metryki, które mogą przechowywać dane liczbowe tylko w określonej strukturze, podczas gdy dzienniki mogą przechowywać różne różne typy danych z ich własnymi strukturami. Możesz również wykonać złożoną analizę danych dzienników przy użyciu zapytań dziennika, które nie mogą być używane do analizy danych metryk.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Co możesz zrobić z metrykami Azure Monitor?
W poniższej tabeli przedstawiono różne sposoby używania metryk w Azure Monitor.

|  |  |
|:---|:---|
| **Analiza** | Użyj [Eksploratora metryk](metrics-charts.md) do analizowania zebranych metryk na wykresie i porównywania metryk z różnych zasobów. |
| **Alert** | Skonfiguruj [regułę alertu metryki](../alerts/alerts-metric.md) , która wysyła powiadomienie lub wykonuje [automatyczne działanie](../alerts/action-groups.md) , gdy wartość metryki przekroczy próg. |
| **Wizualizacja** | Przypinanie wykresu z Eksploratora metryk do [pulpitu nawigacyjnego platformy Azure](../app/tutorial-app-dashboards.md).<br>Utwórz [skoroszyt](../visualize/workbooks-overview.md) , aby połączyć się z wieloma zestawami danych w raporcie interaktywnym. Eksportuj wyniki zapytania do [Grafana](../visualize/grafana-plugin.md) , aby wykorzystać jego pulpit nawigacyjny i połączyć się z innymi źródłami danych. |
| **Automatyzacja** |  Użyj funkcji [automatycznego skalowania](../autoscale/autoscale-overview.md) , aby zwiększyć lub zmniejszyć zasoby na podstawie wartości metryki przekraczającej próg. |
| **Odczytać** | Uzyskiwanie dostępu do wartości metryk z wiersza polecenia przy użyciu  [poleceń cmdlet programu PowerShell](/powershell/module/az.applicationinsights)<br>Dostęp do wartości metryk z aplikacji niestandardowej przy użyciu [interfejsu API REST](./rest-api-walkthrough.md).<br>Uzyskaj dostęp do wartości metryk z wiersza polecenia przy użyciu  [interfejsu CLI](/cli/azure/monitor/metrics). |
| **Eksportowanie** | [Kierowanie metryk do dzienników](./resource-logs.md#send-to-azure-storage) w celu analizowania danych w Azure monitor metryki wraz z danymi w dziennikach Azure monitor i przechowywanie wartości metryk przez dłużej niż 93 dni.<br>Metryki strumienia do [centrum zdarzeń](./stream-monitoring-data-event-hubs.md) w celu kierowania ich do systemów zewnętrznych. |
| **Archiwum** | [Archiwizuj](./platform-logs-overview.md) historię wydajności lub kondycji zasobu w celu zapewnienia zgodności, inspekcji lub raportowania w trybie offline. |

![Przegląd metryk](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Zbieranie danych
Istnieją trzy podstawowe źródła metryk zbieranych przez Azure Monitor. Po zebraniu tych metryk w bazie danych metryk Azure Monitor można je ocenić razem niezależnie od ich źródła.

**Zasoby platformy Azure**. Metryki platformy są tworzone przez zasoby platformy Azure i zapewniają wgląd w ich kondycję i wydajność. Każdy typ zasobu tworzy [odrębny zestaw metryk](./metrics-supported.md) , nie wymaga żadnej konfiguracji. Metryki platformy są zbierane z zasobów platformy Azure w częstotliwości jednej minuty, chyba że określono inaczej w definicji metryki. 

**Aplikacje**. Metryki są tworzone przez Application Insights dla monitorowanych aplikacji i pomagają wykrywać problemy z wydajnością oraz śledzić trendy w sposobie używania aplikacji. Obejmuje to takie wartości jak _czas odpowiedzi serwera_ i _wyjątki przeglądarki_.

**Agenci maszyn wirtualnych**. Metryki są zbierane z systemu operacyjnego gościa maszyny wirtualnej. Włącz metryki systemu operacyjnego gościa dla maszyn wirtualnych z systemem Windows z [rozszerzeniem diagnostyki systemu Windows (funkcji wad)](../agents/diagnostics-extension-overview.md) i maszyn wirtualnych z systemem Linux przy użyciu [agenta InfluxData telegraf](https://www.influxdata.com/time-series-platform/telegraf/).

**Metryki niestandardowe**. Oprócz metryk standardowych, które są automatycznie dostępne, można zdefiniować metryki. Możesz [definiować niestandardowe metryki w aplikacji](../app/api-custom-events-metrics.md) monitorowane przez Application Insights lub tworzyć niestandardowe metryki dla usługi platformy Azure przy użyciu [interfejsu API metryk niestandardowych](./metrics-store-custom-rest-api.md).

- Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby uzyskać pełną listę źródeł danych, które mogą wysyłać dane do metryk Azure monitor.

## <a name="metrics-explorer"></a>Eksplorator metryk
Użyj [Eksplorator metryk](metrics-charts.md) , aby interaktywnie analizować dane w bazie danych metryk i wykresy wartości z wielu metryk w czasie. Możesz przypiąć wykresy do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami. Metryki można także pobrać przy użyciu [interfejsu API REST usługi Azure Monitoring](./rest-api-walkthrough.md).

![Eksplorator metryk](media/data-platform-metrics/metrics-explorer.png)

- Zobacz Rozpoczynanie [pracy z programem Azure monitor Metrics Explorer](./metrics-getting-started.md) , aby rozpocząć korzystanie z Eksploratora metryk.

## <a name="data-structure"></a>Struktura danych
Dane zbierane przez metryki Azure Monitor są przechowywane w bazie danych szeregów czasowych, która jest zoptymalizowana pod kątem analizowania danych z sygnaturami czasowymi. Każdy zestaw wartości metryk jest szeregiem czasowym o następujących właściwościach:

* Godzina zebrania wartości
* Zasób, z którym jest skojarzona wartość
* Przestrzeń nazw, która działa jak kategoria dla metryki
* Nazwa metryki
* Sama wartość
* Niektóre metryki mogą mieć wiele wymiarów, zgodnie z opisem w [wielowymiarowych metrykach](#multi-dimensional-metrics). Metryki niestandardowe mogą mieć maksymalnie 10 wymiarów.

## <a name="multi-dimensional-metrics"></a>Metryki wielowymiarowe
Jednym z wyzwań związanych z danymi metryk jest to, że często zawiera ona ograniczone informacje w celu zapewnienia kontekstu zbieranych wartości. Azure Monitor rozwiązuje to wyzwanie przy użyciu metryk wielowymiarowych. Wymiary metryki to pary nazwa-wartość, które zawierają dodatkowe dane do opisywania wartości metryki. Na przykład _ilość dostępnego miejsca na dysku_ może mieć wymiar o nazwie _dysk_ z wartościami _C:_, _D:_, co zezwoli na wyświetlanie dostępnego miejsca na dysku na wszystkich dyskach lub poszczególnych dyskach osobno.

Poniższy przykład ilustruje dwa zestawy danych dla hipotetycznej metryki o nazwie _przepływność sieci_. Pierwszy zestaw danych nie ma wymiarów. Drugi zestaw danych pokazuje wartości z dwoma wymiarami, _adresem IP_ i _kierunkiem_:

### <a name="network-throughput"></a>Przepływność sieci

| Znacznik czasu     | Wartość metryki |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1 331,8 KB/s |
| 8/9/2017 8:15 | 1 141,4 KB/s |
| 8/9/2017 8:16 | 1 110,2 KB/s |

Ta Metryka niewymiarowa może odpowiedzieć wyłącznie na podstawowe pytanie, takie jak "co to jest przepływność sieci w danym momencie?".

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Przepływność sieci + dwa wymiary ("IP" i "kierunek")

| Znacznik czasu     | Wymiar "IP"   | Wymiar "kierunek" | Wartość metryki|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "Send"    | 646,5 KB/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "Receive" | 420,1 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "Send"    | 150,0 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "Receive" | 115,2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "Send"    | 515,2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "Receive" | 371,1 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "Send"    | 155,0 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "Receive" | 100,1 KB/s |

Ta Metryka może odpowiedzieć na takie pytania, jak "co to jest przepływność sieci dla każdego adresu IP?" oraz "jak dużo danych zostało wysłanych i odebranych?". Metryki wielowymiarowe przenoszą dodatkową wartość analityczną i diagnostyczną w porównaniu do metryk niewymiarowych.

## <a name="retention-of-metrics"></a>Przechowywanie metryk
W przypadku większości zasobów na platformie Azure metryki są przechowywane przez 93 dni. Istnieją pewne wyjątki:

**Metryki systemu operacyjnego gościa**
-   **Metryki klasycznych systemów operacyjnych gościa**. Są to liczniki wydajności zbierane przez [rozszerzenie diagnostyki systemu Windows (funkcji wad)](../agents/diagnostics-extension-overview.md) lub [rozszerzenie diagnostyki Linux (lad)](../../virtual-machines/extensions/diagnostics-linux.md) i kierowane do konta usługi Azure Storage. Przechowywanie tych metryk wynosi 14 dni.
-   **Metryki systemu operacyjnego gościa wysyłane do metryk Azure monitor**. Są to liczniki wydajności zbierane przez [rozszerzenie diagnostyki systemu Windows (funkcji wad)](../agents/diagnostics-extension-overview.md) i wysyłane do [ujścia danych Azure monitor](../agents/diagnostics-extension-overview.md#data-destinations)lub za pośrednictwem [agenta InfluxData telegraf](https://www.influxdata.com/time-series-platform/telegraf/) na maszynach z systemem Linux. Przechowywanie tych metryk to 93 dni.
-   **Metryki systemu operacyjnego gościa zebrane przez agenta log Analytics**. Są to liczniki wydajności zbierane przez agenta Log Analytics i wysyłane do Log Analytics obszaru roboczego. Przechowywanie tych metryk wynosi 31 dni i można je przedłużyć do 2 lat.

**Application Insights metryki opartych na dziennikach**. 
- Za sceną [metryki oparte na dziennikach](../app/pre-aggregated-metrics-log-metrics.md) są tłumaczone na zapytania dziennika. Ich przechowywanie jest zgodne z przechowywaniem zdarzeń w dziennikach bazowych. W przypadku zasobów Application Insights dzienniki są przechowywane przez 90 dni.


> [!NOTE]
> [Metryki platformy dla Azure Monitor zasobów można wysyłać do log Analytics obszaru roboczego](./resource-logs.md#send-to-azure-storage) w celu uzyskania długoterminowych trendów.





## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [platformie danych Azure monitor](../data-platform.md).
- Dowiedz się więcej o [danych dziennika w Azure monitor](../logs/data-platform-logs.md).
- Poznaj [dane monitorowania dostępne](../agents/data-sources.md) dla różnych zasobów na platformie Azure.