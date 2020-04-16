---
title: Definiowanie typów danych
description: Zalecenia dotyczące definiowania typów danych tabeli w synapse SQL.
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
ms.openlocfilehash: 312c58a7df09ebe3e0bdf0749f902199723a331b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429007"
---
# <a name="table-data-types-in-synapse-sql"></a>Typy danych tabeli w języku Synapse SQL

Zalecenia dotyczące definiowania typów danych tabeli w synapse SQL. 

## <a name="what-are-the-data-types"></a>Jakie są typy danych?

Synapse SQL obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typy danych](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizowanie długości wiersza

Minimalizowanie rozmiaru typów danych skraca długość wiersza, co prowadzi do lepszej wydajności kwerendy. Użyj najmniejszego typu danych, który działa dla danych.

- Unikaj definiowania kolumn znakowych o dużej długości domyślnej. Jeśli na przykład najdłuższa wartość wynosi 25 znaków, zdefiniuj kolumnę jako VARCHAR(25).
- Unikaj używania [NVARCHAR][NVARCHAR], gdy potrzebujesz tylko varcharu.
- Jeśli to możliwe, użyj NVARCHAR(4000) lub VARCHAR(8000) zamiast NVARCHAR(MAX) lub VARCHAR(MAX).

> [!NOTE]
> Jeśli używasz tabel zewnętrznych PolyBase do ładowania tabel puli SQL, zdefiniowana długość wiersza tabeli nie może przekraczać 1 MB. Gdy wiersz z danymi o zmiennej długości przekracza 1 MB, można załadować wiersz z BCP, ale nie z PolyBase.

## <a name="identify-unsupported-data-types"></a>Identyfikowanie nieobsługiwało typów danych

W przypadku migracji bazy danych z innej bazy danych SQL mogą wystąpić typy danych, które nie są obsługiwane w języku Synapse SQL. Ta kwerenda służy do odnajdywać nieobsługiwał typy danych w istniejącym schemacie SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Obejścia nieobsługiwały typy danych

Na poniższej liście przedstawiono typy danych, których program Synapse SQL nie obsługuje, i zawiera alternatywy, których można użyć zamiast nieobsługiwały typy danych.

| Nieobsługiwał typ danych | Obejście |
| --- | --- |
| [Geometrii](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [Obrazu](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Tekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Podziel kolumnę na kilka kolumn silnie wpisanych. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeśli używasz puli SQL, można przekonwertować na tabele tymczasowe. W przypadku korzystania z języka SQL (wersja zapoznawcza) można rozważyć przechowywanie danych w magazynie przy użyciu [programu CETAS](../sql/develop-tables-cetas.md). |
| [Sygnatury czasowej](/sql/t-sql/data-types/date-and-time-types) |Przerób kodu do użycia [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funkcji. Tylko stałe są obsługiwane jako wartości domyślne, dlatego nie można current_timestamp być zdefiniowane jako ograniczenie domyślne. Jeśli chcesz przeprowadzić migrację wartości wersji wiersza z kolumny wpisanej sygnaturą czasową, użyj [binary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) lub [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) dla wartości wersji wiersza NOT NULL lub NULL. |
| [Xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [typ zdefiniowany przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types) |Konwertuj z powrotem na natywny typ danych, jeśli to możliwe. |
| wartości domyślne | Wartości domyślne obsługują tylko literały i stałe. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Omówienie tabel .](develop-overview.md)
