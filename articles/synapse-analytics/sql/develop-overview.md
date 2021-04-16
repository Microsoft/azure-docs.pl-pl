---
title: Zasoby do tworzenia Synapse SQL internetowych
description: Pojęcia programistyczne, decyzje projektowe, zalecenia i techniki kodowania dla Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377334"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decyzje projektowe i techniki kodowania Synapse SQL funkcji w Azure Synapse Analytics
W tym artykule znajdziesz listę zasobów dla dedykowanych funkcji puli SQL i bez serwera puli SQL Synapse SQL. Zalecane artykuły zostały podzielone na dwie sekcje: Kluczowe decyzje projektowe oraz techniki programowania i programowania.

Celem tych artykułów jest pomoc w opracowanie optymalnego podejścia technicznego do składników Synapse SQL w Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Kluczowe decyzje projektowe
W poniższych artykułach przedstawiono pojęcia i decyzje projektowe dotyczące Synapse SQL projektowania:

| Artykuł | dedykowana pula SQL | Bezserwerowa pula SQL |
| ------- | -------- | ------------- |
| [Połączenia](connect-overview.md)                    | Tak | Tak |
| [Klasy zasobów i współbieżność](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Tak    | Nie |
| [Transakcje](develop-transactions.md)              | Tak | Nie |
| [Schematy definiowane przez użytkownika](develop-user-defined-schemas.md) | Tak | Tak |
| [Dystrybucja tabel](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Tak | Nie |
| [Indeksy tabel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Tak | Nie |
| [Partycje tabel](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Tak | Nie |
| [Statystyki](develop-tables-statistics.md)            | Tak | Tak |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Tak | Nie |
| [Tabele zewnętrzne](develop-tables-external-tables.md) | Tak | Tak |
| [CETAS](develop-tables-cetas.md)                     | Tak | Tak |


## <a name="recommendations"></a>Zalecenia

Poniżej znajdziesz podstawowe artykuły, w których podkreślono określone techniki kodowania, porady i zalecenia dotyczące programowania:

| Artykuł | dedykowana pula SQL | Bezserwerowa pula SQL |
| ------- | -------- | ------------- |
| [Procedury składowane](develop-stored-procedures.md)  | Tak                | Tak                      |
| [Etykiety](develop-label.md)                           | Tak                | Nie                      |
| [Widoki](develop-views.md)                             | Tak                | Tak                     |
| [Tabele tymczasowe](develop-tables-temporary.md)       | Tak                | Tak                     |
| [Dynamiczny język SQL](develop-dynamic-sql.md)                 | Tak                | Tak                     |
| [Zapętlenie](develop-loops.md)                         | Tak                | Tak                     |
| [Grupuj według opcji](develop-group-by-options.md)       | Tak                | Nie                      |
| [Przypisanie zmiennej](develop-variable-assignment.md) | Tak                | Tak                     |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji referencyjnych, zobacz [Instrukcje języka T-SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)puli SQL .

