---
title: Metryki w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano metryki w usłudze Azure Monitor, które są lekkie dane monitorowania zdolne do obsługi scenariuszy w czasie zbliżonym do rzeczywistego.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457318"
---
# <a name="metrics-in-azure-monitor"></a>Metryki w usłudze Azure Monitor

> [!NOTE]
> Platforma danych usługi Azure Monitor opiera się na dwóch podstawowych typach danych: metryki i dzienniki. W tym artykule opisano metryki. Szczegółowe opis dzienników i [platformy danych usługi Azure Monitor](data-platform.md) można znaleźć w [dziennikach w usłudze Azure Monitor,](data-platform-logs.md) aby do porównania tych dwóch danych.

Metryki w usłudze Azure Monitor są lekkie i mogą obsługiwać scenariusze w czasie zbliżonym do rzeczywistego, dzięki czemu są szczególnie przydatne do alertów i szybkiego wykrywania problemów. W tym artykule opisano, jak metryki są skonstruowane, co można z nimi zrobić i identyfikuje różne źródła danych, które przechowują dane w metrykach.

## <a name="what-are-metrics"></a>Co to są dane?
Metryki to wartości liczbowe, które opisują pewien aspekt systemu w określonym momencie w czasie. Metryki są zbierane w regularnych odstępach czasu i są przydatne do alertów, ponieważ mogą być pobierane często, a alert może być uruchamiany szybko za pomocą stosunkowo prostej logiki.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Co można zrobić z metryk usługi Azure Monitor?
W poniższej tabeli wymieniono różne sposoby używania danych metryk w usłudze Azure Monitor.

