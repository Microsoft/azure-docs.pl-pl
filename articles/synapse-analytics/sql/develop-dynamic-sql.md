---
title: Użyj dynamicznego SQL w Synapse SQL
description: Porady dotyczące korzystania z dynamicznego sql w Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429605"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamiczny SQL w Synapse SQL
W tym artykule znajdziesz wskazówki dotyczące korzystania z dynamicznego języka SQL i tworzenia rozwiązań przy użyciu synapse SQL.

## <a name="dynamic-sql-example"></a>Dynamiczny przykład SQL

Podczas tworzenia kodu aplikacji może być konieczne użycie dynamicznego języka SQL w celu dostarczania elastycznych, ogólnych i modułowych rozwiązań.

> [!NOTE]
> Pula SQL nie obsługuje typów danych obiektów blob w tej chwili. Nie obsługujące typów danych obiektów blob może ograniczyć rozmiar ciągów, ponieważ typy danych obiektów blob obejmują zarówno typy varchar(max) i nvarchar(max). Jeśli użyto tych typów w kodzie aplikacji do tworzenia dużych ciągów, należy podzielić kod na fragmenty i zamiast tego użyć instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótki, można użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) jak zwykle.

> [!NOTE]
> Instrukcje wykonywane jako dynamiczny SQL nadal będą podlegać wszystkim regułom sprawdzania poprawności T-SQL.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](develop-overview.md).
