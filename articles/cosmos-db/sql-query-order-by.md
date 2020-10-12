---
title: Klauzula ORDER BY w Azure Cosmos DB
description: Dowiedz się więcej o klauzuli ORDER BY języka SQL dla Azure Cosmos DB. Użyj programu SQL jako Azure Cosmos DB języka zapytań JSON.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: c4ae66884602989284a427bdc33de7612bd9a8df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84484339"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Klauzula ORDER BY w Azure Cosmos DB

Klauzula opcjonalna `ORDER BY` określa kolejność sortowania dla wyników zwracanych przez zapytanie.

## <a name="syntax"></a>Składnia
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Określa właściwość lub wyrażenie, dla którego ma zostać posortowany zestaw wyników zapytania. Kolumnę sortowania można określić jako nazwę lub alias właściwości.  
  
   Można określić wiele właściwości. Nazwy właściwości muszą być unikatowe. Sekwencja właściwości sortowania w `ORDER BY` klauzuli definiuje organizację zestawu wyników posortowanych. Oznacza to, że zestaw wyników jest sortowany według pierwszej właściwości, a następnie że uporządkowana lista jest posortowana według drugiej właściwości i tak dalej.  
  
   Nazwy właściwości, do których odwołuje się `ORDER BY` klauzula, muszą odpowiadać właściwości na liście wyboru lub do właściwości zdefiniowanej w kolekcji określonej w `FROM` klauzuli bez żadnych niejasności.  
  
- `<sort_expression>`  
  
   Określa jedną lub więcej właściwości lub wyrażeń, dla których ma zostać posortowany zestaw wyników zapytania.  
  
- `<scalar_expression>`  
  
   Aby uzyskać szczegółowe informacje, zobacz sekcję [wyrażenia skalarne](sql-query-scalar-expressions.md) .  
  
- `ASC | DESC`  
  
   Określa, że wartości w określonej kolumnie powinny być sortowane w kolejności rosnącej lub malejącej. `ASC` sortuje od najniższej wartości do najwyższej wartości. `DESC` sortuje od najwyższego do najniższej wartości. `ASC` jest domyślną kolejnością sortowania. Wartości null są traktowane jako najniższe możliwe wartości.  
  
