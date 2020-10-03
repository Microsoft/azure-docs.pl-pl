---
title: Udostępnianie widoków niestandardowych z sparametryzowanymi adresami URL — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak utworzyć sparametryzowane adresy URL, aby łatwo udostępniać niestandardowe widoki Eksploratora w Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665331"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Udostępnianie widoku niestandardowego przy użyciu sparametryzowanego adresu URL

Aby udostępnić widok niestandardowy w Eksploratorze Azure Time Series Insights, można programowo utworzyć sparametryzowany adres URL widoku niestandardowego.

Eksplorator Azure Time Series Insights obsługuje parametry zapytania URL, aby określić widoki w środowisku bezpośrednio z adresu URL. Na przykład przy użyciu tylko adresu URL możesz określić środowisko docelowe, predykat wyszukiwania i żądany przedział czasu. Gdy użytkownik wybierze dostosowany adres URL, Interfejs udostępnia link bezpośrednio do tego zasobu w portalu Azure Time Series Insights. Obowiązują zasady dostępu do danych.

> [!TIP]
>
> * Obejrzyj pokaz bezpłatnej [Azure Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Przeczytaj towarzyszącą dokumentację programu [Azure Time Series Insights Explorer](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>Identyfikator środowiska

Parametr `environmentId=<guid>` określa identyfikator środowiska docelowego. Jest to składnik nazwy FQDN dostępu do danych, który można znaleźć w prawym górnym rogu omówienia środowiska w Azure Portal. To wszystko, co poprzedza `env.timeseries.azure.com` .

Przykładowy parametr identyfikatora środowiska to `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Godzina

Za pomocą sparametryzowanego adresu URL możesz określać bezwzględne lub względne wartości czasu.

### <a name="absolute-time-values"></a>Bezwzględne wartości czasu

Aby podać bezwzględne wartości czasu, użyj parametrów `from=<integer>` i `to=<integer>`.

* Parametr `from=<integer>` to wartość w milisekundach JavaScript określająca czas początkowy dla zakresu wyszukiwania.
* Parametr `to=<integer>` to wartość w milisekundach JavaScript określająca czas końcowy dla zakresu wyszukiwania.

> [!TIP]
> Aby łatwo przetłumaczyć daty na JavaScript w milisekundach, wypróbuj [konwertera Timestamp & UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Względne wartości czasu

W przypadku względnej wartości czasu Użyj `relativeMillis=<value>` , gdzie *wartość* jest w języku JavaScript milisekund od ostatniego znacznika czasu otrzymanego z interfejsu API.

Na przykład parametr `&relativeMillis=3600000` spowoduje wyświetlenie danych z ostatnich 60 minut.

Akceptowane wartości odpowiadają menu **szybkiego czasu** Eksploratora Azure Time Series Insights i obejmują:

* `1800000` (Ostatnie 30 min)
* `3600000` (Ostatnie 60 min)
* `10800000` (Ostatnie 3 godziny)
* `21600000` (Ostatnie 6 godzin)
* `43200000` (Ostatnie 12 godzin)
* `86400000` (Ostatnie 24 godziny)
* `604800000` (Ostatnie 7 dni)
* `2592000000` (Ostatnie 30 godzin)

### <a name="optional-parameters"></a>Parametry opcjonalne

`timeSeriesDefinitions=<collection of term objects>`Parametr określa terminy predykatu, które będą wyświetlane w widoku Azure Time Series Insights:

| Parametr | Element adresu URL | Opis |
| --- | --- | --- |
| **Nazwij** | `\<string>` | Nazwa *terminu*. |
| **splitBy** | `\<string>` | Nazwa kolumny, według której ma zostać wykonany *podział*. |
| **measureName** | `\<string>` | Nazwa kolumny *miary*. |
| **predykatu** | `\<string>` | Klauzula *where* na potrzeby filtrowania po stronie serwera. |
| **useSum** | `true` | Opcjonalny parametr, który określa użycie sum dla miary. |

> [!NOTE]
> Jeśli `Events` jest wybrana miara **useSum** , liczba jest zaznaczona domyślnie.  
> Jeśli `Events` nie jest zaznaczone, wartość średnia jest domyślnie zaznaczona. |

* `multiChartStack=<true/false>`Para klucz-wartość umożliwia tworzenie stosów na wykresie.
* `multiChartSameScale=<true/false>`Para klucz-wartość umożliwia tej samej skali osi Y w warunkach w ramach opcjonalnego parametru.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>`Umożliwia dostosowanie suwaka interwał, aby zapewnić bardziej szczegółowy lub gładszy, bardziej Zagregowany widok wykresu.  
* `timezoneOffset=<integer>`Parametr umożliwia ustawienie strefy czasowej na potrzeby wyświetlania wykresu jako przesunięcia do czasu UTC.

| Pary | Opis |
| --- | --- |
| `multiChartStack=false` | `true` jest domyślnie włączona, dlatego `false` należy przejść do stosu. |
| `multiChartStack=false&multiChartSameScale=true` | Układanie na stosie musi mieć możliwość użycia takiej samej skali osi Y dla warunków.  Jest on `false` Domyślnie, więc przekazywanie `true` włącza tę funkcję. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednostki = `days` , `hours` , `minutes` , `seconds` , `milliseconds` .  Jednostka zawsze wielką literą. </br> Zdefiniuj liczbę jednostek, przekazując żądaną liczbę całkowitą dla **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Liczba całkowita jest zawsze w milisekundach. |

> [!NOTE]
> wartości **timeBucketUnit** można wygładzić do 7 dni.
> wartości **timezoneOffset** nie są czasem UTC ani czasem lokalnym.

### <a name="examples"></a>Przykłady

Aby dodać definicje szeregów czasowych do środowiska Azure Time Series Insights jako parametr adresu URL, Dołącz:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Użyj przykładowych definicji szeregów czasowych dla:

* Identyfikator środowiska
* Ostatnie 60 minut danych
* Warunki (**F1PressureID**, **F2TempStation**i **F3VibrationPL**), które składają się na parametry opcjonalne

Można utworzyć następujący sparametryzowany adres URL dla widoku:

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Sparametryzowany adres URL programu Azure Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Zapoznaj się z Eksploratorem na żywo przy użyciu powyższego przykładu [adresu URL](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) .

Powyższy adres URL opisuje i wyświetla sparametryzowany widok Eksploratora Azure Time Series Insights.

* Predykaty sparametryzowane.

  [![Predykaty sparametryzowane w Eksploratorze Azure Time Series Insights.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Widok udostępnionego wykresu pełny.

  [![Widok udostępnionego wykresu pełny.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wykonywać zapytania dotyczące danych przy użyciu języka C#](time-series-insights-query-data-csharp.md).

* Dowiedz się więcej o [eksploratorze Azure Time Series Insights](./time-series-insights-explorer.md).
