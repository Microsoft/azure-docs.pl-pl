---
title: Azure Cosmos DB języka zapytań
description: Dowiedz się więcej na temat funkcji systemowej języka SQL (EXP) w Azure Cosmos DB, aby zwrócić wartość wykładniczą określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adf80d36edbe8f9a5535e8fa04501918a62ea7ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335640"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość wykładniczą określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="remarks"></a>Uwagi
  
  Stała **e** (2,718281...) jest podstawą logarytmu naturalnego.  
  
  Wykładnik liczby jest stałą **e** podniesioną do potęgi liczby. Na przykład EXP (1.0) = e ^ 1.0 = 2.71828182845905 i EXP (10) = e ^ 10 = 22026.4657948067.  
  
  Wartość wykładnicza logarytmu naturalnego liczby jest samą liczbą: EXP (LOG (n)) = n. A logarytm naturalny liczby wykładniczej jest liczbą: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość wykładniczą określonej zmiennej (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Poniższy przykład zwraca wartość wykładniczą logarytmu naturalnego 20 i logarytm naturalny z wartości wykładniczej wynoszącej 20. Ponieważ te funkcje są funkcją odwrotną dla siebie, wartość zwracana z zaokrągleniem dla matematycznych liczb zmiennoprzecinkowych w obu przypadkach wynosi 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
