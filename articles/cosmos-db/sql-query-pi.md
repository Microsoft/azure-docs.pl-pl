---
title: PI w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji systemowej PI w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349655"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Zwraca stałą wartość liczby PI.  
  
## <a name="syntax"></a>Składnia
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca wartość `PI` .  
  
```sql
SELECT PI() AS pi 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
