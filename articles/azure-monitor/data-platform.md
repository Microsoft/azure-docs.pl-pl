---
title: Platforma danych Azure Monitor | Microsoft Docs
description: Dane monitorowania zbierane przez Azure Monitor są rozdzielone na metryki, które są lekkie i mogą obsługiwać niemal scenariusze i dzienniki, które są używane na potrzeby zaawansowanej analizy.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 7356b9bb814f8bca5465fe74d48409b9dbca6d3b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731701"
---
# <a name="azure-monitor-data-platform"></a>Platforma danych Azure Monitor

Umożliwienie wglądu w współczesnych środowiskach obliczeniowych uruchamiających aplikacje rozproszone korzystające z usług w chmurze i lokalnych, wymaga zbierania danych operacyjnych z każdej warstwy i każdego składnika systemu rozproszonego. Musisz mieć możliwość przeprowadzenia szczegółowego wglądu w dane i skonsolidować je w jednym okienku szkła z różnymi perspektywami, aby wspierać wiele uczestników projektu w organizacji.

[Azure monitor](overview.md) zbiera i agreguje dane z różnych źródeł do wspólnej platformy danych, gdzie można jej używać do analizowania, wizualizacji i generowania alertów. Zapewnia spójne środowisko danych z wielu źródeł, które zapewnia szczegółowe informacje na temat wszystkich monitorowanych zasobów, a nawet danych z innych usług, które przechowują swoje dane w Azure Monitor.


