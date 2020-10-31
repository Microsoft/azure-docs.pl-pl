---
title: Funkcje matematyczne w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcjach matematycznych w Azure Cosmos DB, aby wykonać obliczenia na podstawie wartości wejściowych, które są podane jako argumenty, i zwrócić wartość liczbową.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6b2c47d5a4cb71ecf46d1eb1c33e2f667fa8052
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078258"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funkcje matematyczne (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Każda z funkcji matematycznych wykonuje obliczenie na podstawie wartości wejściowych, które są podawane jako argumenty, i zwraca wartość liczbową.

Można uruchamiać zapytania takie jak Poniższy przykład:

```sql
    SELECT VALUE ABS(-4)
```

Wynik:

```json
    [4]
```

## <a name="functions"></a>Funkcje

Następujące obsługiwane wbudowane funkcje matematyczne wykonują obliczenia, zazwyczaj na podstawie argumentów wejściowych i zwracają wyrażenie liczbowe:
 
* [ABS](sql-query-abs.md)
* [ACOS](sql-query-acos.md)
* [ASIN](sql-query-asin.md)
* [ATAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [CEILING](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [DEGREES](sql-query-degrees.md)
* [EXP](sql-query-exp.md)
* [FLOOR](sql-query-floor.md)
* [LOG](sql-query-log.md)
* [LOG10](sql-query-log10.md)
* [PI](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RADIANS](sql-query-radians.md)
* [RAND](sql-query-rand.md)
* [ROUND](sql-query-round.md)
* [SIGN](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [SQRT](sql-query-sqrt.md)
* [SQUARE](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
Wszystkie funkcje matematyczne, z wyjątkiem RAND, to funkcje deterministyczne. Oznacza to, że zwracają te same wyniki za każdym razem, gdy są wywoływane przy użyciu określonego zestawu wartości wejściowych.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
