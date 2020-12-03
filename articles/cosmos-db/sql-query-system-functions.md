---
title: Funkcje systemowe w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat wbudowanych i zdefiniowanych przez użytkownika funkcji systemu SQL w programie Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 71dc5b84deda04c02b6d1722b11abf6631e9aa03
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546373"
---
# <a name="system-functions-azure-cosmos-db"></a>Funkcje systemowe (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB udostępnia wiele wbudowanych funkcji SQL. Poniżej wymieniono kategorie funkcji wbudowanych.  
  
|Grupa funkcji|Opis|Operacje|  
|--------------|-----------------|-----------------| 
|[Funkcje tablicy](sql-query-array-functions.md)|Funkcja Array wykonuje operację na wartości wejściowej tablicy i zwraca wartość liczbową, logiczną lub tablicową. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funkcje daty i godziny](sql-query-date-time-functions.md)|Funkcje daty i godziny umożliwiają uzyskanie bieżącej daty i godziny UTC w dwóch formach; liczbowa sygnatura czasowa, której wartość to epoka systemu UNIX w milisekundach lub ciąg, który jest zgodny z formatem ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Funkcje matematyczne](sql-query-mathematical-functions.md)|Funkcja matematyczna wykonuje obliczenia, zazwyczaj na podstawie wartości wejściowych, które są podane jako argumenty, i zwraca wartość liczbową. | [ABS](sql-query-abs.md), [Acos](sql-query-acos.md), [ASIN](sql-query-asin.md), [atan](sql-query-atan.md), [ATN2](sql-query-atn2.md), [sufit](sql-query-ceiling.md), [cos](sql-query-cos.md), [COT](sql-query-cot.md), [stopnie](sql-query-degrees.md), [EXP](sql-query-exp.md), [piętro](sql-query-floor.md), [log](sql-query-log.md), [log10 —](sql-query-log10.md), [pi](sql-query-pi.md), [moc](sql-query-power.md), [radiany](sql-query-radians.md), [Los](sql-query-rand.md), [Zaokrąglij](sql-query-round.md), [znak](sql-query-sign.md), [Sin](sql-query-sin.md), [sqrt](sql-query-sqrt.md), [kwadrat](sql-query-square.md), [Tan](sql-query-tan.md), [TRUNC —](sql-query-trunc.md) |
|[Funkcje przestrzenne](sql-query-spatial-functions.md)|Funkcje przestrzenne wykonują operacje na wartości wejściowej obiektu przestrzennego i zwracają wartość liczbową lub logiczną. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Funkcje ciągów](sql-query-string-functions.md)|Funkcja String wykonuje operację na wartości wejściowej ciągu i zwraca ciąg, wartość liczbową lub logiczną. | [Concat](sql-query-concat.md), [Contains](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [Length](sql-query-length.md), [Lower](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[Zastąp](sql-query-replace.md), [replika](sql-query-replicate.md), [Wstecz](sql-query-reverse.md), [Right](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [podciąg](sql-query-substring.md), [ToString](sql-query-tostring.md), [Trim](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Funkcje kontroli typów](sql-query-type-checking-functions.md)|Funkcje kontroli typów pozwalają sprawdzić typ wyrażenia w zapytaniach języka SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Wbudowane a funkcje zdefiniowane przez użytkownika (UDF)

Jeśli obecnie używasz funkcji zdefiniowanej przez użytkownika (UDF), dla której funkcja wbudowana jest teraz dostępna, odpowiednia Wbudowana funkcja będzie szybsza i wydajniejsza.

## <a name="built-in-versus-ansi-sql-functions"></a>Wbudowana funkcja SQL w porównaniu z ANSI

Główna różnica między funkcjami Cosmos DB i funkcjami SQL ANSI polega na tym, że funkcje Cosmos DB są zaprojektowane tak, aby działały ze schematami i danymi mieszanymi w schemacie. Na przykład jeśli brakuje właściwości lub ma wartość nieliczbową, taką jak `unknown` , element jest pomijany, a nie zwraca błędu.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje tablicy](sql-query-array-functions.md)
- [Funkcje daty i godziny](sql-query-date-time-functions.md)
- [Funkcje matematyczne](sql-query-mathematical-functions.md)
- [Funkcje przestrzenne](sql-query-spatial-functions.md)
- [Funkcje ciągów](sql-query-string-functions.md)
- [Funkcje kontroli typów](sql-query-type-checking-functions.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregate-functions.md)
