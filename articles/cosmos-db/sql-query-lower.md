---
title: NIŻSZY w Azure Cosmos DB języku zapytań
description: Zapoznaj się z NIŻSZą funkcją systemu SQL w Azure Cosmos DB, aby zwrócić wyrażenie ciągu po przekonwertowaniu wielkich liter na małe litery
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd1d9ddefd67cadb92632fd6db7a1fbd5a34f35a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338442"
---
# <a name="lower-azure-cosmos-db"></a>NIŻSZY (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery.  

NIŻSZA Funkcja systemowa nie korzysta z indeksu. Jeśli planujesz częste porównywanie bez uwzględniania wielkości liter, niższa Funkcja systemowa może zużywać znaczną ilość jednostek RU. W takim przypadku zamiast używać MNIEJSZEj funkcji systemowej do normalizacji danych za każdym razem w przypadku porównań, można znormalizować wielkość liter po wstawieniu. Następnie zapytanie takie jak SELECT * FROM c, gdzie LOWER (c. Name) = "Bob", po prostu wybiera * z c, gdzie c.name = "Bob".

## <a name="syntax"></a>Składnia
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład przedstawia sposób użycia `LOWER` w zapytaniu.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
