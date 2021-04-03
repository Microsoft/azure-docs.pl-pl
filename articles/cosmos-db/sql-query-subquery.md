---
title: Podzapytania SQL dla Azure Cosmos DB
description: Dowiedz się więcej o podzapytaniach SQL i ich typowych przypadkach użycia oraz różnych typach podzapytań w Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: f5f209229d17a2587258d21ee90e7560e629d082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340859"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Przykłady podzapytań SQL dla Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Podzapytanie jest zagnieżdżona w innym zapytaniu. Podzapytanie jest również nazywane zapytaniem wewnętrznym lub zaznaczeniem wewnętrznym. Instrukcja zawierająca podzapytanie jest zazwyczaj nazywana zewnętrznym zapytaniem.

W tym artykule opisano podzapytania SQL i ich typowe przypadki użycia w programie Azure Cosmos DB. Wszystkie przykładowe zapytania w tym dokumencie można uruchomić względem zestawu danych odżywiania, który jest wstępnie załadowany na [Azure Cosmos DB środowisko testowe zapytań](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Typy podzapytań

Istnieją dwa główne typy podzapytań:

* **Skorelowane**: podzapytanie, które odwołuje się do wartości z zapytania zewnętrznego. Podzapytanie jest oceniane raz dla każdego wiersza, który przetwarza zapytanie zewnętrzne.
* **Nieskorelowane**: podzapytanie, które jest niezależne od zapytania zewnętrznego. Może być uruchamiany samodzielnie bez polegania na zewnętrznym zapytaniu.

> [!NOTE]
> Azure Cosmos DB obsługuje tylko skorelowane podzapytania.

Podzapytania można zaklasyfikować w oparciu o liczbę wierszy i kolumn, które zwracają. Istnieją trzy typy:
* **Tabela**: zwraca wiele wierszy i wiele kolumn.
* Wiele **wartości**: zwraca wiele wierszy i pojedynczą kolumnę.
* **Skalarny**: zwraca pojedynczy wiersz i pojedynczą kolumnę.

Zapytania SQL w Azure Cosmos DB zawsze zwracają pojedynczą kolumnę (prostą lub złożoną). W związku z tym w Azure Cosmos DB są stosowane tylko wielowartościowe i skalarne podzapytania. Można użyć wielowartościowego podzapytania tylko w klauzuli FROM jako wyrażenia relacyjnego. Można użyć podzapytania skalarnego jako wyrażenia skalarnego w klauzuli SELECT lub WHERE lub jako wyrażenie relacyjne w klauzuli FROM.

## <a name="multi-value-subqueries"></a>Podzapytania wielowartościowe

Podzapytania o wiele wartości zwracają zestaw dokumentów i są zawsze używane w klauzuli FROM. Są one używane do:

* Optymalizowanie wyrażeń SPRZĘŻENIa. 
* Ocenianie kosztownych wyrażeń jednokrotnie i odwoływanie się do wielu razy.

## <a name="optimize-join-expressions"></a>Optymalizuj wyrażenia SPRZĘŻENIa

Podzapytania wielowartościowe mogą optymalizować wyrażenia SPRZĘŻENIa przez wypychanie predykatów po każdym wyrażeniu wyboru-wielu, a nie po wszystkich sprzężeniach krzyżowych w klauzuli WHERE.

Rozpatrzmy następujące zapytanie:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Dla tego zapytania indeks będzie pasować do dowolnego dokumentu, który ma tag o nazwie "Formuła niemowląt". Jest to element składnika pokarmowego z wartością z przedziału od 0 do 10 i obsługujący element o wartości większej niż 1. Wyrażenie JOIN w tym miejscu będzie przekroczyć iloczyn wszystkich elementów tagów, składników odżywczych i obsługuje tablice dla każdego pasującego dokumentu przed zastosowaniem filtra. 

Klauzula WHERE następnie zastosuje predykat filtru dla każdej <, t, n, s> krotki. Na przykład, jeśli pasujący dokument ma 10 elementów w każdej z trzech tablic, zostanie rozszerzony na 1 x 10 x 10 x 10 (czyli 1 000) krotek. Użycie podkwerend tutaj może pomóc w filtrowaniu sprzężonych elementów tablicy przed dołączeniem do następnego wyrażenia.

To zapytanie jest równoważne poprzedniemu, ale używa podzapytań:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Załóżmy, że tylko jeden element w tablicy tagów pasuje do filtru i istnieje pięć elementów dla obydwu składników pokarmowych i obsługujących tablice. Wyrażenia SPRZĘŻENIa zostaną następnie rozszerzone na 1 x 1 x 5 x 5 = 25 elementów, w przeciwieństwie do 1 000 elementów w pierwszej kwerendzie.

## <a name="evaluate-once-and-reference-many-times"></a>Oceń raz i odwołuje się wiele razy

Podzapytania mogą pomóc zoptymalizować zapytania z kosztownymi wyrażeniami, takimi jak funkcje zdefiniowane przez użytkownika (UDF), ciągi złożone lub wyrażenia arytmetyczne. Możesz użyć podzapytania wraz z wyrażeniem SPRZĘŻENIa, aby obliczyć wyrażenie jeden raz, ale odwołują się do niego wiele razy.

Następujące zapytanie uruchamia `GetMaxNutritionValue` dwa razy klawisz UDF:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Oto odpowiednik zapytania, które uruchamia tylko jeden raz:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Należy zwrócić uwagę na zachowanie różnych produktów w wyrażeniach SPRZĘŻENIa. Jeśli wyrażenie UDF może oszacować do wartości undefined, należy upewnić się, że wyrażenie JOIN zawsze generuje pojedynczy wiersz, zwracając obiekt z podzapytania, a nie wartość bezpośrednio.
>

Oto podobny przykład, który zwraca obiekt, a nie wartość:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Podejście nie jest ograniczone do UDF. Ma to zastosowanie do dowolnego potencjalnego wyrażenia. Na przykład można zastosować takie samo podejście z funkcją matematyczną `avg` :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Naśladowanie sprzężenia z zewnętrznymi danymi referencyjnymi

Czasami może być konieczne odwołujące się do danych statycznych, które rzadko zmieniają się, na przykład jednostek miary lub kodów krajów. Lepiej nie duplikować takich danych dla każdego dokumentu. Uniknięcie tego duplikacji spowoduje zapisanie w magazynie i zwiększenie wydajności zapisu przez pozostawienie mniejszego rozmiaru dokumentu. Za pomocą podzapytania można naśladować semantykę sprzężenia wewnętrznego z kolekcją danych referencyjnych.

Na przykład rozważmy ten zestaw danych referencyjnych:

| **Jednostka** | **Nazwa**            | **Mnożnik** | **Jednostka podstawowa** |
| -------- | ------------------- | -------------- | ------------- |
| gazu       | Nanogram            | 1.00 e-09       | Sprawdzanie          |
| g       | Microgram           | 1.00 e-06       | Sprawdzanie          |
| mg       | Ilość           | 1,00 e-03       | Sprawdzanie          |
| g        | Sprawdzanie                | 1,00 e + 00       | Sprawdzanie          |
| kilogram       | Kg            | 1,00 e + 03       | Sprawdzanie          |
| Mg       | Megagram            | 1,00 e + 06       | Sprawdzanie          |
| GG       | Gigagram            | 1,00 e + 09       | Sprawdzanie          |
| nJ       | Nanojoule           | 1.00 e-09       | Joule —         |
| µJ       | Microjoule          | 1.00 e-06       | Joule —         |
| mJ       | Millijoule          | 1,00 e-03       | Joule —         |
| J        | Joule —               | 1,00 e + 00       | Joule —         |
| Programu kJ       | Kilojoule           | 1,00 e + 03       | Joule —         |
| MJ       | Megajoule           | 1,00 e + 06       | Joule —         |
| GJ       | Gigajoule           | 1,00 e + 09       | Joule —         |
| cal      | Calorie             | 1,00 e + 00       | calorie       |
| kcal     | Calorie             | 1,00 e + 03       | calorie       |
| IU       | Jednostki międzynarodowe |                |               |


Następujące zapytanie naśladuje sprzężenie z tymi danymi, aby dodać nazwę jednostki do danych wyjściowych:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Podzapytania skalarne

Skalarne wyrażenie podzapytania jest podzapytaniem, którego wynikiem jest pojedyncza wartość. Wartość wyrażenia skalarnego podzapytania jest wartością projekcji (klauzula SELECT) podzapytania.  Wyrażenia skalarnego podzapytania można użyć w wielu miejscach, w których wyrażenie skalarne jest prawidłowe. Można na przykład użyć podzapytania skalarnego w dowolnym wyrażeniu w klauzuli SELECT i WHERE.

Użycie podzapytania skalarnego nie zawsze pomaga zoptymalizować, chociaż. Na przykład przekazywanie podzapytania skalarnego jako argumentu do systemu lub funkcji zdefiniowanych przez użytkownika nie zapewnia żadnej korzyści dotyczącej użycia jednostki zasobów (RU) ani opóźnienia.

Podzapytania skalarne mogą być klasyfikowane jako:
* Podzapytania skalarne w wyrażeniach prostych
* Agreguj podzapytania skalarne

## <a name="simple-expression-scalar-subqueries"></a>Podzapytania skalarne w wyrażeniach prostych

Uproszczone podzapytanie skalarne wyrażeń jest skorelowane podzapytanie, które ma klauzulę SELECT, która nie zawiera żadnych wyrażeń agregujących. Te podzapytania nie zapewniają korzyści optymalizacji, ponieważ kompilator konwertuje je na jedno większe wyrażenie proste. Nie istnieje skorelowany kontekst między zapytaniami wewnętrznymi i zewnętrznymi.

Oto kilka przykładów:

**Przykład 1**

```sql
SELECT 1 AS a, 2 AS b
```

Możesz ponownie napisać to zapytanie, używając prostego podzapytania skalarnego Expression, aby:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Oba zapytania tworzą następujące dane wyjściowe:

```json
[
  { "a": 1, "b": 2 }
]
```

**Przykład 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Możesz ponownie napisać to zapytanie, używając prostego podzapytania skalarnego Expression, aby:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Wyniki zapytania:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Przykład 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Możesz ponownie napisać to zapytanie, używając prostego podzapytania skalarnego Expression, aby:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Wyniki zapytania:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Agreguj podzapytania skalarne

Zagregowane podzapytanie skalarne jest podzapytaniem, które zawiera funkcję agregującą w projekcji lub filtr, który daje w wyniku pojedynczą wartość.

**Przykład 1:**

Oto podzapytanie zawierające wyrażenie pojedynczego funkcji agregującej w projekcji:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Wyniki zapytania:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Przykład 2**

Oto podzapytanie z wieloma wyrażeniami funkcji agregowania:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Wyniki zapytania:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Przykład 3**

Oto zapytanie z podzapytaniem zagregowanym zarówno dla projekcji, jak i filtru:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Wyniki zapytania:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Bardziej optymalnym sposobem pisania tego zapytania jest dołączenie do podzapytania i odwołanie się do aliasu podzapytania w klauzulach SELECT i WHERE. To zapytanie jest bardziej wydajne, ponieważ należy wykonać podzapytanie tylko w instrukcji join, a nie w projekcji i filtrze.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Wyrażenie EXISTS

Azure Cosmos DB obsługuje wyrażenia EXISTS. Jest to zagregowane podzapytanie skalarne wbudowane w interfejs API SQL Azure Cosmos DB. EXISTS jest wyrażeniem logicznym, które przyjmuje wyrażenie subquery i zwraca wartość true, jeśli podzapytanie zwraca wszystkie wiersze. W przeciwnym razie zwraca wartość false.

Ponieważ Azure Cosmos DB interfejs API SQL nie rozróżnia wyrażeń logicznych i innych wyrażeń skalarnych, można użyć operatora EXISTS w obu klauzulach SELECT i WHERE. Jest to w przeciwieństwie do języka T-SQL, gdzie wyrażenie logiczne (na przykład istnieje, między i w) jest ograniczone do filtru.

Jeśli podzapytanie EXISTS zwróci pojedynczą wartość, która nie jest zdefiniowana, istnieje wartość false. Na przykład rozważmy następujące zapytanie, którego wynikiem jest wartość false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Jeśli słowo kluczowe VALUE w powyższym podzapytaniu zostanie pominięte, kwerenda zwróci wartość true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Podzapytanie będzie zawierać listę wartości z wybranej listy w obiekcie. Jeśli wybrana lista nie ma żadnych wartości, podzapytanie zwróci pojedynczą wartość " {} ". Ta wartość jest zdefiniowana, a zatem istnieje wartość true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Przykład: zapisywanie ARRAY_CONTAINS i dołączanie jako istnieje

Typowym przypadkiem użycia ARRAY_CONTAINS jest filtrowanie dokumentu przez istnienie elementu w tablicy. W tym przypadku Sprawdzamy, czy tablica tagów zawiera element o nazwie "pomarańczowy".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Możesz ponownie napisać to samo zapytanie do użycia:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Ponadto ARRAY_CONTAINS można sprawdzić tylko wtedy, gdy wartość jest równa każdemu elementowi w tablicy. Jeśli potrzebujesz bardziej złożonych filtrów dla właściwości tablicy, użyj SPRZĘŻENIa.

Rozważmy następujące zapytanie, które jest oparte na jednostkach i `nutritionValue` właściwościach tablicy: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Dla każdego dokumentu w kolekcji, wiele produktów jest wykonywanych ze swoimi elementami tablicy. Ta operacja łączenia umożliwia filtrowanie właściwości w tablicy. Użycie tego zapytania jest jednak istotne. Na przykład jeśli 1 000 dokumenty mają 100 elementy w każdej tablicy, zostanie rozwinięte do 1 000 x 100 (czyli 100 000) krotek.

Użycie już istnieje może pomóc w uniknięciu tego kosztownego skrzyżowania:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

W takim przypadku należy odfiltrować elementy tablicy w podzapytaniu EXISTS. Jeśli element tablicy jest zgodny z filtrem, należy go projektować i istnieje wartość true.

Istnieje również możliwość aliasowania i odwoływania się do niego w projekcji:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Wyniki zapytania:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Wyrażenie tablicy

Możesz użyć wyrażenia ARRAY, aby zaprojektować wyniki zapytania jako tablicę. Tego wyrażenia można użyć tylko w klauzuli SELECT zapytania.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Wyniki zapytania:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Podobnie jak w przypadku innych podkwerend, filtry z wyrażeniem ARRAY są możliwe.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Wyniki zapytania:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Wyrażenia tablicowe mogą być również dostępne po klauzuli FROM w podzapytaniach.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Wyniki zapytania:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
