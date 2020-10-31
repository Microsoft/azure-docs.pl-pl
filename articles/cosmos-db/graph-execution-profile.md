---
title: Użyj profilu wykonywania, aby oszacować zapytania w Azure Cosmos DB interfejsie API Gremlin
description: Dowiedz się, jak rozwiązywać problemy i ulepszać zapytania Gremlin przy użyciu kroku profilu wykonania.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087489"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Jak użyć kroku profilu wykonywania do oceny zapytań aparatu Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Ten artykuł zawiera omówienie sposobu użycia kroku profilu wykonywania dla baz danych grafów interfejsu API Gremlin usługi Azure Cosmos DB. Ten krok zawiera istotne informacje dotyczące rozwiązywania problemów i optymalizacji zapytań oraz jest zgodny z dowolnymi zapytaniami Gremlin, które można wykonać względem konta interfejsu API Gremlin usługi Cosmos DB.

Aby użyć tego kroku, wystarczy dołączyć `executionProfile()` wywołanie funkcji na końcu zapytania Gremlin. **Zapytanie Gremlin zostanie wykonane** , a wynik operacji zwróci obiekt odpowiedzi JSON z profilem wykonywania zapytania.

Przykład:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Po wywołaniu `executionProfile()` kroku odpowiedź będzie obiektem JSON, który obejmuje wykonany krok Gremlin, łączny czas trwania i tablicę operatorów środowiska uruchomieniowego Cosmos DB, do których prowadzi instrukcja.

> [!NOTE]
> Ta implementacja profilu wykonywania nie została zdefiniowana w specyfikacji Apache Tinkerpop. Jest to specyficzne dla Azure Cosmos DB implementacji interfejsu API Gremlin.


## <a name="response-example"></a>Przykład odpowiedzi

Poniżej znajduje się przykładowe adnotacje danych wyjściowych, które zostaną zwrócone:

> [!NOTE]
> Ten przykład zawiera adnotacje z komentarzami wyjaśniającymi ogólną strukturę odpowiedzi. Rzeczywista odpowiedź executionProfile nie będzie zawierać żadnych komentarzy.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> Krok executionProfile spowoduje wykonanie zapytania Gremlin. Obejmuje to `addV` kroki lub `addE` , które spowodują utworzenie i zatwierdzi zmiany określone w zapytaniu. W efekcie jednostki żądań wygenerowane przez zapytanie Gremlin również będą obciążane opłatami.

## <a name="execution-profile-response-objects"></a>Obiekty odpowiedzi profilu wykonywania

Odpowiedź funkcji executionProfile () zwróci hierarchię obiektów JSON o następującej strukturze:
  - **Obiekt operacji Gremlin** : reprezentuje całą operację Gremlin, która została wykonana. Zawiera następujące właściwości.
    - `gremlin`: Jawna instrukcja Gremlin, która została wykonana.
    - `totalTime`: Czas (w milisekundach) wykonywania kroku. 
    - `metrics`: Tablica zawierająca każdy Cosmos DB operatory środowiska uruchomieniowego, które zostały wykonane w celu spełnienia zapytania. Ta lista jest posortowana w kolejności wykonywania.
    
  - **Operatory środowiska uruchomieniowego Cosmos DB** : reprezentuje każdy składnik całej operacji Gremlin. Ta lista jest posortowana w kolejności wykonywania. Każdy obiekt zawiera następujące właściwości:
    - `name`: Nazwa operatora. Jest to typ kroku, który został oceniony i wykonany. Więcej informacji można znaleźć w poniższej tabeli.
    - `time`: Ilość czasu (w milisekundach), jaką zajęł dany operator.
    - `annotations`: Zawiera dodatkowe informacje specyficzne dla operatora, który został wykonany.
    - `annotations.percentTime`: Procent łącznego czasu, jaki zajęło wykonanie określonego operatora.
    - `counts`: Liczba obiektów, które zostały zwrócone z warstwy magazynu przez ten operator. Ta wartość jest zawarta w `counts.resultCount` wartości skalarnej w.
    - `storeOps`: Reprezentuje operację magazynu, która może obejmować jedną lub wiele partycji.
    - `storeOps.fanoutFactor`: Przedstawia liczbę partycji, do których uzyskuje dostęp do tej konkretnej operacji magazynu.
    - `storeOps.count`: Przedstawia liczbę wyników zwracanych przez tę operację magazynu.
    - `storeOps.size`: Reprezentuje rozmiar w bajtach wyniku danej operacji magazynu.

