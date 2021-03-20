---
title: Średni w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat funkcji systemu SQL średnia (średnia) w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96555420"
---
# <a name="avg-azure-cosmos-db"></a>Średnia (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ta funkcja agregująca zwraca średnią wartości w wyrażeniu.
  
## <a name="syntax"></a>Składnia
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład zwraca średnią wartość `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy). Jeśli którykolwiek z argumentów w `AVG` jest ciąg, wartość logiczna lub wartość null, zostanie zwrócona cała agregowana Funkcja systemowa `undefined` . Jeśli którykolwiek z argumentów ma `undefined` wartość, nie wpłynie to na `AVG` obliczenia.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne w Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe w Azure Cosmos DB](sql-query-system-functions.md)
- [Funkcje agregujące w Azure Cosmos DB](sql-query-aggregate-functions.md)