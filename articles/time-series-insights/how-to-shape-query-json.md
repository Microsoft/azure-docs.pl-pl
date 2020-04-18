---
title: Najważniejsze wskazówki dotyczące kształtowania zapytań json — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak zwiększyć wydajność zapytań usługi Azure Time Series Insights, kształtując JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 63a708f80ad18309269e37c354b047c304a260d3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641289"
---
# <a name="shape-json-to-maximize-query-performance"></a>Ukształtuj JSON, aby zmaksymalizować wydajność kwerendy

Ten artykuł zawiera wskazówki dotyczące kształtowania JSON, aby zmaksymalizować wydajność zapytań usługi Azure Time Series Insights.

## <a name="video"></a>Film wideo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Poznaj najlepsze wskazówki dotyczące kształtowania JSON w celu zaspokojenia potrzeb pamięci masowej.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Najlepsze rozwiązania

Zastanów się, jak wysyłasz zdarzenia do usługi Time Series Insights. Mianowicie zawsze:

1. Przesyłaj dane przez sieć tak efektywnie, jak to możliwe.
1. Upewnij się, że dane są przechowywane w sposób, dzięki czemu można wykonywać agregacje odpowiednie dla danego scenariusza.
1. Upewnij się, że nie osiągniesz maksymalnych limitów właściwości usługi Time Series Insights:
   - 600 właściwości (kolumn) dla środowisk S1.
   - 800 właściwości (kolumn) dla środowisk S2.

> [!TIP]
> Przejrzyj [limity i planowanie](time-series-insights-update-plan.md) w usłudze Azure Time Series Insights Preview.

Następujące wskazówki pomagają zapewnić najlepszą możliwą wydajność kwerendy:

1. Nie używaj właściwości dynamicznych, takich jak identyfikator tagu, jako nazwy właściwości. To użycie przyczynia się do osiągnięcia limitu maksymalnych właściwości.
1. Nie wysyłaj niepotrzebnych właściwości. Jeśli właściwość kwerendy nie jest wymagana, najlepiej jej nie wysyłać. W ten sposób można uniknąć ograniczeń przechowywania.
1. Użyj [danych referencyjnych,](time-series-insights-add-reference-data-set.md) aby uniknąć wysyłania danych statycznych przez sieć.
1. Udostępniaj właściwości wymiaru między wieloma zdarzeniami, aby wydajniej wysyłać dane przez sieć.
1. Nie należy używać zagnieżdżania tablic głębokich. Aplikacja Time Series Insights obsługuje maksymalnie dwa poziomy tablic zagnieżdżonych, które zawierają obiekty. Usługi Time Series Insights spłaszczają tablice w wiadomościach na wiele zdarzeń z parami wartości właściwości.
1. Jeśli istnieje tylko kilka środków dla wszystkich lub większości zdarzeń, lepiej jest wysłać te środki jako oddzielne właściwości w tym samym obiekcie. Wysyłanie ich oddzielnie zmniejsza liczbę zdarzeń i może zwiększyć wydajność kwerendy, ponieważ mniej zdarzeń muszą być przetwarzane. Gdy istnieje kilka miar, wysyłanie ich jako wartości w jednej właściwości minimalizuje możliwość osiągnięcia maksymalnego limitu właściwości.

## <a name="example-overview"></a>Przykładowy przegląd

Poniższe dwa przykłady pokazują, jak wysłać zdarzenia, aby wyróżnić poprzednie zalecenia. Po każdym przykładzie można przejrzeć sposób stosowania zaleceń.

Przykłady są oparte na scenariuszu, w którym wiele urządzeń wysyła pomiary lub sygnały. Pomiary lub sygnały mogą być natężenie przepływu, ciśnienie oleju silnikowego, temperatura i wilgotność. W pierwszym przykładzie istnieje kilka pomiarów na wszystkich urządzeniach. Drugi przykład ma wiele urządzeń, a każde urządzenie wysyła wiele unikatowych pomiarów.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenariusz pierwszy: Istnieje tylko kilka pomiarów

