---
title: Tworzenie aliasów w Azure Cosmos DB
description: Dowiedz się, jak używać aliasów w Azure Cosmos DB zapytań SQL, aby odróżnić dwie właściwości o tej samej nazwie
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873475"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Tworzenie aliasów w Azure Cosmos DB

Można jawnie aliasować wartości w zapytaniach. Jeśli zapytanie ma dwie właściwości o tej samej nazwie, użyj aliasu, aby zmienić nazwę jednej lub obu właściwości, tak aby były one niejednoznaczne w przewidywanym wyniku.

## <a name="examples"></a>Przykłady

Słowo kluczowe AS używane do aliasowania jest opcjonalne, jak pokazano w poniższym przykładzie podczas projekcji drugiej wartości jako `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT — klauzula](sql-query-select.md)
- [Klauzula FROM](sql-query-from.md)
