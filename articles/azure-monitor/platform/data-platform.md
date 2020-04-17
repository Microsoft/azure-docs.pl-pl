---
title: Platforma danych Usługi Azure Monitor | Dokumenty firmy Microsoft
description: Monitorowanie danych zebranych przez usługę Azure Monitor jest podzielony na metryki, które są lekkie i mogą obsługiwać scenariusze i dzienniki w czasie zbliżonym do rzeczywistego, które są używane do zaawansowanej analizy.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457267"
---
# <a name="azure-monitor-data-platform"></a>Platforma danych Usługi Azure Monitor

Włączenie obserwowalności w dzisiejszych złożonych środowiskach obliczeniowych z aplikacjami rozproszonymi, które opierają się zarówno na usługach w chmurze, jak i lokalnych, wymaga zbierania danych operacyjnych z każdej warstwy i każdego składnika systemu rozproszonego. Musisz być w stanie wykonać szczegółowe informacje na temat tych danych i skonsolidować je w jednym okienku szkła z różnych perspektyw, aby wspierać rzeszę interesariuszy w organizacji.

[Usługa Azure Monitor](../overview.md) zbiera i agreguje dane z różnych źródeł do wspólnej platformy danych, gdzie może służyć do analizy, wizualizacji i alertów. Zapewnia spójne środowisko na podstawie danych z wielu źródeł, co zapewnia szczegółowe informacje na temat wszystkich monitorowanych zasobów, a nawet z danymi z innych usług, które przechowują swoje dane w usłudze Azure Monitor.


![Omówienie usługi Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dane dotyczące obserwacji w usłudze Azure Monitor
Metryki, dzienniki i rozproszone ślady są powszechnie określane jako trzy filary obserwowalności. Są to różne rodzaje danych, które narzędzie do monitorowania musi zbierać i analizować, aby zapewnić wystarczającą ekscesję monitorowanego systemu. Zauważalność można osiągnąć poprzez korelację danych z wielu filarów i agregowanie danych w całym zestawie monitorowanych zasobów. Ponieważ usługa Azure Monitor przechowuje dane z wielu źródeł razem, dane mogą być skorelowane i analizowane przy użyciu wspólnego zestawu narzędzi. Koreluje również dane między wieloma subskrypcjami platformy Azure i dzierżawami, oprócz hostowania danych dla innych usług.

Zasoby platformy Azure generują znaczną ilość danych monitorowania. Usługa Azure Monitor konsoliduje te dane wraz z danymi monitorowania z innych źródeł na platformie Metryki lub Dzienniki. Każdy z nich jest zoptymalizowany pod kątem określonych scenariuszy monitorowania, a każdy z nich obsługuje różne funkcje w usłudze Azure Monitor. Funkcje, takie jak analiza danych, wizualizacje lub alerty wymagają zrozumienia różnic, dzięki czemu można zaimplementować wymagany scenariusz w najbardziej efektywny i opłacalny sposób. Szczegółowe informacje w usłudze Azure Monitor, takie jak [usługa Application Insights](../app/app-insights-overview.md) lub Usługa Azure Monitor dla maszyn wirtualnych, zawierają narzędzia [analityczne,](../insights/vminsights-overview.md) które umożliwiają skupienie się na konkretnym scenariuszu monitorowania bez konieczności zrozumienia różnic między dwoma typami danych. 


### <a name="metrics"></a>Metryki
[Metryki](data-platform-metrics.md) są wartościami liczbowymi, które opisują niektóre aspekty systemu w określonym momencie. Są one zbierane w regularnych odstępach czasu i są identyfikowane za pomocą sygnatury czasowej, nazwy, wartości i jednej lub więcej etykiet definiujących. Metryki mogą być agregowane przy użyciu różnych algorytmów, w porównaniu do innych metryk i analizowane pod kątem trendów w czasie. 

Metryki w usłudze Azure Monitor są przechowywane w bazie danych szeregów czasowych, która jest zoptymalizowana pod kątem analizowania danych oznaczonych sygnaturą czasową. Dzięki temu metryki szczególnie nadaje się do alertów i szybkiego wykrywania problemów. Mogą one powiedzieć, jak system działa, ale zazwyczaj muszą być połączone z dzienników w celu zidentyfikowania główną przyczynę problemów.

Metryki są dostępne do interaktywnej analizy w witrynie Azure portal z [Eksploratorem metryk platformy Azure.](../platform/metrics-getting-started.md) Można je dodać do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-app-dashboards.md) w celu wizualizacji w połączeniu z innymi danymi i używane do alertów w czasie [zbliżonym](alerts-metric.md)do rzeczywistego.

