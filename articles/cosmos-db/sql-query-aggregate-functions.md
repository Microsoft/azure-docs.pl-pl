---
title: Funkcje agregujące w Azure Cosmos DB
description: Dowiedz się więcej na temat składni funkcji agregującej SQL, typów funkcji agregujących obsługiwanych przez Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555417"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funkcje agregujące w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Funkcje agregujące wykonują obliczenia na zestawie wartości w `SELECT` klauzuli i zwracają pojedynczą wartość. Na przykład następujące zapytanie zwraca liczbę elementów w kontenerze:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Typy funkcji agregujących

Interfejs API SQL obsługuje następujące funkcje agregujące. `SUM` i `AVG` operować na wartościach liczbowych, i, `COUNT` `MIN` i `MAX` pracować na liczbach, ciągach, wartościach logicznych i wartościach null.

| Funkcja | Opis |
|-------|-------------|
| [ŚR](sql-query-aggregate-avg.md) | Zwraca średnią wartości w wyrażeniu. |
| [COUNT](sql-query-aggregate-count.md) | Zwraca liczbę elementów w wyrażeniu. |
| [MAX](sql-query-aggregate-max.md) | Zwraca maksymalną wartość w wyrażeniu. |
| [MIN](sql-query-aggregate-min.md) | Zwraca minimalną wartość w wyrażeniu. |
| [SUM](sql-query-aggregate-sum.md) | Zwraca sumę wszystkich wartości w wyrażeniu. |


Możesz również zwrócić tylko wartość skalarną agregacji za pomocą słowa kluczowego VALUE. Na przykład poniższe zapytanie zwraca liczbę wartości jako pojedynczą liczbę:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Wyniki są następujące:

```json
    [ 2 ]
```

Można również łączyć agregacje z filtrami. Na przykład następujące zapytanie zwraca liczbę elementów ze stanem adresu `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Wyniki są następujące:

```json
    [ 1 ]
```

## <a name="remarks"></a>Uwagi

Te zagregowane funkcje systemowe będą korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy). Jeśli oczekujesz,,, `AVG` , `COUNT` `MAX` `MIN` lub `SUM` na właściwość, należy [dołączyć odpowiednią ścieżkę do zasad indeksowania](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)