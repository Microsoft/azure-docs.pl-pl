---
title: Rozwiązywanie problemów z kwerendą podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB
description: Dowiedz się, jak identyfikować, diagnozować i rozwiązywać problemy z interfejsem API Azure Cosmos DB na potrzeby zapytań MongoDB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 10/12/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 88ef081c75a64b5cb7517ba6994834b3a64a0e6f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340893"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Rozwiązywanie problemów z kwerendą podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym artykule przedstawiono ogólne zalecane podejście do rozwiązywania problemów z zapytaniami w Azure Cosmos DB. Chociaż nie należy traktować kroków opisanych w tym artykule, pełna ochrona przed potencjalnymi problemami związanymi z zapytaniami zawiera najpopularniejsze porady dotyczące wydajności. Tego artykułu należy używać jako lokalizacji początkowej do rozwiązywania problemów z powolnymi lub kosztownymi zapytaniami w interfejsie API Azure Cosmos DB MongoDB. Jeśli używasz interfejsu API Azure Cosmos DB Core (SQL), zobacz artykuł [Przewodnik dotyczący rozwiązywania problemów z interfejsem API SQL](troubleshoot-query-performance.md) .

Optymalizacje zapytań w Azure Cosmos DB są szeroko kategoryzowane w następujący sposób:

- Optymalizacje, które zmniejszają opłatę jednostki żądania (RU) zapytania
- Optymalizacje, które po prostu skracają opóźnienia

W przypadku obniżenia opłaty za usługę RU z kwerendy zazwyczaj zmniejsza się również opóźnienia.

