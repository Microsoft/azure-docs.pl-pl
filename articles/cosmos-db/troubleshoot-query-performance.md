---
title: Rozwiązywanie problemów z zapytaniami podczas korzystania z usługi Azure Cosmos DB
description: Dowiedz się, jak identyfikować, diagnozować i rozwiązywać problemy z zapytaniami SQL usługi Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870452"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Rozwiązywanie problemów z zapytaniami podczas korzystania z usługi Azure Cosmos DB

W tym artykule oszukuje się ogólne zalecane podejście do rozwiązywania problemów z zapytaniami w usłudze Azure Cosmos DB. Chociaż nie należy brać pod uwagę kroki opisane w tym artykule pełnej obrony przed potencjalnymi problemami z zapytaniami, poniżej uwzględniliśmy najczęściej zadawane wskazówki dotyczące wydajności. Tego artykułu należy użyć jako miejsca rozpoczęcia rozwiązywania problemów z powolnymi lub kosztownymi zapytaniami w interfejsie API rdzenia usługi Azure Cosmos DB (SQL). Dzienniki [diagnostyki](cosmosdb-monitor-resource-logs.md) można również użyć do identyfikowania kwerend, które są powolne lub zużywają znaczne ilości przepływności.

Optymalizacje zapytań można zasadniczo kategoryzować w usłudze Azure Cosmos DB:

- Optymalizacje zmniejszające obciążenie kwerendy jednostką żądań (RU)
- Optymalizacje, które po prostu zmniejszają opóźnienia

Jeśli zmniejszysz ładunek RU kwerendy, prawie na pewno zmniejszyć opóźnienie, jak również.

