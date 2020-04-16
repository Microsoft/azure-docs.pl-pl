---
title: Zasoby do tworzenia funkcji Synapse SQL
description: Koncepcje rozwoju, decyzje projektowe, zalecenia i techniki kodowania dla Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429020"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Projektowania decyzji i technik kodowania funkcji Synapse SQL w usłudze Azure Synapse Analytics
W tym artykule znajdziesz listę zasobów dla puli SQL i SQL na żądanie (wersja zapoznawcza) funkcje Synapse SQL. Zalecane artykuły są podzielone na dwie sekcje: Kluczowe decyzje projektowe oraz techniki programowania i kodowania.

Celem tych artykułów jest pomoc w opracowaniu optymalnego podejścia technicznego dla składników SQL Synapse w ramach analizy Synapse Analytics.

## <a name="key-design-decisions"></a>Kluczowe decyzje projektowe
W poniższych artykułach podkreślono pojęcia i decyzje projektowe dotyczące rozwoju języka SQL Synapse:

|                                                          |   Pula SQL   | SQL na żądanie |
| -----------------------------------------------------    | ---- | ---- |
| [Połączenia](connect-overview.md)                    | Tak | Tak |
| [Klasy zasobów i współbieżność](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Tak    | Nie |
| [Transakcje](develop-transactions.md)              | Tak | Nie |
| [Schematy definiowane przez użytkownika](develop-user-defined-schemas.md) | Tak | Tak |
| [Dystrybucja tabel](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Tak | Nie |
| [Indeksy tabel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Tak | Nie |
| [Partycje tabeli](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Tak | Nie |
| [Statystyki](develop-tables-statistics.md)            | Tak | Tak |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Tak | Nie |
| [Tabele zewnętrzne](develop-tables-external-tables.md) | Tak | Tak |
| [CETA](develop-tables-cetas.md)                     | Tak | Tak |


## <a name="recommendations"></a>Zalecenia

Poniżej znajdziesz podstawowe artykuły, które podkreślają konkretne techniki kodowania, porady i zalecenia dotyczące rozwoju:

|                                            | Pula SQL | SQL na żądanie |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Procedury składowane](develop-stored-procedures.md)  | Tak                | Nie                      |
| [Etykiety](develop-label.md)                           | Tak                | Nie                      |
| [Widoki](develop-views.md)                             | Tak                | Tak                     |
| [Tabele tymczasowe](develop-tables-temporary.md)       | Tak                | Tak                     |
| [Dynamiczny język SQL](develop-dynamic-sql.md)                 | Tak                | Tak                     |
| [Zapętlenie](develop-loops.md)                         | Tak                | Tak                     |
| [Grupuj według opcji](develop-group-by-options.md)       | Tak                | Nie                      |
| [Przypisanie zmiennej](develop-variable-assignment.md) | Tak                | Tak                     |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji referencyjnych, zobacz [instrukcje T-SQL puli SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

