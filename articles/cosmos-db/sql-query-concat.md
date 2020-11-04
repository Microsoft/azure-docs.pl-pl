---
title: CONCAT w Azure Cosmos DB języku zapytań
description: Dowiedz się, w jaki sposób funkcja CONCAT systemu SQL w Azure Cosmos DB zwraca ciąg, który jest wynikiem łączenia dwóch lub więcej wartości ciągu
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5fbbbdd9d29199f5b9bd173b6ab12d3d615943c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339199"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu do łączenia z innymi wartościami. `CONCAT`Funkcja wymaga co najmniej dwóch argumentów *str_expr* .  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca połączony ciąg z określonych wartości.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