W tym artykule przedstawiono przykłady, które można odtworzyć przy użyciu zestawu danych [żywieniowych.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

## <a name="common-sdk-issues"></a>Typowe problemy z SDK

- Aby uzyskać najlepszą wydajność, postępuj zgodnie [z poradami dotyczącymi wydajności](performance-tips.md).
    > [!NOTE]
    > Aby zwiększyć wydajność, zalecamy przetwarzanie hosta 64-bitowego systemu Windows. Zestaw SDK SQL zawiera natywną bibliotekę ServiceInterop.dll do analizowania i optymalizowania zapytań lokalnie. ServiceInterop.dll jest obsługiwany tylko na platformie Windows x64. W przypadku systemu Linux i innych nieobsługiconych platform, na których serviceinterop.dll nie jest dostępny, zostanie wykonane dodatkowe wywołanie sieciowe do bramy w celu uzyskania zoptymalizowanego zapytania.
- Można ustawić `MaxItemCount` dla kwerend, ale nie można określić minimalną liczbę elementów.
    - Kod powinien obsługiwać dowolny rozmiar `MaxItemCount`strony, od zera do .
    - Liczba elementów na stronie będzie zawsze mniejsza `MaxItemCount`niż określona . Jednak, `MaxItemCount` jest ściśle maksimum i nie może być mniej wyników niż ta kwota.
- Czasami kwerendy mogą mieć puste strony, nawet jeśli są wyniki na przyszłej stronie. Przyczyny tego mogą być:
    - Zestaw SDK może wykonywać wiele wywołań sieciowych.
    - Kwerenda może zdążyć dużo czasu, aby pobrać dokumenty.
- Wszystkie kwerendy mają token kontynuacji, który pozwoli kwerendy kontynuować. Pamiętaj, aby całkowicie opróżnić zapytanie. Spójrz na próbki SDK i `while` użyj `FeedIterator.HasMoreResults` pętli, aby opróżnić całą kwerendę.

## <a name="get-query-metrics"></a>Pobierz metryki zapytań

Podczas optymalizacji kwerendy w usłudze Azure Cosmos DB, pierwszym krokiem jest zawsze, aby [uzyskać metryki kwerendy](profile-sql-api-query.md) dla kwerendy. Te metryki są również dostępne za pośrednictwem witryny Azure portal:

[![Uzyskiwanie danych](./media/troubleshoot-query-performance/obtain-query-metrics.png) kwerendy](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Po pobraniu metryk kwerendy porównaj liczbę pobranych dokumentów z liczbą dokumentów wyjściowych dla kwerendy. Użyj tego porównania, aby zidentyfikować odpowiednie sekcje do przejrzenia w tym artykule.

Liczba pobranych dokumentów to liczba dokumentów, które kwerenda jest potrzebna do załadowania. Liczba dokumentów wyjściowych to liczba dokumentów, które były potrzebne do wyników kwerendy. Jeśli liczba pobranych dokumentów jest znacznie wyższa niż liczba dokumentów wyjściowych, istnieje co najmniej jedna część kwerendy, która nie może użyć indeksu i potrzebne do skanowania.

Zapoznaj się z poniższymi sekcjami, aby zrozumieć odpowiednie optymalizacje zapytań dla danego scenariusza.

### <a name="querys-ru-charge-is-too-high"></a>Opłata RU kwerendy jest zbyt wysoka

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Liczba pobranych dokumentów jest znacznie wyższa niż liczba dokumentów wyjściowych

- [Uwzględnij niezbędne ścieżki w zasadach indeksowania.](#include-necessary-paths-in-the-indexing-policy)

- [Dowiedz się, które funkcje systemowe używają indeksu.](#understand-which-system-functions-use-the-index)

- [Dowiedz się, które zapytania agregowane używają indeksu.](#understand-which-aggregate-queries-use-the-index)

- [Modyfikuj kwerendy, które mają zarówno filtr, jak i klauzulę ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optymalizuj wyrażenia JOIN za pomocą podkwerendy.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Liczba pobranych dokumentów jest w przybliżeniu równa liczbie dokumentów wyjściowych

- [Unikaj zapytań między partycjami.](#avoid-cross-partition-queries)

- [Optymalizuj kwerendy, które mają filtry na wielu właściwościach.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Modyfikuj kwerendy, które mają zarówno filtr, jak i klauzulę ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Opłata RU kwerendy jest dopuszczalna, ale opóźnienie jest nadal zbyt wysokie

- [Poprawa bliskości.](#improve-proximity)

- [Zwiększ aprowizowaną przepływność.](#increase-provisioned-throughput)

- [Zwiększenie MaxConcurrency.](#increase-maxconcurrency)

- [Zwiększ MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Kwerendy, w których liczba pobranych dokumentów przekracza liczbę dokumentów wyjściowych

 Liczba pobranych dokumentów to liczba dokumentów, które kwerenda jest potrzebna do załadowania. Liczba dokumentów wyjściowych to liczba dokumentów, które były potrzebne do wyników kwerendy. Jeśli liczba pobranych dokumentów jest znacznie wyższa niż liczba dokumentów wyjściowych, istnieje co najmniej jedna część kwerendy, która nie może użyć indeksu i potrzebne do skanowania.

Oto przykład kwerendy skanowania, która nie była w pełni obsługiwana przez indeks:

Zapytanie:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metryki kwerendy:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Liczba pobranych dokumentów (60 951) jest znacznie wyższa niż liczba dokumentów wyjściowych (7), więc ta kwerenda jest potrzebna do skanowania. W takim przypadku funkcja systemowa [UPPER()](sql-query-upper.md) nie używa indeksu.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Uwzględnij niezbędne ścieżki w zasadach indeksowania

Zasady indeksowania powinny obejmować wszystkie właściwości `WHERE` zawarte `ORDER BY` w klauzulach, klauzulach `JOIN`i większości funkcji systemowych. Ścieżka określona w zasadach indeksu powinna być zgodna z właściwością (z uwzględnieniem wielkości liter) właściwości w dokumentach JSON.

Jeśli uruchomisz proste zapytanie na zestaw danych [żywieniowych,](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) można zaobserwować `WHERE` znacznie niższe opłaty RU, gdy właściwość w klauzuli jest indeksowany:

#### <a name="original"></a>Oryginał

Zapytanie:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Zasady indeksowania:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Opłata RU:** 409,51 RU

#### <a name="optimized"></a>Optymalizacja

Zaktualizowano zasady indeksowania:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Opłata RU:** 2,98 RU

Właściwości można dodać do zasad indeksowania w dowolnym momencie, bez wpływu na dostępność zapisu lub wydajność. Jeśli dodasz nową właściwość do indeksu, kwerendy, które używają właściwości natychmiast użyje nowego dostępnego indeksu. Kwerenda użyje nowego indeksu podczas jego budowy. Więc wyniki kwerendy może być niespójne, gdy przebudowa indeksu jest w toku. Jeśli nowa właściwość jest indeksowana, kwerendy, które używają tylko istniejących indeksów nie będzie miało wpływu podczas przebudowy indeksu. Można [śledzić postęp transformacji indeksu](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Do zrozumienia, które funkcje systemowe używają indeksu

Jeśli wyrażenie można przetłumaczyć na zakres wartości ciągu, można użyć indeksu. W przeciwnym razie nie może.

Oto lista niektórych typowych funkcji ciągu, które można użyć indeksu:

- STARTSWITH(wyrażenie_ciągu, wyrażenie_ciągu)
- LEFT(wyrażenie_ciągu, wyrażenie_liczbowe) = wyrażenie_ciągu
- PODCIĄGNIE(str_expr, num_expr, num_expr) = str_expr, ale tylko wtedy, gdy pierwszy num_expr to 0

Poniżej przedstawiono niektóre typowe funkcje systemowe, które nie używają indeksu i muszą ładować każdy dokument:

| **Funkcja systemowa**                     | **Pomysły na optymalizację**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Użyj usługi Azure Search do wyszukiwania pełnotekstowego.                        |
| GÓRNA/DOLNA                             | Zamiast używać funkcji systemu do normalizacji danych do porównań, znormalizuj obudowę po wstawieniu. Zapytanie ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` takie ```SELECT * FROM c WHERE c.name = 'BOB'```jak staje się . |
| Funkcje matematyczne (inne niż agregaty) | Jeśli chcesz obliczyć wartość często w kwerendzie, należy rozważyć przechowywanie wartości jako właściwości w dokumencie JSON. |

------

Inne części kwerendy może nadal używać indeksu, nawet jeśli funkcje systemowe nie.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Do zrozumienia, które kwerendy agregowane używają indeksu

W większości przypadków zagregowane funkcje systemowe w usłudze Azure Cosmos DB będą używać indeksu. Jednak w zależności od filtrów lub dodatkowych klauzul w kwerendzie agregującej aparat zapytań może być wymagany do załadowania dużej liczby dokumentów. Zazwyczaj aparat zapytań najpierw zastosuje filtry równości i zakresu. Po zastosowaniu tych filtrów aparat zapytań może ocenić dodatkowe filtry i odwołać się do ładowania pozostałych dokumentów, aby obliczyć agregację, jeśli to konieczne.

Na przykład biorąc pod uwagę te dwa przykładowe `CONTAINS` kwerendy, kwerenda z filtrem funkcji równości `CONTAINS` i systemu będzie na ogół bardziej wydajne niż kwerenda z tylko filtr funkcji systemu. Jest tak, ponieważ filtr równości jest stosowany jako pierwszy i używa indeksu `CONTAINS` przed dokumenty muszą być ładowane dla filtru droższe.

Zapytanie `CONTAINS` z tylko filtrem - wyższa opłata RU:

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

Zapytanie z filtrem równości i `CONTAINS` filtrem - niższe opłaty RU:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Oto dodatkowe przykłady zapytań agregacji, które nie będą w pełni używać indeksu:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Kwerendy z funkcjami systemowymi, które nie używają indeksu

Należy odwołać się do [odpowiedniej funkcji systemu strony,](sql-query-system-functions.md) aby sprawdzić, czy używa indeksu.

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Agreguj zapytania z funkcjami zdefiniowanymi przez użytkownika (UDF)

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Zapytania z GROUP BY

Opłata RU `GROUP BY` wzrośnie wraz ze wzrostem kardynalności właściwości w klauzuli. `GROUP BY` W tym przykładzie aparat zapytań musi `c.foodGroup = "Sausages and Luncheon Meats"` załadować każdy dokument, który pasuje do filtru, więc oczekuje się, że opłata RU będzie wysoka.

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

Jeśli planujesz często uruchamiać te same zapytania agregacji, może być bardziej wydajne do tworzenia w czasie rzeczywistym zmaterializowanego widoku z [kanału informacyjnego usługi Azure Cosmos DB zmiany](change-feed.md) niż uruchamianie poszczególnych zapytań.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Modyfikowanie kwerend, które mają zarówno filtr, jak i klauzulę ORDER BY

Mimo że kwerendy, które `ORDER BY` mają filtr i klauzuli zwykle użyje indeksu zakresu, będą one bardziej wydajne, jeśli mogą być obsługiwane z indeksu złożonego. Oprócz modyfikowania zasad indeksowania należy dodać wszystkie właściwości w indeksie `ORDER BY` złożonym do klauzuli. Ta zmiana w kwerendzie zapewni, że używa indeksu złożonego.  Możesz zaobserwować wpływ, uruchamiając zapytanie na zestaw danych [żywieniowych:](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

#### <a name="original"></a>Oryginał

Zapytanie:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Zasady indeksowania:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Opłata RU:** 44,28 RU

#### <a name="optimized"></a>Optymalizacja

Zaktualizowana kwerenda (zawiera `ORDER BY` obie właściwości w klauzuli):

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Zaktualizowano zasady indeksowania:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Opłata RU:** 8,86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optymalzowanie wyrażeń JOIN przy użyciu podkwerendy
Wielowartościowe podkemiecje mogą optymalizować `JOIN` wyrażenia, wypychając predykaty po każdym `WHERE` wyrażeniu select-many, a nie po wszystkich sprzężeniach krzyżowych w klauzuli.

Należy wziąć pod uwagę tę kwerendę:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Opłata RU:** 167,62 RU

W przypadku tej kwerendy indeks będzie zgodny z dowolnym dokumentem, który ma znacznik o nazwie "formuła dla niemowląt", nutritionValue większa niż 0 i ilość porcji większa niż 1. Wyrażenie `JOIN` w tym miejscu wykona cross-product wszystkich elementów tagów, składników odżywczych i tablic porcji dla każdego dokumentu dopasowania przed zastosowaniem dowolnego filtru. Klauzula `WHERE` będzie następnie zastosować predykatu filtru dla każdej `<c, t, n, s>` krotki.

Na przykład jeśli pasujący dokument ma 10 elementów w każdej z trzech tablic, zostanie on rozszerzony do 1 x 10 x 10 x 10 (czyli 1000) krotek. Użycie podkemietów w tym miejscu może pomóc odfiltrować połączone elementy tablicy przed dołączeniem do następnego wyrażenia.

Ta kwerenda jest odpowiednikiem poprzedniej, ale używa podksyłek:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Opłata RU:** 22,17 RU

Załóżmy, że tylko jeden element w tablicy tagów pasuje do filtru i że istnieje pięć elementów dla modułów składników odżywczych i tablic obsługujących. Wyrażenia `JOIN` zostaną rozszerzone do 1 x 1 x 5 x 5 = 25 elementów, w przeciwieństwie do 1000 elementów w pierwszym zapytaniu.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Kwerendy, w których liczba pobranych dokumentów jest równa liczbie dokumentów wyjściowych

Jeśli liczba pobranych dokumentów jest w przybliżeniu równa licznik dokumentów wyjściowych, kwerenda nie trzeba skanować wiele niepotrzebnych dokumentów. W przypadku wielu zapytań, takich jak te, które używają słowa kluczowego TOP, liczba pobranych dokumentów może przekroczyć liczbę dokumentów wyjściowych o 1. Nie musisz się tym przejmować.

### <a name="avoid-cross-partition-queries"></a>Unikaj zapytań dotyczących partycji krzyżowych

Usługa Azure Cosmos DB używa [partycjonowania](partitioning-overview.md) do skalowania poszczególnych kontenerów w miarę zwiększania potrzeb jednostki żądań i magazynu danych. Każda partycja fizyczna ma oddzielny i niezależny indeks. Jeśli kwerenda ma filtr równości, który pasuje do klucza partycji kontenera, należy sprawdzić tylko indeks odpowiedniej partycji. Ta optymalizacja zmniejsza całkowitą liczbę procesorów RU, które wymaga kwerendy.

Jeśli masz dużą liczbę aprowizowanych ru (więcej niż 30 000) lub dużą ilość danych przechowywanych (więcej niż około 100 GB), prawdopodobnie masz wystarczająco duży kontener, aby zobaczyć znaczne zmniejszenie opłat RU kwerendy.

Na przykład w przypadku utworzenia kontenera z kluczem partycji foodGroup, następujące kwerendy będą musiały sprawdzić tylko jedną partycję fizyczną:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Te zapytania będą również zoptymalizowane przez dodanie klucza partycji w kwerendzie:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Kwerendy, które mają filtry zakresu na kluczu partycji lub które nie mają żadnych filtrów na kluczu partycji, będą musiały sprawdzić indeks każdej partycji fizycznej pod kątem wyników:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optymalizuj kwerendy, które mają filtry na wielu właściwościach

Mimo że kwerendy, które mają filtry na wiele właściwości zwykle użyje indeksu zakresu, będą one bardziej wydajne, jeśli mogą być obsługiwane z indeksu złożonego. W przypadku niewielkich ilości danych ta optymalizacja nie będzie miała znaczącego wpływu. Może to być przydatne, jednak dla dużych ilości danych. Można zoptymalizować tylko jeden filtr nie równości na indeks złożony. Jeśli kwerenda ma wiele filtrów nie równości, wybierz jeden z nich, który będzie używać indeksu złożonego. Reszta będzie nadal używać indeksów zakresu. Filtr nie równości musi być zdefiniowany jako ostatni w indeksie złożonym. [Dowiedz się więcej o indeksach złożonych](index-policy.md#composite-indexes).

Oto kilka przykładów zapytań, które można zoptymalizować za pomocą indeksu złożonego:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Oto odpowiedni indeks złożony:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optymalizacje zmniejszające opóźnienie kwerendy

W wielu przypadkach opłata RU może być dopuszczalna, gdy opóźnienie zapytania jest nadal zbyt wysokie. W poniższych sekcjach przedstawiono omówienie wskazówek dotyczących zmniejszania opóźnienia kwerendy. Jeśli uruchomisz tę samą kwerendę wiele razy w tym samym zestawie danych, będzie ono miało za każdym razem taką samą opłatę RU. Ale opóźnienie kwerendy może się różnić między wykonaniami kwerend.

### <a name="improve-proximity"></a>Poprawa bliskości

Kwerendy, które są uruchamiane z innego regionu niż konto usługi Azure Cosmos DB będą miały większe opóźnienia niż gdyby zostały uruchomione w tym samym regionie. Na przykład jeśli używasz kodu na komputerze stacjonarnym, należy oczekiwać opóźnienia mają być dziesiątki lub setki milisekund wyższe (lub więcej) niż jeśli kwerenda pochodzi z maszyny wirtualnej w tym samym regionie platformy Azure jako usługi Azure Cosmos DB. Globalna [dystrybucja danych w usłudze Azure Cosmos DB](distribute-data-globally.md) jest prosta, aby zapewnić przybliżenie danych do aplikacji.

### <a name="increase-provisioned-throughput"></a>Zwiększanie aprowizowanej przepływności

W usłudze Azure Cosmos DB aprowizowana przepływność jest mierzona w jednostkach żądań (NP). Wyobraź sobie, że masz kwerendę, która zużywa 5 procesorów RU przepływności. Na przykład jeśli aprowizujesz 1000 procesorów RU, można uruchomić tę kwerendę 200 razy na sekundę. Jeśli próbowano uruchomić kwerendę, gdy nie było wystarczającej przepływności dostępne, usługa Azure Cosmos DB zwróci błąd HTTP 429. Każdy z bieżących core (SQL) API SDK automatycznie ponowi próbę tej kwerendy po odczekaniu przez krótki czas. Ograniczone żądania trwać dłużej, więc zwiększenie aprowizowanej przepływności może zwiększyć opóźnienie kwerendy. Można zaobserwować [całkowitą liczbę żądań ograniczonego](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) w **bloku Metryki witryny** Azure portal.

### <a name="increase-maxconcurrency"></a>Zwiększ maksymalną cyrku

Zapytania równoległe działają, badając równolegle wiele partycji. Ale dane z pojedynczej kolekcji podzielonej na partycje są pobierane szeregowo w odniesieniu do kwerendy. Tak więc, jeśli ustawisz MaxConcurrency na liczbę partycji, masz największe szanse na osiągnięcie najbardziej wydajne zapytanie, pod warunkiem, że wszystkie inne warunki systemowe pozostają takie same. Jeśli nie znasz liczby partycji, można ustawić MaxConcurrency (lub MaxDegreesOfParallelism w starszych wersjach zestawu SDK) na dużą liczbę. System wybierze minimalną (liczbę partycji, dane wejściowe dostarczone przez użytkownika) jako maksymalny stopień równoległości.

### <a name="increase-maxbuffereditemcount"></a>Zwiększ wartość MaxBufferedItemCount

Zapytania są przeznaczone do wstępnego pobierania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. Pobieranie wstępne pomaga poprawić ogólne opóźnienie kwerendy. Ustawienie MaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Jeśli ustawisz tę wartość na oczekiwaną liczbę zwracanych wyników (lub większą liczbę), kwerenda może uzyskać największe korzyści z pobierania wstępnego. Jeśli ustawisz tę wartość na -1, system automatycznie określi liczbę elementów do buforowania.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, aby uzyskać informacje na temat pomiaru identyfikatorów procesorów na kwerendę, uzyskać statystyki wykonywania, aby dostroić zapytania i więcej:

* [Pobierz metryki wykonywania zapytań SQL przy użyciu narzędzia SDK .NET](profile-sql-api-query.md)
* [Tuning query performance with Azure Cosmos DB (Dostosowywanie wydajności zapytań w usłudze Azure Cosmos DB)](sql-api-sql-query-metrics.md)
* [Porady dotyczące wydajności dla zestawu.NET SDK](performance-tips.md)
