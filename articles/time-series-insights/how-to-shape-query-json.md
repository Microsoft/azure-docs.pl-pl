---
title: Najlepsze rozwiązania dotyczące kształtowania zapytań JSON-Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak poprawić wydajność zapytań Azure Time Series Insights, zmieniając kod JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: b5723433cca20c934d4cb5b129d77c1c6d65feef
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617115"
---
# <a name="shape-json-to-maximize-query-performance-in-your-gen1-environment"></a>Shape JSON w celu zmaksymalizowania wydajności zapytań w środowisku Gen1

> [!CAUTION]
> To jest artykuł Gen1.

Ten artykuł zawiera wskazówki dotyczące sposobu tworzenia kształtu JSON w celu zmaksymalizowania wydajności zapytań Azure Time Series Insights.

## <a name="video"></a>Wideo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi kształtowania JSON w celu spełnienia wymagań dotyczących magazynu.</br>

> [!VIDEO <https://www.youtube.com/embed/b2BD5hwbg5I>]

## <a name="best-practices"></a>Najlepsze rozwiązania

Pomyśl o sposobie wysyłania zdarzeń do Azure Time Series Insights. Mianowicie:

1. Wysyłaj dane za pośrednictwem sieci tak efektywnie, jak to możliwe.
1. Upewnij się, że dane są przechowywane w sposób, aby można było wykonywać agregacje odpowiednie dla danego scenariusza.
1. Upewnij się, że nie osiągnięto limitów maksymalnej właściwości Azure Time Series Insights:
   - 600 właściwości (kolumny) dla środowisk S1.
   - 800 właściwości (kolumny) dla środowisk S2.

Poniższe wskazówki ułatwiają zapewnienie najlepszej możliwej wydajności zapytań:

1. Jako nazwy właściwości nie należy używać właściwości dynamicznych, takich jak identyfikator tagu. Służy do osiągnięcia maksymalnego limitu właściwości.
1. Nie wysyłaj niepotrzebnych właściwości. Jeśli właściwość zapytania nie jest wymagana, nie należy jej wysyłać. W ten sposób można uniknąć ograniczeń dotyczących magazynu.
1. Użyj [danych referencyjnych](time-series-insights-add-reference-data-set.md) , aby uniknąć wysyłania danych statycznych przez sieć.
1. Udostępnianie właściwości wymiarów między wieloma zdarzeniami w celu skuteczniejszego wysyłania danych przez sieć.
1. Nie używaj głębokiego zagnieżdżania tablic. Azure Time Series Insights obsługuje do dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Azure Time Series Insights spłaszcza tablic w komunikatach do wielu zdarzeń z parami wartości właściwości.
1. Jeśli istnieje tylko kilka miar dla wszystkich lub większości zdarzeń, lepiej jest wysłać te miary jako oddzielne właściwości w obrębie tego samego obiektu. Ich wysyłanie osobno zmniejsza liczbę zdarzeń i może poprawić wydajność zapytań, ponieważ wymaga przetworzenia mniejszych zdarzeń. Jeśli istnieje kilka miar, wysłanie ich jako wartości w pojedynczej właściwości minimalizuje możliwość osiągnięcia maksymalnego limitu właściwości.

## <a name="example-overview"></a>Przykład — Omówienie

W poniższych dwóch przykładach pokazano, jak wysyłać zdarzenia, aby wyróżnić poprzednie zalecenia. Poniższy przykład umożliwia przejrzenie sposobu zastosowania zaleceń.

Przykłady są oparte na scenariuszu, w którym wiele urządzeń wysyła pomiary lub sygnały. Pomiary lub sygnały mogą być natężeniem przepływu, ciśnieniem oleju, temperatury i wilgotności. W pierwszym przykładzie istnieje kilka pomiarów dla wszystkich urządzeń. Drugi przykład zawiera wiele urządzeń, a każde urządzenie wysyła wiele unikatowych pomiarów.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenariusz 1: istnieje tylko kilka pomiarów

> [!TIP]
> Zalecamy wysłanie każdego pomiaru lub sygnału jako oddzielnej właściwości lub kolumny.

W poniższym przykładzie istnieje pojedynczy komunikat IoT Hub platformy Azure, w którym tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiarów. Tablica zewnętrzna używa danych referencyjnych w celu zwiększenia wydajności wiadomości. Dane referencyjne zawierają metadane urządzeń, które nie ulegają zmianie przy każdym zdarzeniu, ale udostępniają przydatne właściwości analizy danych. Przetwarzanie wsadowe wspólnych wartości wymiarów i korzystanie z danych referencyjnych polega na zapisywaniu bajtów przesyłanych przez sieć, co sprawia, że komunikat jest bardziej wydajny.

Należy wziąć pod uwagę następujący ładunek JSON wysłany do środowiska Azure Time Series Insights GA przy użyciu [obiektu komunikatu urządzenia IoT](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) , który jest serializowany do formatu JSON w przypadku wysłania do chmury platformy Azure:

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

- Tabela danych referencyjnych, która ma klucz **deviceId**właściwości klucza:

   | deviceId | Identyfikatora | deviceLocation |
   | --- | --- | --- |
   | FXXX | \_dane wiersza | UE |
   | FYYY | \_dane wiersza | USA |

- Azure Time Series Insights tabeli zdarzeń po spłaszczeniu:

   | deviceId | Identyfikatora | deviceLocation | sygnatura czasowa | wiele. Szybkość przepływu ft3/s | wiele. Psi ciśnienie oleju silnikowego |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | \_dane wiersza | UE | 2018 R-01-17T01:17:00Z | 1.0172575712203979 | 34,7 |
   | FXXX | \_dane wiersza | UE | 2018 R-01-17T01:17:00Z | 2.445906400680542 | 49,2 |
   | FYYY | \_dane wiersza | USA | 2018 R-01-17T01:18:00Z | 0.58015072345733643 | 22,2 |

> [!NOTE]

> - Kolumna **deviceId** służy jako nagłówek kolumny dla różnych urządzeń w flotie. Właściwość **deviceId** , która ma własną nazwę właściwości, ogranicza łączną liczbę urządzeń do 595 (dla środowisk S1) lub 795 (dla środowisk S2) z innymi pięcioma kolumnami.
> - Nie można uniknąć niepotrzebnych właściwości (na przykład informacje dotyczące marki i modelu). Ze względu na to, że w przyszłości nie będą wysyłane zapytania do właściwości, eliminuje to lepszą wydajność sieci i magazynu.
> - Dane referencyjne są używane w celu zmniejszenia liczby bajtów transferowanych przez sieć. Dwa atrybuty **MessageID** i **deviceLocation** są sprzężone przy użyciu właściwości Key **deviceId**. Te dane są przyłączone do danych telemetrycznych w czasie, a następnie przechowywane w Azure Time Series Insights na potrzeby wykonywania zapytań.
> - Używane są dwie warstwy zagnieżdżania, które są maksymalną ilością zagnieżdżenia obsługiwaną przez Azure Time Series Insights. Należy unikać głęboko zagnieżdżonych tablic.
> - Miary są wysyłane jako oddzielne właściwości w obrębie tego samego obiektu, ponieważ istnieje kilka miar. Tutaj, **Seria. Szybkość przepływu** i **Seria. Ft3 ciśnienia oleju silnikowego** są unikatowymi kolumnami.

## <a name="scenario-two-several-measures-exist"></a>Scenariusz dwa: istnieją różne miary

> [!TIP]
> Zalecamy wysyłanie pomiarów jako krotek "Type", "jednostki" i "value".

Przykładowy ładunek JSON:

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

- Tabela danych referencyjnych, która zawiera właściwości klucza **deviceId** i **Series. tagId**:

   | deviceId | Seria. tagId | Identyfikatora | deviceLocation | typ | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | \_dane wiersza | UE | Szybkość przepływu | ft3/s |
   | FXXX | oilPressure | \_dane wiersza | UE | Ciśnienie oleju silnikowego | psi |
   | FYYY | pumpRate | \_dane wiersza | USA | Szybkość przepływu | ft3/s |
   | FYYY | oilPressure | \_dane wiersza | USA | Ciśnienie oleju silnikowego | psi |

- Azure Time Series Insights tabeli zdarzeń po spłaszczeniu:

   | deviceId | Seria. tagId | Identyfikatora | deviceLocation | typ | unit | sygnatura czasowa | Serie. wartość |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | \_dane wiersza | UE | Szybkość przepływu | ft3/s | 2018 R-01-17T01:17:00Z | 1.0172575712203979 |
   | FXXX | oilPressure | \_dane wiersza | UE | Ciśnienie oleju silnikowego | psi | 2018 R-01-17T01:17:00Z | 34,7 |
   | FXXX | pumpRate | \_dane wiersza | UE | Szybkość przepływu | ft3/s | 2018 R-01-17T01:17:00Z | 2.445906400680542 |
   | FXXX | oilPressure | \_dane wiersza | UE | Ciśnienie oleju silnikowego | psi | 2018 R-01-17T01:17:00Z | 49,2 |
   | FYYY | pumpRate | \_dane wiersza | USA | Szybkość przepływu | ft3/s | 2018 R-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | \_dane wiersza | USA | Ciśnienie oleju silnikowego | psi | 2018 R-01-17T01:18:00Z | 22,2 |

> [!NOTE]

> - Kolumny **deviceId** i **Series. tagId** pełnią rolę nagłówków kolumn dla różnych urządzeń i tagów w flotie. Użycie każdego z nich jako własnego atrybutu ogranicza zapytanie do 594 (dla środowisk S1) lub 794 (dla środowisk S2) Łączna liczba urządzeń z innymi sześcioma kolumnami.
> - Niepotrzebne właściwości zostały wykluczone z przyczyn podanych w pierwszym przykładzie.
> - Dane referencyjne są używane w celu zmniejszenia liczby bajtów transferowanych przez sieć przez wprowadzenie identyfikatora **deviceId**, który jest używany dla unikatowej pary wartości **MessageID** i **deviceLocation**. Złożona seria kluczy **. tagId** jest używana dla unikatowej pary **typów** i **jednostek**. Klucz złożony umożliwia użycie pary  **deviceId** i **Series. tagId** do odwoływania się do czterech wartości: **MessageID, deviceLocation, Type** i **Unit**. Te dane są przyłączone do danych telemetrycznych w czasie. Jest on następnie przechowywany w Azure Time Series Insights na potrzeby wykonywania zapytań.
> - Używane są dwie warstwy zagnieżdżania, z przyczyn cytowanych w pierwszym przykładzie.

### <a name="for-both-scenarios"></a>Dla obu scenariuszy

Dla właściwości o dużej liczbie możliwych wartości najlepiej wysyłać jako odrębne wartości w jednej kolumnie, zamiast tworzyć nową kolumnę dla każdej wartości. W poprzednich dwóch przykładach:

- W pierwszym przykładzie kilka właściwości ma kilka wartości, więc należy wykonać każdą odrębną właściwość.
- W drugim przykładzie miary nie są określone jako pojedyncze właściwości. Zamiast tego są one tablicą wartości lub miar we właściwości typowej serii. Zostanie wysłany nowy klucz **tagId** , który tworzy nową kolumnę **serie. tagId** w spłaszczonej tabeli. Nowy **Typ** i **Jednostka** właściwości są tworzone przy użyciu danych referencyjnych, dzięki czemu limit właściwości nie zostanie osiągnięty.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat wysyłania [komunikatów dotyczących urządzeń IoT Hub do chmury](../iot-hub/iot-hub-devguide-messages-construct.md).

- Przeczytaj [Azure Time Series Insights składnię zapytania](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-syntax) , aby dowiedzieć się więcej o składni zapytań dla interfejsu API REST Azure Time Series Insights dostępu do danych.

- Dowiedz się [, jak kształtować zdarzenia](./time-series-insights-send-events.md).
