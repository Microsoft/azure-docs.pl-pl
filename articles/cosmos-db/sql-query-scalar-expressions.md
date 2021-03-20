---
title: Wyrażenia skalarne w Azure Cosmos DB zapytania SQL
description: Dowiedz się więcej na temat składni wyrażeń skalarnych SQL dla Azure Cosmos DB. W tym artykule opisano również sposób łączenia wyrażeń skalarnych z wyrażeniami złożonymi przy użyciu operatorów.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 34df2d2f4a2209cca93c3f7ac12dcd203bf4a089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339635"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Wyrażenia skalarne w Azure Cosmos DB zapytania SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Klauzula SELECT](sql-query-select.md) obsługuje wyrażenia skalarne. Wyrażenie skalarne jest kombinacją symboli i operatorów, które mogą być oceniane w celu uzyskania pojedynczej wartości. Przykłady wyrażeń skalarnych obejmują: stałe, odwołania do właściwości, odwołania do elementów tablicy, odwołania aliasów lub wywołania funkcji. Wyrażenia skalarne mogą być łączone w wyrażenia złożone przy użyciu operatorów.

## <a name="syntax"></a>Składnia
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumenty
  
- `<constant>`  
  
   Reprezentuje wartość stałą. Aby uzyskać szczegółowe informacje, zobacz sekcję dotyczącą [stałych](sql-query-constants.md) .  
  
- `input_alias`  
  
   Reprezentuje wartość zdefiniowaną przez `input_alias` wprowadzoną w `FROM` klauzuli.  
  Ta wartość jest gwarantowana jako **niezdefiniowana** **— wartości** w danych wejściowych są pomijane.  
  
- `<scalar_expression>.property_name`  
  
   Reprezentuje wartość właściwości obiektu. Jeśli właściwość nie istnieje lub właściwość jest przywoływana dla wartości, która nie jest obiektem, wyrażenie daje w wyniku **niezdefiniowaną** wartość.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Reprezentuje wartość właściwości o nazwie `property_name` lub element tablicy z indeksem `array_index` tablicy. Jeśli indeks właściwości/tablicy nie istnieje lub odwołuje się do niego wartość, która nie jest obiektem/tablicą, wyrażenie daje w wyniku niezdefiniowaną wartość.  
  
- `unary_operator <scalar_expression>`  
  
   Reprezentuje operatora, który jest stosowany do pojedynczej wartości. Szczegóły można znaleźć w sekcji [operatorów](sql-query-operators.md) .  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Reprezentuje operatora, który jest stosowany do dwóch wartości. Szczegóły można znaleźć w sekcji [operatorów](sql-query-operators.md) .  
  
- `<scalar_function_expression>`  
  
   Reprezentuje wartość zdefiniowaną przez wynik wywołania funkcji.  
  
- `udf_scalar_function`  
  
   Nazwa funkcji skalarnej zdefiniowanej przez użytkownika.  
  
- `builtin_scalar_function`  
  
   Nazwa wbudowanej funkcji skalarnej.  
  
- `<create_object_expression>`  
  
   Reprezentuje wartość uzyskaną przez utworzenie nowego obiektu z określonymi właściwościami i ich wartościami.  
  
- `<create_array_expression>`  
  
   Reprezentuje wartość uzyskaną przez utworzenie nowej tablicy z określonymi wartościami jako elementy  
  
- `parameter_name`  
  
   Reprezentuje wartość określonej nazwy parametru. Nazwy parametrów muszą mieć jeden \@ znak jako pierwszy.  
  
## <a name="remarks"></a>Uwagi
  
  Podczas wywoływania wbudowanej funkcji skalarnej lub zdefiniowanej przez użytkownika, wszystkie argumenty muszą być zdefiniowane. Jeśli którykolwiek z argumentów jest niezdefiniowany, funkcja nie zostanie wywołana, a wynik zostanie niezdefiniowany.  
  
  Podczas tworzenia obiektu Każda właściwość, która ma przypisaną niezdefiniowaną wartość, zostanie pominięta i nie zostanie uwzględniona w utworzonym obiekcie.  
  
  Podczas tworzenia tablicy każda wartość elementu, która ma przypisane **niezdefiniowane** wartości, zostanie pominięta i nie będzie uwzględniona w utworzonym obiekcie. Spowoduje to, że następny zdefiniowany element będzie miał miejsce w taki sposób, aby Utworzona tablica nie miała pominiętych indeksów.  

## <a name="examples"></a>Przykłady

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Wyniki są następujące:

```json
    [{
      "$1": 1.33333
    }]
```

W poniższym zapytaniu wynik wyrażenia skalarnego jest wartością logiczną:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Wyniki są następujące:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Zapytania podrzędne](sql-query-subquery.md)