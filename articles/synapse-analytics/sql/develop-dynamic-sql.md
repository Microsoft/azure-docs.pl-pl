---
title: Używanie dynamicznego języka SQL w programie SQL Synapse
description: Porady dotyczące korzystania z dynamicznego języka SQL w programie SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 4a4ce92278d5f6704fec4a5b9d30b44ddf89b0ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033442"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamiczne SQL w Synapse SQL
W tym artykule znajdziesz porady dotyczące korzystania z dynamicznego języka SQL i tworzenia rozwiązań przy użyciu języka SQL Synapse.

## <a name="dynamic-sql-example"></a>Dynamiczny przykład SQL

Podczas tworzenia kodu aplikacji może być konieczne użycie dynamicznego języka SQL w celu zapewnienia elastycznych, ogólnych i modularnych rozwiązań.

> [!NOTE]
> W tej chwili Pula SQL nie obsługuje typów danych obiektów BLOB. Nieobsługiwane typy danych obiektów BLOB mogą ograniczać rozmiar ciągów, ponieważ typy danych obiektów BLOB zawierają zarówno typy varchar (max), jak i nvarchar (max). Jeśli w kodzie aplikacji użyto tych typów do kompilowania dużych ciągów, należy podzielić kod na fragmenty i zamiast tego użyć instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótki, można użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) jak zwykle.

> [!NOTE]
> Instrukcje wykonane jako dynamiczny kod SQL będą nadal podlegać wszystkim regułom walidacji T-SQL.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](develop-overview.md).
