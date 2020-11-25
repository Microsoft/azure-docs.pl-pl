---
title: Reguły spłaszczania i ucieczki JSON — Azure Time Series Insights Gen2 | Microsoft Docs
description: Informacje na temat sposobu spłaszczania, ucieczki i obsługi tablicowej JSON w Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: 0839d2c734418824952f37cb177490e56e1133c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017971"
---
# <a name="json-flattening-escaping-and-array-handling"></a>Spłaszczanie danych JSON, ucieczka i obsługa tablic

Azure Time Series Insights środowisko Gen2 będzie dynamicznie tworzyć kolumny magazynu ciepłego i zimnego, zgodnie z określonym zestawem konwencji nazewnictwa. W przypadku pozyskania zdarzenia zestaw reguł jest stosowany do nazw ładunku i właściwości JSON. Obejmują one anulowanie niektórych znaków specjalnych i spłaszczonie zagnieżdżonych obiektów JSON. Ważne jest, aby poznać te reguły, aby zrozumieć, w jaki sposób kształt JSON będzie miał wpływ na sposób przechowywania i wykonywania zapytań dotyczących zdarzeń. Pełna lista reguł znajduje się w poniższej tabeli. Przykłady & B pokazują również, jak można efektywnie wsadowo wiele szeregów czasowych w tablicy.