Dowiedz się więcej o metrykach usługi Azure Monitor, w tym o ich źródłach danych w [metrykach w usłudze Azure Monitor.](data-platform-metrics.md)

### <a name="logs"></a>Dzienniki
[Dzienniki](data-platform-logs.md) są zdarzeniami, które wystąpiły w systemie. Mogą one zawierać różne rodzaje danych i mogą być uporządkowane lub tekst formularza swobodnego z sygnaturą czasową. Mogą one być tworzone sporadycznie, jak zdarzenia w środowisku generują wpisy dziennika, a system pod dużym obciążeniem zazwyczaj generuje więcej woluminu dziennika.

Dzienniki w usłudze Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics opartym na [Eksploratorze danych platformy Azure,](/azure/data-explorer/) który zapewnia zaawansowany aparat analizy i [bogaty język zapytań.](/azure/kusto/query/) Dzienniki zazwyczaj zawierają wystarczające informacje, aby zapewnić pełny kontekst identyfikowanego problemu i są przydatne do identyfikowania głównego przypadku problemów.

> [!NOTE]
> Należy odróżnić dzienniki usługi Azure Monitor i źródła danych dziennika na platformie Azure. Na przykład zdarzenia na poziomie subskrypcji na platformie Azure są zapisywane w [dzienniku aktywności,](platform-logs-overview.md) który można wyświetlić z menu Usługi Azure Monitor. Większość zasobów będzie zapisywać informacje operacyjne w [dzienniku zasobów,](platform-logs-overview.md) które można przesyłać dalej do różnych lokalizacji. Dzienniki usługi Azure Monitor to platforma danych dziennika, która zbiera dzienniki aktywności i dzienniki zasobów wraz z innymi danymi monitorowania, aby zapewnić dogłębną analizę całego zestawu zasobów.


 Możesz pracować z [zapytaniami dziennika](../log-query/log-query-overview.md) interaktywnie z [usługą Log Analytics](../log-query/portals.md) w witrynie Azure portal lub dodać wyniki do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-app-dashboards.md) w celu wizualizacji w połączeniu z innymi danymi. Można również utworzyć [alerty dziennika,](alerts-log.md) które będą wyzwalać alert na podstawie wyników kwerendy harmonogramu.

Dowiedz się więcej o dziennikach usługi Azure Monitor, w tym o ich źródłach danych w [dziennikach w usłudze Azure Monitor.](data-platform-logs.md)

### <a name="distributed-traces"></a>Rozproszone ślady
Ślady są szeregi powiązanych zdarzeń, które następują żądanie użytkownika za pośrednictwem systemu rozproszonego. Mogą one służyć do określania zachowania kodu aplikacji i wydajności różnych transakcji. Podczas gdy dzienniki będą często tworzone przez poszczególne składniki systemu rozproszonego, śledzenie mierzy działanie i wydajność aplikacji w całym zestawie składników.

Śledzenie rozproszone w usłudze Azure Monitor jest włączone za pomocą [zestawu SDK usługi Application Insights,](../app/distributed-tracing.md)a dane śledzenia są przechowywane z innymi danymi dziennika aplikacji zebranymi przez usługę Application Insights. Dzięki temu jest dostępny dla tych samych narzędzi analizy, jak inne dane dziennika, w tym zapytania dziennika, pulpity nawigacyjne i alerty.

