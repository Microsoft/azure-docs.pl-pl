---
title: StringToNumber w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToNumber systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f2622958a2315458ccd01da4aea7c6b75941e755
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339567"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie tłumaczone na liczbę. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie liczbowe JSON. Liczby w formacie JSON muszą być liczbami całkowitymi lub liczbą zmiennoprzecinkową. Aby uzyskać szczegółowe informacje o formacie JSON, zobacz [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToNumber` działa w różnych typach. 

Odstęp jest dozwolony tylko przed lub po liczbie.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

W formacie JSON prawidłowa liczba musi być liczbą całkowitą lub liczbą zmiennoprzecinkową.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
{{}}
```  

Przesłane wyrażenie zostanie przeanalizowane jako wyrażenie liczbowe; te dane wejściowe nie są obliczane do typu Number i w ten sposób zwracają niezdefiniowane. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
{{}}
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
