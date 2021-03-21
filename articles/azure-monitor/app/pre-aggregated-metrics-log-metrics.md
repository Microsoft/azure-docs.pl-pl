---
title: Metryki oparte na dzienniku i wstępnie agregowane na platformie Azure Application Insights | Microsoft Docs
description: Dlaczego warto używać metryk opartych na dziennikach i wstępnie agregowanych na platformie Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: acbe535d740eb527d165be1675f31e759851a987
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717829"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Metryki oparte na dzienniku i metryki wstępnie zagregowane w usłudze Application Insights

W tym artykule opisano różnicę między "tradycyjnymi" Application Insights metrykami opartymi na dziennikach i metrykami wstępnie zagregowanymi. Oba typy metryk są dostępne dla użytkowników Application Insights, a każda z nich ma unikatową wartość monitorowania kondycji aplikacji, diagnostyki i analizy. Deweloperzy, którzy tworzą instrumentację aplikacji, mogą zdecydować, który typ metryki jest najlepiej dostosowany do określonego scenariusza, w zależności od rozmiaru aplikacji, oczekiwanej ilości danych telemetrycznych i wymagań firmy dotyczących dokładności metryk i alertów.

## <a name="log-based-metrics"></a>Metryki oparte na dzienniku

W przeszłości model danych telemetrii monitorowania aplikacji w Application Insights dotyczył wyłącznie niewielkiej liczby wstępnie zdefiniowanych typów zdarzeń, takich jak żądania, wyjątki, wywołania zależności, wyświetlenia stron itd. Deweloperzy mogą użyć zestawu SDK do emisji tych zdarzeń ręcznie (przez napisanie kodu, który jawnie wywołuje zestaw SDK), lub może polegać na automatycznym zbieraniu zdarzeń z automatycznej Instrumentacji. W obu przypadkach Application Insights zaplecza przechowuje wszystkie zebrane zdarzenia jako dzienniki i Application Insightse w Azure Portal działa jako narzędzie analityczne i diagnostyczne służące do wizualizacji danych opartych na zdarzeniach z dzienników.

Korzystanie z dzienników w celu zachowania kompletnego zestawu zdarzeń może przynieść dobrą wartość analityczną i diagnostykę. Na przykład można uzyskać dokładną liczbę żądań do określonego adresu URL z liczbą różnych użytkowników, którzy wykonali te wywołania. Można też uzyskać szczegółowe dane śledzenia diagnostyczne, w tym wyjątki i wywołania zależności dla każdej sesji użytkownika. Informacje tego typu mogą znacząco poprawić widoczność kondycji i użycia aplikacji, co pozwala skrócić czas potrzebny na zdiagnozowanie problemów z aplikacją.

W tym samym czasie zbieranie kompletnego zestawu zdarzeń może być niepraktyczne (lub nawet niemożliwe) dla aplikacji generujących dużą ilość danych telemetrycznych. W sytuacjach, gdy ilość zdarzeń jest zbyt duża, Application Insights implementuje kilka technik redukcji woluminu telemetrii, takich jak [próbkowanie](./sampling.md) i [filtrowanie](./api-filtering-sampling.md) , które zmniejszają liczbę zebranych i przechowywanych zdarzeń. Niestety, zmniejszenie liczby przechowywanych zdarzeń również zmniejsza dokładność metryk, które w tle, muszą wykonywać agregacje czasu zapytania dla zdarzeń przechowywanych w dziennikach.

> [!NOTE]
> W Application Insights metryki oparte na agregacji czasu zapytania i zdarzenia przechowywane w dziennikach są nazywane metrykami opartymi na dzienniku. Te metryki zwykle mają wiele wymiarów na podstawie właściwości zdarzenia, co sprawia, że są one przełożone na potrzeby analiz, ale dokładność tych metryk ma negatywny wpływ na próbkowanie i filtrowanie.

## <a name="pre-aggregated-metrics"></a>Metryki wstępnie zagregowane

Oprócz metryk opartych na dziennikach w późnej 2018 zespół Application Insights wysłał publiczną wersję zapoznawczą metryk, które są przechowywane w wyspecjalizowanym repozytorium, które jest zoptymalizowane pod kątem szeregów czasowych. Nowe metryki nie są już przechowywane jako pojedyncze zdarzenia z wieloma właściwościami. Zamiast tego są one przechowywane jako seria czasowo agregowane i tylko z kluczowymi wymiarami. To sprawia, że nowe metryki przewyższają czas zapytania: pobieranie danych odbywa się znacznie szybciej i wymaga mniejszej mocy obliczeniowej. W związku z tym umożliwiają nowe scenariusze, takie jak [alerty niemal w czasie rzeczywistym na podstawie wymiarów metryk](../alerts/alerts-metric-near-real-time.md), więcej odpowiedzi na [pulpity nawigacyjne](./overview-dashboard.md)i inne.

