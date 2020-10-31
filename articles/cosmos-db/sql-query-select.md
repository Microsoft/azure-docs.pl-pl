---
title: Klauzula SELECT w Azure Cosmos DB
description: Dowiedz się więcej na temat klauzuli SELECT języka SQL dla Azure Cosmos DB. Użyj programu SQL jako Azure Cosmos DB języka zapytań JSON.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 4d518461f8526a742a59d92140e2cf323e8bfc16
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082559"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Klauzula SELECT w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Każde zapytanie składa się z `SELECT` klauzuli i opcjonalnych klauzul [from](sql-query-from.md) i [WHERE](sql-query-where.md) , zgodnie ze standardami ANSI języka SQL. Typowo, źródło w `FROM` klauzuli jest wyliczane i `WHERE` klauzula stosuje filtr na źródle, aby pobrać PODZESTAW elementów JSON. `SELECT`Następnie klauzula umożliwia projekty żądanych wartości JSON na liście wyboru.

## <a name="syntax"></a>Składnia

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<select_specification>`  

  Właściwości lub wartości, które mają zostać wybrane dla zestawu wyników.  
  
- `'*'`  

  Określa, że wartość powinna być pobierana bez wprowadzania żadnych zmian. W przypadku, gdy przetworzona wartość jest obiektem, zostaną pobrane wszystkie właściwości.  
  
- `<object_property_list>`  
  
  Określa listę właściwości, które mają zostać pobrane. Każda zwrócona wartość będzie obiektem ze wskazanymi właściwościami.  
  
- `VALUE`  

  Określa, że wartość JSON ma być pobierana zamiast pełnego obiektu JSON. W przeciwieństwie do `<property_list>` nie powoduje zawijania przewidywanej wartości w obiekcie.  

- `DISTINCT`
  
  Określa, że duplikaty właściwości rzutowane powinny zostać usunięte.  

- `<scalar_expression>`  

  Wyrażenie reprezentujące wartość, która ma zostać obliczona. Szczegóły można znaleźć w sekcji [wyrażenia skalarne](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Uwagi

`SELECT *`Składnia jest prawidłowa tylko wtedy, gdy klauzula FROM deklaruje dokładnie jeden alias. `SELECT *` dostarcza projekcję tożsamości, która może być przydatna, jeśli nie jest wymagana projekcja. SELECT * jest prawidłowy tylko wtedy, gdy klauzula FROM jest określona i wprowadza tylko pojedyncze źródło danych wejściowych.  
  
Zarówno `SELECT <select_list>` , `SELECT *` jak i są "cukrem" i mogą być również wyrażone przy użyciu prostych instrukcji SELECT, jak pokazano poniżej.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   jest równoważne:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   jest równoważne:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Przykłady

Poniższy przykład zapytania SELECT zwraca, `address` z `Families` którego `id` pasuje `AndersenFamily` :

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula WHERE](sql-query-where.md)
