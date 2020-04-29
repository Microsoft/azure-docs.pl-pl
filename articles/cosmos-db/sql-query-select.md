---
title: Klauzula SELECT w Azure Cosmos DB
description: Dowiedz się więcej na temat klauzuli SELECT języka SQL dla Azure Cosmos DB. Użyj programu SQL jako Azure Cosmos DB języka zapytań JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77469939"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Klauzula SELECT w Azure Cosmos DB

Każde zapytanie składa się z klauzuli SELECT i opcjonalnych klauzul from i [WHERE](sql-query-where.md) , zgodnie [ze](sql-query-from.md) standardami ANSI języka SQL. Typowo, źródło w klauzuli FROM jest wyliczane i klauzula WHERE stosuje filtr dla źródła, aby pobrać podzestaw elementów JSON. Klauzula SELECT następnie projektuje żądane wartości JSON na liście wyboru.

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

  Określa, że wartość JSON ma być pobierana zamiast pełnego obiektu JSON. W przeciwieństwie `<property_list>` do nie powoduje zawijania przewidywanej wartości w obiekcie.  
 
- `DISTINCT`
  
  Określa, że duplikaty właściwości rzutowane powinny zostać usunięte.  

- `<scalar_expression>`  

  Wyrażenie reprezentujące wartość, która ma zostać obliczona. Szczegóły można znaleźć w sekcji [wyrażenia skalarne](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Uwagi

Składnia `SELECT *` jest prawidłowa tylko wtedy, gdy klauzula FROM deklaruje dokładnie jeden alias. `SELECT *`dostarcza projekcję tożsamości, która może być przydatna, jeśli nie jest wymagana projekcja. SELECT * jest prawidłowy tylko wtedy, gdy klauzula FROM jest określona i wprowadza tylko pojedyncze źródło danych wejściowych.  
  
Zarówno `SELECT <select_list>` , `SELECT *` jak i są "cukrem" i mogą być również wyrażone przy użyciu prostych instrukcji SELECT, jak pokazano poniżej.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   jest równoważne:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   jest równoważne:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Przykłady

Poniższy przykład `address` zapytania SELECT zwraca, z `Families` którego `id` pasuje `AndersenFamily`:

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

### <a name="quoted-property-accessor"></a>Metoda dostępu do właściwości cytowanej
Można uzyskać dostęp do właściwości przy użyciu operatora właściwości cytowane []. Na przykład elementy `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatna do ucieczki właściwości, która zawiera spacje, znaki specjalne lub ma taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Właściwości zagnieżdżone

W poniższym przykładzie przedstawiono projekty dwóch zagnieżdżonych `f.address.state` właściwości `f.address.city`i.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Wyrażenia JSON

Projekcja obsługuje również wyrażenia JSON, jak pokazano w następującym przykładzie:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

W poprzednim przykładzie klauzula SELECT musi utworzyć obiekt JSON, a ponieważ przykład nie zawiera klucza, klauzula używa nazwy `$1`zmiennej argumentu niejawnego. Następujące zapytanie zwraca dwie niejawne zmienne argumentów `$1` : `$2`i.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```
## <a name="reserved-keywords-and-special-characters"></a>Zastrzeżone słowa kluczowe i znaki specjalne

Jeśli dane zawierają właściwości o takich samych nazwach jak zastrzeżone słowa kluczowe, takie jak "Order" lub "Group", kwerendy dotyczące tych dokumentów będą powodowały błędy składniowe. Należy jawnie uwzględnić właściwość w `[]` znaku, aby pomyślnie uruchomić zapytanie.

Załóżmy na przykład, że dokument ma właściwość o nazwie `order` i Właściwość `price($)` , która zawiera znaki specjalne:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

W przypadku uruchomienia zapytania zawierającego `order` właściwość lub `price($)` właściwość zostanie wyświetlony komunikat o błędzie składni.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
Wynik:

`
Syntax error, incorrect syntax near 'order'
`

Należy ponownie napisać te same zapytania jak poniżej:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula WHERE](sql-query-where.md)