![Omówienie usługi Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dane dotyczące zaobserwowania w Azure Monitor
Metryki, dzienniki i rozproszone ślady są często określane jako trzy filary przestrzegania. Są to różne rodzaje danych, które narzędzie monitorowania musi zbierać i analizować, aby zapewnić wystarczającą zauważalność monitorowanego systemu. Zauważalność można osiągnąć poprzez skorelowanie danych z wielu filarów i agregowanie danych w całym monitorowanym zestawie zasobów. Ponieważ Azure Monitor przechowuje dane z wielu źródeł jednocześnie, dane mogą być skorelowane i analizowane przy użyciu wspólnego zestawu narzędzi. W programie są również skorelowane dane wielu subskrypcji platformy Azure i dzierżawców, a także dane hostingu dla innych usług.

Zasoby platformy Azure generują znaczną ilość danych monitorowania. Azure Monitor konsoliduje te dane wraz z danymi monitorowania z innych źródeł na platformie metryk lub dzienników. Każda z nich jest zoptymalizowana pod kątem określonych scenariuszy monitorowania, a każdy z nich obsługuje różne funkcje w Azure Monitor. Funkcje, takie jak analiza danych, wizualizacje lub alerty, wymagają zrozumienia różnic, dzięki czemu można zaimplementować wymagany scenariusz w najbardziej efektywny i ekonomiczny sposób. Szczegółowe informacje w Azure Monitor takich jak [Application Insights](app/app-insights-overview.md) lub [Informacje o maszynie wirtualnej](vm/vminsights-overview.md) zawierają narzędzia analityczne, które umożliwiają skoncentrowanie się na konkretnym scenariuszu monitorowania bez konieczności zrozumienia różnic między dwoma typami danych. 


### <a name="metrics"></a>Metryki
[Metryki](essentials/data-platform-metrics.md) to wartości liczbowe, które opisują część systemu w konkretnym momencie. Są one zbierane w regularnych odstępach czasu i identyfikowane za pomocą znacznika czasu, nazwy, wartości i co najmniej jednej etykiety definiującej. Metryki można agregować przy użyciu różnych algorytmów, porównywać z innymi metrykami i analizować pod kątem trendów w czasie. 

Metryki w Azure Monitor są przechowywane w bazie danych szeregów czasowych, która jest zoptymalizowana pod kątem analizowania danych z sygnaturami czasowymi. Dzięki temu metryki są szczególnie odpowiednie dla alertów i szybkiego wykrywania problemów. Mogą oni powiedzieć, jak działa system, ale zazwyczaj musi być połączony z dziennikami, aby identyfikować główną przyczynę problemów.

Metryki są dostępne dla interaktywnej analizy w Azure Portal za pomocą [usługi Azure Eksplorator metryk](essentials/metrics-getting-started.md). Można je dodać do [pulpitu nawigacyjnego platformy Azure](app/tutorial-app-dashboards.md) w celu wizualizacji w połączeniu z innymi danymi, które są używane do wysyłania [alertów](alerts/alerts-metric.md)w czasie niemal w czasie rzeczywistym.

Przeczytaj więcej na temat Azure Monitor metryk, w tym ich źródeł danych, w [Azure monitor](essentials/data-platform-metrics.md).

### <a name="logs"></a>Dzienniki
[Dzienniki](logs/data-platform-logs.md) to zdarzenia, które wystąpiły w systemie. Mogą one zawierać różne rodzaje danych i mogą być strukturalne lub bezpłatne tekstu formularza z sygnaturą czasową. Mogą być tworzone sporadycznie, w miarę generowania wpisów przez zdarzenia w środowisku. W obciążonym systemie objętość tworzonych dzienników zwykle jest większa.

Dzienniki w Azure Monitor są przechowywane w Log Analytics obszarze roboczym, który jest oparty na [Eksplorator danych platformy Azure](/azure/data-explorer/) , który oferuje zaawansowany aparat analityczny i [bogaty język zapytań](/azure/kusto/query/). Dzienniki zapewniają zazwyczaj wystarczającą ilość informacji, aby zapewnić pełen kontekst identyfikowanego problemu i są przydatne do identyfikowania głównych przypadków problemów.

> [!NOTE]
> Ważne jest rozróżnienie między dziennikami Azure Monitor i źródłami danych dzienników na platformie Azure. Na przykład zdarzenia na poziomie subskrypcji na platformie Azure są zapisywane w [dzienniku aktywności](essentials/platform-logs-overview.md) , który można wyświetlić z menu Azure monitor. Większość zasobów będzie zapisywać informacje operacyjne w [dzienniku zasobów](essentials/platform-logs-overview.md) , które można przesłać dalej do różnych lokalizacji. Dzienniki Azure Monitor to platforma danych dziennika, która zbiera dzienniki aktywności i dzienniki zasobów wraz z innymi danymi monitorowania w celu zapewnienia głębokiej analizy w całym zestawie zasobów.


 Możesz interakcyjnie korzystać z [zapytań dzienników](logs/log-query-overview.md) w [log Analytics](logs/log-query-overview.md) w Azure Portal lub dodać wyniki do [pulpitu nawigacyjnego platformy Azure](app/tutorial-app-dashboards.md) w celu wizualizacji w połączeniu z innymi danymi. Możesz również utworzyć [alerty dziennika](alerts/alerts-log.md) , które będą wyzwalać alert w oparciu o wyniki zapytania dotyczącego harmonogramu.

Przeczytaj więcej na temat dzienników Azure Monitor, w tym ich źródeł danych w [dziennikach Azure monitor](logs/data-platform-logs.md).

### <a name="distributed-traces"></a>Rozproszone ślady
Ślady to seria powiązanych zdarzeń, które są zgodne z żądaniem użytkownika w systemie rozproszonym. Mogą służyć do określania zachowania kodu aplikacji oraz wydajności różnych transakcji. Dzienniki często są tworzone przez poszczególne składniki systemu rozproszonego, a śledzenie mierzy działanie i wydajność aplikacji w całym zestawie składników.

Śledzenie rozproszone w Azure Monitor jest włączone przy użyciu [zestawu SDK Application Insights](app/distributed-tracing.md), a dane śledzenia są przechowywane przy użyciu innych danych dziennika aplikacji zebranych przez Application Insights. Dzięki temu można korzystać z tych samych narzędzi analitycznych co inne dane dziennika, w tym zapytań dzienników, pulpitów nawigacyjnych i alertów.

Dowiedz się więcej na temat śledzenia rozproszonego, co [to jest śledzenie rozproszone?](app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Porównanie metryk Azure Monitor i dzienników

Poniższa tabela zawiera porównanie metryk i dzienników w Azure Monitor.

| Atrybut  | Metryki | Dzienniki |
|:---|:---|:---|
| Korzyści | Lekkie i możliwe do zrealizowania scenariusze w czasie zbliżonym do rzeczywistego, takie jak alerty. Idealne rozwiązanie do szybkiego wykrywania problemów. | Przeanalizowane przy użyciu zaawansowanego języka zapytań. Idealne dla głębokiej analizy i identyfikowania głównej przyczyny. |
| Dane | Tylko wartości liczbowe | Dane tekstowe lub liczbowe |
| Struktura | Standardowy zestaw właściwości, w tym czas próbkowania, monitorowany zasób, wartość liczbowa. Niektóre metryki obejmują wiele wymiarów w celu dalszej definicji. | Unikatowy zestaw właściwości w zależności od typu dziennika. |
| Kolekcja | Zbierane w regularnych odstępach czasu. | Mogą być zbierane sporadycznie, ponieważ zdarzenia wyzwalają rekord, który ma zostać utworzony. |
| Wyświetlanie w witrynie Azure Portal | Eksplorator metryk | Log Analytics |
| Źródła danych obejmują | Metryki platformy zebrane z zasobów platformy Azure.<br>Aplikacje monitorowane przez Application Insights.<br>Niestandardowe zdefiniowane przez aplikację lub interfejs API. | Dzienniki aplikacji i zasobów.<br>Rozwiązania do monitorowania.<br>Agenci i rozszerzenia maszyn wirtualnych.<br>Żądania i wyjątki aplikacji.<br>Azure Security Center.<br>Interfejs API modułu zbierającego dane. |

## <a name="collect-monitoring-data"></a>Zbieranie danych monitorowania
Różne [źródła danych dla Azure monitor](agents/data-sources.md) będą zapisywać w log Analytics obszarze roboczym (Dzienniku) lub w bazie danych metryk Azure monitor (metrycznych) lub w obu tych przypadkach. Niektóre źródła zapisują bezpośrednio do tych magazynów danych, a inne mogą zapisywać w innej lokalizacji, takiej jak usługa Azure Storage, i wymagać konfiguracji w celu wypełnienia dzienników lub metryk. 

Zobacz [metryki w Azure monitor](essentials/data-platform-metrics.md) i [dzienniki w Azure monitor](logs/data-platform-logs.md) , aby wyświetlić listę różnych źródeł danych, które wypełniają każdy typ.


## <a name="stream-data-to-external-systems"></a>Przesyłanie strumieniowe danych do systemów zewnętrznych
Oprócz korzystania z narzędzi platformy Azure do analizowania danych monitorowania, może być wymagane przekazanie go do zewnętrznego narzędzia, takiego jak informacje o zabezpieczeniach i produkt zarządzania zdarzeniami (SIEM). Takie przekazywanie zazwyczaj odbywa się bezpośrednio z monitorowanych zasobów za pomocą [usługi Azure Event Hubs](../event-hubs/index.yml). Niektóre źródła można skonfigurować tak, aby wysyłali dane bezpośrednio do centrum zdarzeń, podczas gdy można użyć innego procesu, takiego jak aplikacja logiki, aby pobrać wymagane dane. Aby uzyskać szczegółowe informacje [, zobacz Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń w celu użycia przez zewnętrzne narzędzie](essentials/stream-monitoring-data-event-hubs.md) .



## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej [na temat metryk w Azure monitor](essentials/data-platform-metrics.md).
- Przeczytaj więcej [na temat dzienników w Azure monitor](logs/data-platform-logs.md).
- Poznaj [dane monitorowania dostępne](agents/data-sources.md) dla różnych zasobów na platformie Azure.

