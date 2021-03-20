---
title: Język zapytań dodatku Azure Cosmos DB
description: Dowiedz się więcej o MOŻLIWOŚCIach systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 39db52223feea63a4e3afbd4775f884acbbca548
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341692"
---
# <a name="power-azure-cosmos-db"></a>MOC (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość określonego wyrażenia do określonej potęgi.  
  
## <a name="syntax"></a>Składnia
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr1*  
   Jest wyrażeniem liczbowym.  
  
*numeric_expr2*  
   Jest potęgą, do której należy podnieść *numeric_expr1*.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak podnieść liczbę do potęgi 3 (sześcian liczby).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
