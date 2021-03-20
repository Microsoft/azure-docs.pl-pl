---
title: Rozwiązywanie problemów z zapytaniami podczas korzystania z usługi Azure Cosmos DB
description: Dowiedz się, jak identyfikować, diagnozować i rozwiązywać problemy z Azure Cosmos DB zapytań SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6701a580cbe7790dcce2cbbcc46889f9dff00107
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559977"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Rozwiązywanie problemów z zapytaniami podczas korzystania z usługi Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule przedstawiono ogólne zalecane podejście do rozwiązywania problemów z zapytaniami w Azure Cosmos DB. Chociaż nie należy traktować kroków opisanych w tym artykule, pełna ochrona przed potencjalnymi problemami związanymi z zapytaniami zawiera najpopularniejsze porady dotyczące wydajności. Ten artykuł powinien być używany jako punkt wyjścia do rozwiązywania problemów z powolnymi lub kosztownymi zapytaniami w podstawowym interfejsie API usługi Azure Cosmos DB (SQL). Można również używać [dzienników diagnostycznych](cosmosdb-monitor-resource-logs.md) do identyfikowania zapytań, które są powolne lub zużywają znaczną ilość przepływności. Jeśli używasz interfejsu API Azure Cosmos DB dla MongoDB, należy użyć [interfejsu api Azure Cosmos DB dla przewodnika rozwiązywania problemów z kwerendą MongoDB](mongodb-troubleshoot-query.md)

Optymalizacje zapytań w Azure Cosmos DB są szeroko kategoryzowane w następujący sposób:

- Optymalizacje, które zmniejszają opłatę jednostki żądania (RU) zapytania
- Optymalizacje, które po prostu skracają opóźnienia

W przypadku obniżenia opłaty za usługę RU z kwerendy zazwyczaj zmniejsza się również opóźnienia.