|  |  |
|:---|:---|
| Analiza | [Eksplorator metryk](metrics-charts.md) służy do analizowania zebranych metryk na wykresie i porównywania metryk z różnych zasobów. |
| Wizualizacja | Przypnij wykres z eksploratora metryk do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-app-dashboards.md).<br>Utwórz [skoroszyt](../app/usage-workbooks.md) do łączenia z wieloma zestawami danych w raporcie interaktywnym. Eksportuj wyniki kwerendy do [grafany,](grafana-plugin.md) aby wykorzystać jej pulpit nawigacyjny i połączyć z innymi źródłami danych. |
| Alerty | Skonfiguruj [regułę alertu metryki,](alerts-metric.md) która wysyła powiadomienie lub podejmuje [zautomatyzowane działania,](action-groups.md) gdy wartość metryki przekroczy próg. |
| Automatyzacja |  Użyj [skalowania automatycznego,](autoscale-overview.md) aby zwiększyć lub zmniejszyć zasoby na podstawie wartości metryki przekraczającej próg. |
| Eksportowanie | [Rozsyłaj metryki do dzienników,](resource-logs-collect-storage.md) aby analizować dane w metrykach usługi Azure Monitor wraz z danymi w dziennikach monitora platformy Azure i przechowywać wartości metryk przez okres dłuższy niż 93 dni.<br>Przesyłaj strumieniowo metryki do [Centrum zdarzeń,](stream-monitoring-data-event-hubs.md) aby przekierować je do systemów zewnętrznych. |
| Pobrać | Uzyskiwanie dostępu do wartości metryk z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Uzyskaj dostęp do wartości metryk z niestandardowej aplikacji przy użyciu [interfejsu API REST](rest-api-walkthrough.md).<br>Dostęp do wartości metryk z wiersza polecenia przy użyciu [interfejsu wiersza polecenia](/cli/azure/monitor/metrics). |
| Archiwum | [Archiwizuj](..//learn/tutorial-archive-data.md) historię wydajności lub historii kondycji zasobu w celu zapewnienia zgodności, inspekcji lub raportowania w trybie offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Jak mają strukturę danych w metrykach usługi Azure Monitor?
Dane zbierane przez metryki usługi Azure Monitor są przechowywane w bazie danych szeregów czasowych, która jest zoptymalizowana pod kątem analizowania danych oznaczonych sygnaturą czasową. Każdy zestaw wartości metryk jest szeregiem czasowym z następującymi właściwościami:

* Czas zbierania wartości
* Zasób, z który jest skojarzona wartość
* Obszar nazw, który działa jak kategoria dla metryki
* Nazwa metryki
* Sama wartość
* Niektóre metryki mogą mieć wiele wymiarów, zgodnie z opisem w [metrykach wielowymiarowych](#multi-dimensional-metrics). Dane niestandardowe mogą mieć maksymalnie 10 wymiarów.

## <a name="multi-dimensional-metrics"></a>Metryki wielowymiarowe
Jednym z wyzwań dla danych metryk jest to, że często ma ograniczone informacje, aby zapewnić kontekst dla zebranych wartości. Usługa Azure Monitor rozwiązuje to wyzwanie za pomocą metryk wielowymiarowych. Wymiary metryki są parami nazwa-wartość, które zawierają dodatkowe dane do opisania wartości metryki. Na przykład metryka _Dostępne miejsce na dysku_ może mieć wymiar o nazwie _Dysk_ z wartościami _C:_, _D:_, który umożliwia przeglądanie dostępnego miejsca na dysku na wszystkich dyskach lub dla każdego dysku indywidualnie.

Poniższy przykład ilustruje dwa zestawy danych dla hipotetycznej metryki o nazwie _Przepustowość sieci_. Pierwszy zestaw danych nie ma wymiarów. Drugi zestaw danych pokazuje wartości z dwoma wymiarami, _adresem IP_ i _kierunkiem:_

### <a name="network-throughput"></a>Przepustowość sieci

| Znacznik czasu     | Wartość metryczna |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1 331,8 kb/s |
| 8/9/2017 8:15 | 1 141,4 kb/s |
| 8/9/2017 8:16 | 1110,2 Kb/s |

Ta niewymiarowa metryka może odpowiedzieć tylko na podstawowe pytanie, takie jak "jaka była moja przepustowość sieci w danym czasie?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Przepustowość sieci + dwa wymiary ("IP" i "Kierunek")

| Znacznik czasu     | Wymiar "IP"   | Wymiar "Kierunek" | Wartość metryczna|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Kierunek="Wyślij"    | 646,5 kb/s |
| 8/9/2017 8:14 | IP="192.168.5.2" | Kierunek="Odbiór" | 420,1 kb/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Kierunek="Wyślij"    | 150,0 kb/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Kierunek="Odbiór" | 115,2 kb/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Kierunek="Wyślij"    | 515,2 kb/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Kierunek="Odbiór" | 371,1 kb/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Kierunek="Wyślij"    | 155,0 kb/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Kierunek="Odbiór" | 100,1 kb/s |

Ta metryka może odpowiadać na pytania, takie jak "jaka była przepustowość sieci dla każdego adresu IP?", "ile danych zostało wysłanych w porównaniu z odebraniem?" Metryki wielowymiarowe zawierają dodatkową wartość analityczną i diagnostyczną w porównaniu z metrykami niewymiarowymi.

## <a name="interacting-with-azure-monitor-metrics"></a>Interakcja z metrykami usługi Azure Monitor
Użyj [Eksploratora metryk,](metrics-charts.md) aby interaktywnie analizować dane w bazie danych metryk i wykres wartości wielu metryk w czasie. Wykresy można przypiąć do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami. Można również pobrać metryki przy użyciu [interfejsu API REST monitorowania platformy Azure](rest-api-walkthrough.md).

![Eksplorator metryk](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Źródła metryk usługi Azure Monitor
Istnieją trzy podstawowe źródła metryk zebranych przez usługę Azure Monitor. Gdy te metryki są zbierane w bazie danych metryk usługi Azure Monitor, mogą być oceniane razem niezależnie od ich źródła.

**Metryki platformy** są tworzone przez zasoby platformy Azure i zapewniają wgląd w ich kondycję i wydajność. Każdy typ zasobu tworzy [odrębny zestaw metryk](metrics-supported.md) bez wymaganej konfiguracji. Metryki platformy są zbierane z zasobów platformy Azure z częstotliwością jednej minuty, chyba że określono inaczej w definicji metryki. 

**Metryki systemu operacyjnego gościa** są zbierane z systemu operacyjnego gościa maszyny wirtualnej. Włącz metryki systemu operacyjnego gościa dla maszyn wirtualnych systemu Windows z [rozszerzeniem diagnostycznym systemu Windows (WAD)](../platform/diagnostics-extension-overview.md) i maszyn wirtualnych systemu Linux za pomocą [programu InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

**Metryki aplikacji** są tworzone przez usługa Application Insights dla monitorowanych aplikacji i pomagają wykrywać problemy z wydajnością i śledzić trendy w sposobie użycia aplikacji. Obejmuje to takie wartości, jak _czas odpowiedzi serwera_ i _wyjątki przeglądarki_.

**Metryki niestandardowe** to metryki definiowane oprócz standardowych metryk, które są automatycznie dostępne. Można [zdefiniować metryki niestandardowe w aplikacji,](../app/api-custom-events-metrics.md) która jest monitorowana przez usługę Application Insights lub utworzyć niestandardowe metryki dla usługi platformy Azure przy użyciu [niestandardowego interfejsu API metryk.](metrics-store-custom-rest-api.md)

## <a name="retention-of-metrics"></a>Przechowywanie wskaźników
W przypadku większości zasobów na platformie Azure metryki są przechowywane przez 93 dni. Istnieją pewne wyjątki:

**Dane systemu operacyjnego gościa**
-   **Klasyczne dane systemu operacyjnego gościa**. Są to liczniki wydajności zebrane przez [rozszerzenie diagnostyczne systemu Windows (WAD)](../platform/diagnostics-extension-overview.md) lub [rozszerzenie diagnostyczne systemu Linux (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) i kierowane do konta magazynu platformy Azure. Przechowywanie tych wskaźników wynosi 14 dni.
-   **Metryki systemu operacyjnego gościa wysyłane do metryk usługi Azure Monitor**. Są to liczniki wydajności zebrane przez [rozszerzenie diagnostyczne systemu Windows (WAD)](diagnostics-extension-overview.md) i wysyłane do [ujścia danych usługi Azure Monitor](diagnostics-extension-overview.md#data-destinations)lub za pośrednictwem [agenta Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) na komputerach z systemem Linux. Przechowywanie tych wskaźników wynosi 93 dni.
-   **Dane systemu operacyjnego gościa zebrane przez agenta usługi Log Analytics**. Są to liczniki wydajności zebrane przez agenta usługi Log Analytics i wysłane do obszaru roboczego usługi Log Analytics. Przechowywanie tych wskaźników wynosi 31 dni i może zostać przedłużone do 2 lat.

**Metryki oparte na dziennikach usługi Application Insights**. 
- Za sceną [metryki oparte na dzienniku](../app/pre-aggregated-metrics-log-metrics.md) przekładają się na zapytania dziennika. Ich przechowywanie odpowiada przechowywania zdarzeń w podstawowych dzienników. W przypadku zasobów usługi Application Insights dzienniki są przechowywane przez 90 dni.


> [!NOTE]
> Metryki platformy dla zasobów usługi Azure Monitor można [wysyłać do obszaru roboczego usługi Log Analytics](resource-logs-collect-storage.md) w celu uzyskania długoterminowych trendów.





## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [platformie danych Usługi Azure Monitor](data-platform.md).
- Dowiedz się więcej o [danych dziennika w usłudze Azure Monitor](data-platform-logs.md).
- Dowiedz się więcej o [danych monitorowania dostępnych](data-sources.md) dla różnych zasobów na platformie Azure.
