---
title: Udostępnianie widoków niestandardowych za pomocą sparametryzowanych adresów URL — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć sparametryzowane adresy URL, aby łatwo udostępniać niestandardowe widoki eksploratora w usłudze Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 10616c8003d9bbbe42cb70bd1bac4193044907c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416991"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Udostępnianie widoku niestandardowego przy użyciu sparametryzowanego adresu URL

Aby udostępnić widok niestandardowy w Eksploratorze wglądu w szeregi czasowe, można programowo utworzyć sparametryzowany adres URL widoku niestandardowego.

Explorer usługi Time Series Insights obsługuje parametry zapytania URL, aby określić widoki w środowiskach bezpośrednio z adresu URL. Na przykład przy użyciu tylko adresu URL możesz określić środowisko docelowe, predykat wyszukiwania i żądany przedział czasu. Gdy użytkownik wybierze niestandardowy adres URL, interfejs udostępnia łącze bezpośrednio do tego zasobu w portalu Time Series Insights. Obowiązują zasady dostępu do danych.

> [!TIP]
> * Wyświetl bezpłatną [prezentację aplikacji Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Przeczytaj dołączoną dokumentację [eksploratora usługi Time Series Insights.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>Identyfikator środowiska

Parametr `environmentId=<guid>` określa identyfikator środowiska docelowego. Jest składnikiem fqdn dostępu do danych i można go znaleźć w prawym górnym rogu środowiska omówienie w witrynie Azure portal. To wszystko, co `env.timeseries.azure.com`poprzedza .

Przykładowy parametr identyfikatora środowiska to `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

Za pomocą sparametryzowanego adresu URL możesz określać bezwzględne lub względne wartości czasu.

### <a name="absolute-time-values"></a>Bezwzględne wartości czasu

Aby podać bezwzględne wartości czasu, użyj parametrów `from=<integer>` i `to=<integer>`.

* Parametr `from=<integer>` to wartość w milisekundach JavaScript określająca czas początkowy dla zakresu wyszukiwania.
* Parametr `to=<integer>` to wartość w milisekundach JavaScript określająca czas końcowy dla zakresu wyszukiwania.

> [!TIP]
> Aby łatwo przetłumaczyć daty na milisekundy JavaScript, wypróbuj [konwerter sygnatury czasowej Epoch & Unix](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Względne wartości czasu

Dla względnej wartości `relativeMillis=<value>`czasu użyj , gdzie *wartość* jest w milisekundach JavaScript z najnowszej sygnatury czasowej otrzymanej z interfejsu API.

Na przykład parametr `&relativeMillis=3600000` spowoduje wyświetlenie danych z ostatnich 60 minut.

Zaakceptowane wartości odpowiadają menu **szybki** czas eksploratora usługi Time Series Insights i obejmują:

* `1800000`(Ostatnie 30 min)
* `3600000`(Ostatnie 60 min)
* `10800000`(Ostatnie 3 godziny)
* `21600000`(Ostatnie 6 godzin)
* `43200000`(Ostatnie 12 godzin)
* `86400000`(Ostatnie 24 godziny)
* `604800000`(Ostatnie 7 dni)
* `2592000000`(Ostatnie 30 godzin)

### <a name="optional-parameters"></a>Parametry opcjonalne

Parametr `timeSeriesDefinitions=<collection of term objects>` określa warunki predykatu, które będą wyświetlane w widoku Usługi Time Series Insights:

| Parametr | Element adresu URL | Opis |
| --- | --- | --- |
| **Nazwa** | `\<string>` | Nazwa *terminu*. |
| **splitBy (podział)** | `\<string>` | Nazwa kolumny, według której ma zostać wykonany *podział*. |
| **nazwa miary** | `\<string>` | Nazwa kolumny *miary*. |
| **Predykat** | `\<string>` | Klauzula *where* na potrzeby filtrowania po stronie serwera. |
| **useSum** | `true` | Opcjonalny parametr określający użycie sumy dla miary. |

> [!NOTE]
> Jeśli `Events` jest to wybrana **miara useSum,** liczba jest zaznaczona domyślnie.  
> Jeśli `Events` opcja nie jest zaznaczona, średnia jest wybierana domyślnie. |

* Para `multiChartStack=<true/false>` klucz-wartość umożliwia układanie w tabeli na wykresie.
* Para `multiChartSameScale=<true/false>` klucz-wartość umożliwia tę samą skalę osi Y w różnych terminach w ramach opcjonalnego parametru.  
* Umożliwia `timeBucketUnit=<Unit>&timeBucketSize=<integer>` dostosowanie suwaka interwału w celu zapewnienia bardziej szczegółowego lub gładszego, bardziej zagregowanego widoku wykresu.  
* Parametr `timezoneOffset=<integer>` umożliwia ustawienie strefy czasowej dla wykresu, który ma być oglądany jako przesunięcie do czasu UTC.

| Para(-y) | Opis |
| --- | --- |
| `multiChartStack=false` | `true`jest domyślnie włączona, więc przechodzą `false` do stosu. |
| `multiChartStack=false&multiChartSameScale=true` | Układanie na stosie musi mieć możliwość użycia takiej samej skali osi Y dla warunków.  Jest to `false` domyślnie, `true` więc przekazywanie włącza tę funkcję. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednostki `days` `hours`= `minutes` `seconds`, `milliseconds`, , .  Jednostka zawsze wielką literą. </br> Zdefiniuj liczbę jednostek, przekazując żądaną liczbę całkowitą dla **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Liczba całkowita jest zawsze w milisekundach. |

> [!NOTE]
> **timeBucketUnit** wartości mogą być wygładzone do 7 dni.
> **timezoneOffset** wartości nie są ani UTC ani czasu lokalnego.

### <a name="examples"></a>Przykłady

Aby dodać definicje szeregów czasowych do środowiska usługi Time Series Insights jako parametr adresu URL, dołącz:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Użyj przykładowych definicji szeregów czasowych dla:

* Identyfikator środowiska
* Ostatnie 60 minut danych
* Terminy **(F1PressureID**, **F2TempStation**i **F3VibrationPL),** które składają się na parametry opcjonalne

Dla widoku można utworzyć następujący sparametryzowany adres URL:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Sparametryczny adres URL programu Explorer usługi Time Series Insights](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Zobacz Eksploratora na żywo [przy użyciu przykładu adresu URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) powyżej.

Powyższy adres URL opisuje i wyświetla sparametryzowany widok eksploratora usługi Time Series Insights. 

* Sparametryzowane predykaty.

  [![Explorer usługi Time Series Insights sparametryzowany predykatów.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Udostępniony pełny widok wykresu.

  [![Udostępniony pełny widok wykresu.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [badać dane przy użyciu języka C#](time-series-insights-query-data-csharp.md).

* Dowiedz się więcej o [Eksploratorze wglądu w szeregi czasowe](./time-series-insights-explorer.md).