W tym artykule przedstawiono przykłady, które można utworzyć ponownie przy użyciu [zestawu danych odżywiania](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Typowe problemy z zestawem SDK

Przed przeczytaniem tego przewodnika warto rozważyć typowe problemy z zestawem SDK, które nie są związane z aparatem zapytań.

- Postępuj zgodnie z tymi [wskazówkami dotyczącymi wydajności zestawu SDK](performance-tips.md).
    - [Przewodnik rozwiązywania problemów z zestawem SDK platformy .NET](troubleshoot-dot-net-sdk.md)
    - [Przewodnik rozwiązywania problemów z zestawem SDK języka Java](troubleshoot-java-sdk-v4-sql.md)
- Zestaw SDK umożliwia ustawienie `MaxItemCount` dla zapytań, ale nie można określić minimalnej liczby elementów.
    - Kod powinien obsługiwać dowolny rozmiar strony od zera do `MaxItemCount` .
- Czasami zapytania mogą mieć puste strony nawet wtedy, gdy wyniki są na przyszłość. Przyczyny tego mogą być następujące:
    - Zestaw SDK może wykonywać wiele wywołań sieciowych.
    - Pobieranie dokumentów może potrwać dłuższy czas.
- Wszystkie zapytania mają token kontynuacji, który umożliwi kontynuowanie zapytania. Pamiętaj, aby całkowicie opróżnić zapytanie. Dowiedz się więcej [na temat obsługi wielu stron wyników](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Pobierz metryki zapytania

Podczas optymalizowania zapytania w Azure Cosmos DB, pierwszym krokiem jest zawsze [pobieranie metryk zapytania](profile-sql-api-query.md) dla zapytania. Te metryki są również dostępne za pomocą Azure Portal. Po uruchomieniu zapytania w Eksplorator danych metryki zapytania są widoczne obok karty **wyniki** :

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Pobieranie metryk zapytania" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Po otrzymaniu metryk zapytania Porównaj **liczbę pobranych dokumentów** z **liczbą dokumentów wyjściowych** dla zapytania. To porównanie służy do identyfikowania odpowiednich sekcji do przejrzenia w tym artykule.

**Liczba pobranych dokumentów** to liczba dokumentów, które aparat zapytań potrzebował do załadowania. **Liczba dokumentów wyjściowych** to liczba dokumentów, które były zbędne dla wyników zapytania. Jeśli **liczba pobranych dokumentów** jest znacznie większa niż **Liczba dokumentów wyjściowych**, istniała co najmniej jedna część zapytania, która nie mogła użyć indeksu i jest wymagana do skanowania.

Zapoznaj się z następującymi sekcjami, aby zrozumieć odpowiednie optymalizacje zapytań dla danego scenariusza.

### <a name="querys-ru-charge-is-too-high"></a>Zbyt wysoka opłata za zapytanie RU

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Liczba pobranych dokumentów jest znacznie wyższa niż liczba dokumentów wyjściowych

- [Dołącz wymagane ścieżki do zasad indeksowania.](#include-necessary-paths-in-the-indexing-policy)

- [Informacje o tym, które funkcje systemowe używają indeksu.](#understand-which-system-functions-use-the-index)

- [Popraw ciąg wykonywania funkcji systemu.](#improve-string-system-function-execution)

- [Zrozumieć, które zapytania agregujące używają indeksu.](#understand-which-aggregate-queries-use-the-index)

- [Optymalizuj zapytania, które mają klauzulę Filter i ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optymalizuj wyrażenia SPRZĘŻENIa przy użyciu podzapytania.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Liczba pobranych dokumentów jest w przybliżeniu równa liczbie dokumentów wyjściowych

- [Minimalizuj zapytania między partycjami.](#minimize-cross-partition-queries)

- [Optymalizuj zapytania z filtrami dla wielu właściwości.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Optymalizuj zapytania, które mają klauzulę Filter i ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Opłata za usługę RU dla zapytania jest akceptowalna, ale opóźnienie jest wciąż zbyt wysokie

- [Zwiększ bliskość.](#improve-proximity)

- [Zwiększ przepływność aprowizacji.](#increase-provisioned-throughput)

- [Zwiększ MaxConcurrency.](#increase-maxconcurrency)

- [Zwiększ MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Zapytania, w przypadku których liczba pobranych dokumentów przekracza liczbę dokumentów wyjściowych

 **Liczba pobranych dokumentów** to liczba dokumentów, które aparat zapytań potrzebował do załadowania. **Liczba dokumentów wyjściowych** to liczba dokumentów zwróconych przez zapytanie. Jeśli **liczba pobranych dokumentów** jest znacznie większa niż **Liczba dokumentów wyjściowych**, istniała co najmniej jedna część zapytania, która nie mogła użyć indeksu i jest wymagana do skanowania.

Oto przykład zapytania skanowania, które nie było całkowicie obsługiwane przez indeks:

Zapytanie:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metryki zapytań:

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

**Liczba pobranych dokumentów** (60 951) jest znacznie większa niż **Liczba dokumentów wyjściowych** (7), co oznacza, że to zapytanie spowodowało przeprowadzenie skanowania dokumentów. W takim przypadku funkcja systemu [Upper ()](sql-query-upper.md) nie używa indeksu.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Dołącz wymagane ścieżki do zasad indeksowania

Zasady indeksowania powinny obejmować wszystkie właściwości zawarte w `WHERE` klauzulach, `ORDER BY` klauzulach `JOIN` i większości funkcji systemu. Żądane ścieżki określone w zasadach indeksu powinny być zgodne z właściwościami w dokumentach JSON.

> [!NOTE]
> W przypadku właściwości w Azure Cosmos DB zasad indeksowania jest uwzględniana wielkość liter

Jeśli uruchamiasz następujące proste zapytanie w zestawie danych [odżywiania](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) , zobaczysz znacznie mniejszą opłatą ru, gdy właściwość w `WHERE` klauzuli jest indeksowana:

#### <a name="original"></a>Oryginalne

Zapytanie:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
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

**Opłata za ru:** 409,51 jednostek ru

#### <a name="optimized"></a>Optymalizacja

Zaktualizowane zasady indeksowania:

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

**Opłata za ru:** 2,98 jednostek ru

W dowolnej chwili można dodać właściwości do zasad indeksowania bez wpływu na dostępność zapisu lub odczytu. [Postęp przekształcania indeksów można śledzić](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Informacje o tym, które funkcje systemowe używają indeksu

Większość funkcji systemowych używa indeksów. Poniżej znajduje się lista niektórych typowych funkcji ciągów, które używają indeksów:

- StartsWith
- Contains
- RegexMatch
- Lewe
- Podciąg — ale tylko wtedy, gdy pierwsze num_expr wynosi 0

Poniżej przedstawiono niektóre typowe funkcje systemowe, które nie używają indeksu i muszą ładować każdy dokument, gdy jest używany w `WHERE` klauzuli:

| **Funkcja systemowa**                     | **Pomysły dotyczące optymalizacji**             |
| --------------------------------------- |------------------------------------------------------------ |
| Górny/dolny                         | Zamiast używać funkcji system do normalizacji danych do porównania, należy znormalizować wielkość liter po wstawieniu. Zostanie zapytanie ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` ```SELECT * FROM c WHERE c.name = 'BOB'``` . |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Oblicz bieżącą godzinę przed wykonaniem zapytania i Użyj tej wartości ciągu w `WHERE` klauzuli. |
| Funkcje matematyczne (inne niż zagregowane) | Jeśli trzeba często obliczać wartość w zapytaniu, należy rozważyć przechowywanie wartości jako właściwości w dokumencie JSON. |

Te funkcje systemowe mogą używać indeksów, z wyjątkiem przypadków, gdy są używane w zapytaniach z agregacjami:

| **Funkcja systemowa**                     | **Pomysły dotyczące optymalizacji**             |
| --------------------------------------- |------------------------------------------------------------ |
| Funkcje systemu przestrzennego                        | Przechowywanie wyniku zapytania w widoku z materiałami w czasie rzeczywistym |

W przypadku użycia w `SELECT` klauzuli niewydajne funkcje systemowe nie wpłyną na sposób używania indeksów przez zapytania.

### <a name="improve-string-system-function-execution"></a>Ulepszanie wykonywania funkcji systemu ciągów

W przypadku niektórych funkcji systemowych, które używają indeksów, można poprawić wykonanie zapytania, dodając `ORDER BY` klauzulę do zapytania. 

Dokładniej mówiąc, każda funkcja systemowa, której koszt RU zwiększa się w miarę wzrostu kardynalności właściwości, może korzystać z `ORDER BY` zapytania. Te zapytania wykonują skanowanie indeksu, dlatego posortowane wyniki zapytania mogą sprawiać, że zapytanie jest bardziej wydajne.

Ta optymalizacja może poprawić wykonywanie następujących funkcji systemowych:

- StartsWith (bez uwzględniania wielkości liter = true)
- StringEquals (bez uwzględniania wielkości liter = true)
- Contains
- RegexMatch
- EndsWith

Rozważmy na przykład poniższe zapytanie z `CONTAINS` . `CONTAINS` użyje indeksów, ale czasami, nawet po dodaniu odpowiedniego indeksu, można nadal obserwować bardzo wysokie obciążenie RU podczas uruchamiania poniższego zapytania.

Oryginalne zapytanie:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

Aby poprawić wykonywanie zapytań, Dodaj `ORDER BY` następujące:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

Ta sama Optymalizacja może pomóc w zapytaniach z dodatkowymi filtrami. W tym przypadku najlepiej dodać właściwości z filtrami równości do `ORDER BY` klauzuli.

Oryginalne zapytanie:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

Można poprawić wykonywanie zapytania przez dodanie `ORDER BY` i [indeks złożony](index-policy.md#composite-indexes) dla (c.Name, c. miasto):

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Zrozumienie, które zapytania agregujące używają indeksu

W większości przypadków zagregowane funkcje systemowe w Azure Cosmos DB będą używały indeksu. Jednak w zależności od filtrów lub dodatkowych klauzul w kwerendzie agregującej aparat zapytań może być wymagany do załadowania dużej liczby dokumentów. Zwykle aparat zapytań zastosuje najpierw filtry równości i zakresu. Po zastosowaniu tych filtrów aparat zapytań może oszacować dodatkowe filtry i wykonać ładowanie pozostałych dokumentów w celu obliczenia agregacji, jeśli jest to konieczne.

Na przykład biorąc pod tym dwa przykładowe zapytania, zapytanie z `CONTAINS` filtrem równości i funkcji systemu zwykle będzie bardziej wydajne niż zapytanie zawierające tylko `CONTAINS` Filtr funkcji systemu. Dzieje się tak, ponieważ filtr równości jest stosowany jako pierwszy i używa indeksu przed załadowaniem dokumentów dla bardziej kosztownego `CONTAINS` filtru.

Kwerenda z tylko `CONTAINS` filtrem o wyższym poziomie filtrów:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Zapytanie z zarówno filtrem równości, jak i `CONTAINS` filtrem o niższych wartościach ru:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Poniżej znajdują się dodatkowe przykłady kwerend agregujących, które nie w pełni wykorzystują indeks:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Zapytania z funkcjami systemowymi, które nie używają indeksu

Należy odwołać się do [strony odpowiedniej funkcji systemu](sql-query-system-functions.md) , aby sprawdzić, czy używa indeksu.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Agregowanie zapytań przy użyciu funkcji zdefiniowanych przez użytkownika (UDF)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Zapytania z grupą według

Opłata za usługę RU dla zapytań w programie zwiększa się `GROUP BY` w miarę wzrostu kardynalności właściwości w `GROUP BY` klauzuli. W poniższym zapytaniu, na przykład, opłata za wartość RU zapytania zostanie zwiększona w miarę wzrostu liczby unikatowych opisów.

Opłata za obiekt RU funkcji agregującej z `GROUP BY` klauzulą będzie większa niż wartość "ru" funkcji agregującej. W tym przykładzie aparat zapytań musi załadować każdy dokument pasujący do filtru, `c.foodGroup = "Sausages and Luncheon Meats"` Aby opłata ru była wysoka.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Jeśli planujesz często uruchamiać te same zapytania agregujące, może być wydajniejsze Kompilowanie widoku z materiałami w czasie rzeczywistym za pomocą [kanału informacyjnego zmiany Azure Cosmos DB](change-feed.md) niż uruchamianie pojedynczych zapytań.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Optymalizowanie zapytań, które mają klauzulę Filter i ORDER BY

Mimo że zapytania z filtrem i `ORDER BY` klauzulą zwykle używają indeksu zakresu, będą bardziej wydajne, jeśli będą mogły być obsługiwane z indeksu złożonego. Oprócz modyfikowania zasad indeksowania należy dodać wszystkie właściwości w indeksie złożonym do `ORDER BY` klauzuli. Ta zmiana w zapytaniu zapewni użycie indeksu złożonego.  Można obserwować wpływ przez uruchomienie zapytania na zestawie danych [odżywiania](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) :

#### <a name="original"></a>Oryginalne

Zapytanie:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
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

**Opłata za ru:** 44,28 jednostek ru

#### <a name="optimized"></a>Optymalizacja

Zaktualizowane zapytanie (zawiera obie właściwości w `ORDER BY` klauzuli):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Zaktualizowane zasady indeksowania:

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

**Opłata za ru:** 8,86 jednostek ru

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optymalizowanie wyrażeń SPRZĘŻENIa przy użyciu podzapytania

Podzapytania wielowartościowe mogą optymalizować `JOIN` wyrażenia przez wypychanie predykatów po każdym wyrażeniu SELECT-wielu, a nie po wszystkich sprzężeniach krzyżowych w `WHERE` klauzuli.

Weź pod uwagę następujące zapytanie:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Opłata za ru:** 167,62 jednostek ru

W przypadku tego zapytania indeks będzie pasować do dowolnego dokumentu, który ma tag o nazwie `infant formula` , `nutritionValue` większy niż 0 i `amount` większy od 1. W `JOIN` tym miejscu wyrażenie będzie przekroczyć iloczyn wszystkich elementów tagów, składników odżywczych i obsługuje tablice dla każdego pasującego dokumentu przed zastosowaniem filtra. `WHERE`Klauzula zastosuje predykat filtru dla każdej `<c, t, n, s>` krotki.

Na przykład, jeśli pasujący dokument zawiera 10 elementów w każdej z trzech tablic, zostanie rozszerzony na 1 x 10 x 10 x 10 (czyli 1 000) krotek. Użycie podkwerend w tym miejscu może pomóc w odfiltrowaniu sprzężonych elementów tablicy przed dołączeniem do następnego wyrażenia.

To zapytanie jest równoważne poprzedniemu, ale używa podzapytań:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Opłata za ru:** 22,17 jednostek ru

Załóżmy, że tylko jeden element w tablicy tagów pasuje do filtru i że istnieje pięć elementów dla elementów odżywczych i obsługujących tablice. `JOIN`Wyrażenia zostaną rozszerzone na 1 x 1 x 5 x 5 = 25 elementów, w przeciwieństwie do 1 000 elementów w pierwszym zapytaniu.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Zapytania, w przypadku których liczba pobranych dokumentów jest równa liczbie dokumentów wyjściowych

Jeśli **liczba pobranych dokumentów** jest w przybliżeniu równa **liczbie dokumentów wyjściowych**, aparat zapytań nie musiał skanować wielu zbędnych dokumentów. W przypadku wielu zapytań, takich jak te, które używają `TOP` słowa kluczowego, **liczba pobranych dokumentów** może przekroczyć **liczbę dokumentów wyjściowych** o 1. Nie musisz się z nim wiązać.

### <a name="minimize-cross-partition-queries"></a>Minimalizuj zapytania między partycjami

Azure Cosmos DB używa [partycjonowania](partitioning-overview.md) do skalowania poszczególnych kontenerów jako jednostki żądania i magazynu danych. Każda partycja fizyczna ma oddzielny i niezależny indeks. Jeśli zapytanie ma filtr równości pasujący do klucza partycji kontenera, należy sprawdzić tylko odpowiedni indeks partycji. Ta optymalizacja zmniejsza łączną liczbę jednostek ru wymaganych przez zapytanie.

Jeśli masz dużą liczbę zainicjowanych jednostek ru (ponad 30 000) lub dużą ilość przechowywanych danych (więcej niż około 100 GB), prawdopodobnie masz wystarczająco duży rozmiar kontenera, aby zobaczyć znaczną redukcję opłat za zapytania RU.

Na przykład, jeśli utworzysz kontener z kluczem partycji żywność, następujące zapytania będą musiały sprawdzić tylko jedną partycję fizyczną:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Zapytania `IN` z filtrem z kluczem partycji będą sprawdzać tylko odpowiednie partycje fizyczne i nie będą "wentylatorem":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Zapytania, które mają filtry zakresu w kluczu partycji lub nie mają żadnych filtrów w kluczu partycji, muszą mieć wartość "wentylator-out" i sprawdzać każdy indeks partycji fizycznej pod kątem wyników:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optymalizowanie zapytań z filtrami dla wielu właściwości

Mimo że zapytania zawierające filtry dla wielu właściwości zwykle używają indeksu zakresu, będą bardziej wydajne, jeśli będą mogły być obsługiwane z indeksu złożonego. W przypadku małych ilości danych Ta optymalizacja nie ma znaczącego wpływu. Mogą jednak być przydatne w przypadku dużych ilości danych. Można zoptymalizować maksymalnie jeden filtr nierówności na indeks złożony. Jeśli zapytanie ma wiele filtrów bez równości, wybierz jedną z nich, która będzie używać indeksu złożonego. Pozostałe będą nadal używać indeksów zakresów. Filtr bez równości musi być zdefiniowany jako ostatni w indeksie złożonym. [Dowiedz się więcej na temat indeksów złożonych](index-policy.md#composite-indexes).

Poniżej przedstawiono kilka przykładów zapytań, które można zoptymalizować za pomocą indeksu złożonego:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
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

## <a name="optimizations-that-reduce-query-latency"></a>Optymalizacje zmniejszające czas oczekiwania na zapytanie

W wielu przypadkach opłata za usługę RU może być akceptowalna, gdy opóźnienie zapytania jest wciąż zbyt wysokie. Poniższe sekcje zawierają omówienie wskazówek dotyczących skracania opóźnień zapytań. W przypadku uruchomienia tego samego zapytania wiele razy w tym samym zestawie danych w każdym momencie będzie ona mieć ten sam koszt. Opóźnienia zapytań mogą się różnić między wykonaniami zapytań.

### <a name="improve-proximity"></a>Zwiększ bliskość

Zapytania uruchamiane z innego regionu niż konto Azure Cosmos DB będą miały wyższy czas oczekiwania niż w przypadku uruchomienia w tym samym regionie. Na przykład jeśli używasz kodu na komputerze stacjonarnym, należy oczekiwać opóźnienia na dziesiątki lub setki milisekund (lub więcej) niż w przypadku, gdy zapytanie pochodzi z maszyny wirtualnej w tym samym regionie platformy Azure co Azure Cosmos DB. Jest ona prosta do [dystrybuowania danych w Azure Cosmos DB](distribute-data-globally.md) , aby zapewnić, że dane będą bliżej Twojej aplikacji.

### <a name="increase-provisioned-throughput"></a>Zwiększ przepływność aprowizacji

W Azure Cosmos DB zainicjowana przepływność jest mierzona w jednostkach żądania (jednostek ru). Wyobraź sobie, że masz zapytanie, które zużywa 5 jednostek ru przepływności. Na przykład jeśli zainicjujesz 1 000 jednostek ru, będzie można uruchamiać zapytanie o godzinie 200 na sekundę. Jeśli podjęto próbę uruchomienia zapytania w przypadku braku wystarczającej przepływności, Azure Cosmos DB zwróci błąd HTTP 429. Wszystkie podstawowe zestawy SDK interfejsu API (SQL) są automatycznie ponowią próbę wykonania tego zapytania po krótkim czasie oczekiwania. Żądania ograniczające przepustowość są dłuższe, więc zwiększenie zainicjowanej przepływności może poprawić opóźnienia zapytań. Można obserwować [łączną liczbę żądań z ograniczeniami](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) w bloku **metryk** Azure Portal.

### <a name="increase-maxconcurrency"></a>Zwiększ MaxConcurrency

Zapytania równoległe działają przez wykonywanie zapytań na wielu partycjach równolegle. Ale dane z pojedynczej kolekcji partycjonowanej są pobierane sekwencyjnie w odniesieniu do zapytania. Tak więc, jeśli ustawisz MaxConcurrency na liczbę partycji, masz najlepszą szansę osiągnięcia najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz ustawić wysoką liczbę MaxConcurrency (lub MaxDegreesOfParallelism we wcześniejszych wersjach zestawu SDK). System wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości.

### <a name="increase-maxbuffereditemcount"></a>Zwiększ MaxBufferedItemCount

Zapytania są zaprojektowane w celu wstępnego pobrania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. Przed pobraniem pomaga poprawić ogólne opóźnienie zapytania. Ustawienie MaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Jeśli ustawisz tę wartość na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę), zapytanie może uzyskać największą korzyść z pobierania wstępnego. W przypadku ustawienia tej wartości na wartość-1 system automatycznie określi liczbę elementów do buforowania.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, aby uzyskać informacje na temat mierzenia jednostek ru na zapytanie, uzyskać statystykę wykonywania w celu dostrajania zapytań i nie tylko:

* [Pobieranie metryk wykonywania zapytań SQL przy użyciu zestawu .NET SDK](profile-sql-api-query.md)
* [Tuning query performance with Azure Cosmos DB (Dostosowywanie wydajności zapytań w usłudze Azure Cosmos DB)](./sql-api-query-metrics.md)
* [Porady dotyczące wydajności dla zestawu.NET SDK](performance-tips.md)
* [Wskazówki dotyczące wydajności dla zestawu SDK Java v4](performance-tips-java-sdk-v4-sql.md)