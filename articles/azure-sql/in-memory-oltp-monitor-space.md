---
title: Monitorowanie magazynu XTP w pamięci
description: Oszacuj i monitoruj XTP użycie magazynu w pamięci; błąd rozwiązywania problemów z pojemnością 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5717c2479c1d894117bae44826a814c3cfeaa98a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96493393"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitoruj magazyn In-Memory OLTP w Azure SQL Database i wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

W przypadku korzystania [z funkcji OLTP w pamięci](in-memory-oltp-overview.md)dane w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabeli znajdują się w In-Memory OLTP.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Określanie, czy dane mieszczą się w limicie magazynu In-Memory OLTP

Ustal limity magazynu dla różnych warstw usług. Każda warstwa usług premium i Krytyczne dla działania firmy ma maksymalny rozmiar magazynu OLTP In-Memory.

- [Limity zasobów na podstawie jednostek DTU — pojedyncza baza danych](database/resource-limits-dtu-single-databases.md)
- [Limity zasobów oparte na jednostkach DTU — pule elastyczne](database/resource-limits-dtu-elastic-pools.md)
- [Limity zasobów opartych na rdzeń wirtualny — pojedyncze bazy danych](database/resource-limits-vcore-single-databases.md)
- [Limity zasobów opartych na rdzeń wirtualny — pule elastyczne](database/resource-limits-vcore-elastic-pools.md)
- [Limity zasobów na podstawie rdzeń wirtualny — wystąpienie zarządzane](managed-instance/resource-limits.md)

Szacowanie wymagań dotyczących pamięci dla tabeli zoptymalizowanej pod kątem pamięci działa tak samo jak w przypadku SQL Server, jak w Azure SQL Database i w wystąpieniu zarządzanym usługi Azure SQL. Zapoznaj się z [wymaganiami dotyczącymi pamięci szacowanej](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables), poświęć kilka minut.

Wiersze zmiennych tabeli i tabeli, a także indeksy, są wliczane do maksymalnego rozmiaru danych użytkownika. Ponadto instrukcja ALTER TABLE wymaga wystarczającej ilości miejsca, aby utworzyć nową wersję całej tabeli i jej indeksów.

Po przekroczeniu tego limitu operacje INSERT i Update mogą zakończyć się niepowodzeniem z błędem 41823 dla pojedynczych baz danych w Azure SQL Database i bazach danych w wystąpieniu zarządzanym usługi Azure SQL, a w przypadku pul elastycznych w Azure SQL Database Wystąpił błąd 41840. W tym momencie należy usunąć dane, aby odzyskiwać pamięć, lub uaktualnić warstwę usług lub wielkość obliczeniową bazy danych.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Można monitorować użycie magazynu w pamięci jako procent limitu magazynu dla rozmiaru obliczeń w [Azure Portal](https://portal.azure.com/):

1. W bloku baza danych Znajdź pole wykorzystanie zasobów i kliknij przycisk Edytuj.
2. Wybierz metrykę `In-Memory OLTP Storage percentage` .
3. Aby dodać alert, kliknij pole użycie zasobów, aby otworzyć blok Metryka, a następnie kliknij pozycję Dodaj alert.

Lub użyj następującego zapytania, aby pokazać użycie magazynu w pamięci:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Popraw sytuacje magazynu OLTP w pamięci — błędy 41823 i 41840

Nastąpi przekroczenie limitu magazynu OLTP w bazie danych w wyniku operacji INSERT, UPDATE, ALTER i CREATE z komunikatem o błędzie 41823 (dla pojedynczych baz danych) lub Error 41840 (w przypadku pul elastycznych). In-Memory Oba błędy powodują przerwanie aktywnej transakcji.

Komunikaty o błędach 41823 i 41840 wskazują, że tabele zoptymalizowane pod kątem pamięci i zmienne tabeli w bazie danych lub puli osiągnęły maksymalny rozmiar magazynu OLTP In-Memory.

Aby rozwiązać ten problem, należy:

- Usuwanie danych z tabel zoptymalizowanych pod kątem pamięci, które mogą odciążać dane w tradycyjnych, opartych na dyskach tabelach; oraz
- Uaktualnij warstwę usług do jednego z wystarczającą ilością miejsca w pamięci, aby uzyskać dane, które mają być przechowywane w tabelach zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> W rzadkich przypadkach błędy 41823 i 41840 mogą być przejściowe, co oznacza, że jest wystarczająca ilość dostępnego In-Memory magazynu OLTP i ponawianie próby wykonania operacji zakończy się pomyślnie. Dlatego zalecamy monitorowanie ogólnego dostępnego In-Memory magazynu OLTP i ponawianie próby w przypadku pierwszego wystąpienia błędu 41823 lub 41840. Aby uzyskać więcej informacji na temat logiki ponawiania, zobacz [wykrywanie konfliktów i logikę ponowień przy użyciu In-Memory OLTP](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące monitorowania, zobacz [monitorowanie przy użyciu dynamicznych widoków zarządzania](database/monitoring-with-dmvs.md).