W tym artykule przedstawiono przykłady, które można utworzyć ponownie przy użyciu [zestawu danych odżywiania](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> W tym artykule przyjęto założenie, że korzystasz z wersji 3,6 interfejsu API Azure Cosmos DB "s MongoDB. Niektóre zapytania, które działają nieprawidłowo w wersji 3,2, mają znaczące ulepszenia w wersji 3,6. Uaktualnij do wersji 3,6, zgłaszając [żądanie pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="use-explain-command-to-get-metrics"></a>Użyj polecenia $explain, aby uzyskać metryki

Podczas optymalizowania zapytania w Azure Cosmos DB, pierwszym krokiem jest zawsze [uzyskanie opłaty](find-request-unit-charge-mongodb.md) za obiekt ru dla tego zapytania. Jako orientacyjną wskazówkę należy zapoznać się z sposobami obniżenia opłaty za usługę RU dla zapytań o opłaty większe niż 50 jednostek ru. 

Oprócz uzyskania opłaty za usługę RU należy użyć `$explain` polecenia, aby uzyskać metryki użycia zapytań i indeksów. Oto przykład, który uruchamia zapytanie i używa `$explain` polecenia, aby wyświetlić metryki użycia zapytań i indeksów:

**$explain polecenie:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Rozdzielczości**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`$explain`Dane wyjściowe polecenia są długie i zawierają szczegółowe informacje o wykonywaniu zapytania. Ogólnie rzecz biorąc, istnieje kilka sekcji, w których należy skoncentrować się na optymalizowaniu wydajności zapytań:

| Metryka | Opis | 
| ------ | ----------- |
| `timeInclusiveMS` | Opóźnienie zapytania wewnętrznej bazy danych |
| `pathsIndexed` | Pokazuje indeksy używane przez zapytanie | 
| `pathsNotIndexed` | Pokazuje indeksy, które mogą być używane w zapytaniu, jeśli są dostępne | 
| `shardInformation` | Podsumowanie wydajności zapytań dla określonej [partycji fizycznej](./partitioning-overview.md#physical-partitions) | 
| `retrievedDocumentCount` | Liczba dokumentów załadowanych przez aparat zapytań | 
| `outputDocumentCount` | Liczba dokumentów zwróconych w wynikach zapytania | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Szacowane dodatkowe opóźnienie zapytania z powodu ograniczenia szybkości | 

Po otrzymaniu metryk zapytania Porównaj je `retrievedDocumentCount` z `outputDocumentCount` dla zapytania. To porównanie służy do identyfikowania odpowiednich sekcji do przejrzenia w tym artykule. `retrievedDocumentCount`Jest to liczba dokumentów wymaganych do załadowania aparatu zapytań. `outputDocumentCount`Jest to liczba dokumentów, które były niezbędne dla wyników zapytania. Jeśli wartość `retrievedDocumentCount`  jest znacznie wyższa niż, istniała `outputDocumentCount` co najmniej jedna część zapytania, która nie może użyć indeksu i jest wymagana do przeskanowania.

Zapoznaj się z następującymi sekcjami, aby zrozumieć odpowiednie optymalizacje zapytań dla danego scenariusza.

### <a name="querys-ru-charge-is-too-high"></a>Zbyt wysoka opłata za zapytanie RU

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Liczba pobranych dokumentów jest znacznie wyższa niż liczba dokumentów wyjściowych

- [Uwzględnij niezbędne indeksy.](#include-necessary-indexes)

- [Zrozumieć, które operacje agregacji używają indeksu.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Liczba pobranych dokumentów jest w przybliżeniu równa liczbie dokumentów wyjściowych

- [Minimalizuj zapytania między partycjami.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Opłata za usługę RU dla zapytania jest akceptowalna, ale opóźnienie jest wciąż zbyt wysokie

- [Zwiększ bliskość.](#improve-proximity)

- [Zwiększ przepływność aprowizacji.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Zapytania, w przypadku których liczba pobranych dokumentów przekracza liczbę dokumentów wyjściowych

 `retrievedDocumentCount`Jest to liczba dokumentów, które aparat kwerend potrzebował do załadowania. `outputDocumentCount`Jest to liczba dokumentów zwróconych przez zapytanie. Jeśli wartość `retrievedDocumentCount` jest znacznie wyższa niż, istniała `outputDocumentCount` co najmniej jedna część zapytania, która nie może użyć indeksu i jest wymagana do przeskanowania.

Oto przykład zapytania skanowania, które nie było całkowicie obsługiwane przez indeks:

**$explain polecenie:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Rozdzielczości**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

Wartość `retrievedDocumentCount` (8618) jest znacznie wyższa niż wartość `outputDocumentCount` (1), co oznacza, że to zapytanie wymagało skanowania dokumentów. 

### <a name="include-necessary-indexes"></a>Uwzględnij niezbędne indeksy

Należy sprawdzić `pathsNotIndexed` tablicę i dodać te indeksy. W tym przykładzie ścieżki `foodGroup` i `description` powinny być indeksowane.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Najlepsze rozwiązania dotyczące indeksowania w interfejsie API Azure Cosmos DB MongoDB różnią się od MongoDB. W interfejsie API Azure Cosmos DB dla MongoDB indeksy złożone są używane tylko w zapytaniach, które muszą być efektywnie sortowane według wielu właściwości. Jeśli masz zapytania z filtrami dla wielu właściwości, należy utworzyć indeksy jednego pola dla każdej z tych właściwości. Predykaty zapytań mogą używać wielu indeksów pojedynczego pola.

[Indeksy wieloznaczne](mongodb-indexing.md#wildcard-indexes) mogą uprościć indeksowanie. W przeciwieństwie do MongoDB, indeksy symboli wieloznacznych mogą obsługiwać wiele pól w predykatach zapytań. W przypadku użycia jednego indeksu wieloznacznego zamiast tworzenia oddzielnego indeksu dla każdej właściwości nie będzie różnica w wydajności zapytań. Dodanie indeksu wieloznacznego dla wszystkich właściwości jest najprostszym sposobem optymalizacji wszystkich zapytań.

Nowe indeksy można dodać w dowolnym momencie, bez wpływu na dostępność operacji zapisu lub odczytu. [Postęp przekształcania indeksów można śledzić](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Informacje o tym, które operacje agregacji używają indeksu

W większości przypadków operacje agregacji w interfejsie API Azure Cosmos DB dla MongoDB będą częściowo używały indeksów. Zwykle aparat zapytań zastosuje najpierw filtry równości i zakresu, a następnie użyj indeksów. Po zastosowaniu tych filtrów aparat zapytań może oszacować dodatkowe filtry i wykonać ładowanie pozostałych dokumentów w celu obliczenia agregacji, jeśli jest to konieczne. 

Oto przykład:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

W takim przypadku indeksy mogą zoptymalizować `$match` etap. Dodanie indeksu dla `foodGroup` znacznie poprawi wydajność zapytań. Podobnie jak w MongoDB, należy jak `$match` najszybciej umieścić w potoku agregacji, aby zmaksymalizować użycie indeksów.

W interfejsie API Azure Cosmos DB dla MongoDB indeksy nie są używane dla rzeczywistej agregacji, która w tym przypadku jest `$max` . Dodanie indeksu w programie `version` nie poprawi wydajności zapytań.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Zapytania, w przypadku których liczba pobranych dokumentów jest równa liczbie dokumentów wyjściowych

Jeśli `retrievedDocumentCount` jest to przybliżenie równe `outputDocumentCount` , aparat zapytań nie musiał skanować wielu zbędnych dokumentów.

### <a name="minimize-cross-partition-queries"></a>Minimalizuj zapytania między partycjami

Azure Cosmos DB używa [partycjonowania](partitioning-overview.md) do skalowania poszczególnych kontenerów jako jednostki żądania i magazynu danych. Każda partycja fizyczna ma oddzielny i niezależny indeks. Jeśli zapytanie ma filtr równości pasujący do klucza partycji kontenera, należy sprawdzić tylko odpowiedni indeks partycji. Ta optymalizacja zmniejsza łączną liczbę jednostek ru wymaganych przez zapytanie. [Dowiedz się więcej o różnicach między zapytaniami w partycji a zapytaniami między partycjami](how-to-query-container.md).

Jeśli masz dużą liczbę zainicjowanych jednostek ru (ponad 30 000) lub dużą ilość przechowywanych danych (więcej niż około 100 GB), prawdopodobnie masz wystarczająco duży rozmiar kontenera, aby zobaczyć znaczną redukcję opłat za zapytania RU. 

Możesz sprawdzić `shardInformation` tablicę, aby zrozumieć metryki zapytania dla każdej pojedynczej partycji fizycznej. Liczba unikatowych `shardKeyRangeId` wartości to liczba partycji fizycznych, w których zapytanie wymagało wykonania. W tym przykładzie zapytanie zostało wykonane na czterech fizycznych partycjach. Ważne jest, aby zrozumieć, że wykonywanie jest całkowicie niezależne od użycia indeksu. Innymi słowy, zapytania między partycjami nadal mogą używać indeksów.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Optymalizacje zmniejszające czas oczekiwania na zapytanie

W wielu przypadkach opłata za usługę RU może być akceptowalna, gdy opóźnienie zapytania jest wciąż zbyt wysokie. Poniższe sekcje zawierają omówienie wskazówek dotyczących skracania opóźnień zapytań. W przypadku uruchomienia tego samego zapytania wiele razy w tym samym zestawie danych w każdym momencie będzie ona mieć ten sam koszt. Opóźnienia zapytań mogą się różnić między wykonaniami zapytań.

### <a name="improve-proximity"></a>Zwiększ bliskość

Zapytania uruchamiane z innego regionu niż konto Azure Cosmos DB będą miały wyższy czas oczekiwania niż w przypadku uruchomienia w tym samym regionie. Na przykład jeśli używasz kodu na komputerze stacjonarnym, należy oczekiwać opóźnienia na dziesiątki lub setki milisekund (lub więcej) niż w przypadku, gdy zapytanie pochodzi z maszyny wirtualnej w tym samym regionie platformy Azure co Azure Cosmos DB. Jest ona prosta do [dystrybuowania danych w Azure Cosmos DB](tutorial-global-distribution-mongodb.md) , aby zapewnić, że dane będą bliżej Twojej aplikacji.

### <a name="increase-provisioned-throughput"></a>Zwiększ przepływność aprowizacji

W Azure Cosmos DB zainicjowana przepływność jest mierzona w jednostkach żądania (jednostek ru). Wyobraź sobie, że masz zapytanie, które zużywa 5 jednostek ru przepływności. Na przykład jeśli zainicjujesz 1 000 jednostek ru, będzie można uruchamiać zapytanie o godzinie 200 na sekundę. Jeśli podjęto próbę uruchomienia zapytania w przypadku braku wystarczającej przepływności, Azure Cosmos DB będzie ograniczać liczbę żądań. Interfejs API Azure Cosmos DB dla MongoDB automatycznie ponowi próbę wykonania tego zapytania po krótkim czasie oczekiwania. Żądania ograniczające przepustowość są dłuższe, więc zwiększenie zainicjowanej przepływności może poprawić opóźnienia zapytań.

Wartość `estimatedDelayFromRateLimitingInMilliseconds` daje sensie potencjalnych korzyści opóźnienia w przypadku zwiększenia przepływności.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z wydajnością zapytań (interfejs API SQL)](troubleshoot-query-performance.md)
* [Zarządzanie indeksowaniem w interfejsie API Azure Cosmos DB dla MongoDB](mongodb-indexing.md)