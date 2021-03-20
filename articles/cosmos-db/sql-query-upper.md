---
title: GÓRNy w Azure Cosmos DB języku zapytań
description: Więcej informacji na temat funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 06f85d97266e78b343ad8da233b77e369da5ee65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334909"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery.  

Górna Funkcja systemowa nie korzysta z indeksu. Jeśli planujesz częste porównywanie bez uwzględniania wielkości liter, Górna Funkcja systemowa może zużywać znaczną ilość jednostek RU. W takim przypadku zamiast używać górnej funkcji systemowej do normalizacji danych za każdym razem w przypadku porównań, można znormalizować wielkość liter po wstawieniu. Następnie zapytanie takie jak SELECT * FROM c, gdzie UPPER (c. Name) = "BOB", po prostu wybiera * z c, gdzie c.name = "BOB".

## <a name="syntax"></a>Składnia
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład przedstawia sposób użycia `UPPER` w zapytaniu  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