> [!TIP]
> Zaleca się wysłanie każdego pomiaru lub sygnału jako osobnej właściwości lub kolumny.

W poniższym przykładzie istnieje pojedynczy komunikat usługi Azure IoT Hub, w którym tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiarów. Tablica zewnętrzna używa danych referencyjnych, aby zwiększyć wydajność wiadomości. Dane referencyjne zawierają metadane urządzenia, które nie zmieniają się z każdym zdarzeniem, ale zapewniają przydatne właściwości do analizy danych. Przetwarzanie typowych wartości wymiarów i stosowanie danych referencyjnych zapisuje bajty wysyłane przez sieć, co sprawia, że wiadomość jest bardziej wydajna.

Należy wziąć pod uwagę następujące ładunku JSON wysyłane do środowiska ga usługi Time Series Insights przy użyciu [obiektu komunikat urządzenia IoT,](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) który jest serializowany do JSON po wysłaniu do chmury platformy Azure:


```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* Tabela danych referencyjnych, która ma **identyfikator właściwości klucza:**

   | deviceId | Messageid | deviceLocation (Lokalizacja urządzenia) |
   | --- | --- | --- |
   | FXXX (polski) | DANE\_LINIOWE | UE |
   | W YYY | DANE\_LINIOWE | USA |

* Tabela zdarzeń usługi Time Series Insights po spłaszczeniu:

   | deviceId | Messageid | deviceLocation (Lokalizacja urządzenia) | sygnatura czasowa | Serii. Natężenie przepływu ft3/s | Serii. Ciśnienie oleju silnikowego psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX (polski) | DANE\_LINIOWE | UE | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX (polski) | DANE\_LINIOWE | UE | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | W YYY | DANE\_LINIOWE | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - Kolumna **deviceId** służy jako nagłówek kolumny dla różnych urządzeń we flocie. Dokonywanie **deviceId** wartość własnej nazwy właściwości ogranicza całkowitą liczbę urządzeń do 595 (dla środowisk S1) lub 795 (dla środowisk S2) z pozostałych pięciu kolumn.
> - Unika się niepotrzebnych właściwości (na przykład informacji o make i model). Ponieważ właściwości nie będą wyszukiwane w przyszłości, wyeliminowanie ich umożliwia lepszą wydajność sieci i pamięci masowej.
> - Dane referencyjne są używane w celu zmniejszenia liczby bajtów przesyłanych przez sieć. Dwa atrybuty **messageId** i **deviceLocation** są połączone przy użyciu funkcji key **deviceId**. Te dane są łączone z danymi telemetrycznymi w czasie transferu danych przychodzących, a następnie są przechowywane w usłudze Time Series Insights do wykonywania zapytań.
> - Używane są dwie warstwy zagnieżdżania, co jest maksymalną ilością zagnieżdżania obsługiwane przez usługi Time Series Insights. Jest to bardzo ważne, aby uniknąć głęboko zagnieżdżonych tablic.
> - Miary są wysyłane jako oddzielne właściwości w tym samym obiekcie, ponieważ istnieje kilka miar. Tutaj, **seria. Natężenie przepływu psi** i **serii. Ciśnienie oleju silnikowego ft3/s** to unikalne kolumny.

## <a name="scenario-two-several-measures-exist"></a>Scenariusz drugi: Istnieje kilka miar

> [!TIP]
> Zaleca się wysyłanie miar jako krotek "typ", "jednostka" i "value".

Przykład ładunku JSON:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Tabela danych referencyjnych, która ma właściwości **klucza deviceId** i **series.tagId:**

   | deviceId | identyfikator series.tagId | Messageid | deviceLocation (Lokalizacja urządzenia) | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX (polski) | pompaWłasna | DANE\_LINIOWE | UE | Przepływ | ft3/s |
   | FXXX (polski) | olejPressure | DANE\_LINIOWE | UE | Ciśnienie oleju silnikowego | psi |
   | W YYY | pompaWłasna | DANE\_LINIOWE | USA | Przepływ | ft3/s |
   | W YYY | olejPressure | DANE\_LINIOWE | USA | Ciśnienie oleju silnikowego | psi |

* Tabela zdarzeń usługi Time Series Insights po spłaszczeniu:

   | deviceId | identyfikator series.tagId | Messageid | deviceLocation (Lokalizacja urządzenia) | type | unit | sygnatura czasowa | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX (polski) | pompaWłasna | DANE\_LINIOWE | UE | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX (polski) | olejPressure | DANE\_LINIOWE | UE | Ciśnienie oleju silnikowego | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX (polski) | pompaWłasna | DANE\_LINIOWE | UE | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX (polski) | olejPressure | DANE\_LINIOWE | UE | Ciśnienie oleju silnikowego | psi | 2018-01-17T01:17:00Z | 49.2 |
   | W YYY | pompaWłasna | DANE\_LINIOWE | USA | Przepływ | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | W YYY | olejPressure | DANE\_LINIOWE | USA | Ciśnienie oleju silnikowego | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - Kolumny **deviceId** i **series.tagId** służą jako nagłówki kolumn dla różnych urządzeń i tagów we flocie. Przy użyciu każdego jako własny atrybut ogranicza kwerendę do 594 (dla środowisk S1) lub 794 (dla środowisk S2) całkowita liczba urządzeń z pozostałych sześciu kolumn.
> - Uniknięto niepotrzebnych właściwości, z powodu przytoczonego w pierwszym przykładzie.
> - Dane referencyjne są używane w celu zmniejszenia liczby bajtów przesyłanych przez sieć poprzez wprowadzenie **deviceId**, który jest używany dla unikatowej pary **messageId** i **deviceLocation**. Plik composite key **series.tagId** jest używany dla unikatowej pary **typu** i **jednostki**. Klucz złożony umożliwia **deviceId** i **series.tagId** pary, które mają być używane do odwoływania się do czterech wartości: **messageId, deviceLocation, type,** i **unit**. Te dane są łączone z danymi telemetrycznymi w czasie transferu danych przychodzących. Następnie jest przechowywany w usłudze Time Series Insights do wykonywania zapytań.
> - Dwie warstwy zagnieżdżania są używane, z powodu cytowanego w pierwszym przykładzie.

### <a name="for-both-scenarios"></a>W obu scenariuszach

Dla właściwości z dużą liczbą możliwych wartości najlepiej jest wysłać jako różne wartości w jednej kolumnie zamiast tworzyć nową kolumnę dla każdej wartości. Z dwóch poprzednich przykładów:

  - W pierwszym przykładzie kilka właściwości ma kilka wartości, więc należy zrobić każdy oddzielne właściwości.
  - W drugim przykładzie miary nie są określone jako poszczególne właściwości. Zamiast tego są one tablicy wartości lub miar w ramach właściwości common series. Zostanie wysłany nowy **identyfikator tagId** klucza, który tworzy nowy **identyfikator series.tagId** kolumny w tabeli spłaszczone. Nowy **typ** właściwości i **jednostka** są tworzone przy użyciu danych referencyjnych, tak aby limit właściwości nie został osiągnięty.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wysyłaniu komunikatów urządzenia usługi IoT Hub do chmury](../iot-hub/iot-hub-devguide-messages-construct.md).

- Przeczytaj [składnię kwerend usługi Azure Time Series Insights,](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) aby dowiedzieć się więcej o składni kwerendy dla interfejsu API REST dostępu do danych usługi Time Series Insights.

- Dowiedz [się, jak kształtować zdarzenia](./time-series-insights-send-events.md).
