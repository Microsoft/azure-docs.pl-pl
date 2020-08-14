---
title: GetCurrentTicks w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji GetCurrentTicks systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6b3cd5ab5849c33172e4a629c79fb792b82f1255
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227370"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

Zwraca bieżącą datę i godzinę mierzoną w taktach.
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Typy zwracane

Zwraca dodatnią liczbę całkowitą.

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="examples"></a>Przykłady

Oto przykład, który zwraca bieżący czas mierzony w taktach:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