## <a name="remarks"></a>Uwagi  
  
   `ORDER BY`Klauzula wymaga, aby zasady indeksowania zawierały indeks dla sortowanych pól. Środowisko uruchomieniowe zapytań Azure Cosmos DB obsługuje sortowanie względem nazwy właściwości, a nie do właściwości obliczanych. Azure Cosmos DB obsługuje wiele `ORDER BY` właściwości. Aby uruchomić zapytanie z wieloma właściwościami ORDER BY, należy zdefiniować [indeks złożony](index-policy.md#composite-indexes) dla sortowanych pól.

> [!Note]
> Jeśli sortowane właściwości mogą być niezdefiniowane w niektórych dokumentach i chcesz je pobrać w kwerendzie ORDER BY, musisz jawnie dołączyć tę ścieżkę w indeksie. Domyślne zasady indeksowania nie będą zezwalać na pobieranie dokumentów, w których właściwość Sort jest niezdefiniowana. [Przejrzyj przykładowe zapytania dotyczące dokumentów zawierających brakujące pola](#documents-with-missing-fields).

## <a name="examples"></a>Przykłady

Załóżmy na przykład, że jest to zapytanie, które pobiera rodziny w kolejności rosnącej nazwy miejscowości rezydentnej:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Wyniki są następujące:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Poniższe zapytanie pobiera rodziny `id` s w kolejności ich tworzenia elementów. Element `creationDate` jest liczbą reprezentującą *czas epoki*lub czas, który upłynął od sty. 1, 1970 w sekundach.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Wyniki są następujące:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Ponadto możesz zamówić według wielu właściwości. Zapytanie, które porządkuje według wielu właściwości, wymaga [indeksu złożonego](index-policy.md#composite-indexes). Rozpatrzmy następujące zapytanie:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

To zapytanie pobiera rodzinę `id` w kolejności rosnącej według nazwy miasta. Jeśli wiele elementów ma taką samą nazwę miasta, zapytanie zostanie uporządkowane według `creationDate` kolejności malejącej.

## <a name="documents-with-missing-fields"></a>Dokumenty z brakującymi polami

Zapytania z tymi `ORDER BY` , które są uruchamiane względem kontenerów z domyślną zasadą indeksowania, nie będą zwracać dokumentów, w których właściwość Sort jest niezdefiniowana. Jeśli chcesz uwzględnić dokumenty, w których właściwość Sort jest niezdefiniowana, należy jawnie dołączyć tę właściwość do zasad indeksowania.

Na przykład poniżej znajduje się kontener z zasadami indeksowania, które nie zawierają jawnie żadnych ścieżek `"/*"` :

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

W przypadku uruchomienia zapytania zawierającego `lastName` w `Order By` klauzuli wyniki będą zawierać tylko dokumenty, które mają `lastName` zdefiniowaną właściwość. Nie zdefiniowano jawnie dołączonej ścieżki dla `lastName` , dlatego wszystkie dokumenty bez elementu `lastName` nie będą widoczne w wynikach zapytania.

Oto zapytanie, które sortuje według `lastName` dwóch dokumentów, z których jeden nie ma `lastName` zdefiniowanego:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Wyniki obejmują tylko dokument, który ma zdefiniowane `lastName` :

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

W przypadku aktualizowania zasad indeksowania kontenera w celu jawnego uwzględnienia ścieżki do programu `lastName` zostaną uwzględnione dokumenty z niezdefiniowaną właściwością sortowania w wynikach zapytania. Należy jawnie zdefiniować ścieżkę, która ma prowadzić do tej wartości skalarnej (a nie poza nią). Należy użyć `?` znaku w definicji ścieżki w zasadach indeksowania, aby upewnić się, że jawnie indeksuje Właściwość `lastName` i nie ma dodatkowych ścieżek zagnieżdżonych poza nią. Jeśli `Order By` zapytanie używa [indeksu złożonego](index-policy.md#composite-indexes), w wynikach zapytania będą zawsze uwzględniane dokumenty z niezdefiniowaną właściwością sortowania.

Poniżej przedstawiono przykładowe zasady indeksowania, które umożliwiają `lastName` Wyświetlanie w wynikach zapytania dokumentów z niezdefiniowanymi komunikatami:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Jeśli ponownie uruchomisz to samo zapytanie, brakujące dokumenty `lastName` są wyświetlane jako pierwsze w wynikach zapytania:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Wyniki są następujące:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

W przypadku zmodyfikowania porządku sortowania do `DESC` , brakujące dokumenty `lastName` są wyświetlane jako ostatnie w wynikach zapytania:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Wyniki są następujące:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Tylko zestaw SDK .NET w wersji 3.4.0 lub nowszej obsługuje funkcję ORDER BY with Mixed Types. W związku z tym, jeśli chcesz sortować według kombinacji wartości zdefiniowanej i zdefiniowanej, Użyj tej wersji (lub nowszej).

Nie można kontrolować kolejności, w której różne typy pojawiają się w wynikach. W powyższym przykładzie pokazano, jak niezdefiniowane wartości zostały posortowane przed wartościami ciągu. Jeśli na przykład chcesz mieć większą kontrolę nad kolejnością sortowania niezdefiniowanych wartości, możesz przypisać wszystkie niezdefiniowane właściwości wartością ciągu "Aaaaaaaaa" lub "zzzzzzzz", aby upewnić się, że były one pierwsze lub ostatnie.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Zasady indeksowania w usłudze Azure Cosmos DB](index-policy.md)
- [Klauzula limitu przesunięcia](sql-query-offset-limit.md)
