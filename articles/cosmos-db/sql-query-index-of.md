---
title: INDEX_OF w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL INDEX_OF w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71350992"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 Zwraca pozycję początkową pierwszego wystąpienia drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli ciąg nie zostanie znaleziony.  
  
## <a name="syntax"></a>Składnia
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu do przeszukania.  
  
*str_expr2*  
   Jest wyrażeniem ciągu, które ma zostać wyszukane.  

*numeric_expr* Opcjonalne wyrażenie liczbowe ustawiające pozycję, o której rozpocznie się wyszukiwanie. Pierwsza pozycja w *str_expr1* jest równa 0. 
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca indeks różnych podciągów w ciągu "ABC".  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