> [!IMPORTANT]
>
> * Przejrzyj poniższe reguły przed wybraniem [Właściwości identyfikatora szeregów czasowych](./how-to-select-tsid.md) i/lub właściwych dla zdarzenia [sygnatury czasowej](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)źródła zdarzeń. Jeśli identyfikator TS lub sygnatura czasowa znajduje się w obiekcie zagnieżdżonym lub ma co najmniej jeden znak specjalny poniżej, należy się upewnić, że nazwa właściwości, która jest zgodna z nazwą kolumny *po* zastosowaniu reguł pozyskiwania. Zobacz przykład [B](concepts-json-flattening-escaping-rules.md#example-b) poniżej.

| Reguła | Przykładowy kod JSON | [Składnia wyrażenia szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Nazwa kolumny właściwości w Parquet
|---|---|---|---|
| Typ danych Azure Time Series Insights Gen2 jest dołączany na końcu nazwy kolumny jako "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| [Właściwość sygnatury czasowej](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) źródła zdarzeń zostanie zapisana w Azure Time Series Insights Gen2 jako "timestamp" w magazynie i wartość przechowywana w formacie UTC. Możesz dostosować właściwość sygnatury czasowej źródła zdarzeń, aby spełniała potrzeby rozwiązania, ale nazwa kolumny w magazynie ciepłego i zimnego to "timestamp". Inne właściwości JSON typu DateTime, które nie są sygnaturą czasową źródła zdarzenia, zostaną zapisane z "_datetime" w nazwie kolumny, jak wspomniano w powyższej regule.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| Nazwy właściwości JSON, które zawierają znaki specjalne. [\ i "są wyprowadzane przy użyciu [" i "]  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| W obrębie ["i"] istnieje dodatkowe anulowanie apostrofów i ukośników odwrotnych. Pojedynczy cytat zostanie zapisany jako ", a ukośnik odwrotny zostanie zapisany jako \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| Zagnieżdżone obiekty JSON są spłaszczone z kropką jako separatorem. Obsługiwane jest zagnieżdżanie do 10 poziomów. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long``$event['series']['value'].Long`lub`$event.series['value'].Long` |  `series.value_long` |
| Tablice typów pierwotnych są przechowywane jako typ dynamiczny |  ```"values": [154, 149, 147]``` | Typy dynamiczne można pobrać tylko za pośrednictwem interfejsu API [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) | `values_dynamic` |
| Tablice zawierające obiekty mają dwa zachowania w zależności od zawartości obiektu: jeśli identyfikatory TS (s) lub właściwości sygnatur czasowych znajdują się w obiektach tablicy, tablica zostanie wycofana w taki sposób, że początkowy ładunek JSON generuje wiele zdarzeń. Pozwala to na przetwarzanie wsadowe wielu zdarzeń w jedną strukturę JSON. Wszystkie właściwości najwyższego poziomu, które są elementami równorzędnymi do tablicy, zostaną zapisane przy użyciu każdego nierzutowanego obiektu. Jeśli identyfikatory TS i sygnatura czasowa *nie* znajdują się w tablicy, zostanie ona zapisana jako typ dynamiczny. | Zobacz przykłady [a](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b)i [C](concepts-json-flattening-escaping-rules.md#example-c) poniżej
| Tablice zawierające elementy mieszane nie są spłaszczone. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Typy dynamiczne można pobrać tylko za pośrednictwem interfejsu API [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) | `values_dynamic` |
| 512 znaków to limit nazw właściwości JSON. Jeśli długość nazwy przekracza 512 znaków, zostanie ona obcięta do 512 i zostanie dołączona wartość "_<" skrótu ">". **Należy zauważyć** , że dotyczy to również nazw właściwości, które zostały połączone z obiektu spłaszczonego, co oznacza zagnieżdżoną ścieżkę obiektu. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Zrozumienie podwójnego zachowania dla tablic

Tablice obiektów będą przechowywane całościowo lub podzielone na wiele zdarzeń w zależności od sposobu modelowania danych. Pozwala to na użycie tablicy do zdarzeń wsadowych i uniknięcie powtarzających się właściwości telemetrii, które są zdefiniowane na poziomie obiektu głównego. Przetwarzanie wsadowe może być korzystne, ponieważ skutkuje mniej Event Hubs lub IoT Hub wysłanych komunikatów.

Jednak w niektórych przypadkach tablice zawierające obiekty są zrozumiałe tylko w kontekście innych wartości. Utworzenie wielu zdarzeń spowoduje nieznaczenie danych. Aby zapewnić, że tablica obiektów jest przechowywana jako typ dynamiczny, postępuj zgodnie z poniższymi wskazówkami dotyczącymi modelowania danych i zapoznaj się z [przykładem C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Jak dowiedzieć się, czy moja tablica obiektów będzie generować wiele zdarzeń

Jeśli co najmniej jeden identyfikator szeregów czasowych jest zagnieżdżony w obrębie obiektów w tablicy *lub* Jeśli właściwość sygnatury czasowej źródła zdarzenia jest zagnieżdżona, aparat pozyskiwania podzieli go w celu utworzenia wielu zdarzeń. Nazwy właściwości podane dla identyfikatorów i/lub sygnatury czasowej usług terminalowych powinny być zgodne z powyższymi regułami spłaszczania i w związku z tym wskazują kształt JSON. Zapoznaj się z poniższymi przykładami i zapoznaj się z przewodnikiem dotyczącym [wybierania identyfikatora szeregów czasowych.](./how-to-select-tsid.md)

### <a name="example-a"></a>Przykład

Identyfikator szeregów czasowych w elemencie głównym obiektu i w zagnieżdżonej sygnaturze czasowej \
**Identyfikator szeregów czasowych środowiska:**`"id"`\
**Sygnatura czasowa źródła zdarzeń:**`"values.time"`\
**Ładunek JSON:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Wynik w pliku Parquet:**\
Powyższa konfiguracja i ładunek będą generować trzy kolumny i cztery zdarzenia

| sygnatura czasowa  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Przykład B

Złożony identyfikator szeregów czasowych z jedną właściwością zagnieżdżoną \
**Identyfikator szeregów czasowych środowiska:** `"plantId"` lub `"telemetry.tagId"`\
**Sygnatura czasowa źródła zdarzeń:**`"timestamp"`\
**Ładunek JSON:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Wynik w pliku Parquet:**\
Powyższa konfiguracja i ładunek będą generować cztery kolumny i sześć zdarzeń

| sygnatura czasowa  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30,9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19,960796 |

### <a name="example-c"></a>Przykład C

Identyfikator szeregów czasowych i sygnatura czasowa znajdują się w katalogu głównym obiektu \
**Identyfikator szeregów czasowych środowiska:**`"id"`\
**Sygnatura czasowa źródła zdarzeń:**`"timestamp"`\
**Ładunek JSON:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Wynik w pliku Parquet:**\
Powyższa konfiguracja i ładunek spowodują utworzenie trzech kolumn i jednego zdarzenia

| sygnatura czasowa  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [ograniczeniami przepływności](./concepts-streaming-ingress-throughput-limits.md) środowiska