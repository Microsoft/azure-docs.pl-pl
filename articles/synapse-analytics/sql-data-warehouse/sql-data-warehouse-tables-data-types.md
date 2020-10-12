---
title: Typy danych tabeli w puli SQL Synapse
description: Zalecenia dotyczące definiowania typów danych tabeli w puli SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 8118329564bbf8193797be0db619f821c064429a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87089162"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Typy danych tabeli w puli SQL Synapse

W tym artykule zamieszczono zalecenia dotyczące definiowania typów danych tabeli w puli SQL.

## <a name="supported-data-types"></a>Obsługiwane typy danych

Pula SQL obsługuje najczęściej używane typy danych. Aby zapoznać się z listą obsługiwanych typów danych, zobacz [typy danych](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) w instrukcji CREATE TABLE.

## <a name="minimize-row-length"></a>Minimalizuj długość wiersza

Minimalizacja rozmiaru typów danych skraca długość wiersza, co prowadzi do lepszej wydajności zapytań. Użyj najmniejszego typu danych, który działa dla danych.

- Należy unikać definiowania kolumn znaków o dużej długości domyślnej. Na przykład jeśli najdłuższa wartość to 25 znaków, zdefiniuj kolumnę jako VARCHAR (25).
- Należy unikać używania [nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , gdy wymagany jest tylko varchar.
- Jeśli to możliwe, należy użyć parametru NVARCHAR (4000) lub VARCHAR (8000) zamiast typu NVARCHAR (MAX) lub VARCHAR (MAX).

Jeśli używasz wielobazowych tabel zewnętrznych do ładowania tabel, określona długość wiersza tabeli nie może przekroczyć 1 MB. Gdy wiersz z danymi o zmiennej długości przekracza 1 MB, można załadować wiersz za pomocą narzędzia BCP, ale nie z bazą danych.

## <a name="identify-unsupported-data-types"></a>Zidentyfikuj nieobsługiwane typy danych

W przypadku migrowania bazy danych z innej bazy danych SQL mogą znajdować się typy danych, które nie są obsługiwane w puli SQL. Użyj następującego zapytania, aby odnaleźć nieobsługiwane typy danych w istniejącym schemacie SQL:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Obejścia dla nieobsługiwanych typów danych

Na poniższej liście przedstawiono typy danych, które nie są obsługiwane przez pulę SQL i dają użyteczne alternatywy dla nieobsługiwanych typów danych.

| Nieobsługiwany typ danych | Obejście |
| --- | --- |
| [geometrii](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [geograficzne](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [Image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Opis](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Podziel kolumnę na kilka kolumn o jednoznacznie określonym typie. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Konwertuj na tabele tymczasowe. |
| [znacznik czasu](/sql/t-sql/data-types/date-and-time-types) |Przepracuj kod, aby użyć [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) i funkcji [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . Tylko stałe są obsługiwane jako wartości domyślne, więc current_timestamp nie można zdefiniować jako ograniczenia domyślnego. Jeśli konieczne jest przeprowadzenie migracji wartości wersji wiersza z kolumny z określonym typem sygnatury czasowej, użyj wartości [binarnych](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(8) lub [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(8) dla NIEprawidłowych lub zerowych wersji wierszy. |
| [dokument](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [typ zdefiniowany przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Przekonwertuj z powrotem na natywny typ danych, gdy jest to możliwe. |
| wartości domyślne | Wartości domyślne obsługują tylko literały i stałe. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Omówienie tabeli](sql-data-warehouse-tables-overview.md).
