---
title: Typy danych tabeli w Synapse SQL
description: Zalecenia dotyczące definiowania typów danych tabeli w języku SQL Synapse.
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
ms.openlocfilehash: ae919a12dc1c50fcb30d08128e4ebf2faa2b2ccb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674161"
---
# <a name="table-data-types-in-synapse-sql"></a>Typy danych tabeli w Synapse SQL

W tym artykule znajdziesz zalecenia dotyczące definiowania typów danych tabeli w języku SQL Synapse. 

## <a name="data-types"></a>Typy danych

Synapse SQL obsługuje najczęściej używane typy danych. Aby zapoznać się z listą obsługiwanych typów danych, zobacz [typy danych](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true) w instrukcji CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizuj długość wiersza

Minimalizacja rozmiaru typów danych skraca długość wiersza, co prowadzi do lepszej wydajności zapytań. Użyj najmniejszego typu danych, który działa dla danych.

- Należy unikać definiowania kolumn znaków o dużej długości domyślnej. Na przykład jeśli najdłuższa wartość to 25 znaków, zdefiniuj kolumnę jako VARCHAR (25).
- Należy unikać używania [NVARCHAR] [NVARCHAR], gdy wymagany jest tylko VARCHAR.
- Jeśli to możliwe, należy użyć parametru NVARCHAR (4000) lub VARCHAR (8000) zamiast typu NVARCHAR (MAX) lub VARCHAR (MAX).

> [!NOTE]
> Jeśli używasz wielobazowych tabel zewnętrznych do ładowania tabel SQL Synapse, określona długość wiersza tabeli nie może przekroczyć 1 MB. Gdy wiersz z danymi o zmiennej długości przekracza 1 MB, można załadować wiersz za pomocą narzędzia BCP, ale nie z bazą danych.

## <a name="identify-unsupported-data-types"></a>Zidentyfikuj nieobsługiwane typy danych

W przypadku migrowania bazy danych z innej bazy danych SQL mogą wystąpić typy danych, które nie są obsługiwane w programie SQL Synapse. Użyj tego zapytania, aby odnaleźć nieobsługiwane typy danych w istniejącym schemacie SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Obejścia dla nieobsługiwanych typów danych

Na poniższej liście przedstawiono typy danych, które Synapse SQL nie obsługuje i dają alternatywy, których można użyć zamiast nieobsługiwanych typów danych.

| Nieobsługiwany typ danych | Obejście |
| --- | --- |
| [geometrii](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [geograficzne](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?view=azure-sqldw-latest&preserve-view=true)(4000) |
| [Image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [Opis](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Podziel kolumnę na kilka kolumn o jednoznacznie określonym typie. |
| [table](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Konwertuj na tabele tymczasowe lub Rozważ przechowywanie danych w magazynie przy użyciu [CETAS](../sql/develop-tables-cetas.md). |
| [znacznik czasu](/sql/t-sql/data-types/date-and-time-types) |Przepracuj kod, aby użyć [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?view=azure-sqldw-latest&preserve-view=true) i funkcji [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?view=azure-sqldw-latest&preserve-view=true) . Tylko stałe są obsługiwane jako wartości domyślne, dlatego current_timestamp nie można zdefiniować jako ograniczenia domyślnego. Jeśli konieczne jest przeprowadzenie migracji wartości wersji wiersza z kolumny o określonym typie sygnatury czasowej, użyj wartości [binarnych](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true)(8) lub [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true)(8), aby uzyskać wartość niezerową lub null. |
| [dokument](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [typ zdefiniowany przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types) |Przekonwertuj z powrotem na natywny typ danych, gdy jest to możliwe. |
| wartości domyślne | Wartości domyślne obsługują tylko literały i stałe. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Omówienie tabeli](develop-overview.md).
