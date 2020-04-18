---
title: Klauzula ORDER BY w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli SQL ORDER BY dla usługi Azure Cosmos DB. Użyj języka SQL jako języka zapytań JSON usługi Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641393"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Klauzula ORDER BY w usłudze Azure Cosmos DB

Klauzula `ORDER BY` opcjonalna określa kolejność sortowania wyników zwracanych przez kwerendę.

## <a name="syntax"></a>Składnia
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Określa właściwość lub wyrażenie, na których ma być sortowane zestaw wyników kwerendy. Kolumnę sortowania można określić jako nazwę lub alias właściwości.  
  
   Można określić wiele właściwości. Nazwy właściwości muszą być unikatowe. Sekwencja właściwości sortowania w `ORDER BY` klauzuli definiuje organizację posortowanego zestawu wyników. Oznacza to, że zestaw wyników jest sortowany według pierwszej właściwości, a następnie, że uporządkowana lista jest sortowana przez drugą właściwość i tak dalej.  
  
   Nazwy właściwości, o `ORDER BY` których mowa w klauzuli musi odpowiadać właściwości na liście select lub `FROM` właściwości zdefiniowanej w kolekcji określonej w klauzuli bez żadnych niejasności.  
  
- `<sort_expression>`  
  
   Określa jedną lub więcej właściwości lub wyrażeń, na których ma być sortowane zestaw wyników kwerendy.  
  
- `<scalar_expression>`  
  
   Zobacz [skalarne wyrażenia](sql-query-scalar-expressions.md) sekcji, aby uzyskać szczegółowe informacje.  
  
- `ASC | DESC`  
  
   Określa, że wartości w określonej kolumnie powinny być sortowane w kolejności rosnącej lub malejącej. `ASC`od najniższej do najwyższej wartości. `DESC`od najwyższej wartości do najniższej wartości. `ASC`jest domyślną kolejnością sortowania. Wartości null są traktowane jako najniższe możliwe wartości.  
  
## <a name="remarks"></a>Uwagi  
  
   Klauzula `ORDER BY` wymaga, aby zasady indeksowania zawierały indeks dla sortowanych pól. Środowisko uruchomieniowe kwerendy usługi Azure Cosmos DB obsługuje sortowanie względem nazwy właściwości, a nie względem właściwości obliczeniowych. Usługa Azure Cosmos `ORDER BY` DB obsługuje wiele właściwości. Aby uruchomić kwerendę z wieloma właściwościami ORDER BY, należy zdefiniować [indeks złożony](index-policy.md#composite-indexes) dla posortowanych pól.

> [!Note]
> Jeśli właściwości sortowane mogą być niezdefiniowane dla niektórych dokumentów i chcesz je pobrać w zapytaniu ORDER BY, należy jawnie uwzględnić tę ścieżkę w indeksie. Domyślna zasada indeksowania nie zezwala na pobieranie dokumentów, w których właściwość sortowania jest niezdefiniowana. [Przejrzyj przykładowe zapytania dotyczące dokumentów z brakującymi polami](#documents-with-missing-fields).

## <a name="examples"></a>Przykłady

Na przykład oto zapytanie, które pobiera rodziny w porządku rosnącym nazwy miasta rezydenta:

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

Następująca kwerenda `id`pobiera rodziny s w kolejności ich daty utworzenia towaru. Pozycja `creationDate` jest liczbą reprezentującą *czas epoki*lub czas, jaki upłynął od 1 stycznia 1970 r. w sekundach.

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

Ponadto można zamówić według wielu właściwości. Kwerenda, która zamawia według wielu właściwości, wymaga [indeksu złożonego](index-policy.md#composite-indexes). Rozpatrzmy następujące zapytanie:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Ta kwerenda pobiera `id` rodzinę w porządku rosnącym nazwy miasta. Jeśli wiele elementów ma taką samą nazwę `creationDate` miasta, kwerenda będzie porządkować w kolejności malejącej.

## <a name="documents-with-missing-fields"></a>Dokumenty z brakującymi polami

Kwerendy `ORDER BY` z, które są uruchamiane względem kontenerów z domyślną zasadą indeksowania nie zwróci dokumentów, w których właściwość sortowania jest niezdefiniowana. Jeśli chcesz dołączyć dokumenty, w których właściwość sortowania jest niezdefiniowana, należy jawnie dołączyć tę właściwość do zasad indeksowania.

Na przykład oto kontener z zasadami indeksowania, który jawnie nie `"/*"`zawiera żadnych ścieżek oprócz:

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

Po uruchomieniu kwerendy, `lastName` która `Order By` zawiera w klauzuli, wyniki `lastName` będą zawierać tylko dokumenty, które mają zdefiniowaną właściwość. Nie zdefiniowaliśmy jawnej ścieżki `lastName` dołączonej do `lastName` wiadomości, więc wszystkie dokumenty bez a nie pojawią się w wynikach kwerendy.

Oto zapytanie, które `lastName` sortuje na dwa dokumenty, `lastName` z których jeden nie ma zdefiniowane:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Wyniki obejmują tylko dokument, który `lastName`ma zdefiniowany:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Jeśli zaktualizujemy zasady indeksowania kontenera, `lastName`aby jawnie uwzględnić ścieżkę dla , będziemy dołączać dokumenty z właściwością niezdefiniowanego sortowania w wynikach kwerendy. Należy jawnie zdefiniować ścieżkę, aby prowadzić do tej wartości skalarnej (a nie poza nią). Należy użyć `?` znaku w definicji ścieżki w zasadach indeksowania, aby `lastName` upewnić się, że jawnie indeksować właściwość i żadnych dodatkowych ścieżek zagnieżdżonych poza nim. Jeśli `Order By` kwerenda używa [indeksu złożonego,](index-policy.md#composite-indexes)wyniki będą zawsze zawierać dokumenty z właściwością niezdefiniowanego sortowania w wynikach kwerendy.

Oto przykładowa zasada indeksowania, która umożliwia umieszczanie w `lastName` wynikach kwerendy dokumentów z niezdefiniowanym:

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

Jeśli ponownie uruchomisz tę samą `lastName` kwerendę, dokumenty, których brakuje, pojawią się najpierw w wynikach kwerendy:

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

Jeśli zmodyfikujesz `DESC`kolejność sortowania `lastName` na , dokumenty, których brakuje, pojawią się ostatnio w wynikach kwerendy:

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

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Zasady indeksowania w usłudze Azure Cosmos DB](index-policy.md)
- [Klauzula LIMITU PRZESUNIĘCIA](sql-query-offset-limit.md)