> [!IMPORTANT]
> Zarówno metryki oparte na dzienniku, jak i wstępnie zagregowane współistnieją w Application Insights. Aby odróżnić te dwa, w Application Insights środowisku użytkownika metryki wstępnie zagregowane są teraz nazywane "metrykami standardowymi (wersja zapoznawcza)", podczas gdy tradycyjne metryki ze zdarzeń zostały zmienione na "metryki oparte na dziennikach".

Nowsze zestawy SDK ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK lub nowsze dla platformy .NET) wstępnie zagregowane metryki podczas zbierania. Dotyczy to  [standardowych metryk wysyłanych domyślnie](../essentials/metrics-supported.md#microsoftinsightscomponents) , tak aby dokładność nie dotyczyła pobierania próbek ani filtrowania. Ma również zastosowanie do metryk niestandardowych wysyłanych przy użyciu funkcji [GetMetric](./api-custom-events-metrics.md#getmetric) , co zmniejsza wzrost ilości danych i obniża koszty.

W przypadku zestawów SDK, które nie implementują wstępnej agregacji (to jest starsze wersje zestawów SDK Application Insights lub dla Instrumentacji przeglądarki), Application Insights zaplecza nadal wypełnia nowe metryki, agregowanie zdarzeń odebranych przez punkt końcowy zbierania zdarzeń Application Insights. Oznacza to, że chociaż nie korzystasz ze zmniejszonej ilości danych przesyłanych za pośrednictwem sieci, nadal możesz użyć wstępnie zagregowanych metryk i zapewnić lepszą wydajność i obsługę alertów w czasie rzeczywistym, korzystając z zestawów SDK, które nie agregują wstępnie metryk podczas zbierania.

Warto zauważyć, że punkt końcowy kolekcji wstępnie agreguje zdarzenia przed pobraniem próbek, co oznacza, że [pobieranie próbek](./sampling.md) nie będzie miało wpływu na dokładność metryk przedzagregowanych, niezależnie od używanej wersji zestawu SDK z aplikacją.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>Wstępnie zagregowana tabela metryk obsługiwana przez zestaw SDK

| Bieżące zestawy SDK produkcji | Metryki standardowe (wstępne agregacja zestawu SDK) | Metryki niestandardowe (bez agregacji wstępnej zestawu SDK) | Metryki niestandardowe (z funkcją agregacji wstępnej zestawu SDK)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core i .NET Framework | Obsługiwane (V 2.13.1 +)| Obsługiwane przez [TrackMetric](api-custom-events-metrics.md#trackmetric)| Obsługiwane (V 2.7.2 +) za pomocą metody [GetMetric](get-metric.md) |
| Java                         | Nieobsługiwane       | Obsługiwane przez [TrackMetric](api-custom-events-metrics.md#trackmetric)| Nieobsługiwane                           |
| Node.js                      | Nieobsługiwane       | Obsługiwane przez  [TrackMetric](api-custom-events-metrics.md#trackmetric)| Nieobsługiwane                           |
| Python                       | Nieobsługiwane       | Obsługiwane                                 | Częściowo obsługiwane przez [OpenCensus. destatystyka](opencensus-python.md#metrics) |  

> [!NOTE]
>  Implementacja metryk dla języka Python używająca OpenCensus. destatystyka różni się od GetMetric. Szczegółowe informacje znajdują [się w dokumentacji języka Python dotyczącej metryk](./opencensus-python.md#metrics).

### <a name="codeless-supported-pre-aggregated-metrics-table"></a>Tabela metryk w nieobsługiwanej postaci bezkodowej

| Bieżące zestawy SDK produkcji | Metryki standardowe (wstępne agregacja zestawu SDK) | Metryki niestandardowe (bez agregacji wstępnej zestawu SDK) | Metryki niestandardowe (z funkcją agregacji wstępnej zestawu SDK)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | Obsługiwane <sup> 1<sup>    | Nieobsługiwane                             | Nieobsługiwane                           |
| ASP.NET Core            | Obsługiwane <sup> 2<sup>    | Nieobsługiwane                             | Nieobsługiwane                           |
| Java                    | Nieobsługiwane            | Nieobsługiwane                             | [Obsługiwane](java-in-process-agent.md#metrics) |
| Node.js                 | Nieobsługiwane            | Nieobsługiwane                             | Nieobsługiwane                           |

1. ASP.NET dołączania bezkodowego nie jest włączone, App Service tylko emituje metryki w trybie monitorowania "Full". Dołączenie bez kodu ASP.NET App Service, VM/VMSS i on-premises emituje standardowe metryki bez wymiarów. Zestaw SDK jest wymagany dla wszystkich wymiarów.
2. ASP.NET Core dołączenie bezkodowe App Service emituje metryki standardowe bez wymiarów. Zestaw SDK jest wymagany dla wszystkich wymiarów.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Używanie wstępnej agregacji z niestandardowymi metrykami Application Insights

Można użyć wstępnej agregacji z metrykami niestandardowymi. Dwie główne korzyści to możliwość konfiguracji i alertu na wymiarze metryki niestandardowej oraz zmniejszenia ilości danych wysyłanych z zestawu SDK do punktu końcowego kolekcji Application Insights.

Istnieje kilka [sposobów wysyłania niestandardowych metryk z zestawu SDK Application Insights](./api-custom-events-metrics.md). Jeśli Twoja wersja zestawu SDK oferuje metody [GetMetric i TrackValue](./api-custom-events-metrics.md#getmetric) , jest to preferowany sposób wysyłania metryk niestandardowych, ponieważ w tym przypadku wstępne agregacja występuje w ramach zestawu SDK, nie tylko zmniejszenie ilości danych przechowywanych na platformie Azure, ale także ilości danych przesyłanych z zestawu sdk do Application Insights. W przeciwnym razie użyj metody [trackMetric](./api-custom-events-metrics.md#trackmetric)  , która spowoduje wstępne agregowanie zdarzeń metryk podczas pozyskiwania danych.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Niestandardowe wymiary metryk i wstępne agregacja

Wszystkie metryki wysyłane przy użyciu wywołań interfejsu API [trackMetric](./api-custom-events-metrics.md#trackmetric) lub [GetMetric i TrackValue](./api-custom-events-metrics.md#getmetric) są automatycznie przechowywane w magazynach dzienników i metryk. Jednak podczas gdy wersja metryki niestandardowej jest zawsze zachowywana wszystkie wymiary, wstępnie zagregowana wersja metryki jest domyślnie przechowywana bez wymiarów. Możesz włączyć zbieranie wymiarów niestandardowych metryk na karcie [użycie i szacowane koszty](./pricing.md) , zaznaczając opcję "Włącz alerty w niestandardowych wymiarach metryk": 

![Użycie i szacowany koszt](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Dlaczego zbieranie niestandardowych wymiarów metryk jest wyłączone domyślnie?

Kolekcja niestandardowych wymiarów metryk jest domyślnie wyłączona, ponieważ w przyszłości podczas przechowywania metryk niestandardowych z wymiarami opłaty są naliczane oddzielnie od Application Insights, a przechowywanie niewymiarowych metryk niestandardowych pozostanie bez zmian (do limitu przydziału). Możesz dowiedzieć się więcej o nadchodzących zmianach modelu cen na naszej oficjalnej [stronie z cennikiem](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Tworzenie wykresów i eksplorowanie metryk przedzagregowanych opartych na dziennikach i standardach

Korzystając z [Azure Monitor Eksplorator metryk](../essentials/metrics-getting-started.md) , można wykreślić wykresy z metryk wstępnie zagregowanych i opartych na dziennikach oraz tworzyć pulpity nawigacyjne z użyciem wykresów. Po wybraniu żądanego zasobu Application Insights Użyj selektora przestrzeni nazw, aby przełączać się między standardowym (podglądem) i metrykami opartymi na dzienniku, lub wybrać niestandardową przestrzeń nazw metryki:

![Przestrzeń nazw metryki](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Modele cen dla metryk Application Insights

Pozyskiwanie metryk do Application Insights, zarówno w przypadku dzienników, jak i wstępnie zagregowanych, spowoduje wygenerowanie kosztów na podstawie wielkości pozyskanych danych, zgodnie z opisem w [tym miejscu](./pricing.md#pricing-model). Metryki niestandardowe, w tym wszystkie jej wymiary, są zawsze przechowywane w magazynie dzienników Application Insights. Ponadto wstępnie zagregowana wersja metryk niestandardowych (bez wymiarów) jest domyślnie przekazywana do magazynu metryk.

Wybranie opcji [Włącz alerty dla niestandardowych wymiarów metryk](#custom-metrics-dimensions-and-pre-aggregation) umożliwia przechowywanie wszystkich wymiarów wstępnie zagregowanych metryk w magazynie metryk, a także generowanie **dodatkowych** kosztów na podstawie [cen metryk niestandardowych](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Następne kroki

* [Alerty niemal w czasie rzeczywistym](../alerts/alerts-metric-near-real-time.md)
* [GetMetric i TrackValue](./api-custom-events-metrics.md#getmetric)