Cosmos DB — operator środowiska uruchomieniowego Gremlin|Opis
---|---
`GetVertices`| Ten krok uzyskuje predykat zestawu obiektów z warstwy trwałości. 
`GetEdges`| Ten krok umożliwia uzyskanie krawędzi sąsiadujących z zestawem wierzchołków. Ten krok może skutkować jedną lub wieloma operacjami magazynu.
`GetNeighborVertices`| Ten krok umożliwia uzyskanie wierzchołków, które są połączone z zestawem krawędzi. Krawędzie zawierają klucze partycji i identyfikatory obu wierzchołków źródłowych i docelowych.
`Coalesce`| Ten krok polega na ocenie dwóch operacji za każdym razem, gdy `coalesce()` zostanie wykonany krok Gremlin.
`CartesianProductOperator`| Ten krok obliczy produkt kartezjańskiego między dwoma zestawami danych. Zwykle wykonywane za każdym razem, gdy predykaty `to()` lub `from()` są używane.
`ConstantSourceOperator`| Ten krok oblicza wyrażenie, aby utworzyć jako wynik wartość stałą.
`ProjectOperator`| Ten krok przygotowuje i serializować odpowiedź przy użyciu wyniku poprzednich operacji.
`ProjectAggregation`| Ten krok przygotowuje i serializować odpowiedź dla operacji agregowania.

> [!NOTE]
> Ta lista będzie nadal aktualizowana w miarę dodawania nowych operatorów.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Przykłady dotyczące analizowania odpowiedzi profilu wykonywania

Poniżej przedstawiono przykłady typowych optymalizacji, które można wycofać za pomocą odpowiedzi profilu wykonywania:
  - Zapytanie o wentylatory niewidome.
  - Niefiltrowane zapytanie.

### <a name="blind-fan-out-query-patterns"></a>Nieniewidome wzorce zapytań

Załóżmy, że następująca odpowiedź profilu wykonania z **partycjonowanego wykresu** :

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Następujące wnioski można wykonać z tego:
- Zapytanie jest wyszukiwaniem pojedynczego identyfikatora, ponieważ instrukcja Gremlin jest zgodna ze wzorcem `g.V('id')` .
- Oceniania z `time` metryki, opóźnienie tego zapytania jest wysokie, ponieważ jest ono [większe niż 10 ms dla operacji odczytu punktu](./introduction.md#guaranteed-speed-at-any-scale).
- Jeśli przejdziemy do `storeOps` obiektu, zobaczymy `fanoutFactor` , że jest to `5` , co oznacza, że do tej operacji uzyskuje się dostęp do [5 partycji](./partitioning-overview.md) .

W ramach tej analizy możemy określić, że pierwsze zapytanie uzyskuje dostęp do większej liczby partycji niż jest to konieczne. Można to rozwiązać przez określenie klucza partycjonowania w zapytaniu jako predykatu. Pozwoli to zmniejszyć opóźnienia i obsłużyć mniej kosztów na zapytanie. Dowiedz się więcej o [partycjonowaniu grafów](graph-partitioning.md). Bardziej optymalne może być zapytanie `g.V('tt0093640').has('partitionKey', 't1001')` .

### <a name="unfiltered-query-patterns"></a>Niefiltrowane wzorce zapytań

Porównaj poniższe dwa odpowiedzi dotyczące profilu wykonania. Dla uproszczenia w poniższych przykładach użyto pojedynczego wykresu partycjonowanego.

Pierwsze zapytanie pobiera wszystkie wierzchołki z etykietą `tweet` , a następnie uzyskuje sąsiadujące wierzchołki:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Zwróć uwagę na profil tego samego zapytania, ale teraz z dodatkowym filtrem, `has('lang', 'en')` przed eksplorowaniem sąsiednich wierzchołków:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Te dwa zapytania osiągnęły ten sam wynik, ale pierwszy z nich będzie wymagał większej liczby jednostek żądań, ponieważ wymagało to iteracji większego początkowego zestawu danych przed wysłaniem zapytania do sąsiadujących elementów. Wskaźniki tego zachowania można zobaczyć, porównując następujące parametry z obu odpowiedzi:
- `metrics[0].time`Wartość jest wyższa w pierwszej odpowiedzi, co oznacza, że ten pojedynczy krok trwał dłużej.
- `metrics[0].counts.resultsCount`Wartość jest większa, jak również w pierwszej odpowiedzi, co oznacza, że początkowy roboczy zestaw danych był większy.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obsługiwanych funkcjach Gremlin](gremlin-support.md) w Azure Cosmos DB. 
* Dowiedz się więcej o [interfejsie API Gremlin w Azure Cosmos DB](graph-introduction.md).