---
title: Klauzula limitu przesunięcia w Azure Cosmos DB
description: Dowiedz się, jak używać klauzuli LIMIT przesunięcia do pomijania i wykonywania niektórych wartości podczas wykonywania zapytań w Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 9342c0c10ed4f7287716d21823fddfe992e0568f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432719"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Klauzula limitu przesunięcia w Azure Cosmos DB

Klauzula LIMIT przesunięcia jest opcjonalną klauzulą, aby pominąć, a następnie pobrać pewną liczbę wartości z zapytania. W klauzuli LIMIT przesunięcia są wymagane liczby przesunięcia i liczba LIMITów.

Gdy LIMIT przesunięcia jest używany w połączeniu z klauzulą ORDER BY, zestaw wyników jest tworzony przez wykonanie pominięcia i wykonania uporządkowanych wartości. Jeśli nie jest używana klauzula ORDER BY, spowoduje to deterministyczną kolejność wartości.

## <a name="syntax"></a>Składnia
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenty

- `<offset_amount>`

   Określa liczbę całkowitą elementów, które mają zostać pominięte w wynikach zapytania.

- `<limit_amount>`
  
   Określa liczbę całkowitą elementów, które powinny zawierać wyniki zapytania

## <a name="remarks"></a>Uwagi
  
  `OFFSET`W klauzuli są wymagane zarówno licznik, jak i `LIMIT` liczba `OFFSET LIMIT` . Jeśli `ORDER BY` jest używana klauzula opcjonalna, zestaw wyników jest tworzony przez przeskoczenie nad uporządkowane wartości. W przeciwnym razie zapytanie zwróci ustaloną kolejność wartości.

  Opłata za obiekt RU zapytania i `OFFSET LIMIT` zostanie zwiększona w miarę zwiększania się liczby przesunięć. W przypadku zapytań mających [wiele stron wyników](sql-query-pagination.md)zwykle zalecamy użycie [tokenów kontynuacji](sql-query-pagination.md#continuation-tokens). Tokeny kontynuacji są "zakładkami" dla miejsca, w którym zapytanie może zostać później wznowione. Jeśli używasz `OFFSET LIMIT` , nie istnieje "zakładka". Jeśli chcesz zwrócić następną stronę zapytania, trzeba zacząć od początku.
  
  Należy używać `OFFSET LIMIT` w przypadku przypadków, gdy chcesz całkowicie pominąć elementy i zapisać zasoby klienta. Na przykład, należy użyć, `OFFSET LIMIT` Jeśli chcesz przejść do wyniku zapytania 1000th i nie ma potrzeby wyświetlania wyników od 1 do 999. W zapleczu program `OFFSET LIMIT` nadal ładuje każdy element, łącznie z tymi, które zostały pominięte. Zalety wydajności to oszczędności zasobów klientów, unikając przetwarzania niekoniecznych elementów.

## <a name="examples"></a>Przykłady

Na przykład jest to zapytanie, które pomija pierwszą wartość i zwraca drugą wartość (w kolejności nazwy miasta rezydenta):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Wyniki są następujące:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Oto zapytanie, które pomija pierwszą wartość i zwraca drugą wartość (bez porządkowania):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Wyniki są następujące:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [SELECT — klauzula](sql-query-select.md)
- [Klauzula ORDER BY](sql-query-order-by.md)
