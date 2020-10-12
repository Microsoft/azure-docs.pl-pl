---
title: Funkcje sprawdzania typu w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji sprawdzania systemu w programie SQL Server w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3ffe8fa9286c8594dc72d78582f8c7b0a3d05c78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85563339"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funkcje sprawdzania typu (Azure Cosmos DB)

Funkcje sprawdzania typu umożliwiają sprawdzanie typu wyrażenia w zapytaniu SQL. Za pomocą funkcji sprawdzania typu można określić typy właściwości w obrębie elementów na bieżąco, gdy są one zmienne lub nieznane. 

## <a name="functions"></a>Funkcje

Oto tabela obsługiwanych wbudowanych funkcji sprawdzania typów:

Następujące funkcje obsługują sprawdzanie typu względem wartości wejściowych, a każdy zwraca wartość logiczną.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