Dowiedz się więcej o śledzeniu rozproszonym w [: Co to jest śledzenie rozproszone?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Porównanie metryk i dzienników usługi Azure Monitor

W poniższej tabeli porównano metryki i dzienniki w usłudze Azure Monitor.

| Atrybut  | Metryki | Dzienniki |
|:---|:---|:---|
| Zalety | Lekki i zdolny do niemal w czasie rzeczywistym scenariuszy, takich jak alerty. Idealny do szybkiego wykrywania problemów. | Analizowane z językiem zapytania. Idealny do głębokiej analizy i identyfikacji głównej przyczyny. |
| Dane | Tylko wartości liczbowe | Dane tekstowe lub liczbowe |
| Struktura | Standardowy zestaw właściwości, w tym czas próbkowania, monitorowany zasób, wartość liczbowa. Niektóre metryki zawierają wiele wymiarów do dalszej definicji. | Unikatowy zestaw właściwości w zależności od typu dziennika. |
| Collection | Zbierane w regularnych odstępach czasu. | Mogą być zbierane sporadycznie, gdy zdarzenia wyzwalają rekord, który ma zostać utworzony. |
| Wyświetlanie w witrynie Azure Portal | Eksplorator metryk | Log Analytics |
| Źródła danych obejmują | Metryki platformy zebrane z zasobów platformy Azure.<br>Aplikacje monitorowane przez aplikację Insights.<br>Niestandardowe zdefiniowane przez aplikację lub interfejs API. | Dzienniki aplikacji i zasobów.<br>Rozwiązania do monitorowania.<br>Agenci i rozszerzenia maszyn wirtualnych.<br>Żądania aplikacji i wyjątki.<br>Centrum zabezpieczeń platformy Azure.<br>Interfejs API modułu zbierającego dane. |

## <a name="collect-monitoring-data"></a>Zbieranie danych z monitorowania
Różne [źródła danych dla usługi Azure Monitor](data-sources.md) będą zapisywać w obszarze roboczym usługi Log Analytics (dzienniki) lub w bazie danych metryk usługi Azure Monitor (metryki) lub w obu tych obszarach. Niektóre źródła będą zapisywać bezpośrednio do tych magazynów danych, podczas gdy inne mogą zapisywać w innej lokalizacji, takiej jak magazyn platformy Azure i wymagają konfiguracji, aby wypełnić dzienniki lub metryki. 

Zobacz [metryki w usłudze Azure Monitor](data-platform-metrics.md) i [dzienniki w usłudze Azure Monitor, aby](data-platform-logs.md) uzyskać listę różnych źródeł danych, które wypełniają każdy typ.


## <a name="stream-data-to-external-systems"></a>Przesyłanie strumieniowe danych do systemów zewnętrznych
Oprócz używania narzędzi na platformie Azure do analizowania danych monitorowania, może być wymagane przekazanie ich do zewnętrznego narzędzia, takiego jak produkt zarządzania informacjami o zabezpieczeniach i zdarzeniami (SIEM). To przekazywanie jest zazwyczaj wykonywane bezpośrednio z monitorowanych zasobów za pośrednictwem [usługi Azure Event Hubs.](/azure/event-hubs/) Niektóre źródła można skonfigurować do wysyłania danych bezpośrednio do centrum zdarzeń, podczas gdy można użyć innego procesu, takiego jak aplikacja logiki, aby pobrać wymagane dane. Zobacz [strumieniowanie danych monitorowania platformy Azure do centrum zdarzeń do użycia za pomocą zewnętrznego narzędzia, aby](stream-monitoring-data-event-hubs.md) uzyskać szczegółowe informacje.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metrykach w usłudze Azure Monitor](data-platform-metrics.md).
- Dowiedz się więcej o [dziennikach w usłudze Azure Monitor](data-platform-logs.md).
- Dowiedz się więcej o [danych monitorowania dostępnych](data-sources.md) dla różnych zasobów na platformie Azure.
