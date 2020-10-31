---
title: Podciąg w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o podciągu funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6cb96270d1db970230e3abb528b58b7341d652f0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081488"
---
# <a name="substring-azure-cosmos-db"></a>Podciąg (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca część wyrażenia ciągu rozpoczynającą się od określonego znaku na podstawie zera i kontynuuje określoną długość lub na końcu ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.
  
*num_expr1*  
   Jest wyrażeniem liczbowym służącym do określenia znaku początkowego. Wartość 0 jest pierwszym znakiem *str_expr* .
  
*num_expr2*  
   Jest wyrażeniem liczbowym określającym maksymalną liczbę znaków *str_expr* do zwrócenia. Wartość 0 lub mniej skutkuje ciągiem pustym.

## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca podciąg "ABC" zaczynający się od 1 i o długości 1 znaku.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy) , jeśli pozycja początkowa jest `0` .

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
