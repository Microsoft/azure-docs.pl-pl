---
title: Zasoby do tworzenia puli SQL Synapse w usłudze Azure Synapse Analytics
description: Koncepcje programistyczne, decyzje projektowe, zalecenia i techniki kodowania dla usługi SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 12f91633834ee98582eaad886b48eb8619378265
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411670"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Projektowaniaj decyzji i technik kodowania puli SQL Synapse w usłudze Azure Synapse Analytics 
 W tym artykule znajdziesz dodatkowe zasoby, które pomogą Ci lepiej zrozumieć kluczowe decyzje projektowe, zalecenia i techniki kodowania dla puli SQL w usłudze Azure Synapse.

## <a name="key-design-decisions"></a>Kluczowe decyzje projektowe
W poniższych artykułach przedstawiono pojęcia i decyzje projektowe dotyczące tworzenia rozproszonego magazynu danych przy użyciu możliwości puli SQL w usłudze Azure Synapse:

* [Połączenia](../sql/connect-overview.md)
* [Współbieżności](resource-classes-for-workload-management.md)
* [Transakcji](sql-data-warehouse-develop-transactions.md)
* [schematy zdefiniowane przez użytkownika](sql-data-warehouse-develop-user-defined-schemas.md)
* [rozkład tabeli](sql-data-warehouse-tables-distribute.md)
* [indeksy tabel](sql-data-warehouse-tables-index.md)
* [partycje tabeli](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Statystyki](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące rozwoju i techniki kodowania
Następujące artykuły zawierają określone techniki kodowania, porady i zalecenia dotyczące tworzenia puli SQL:

* [procedury składowane](sql-data-warehouse-develop-stored-procedures.md)
* [Etykiety](sql-data-warehouse-develop-label.md)
* [Widoki](sql-data-warehouse-develop-views.md)
* [tabele tymczasowe](sql-data-warehouse-tables-temporary.md)
* [dynamiczny SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [Pętli](sql-data-warehouse-develop-loops.md)
* [grupowanie według opcji](sql-data-warehouse-develop-group-by-options.md)
* [przypisanie zmiennej](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji referencyjnych, zobacz [Instrukcje T-SQL](sql-data-warehouse-reference-tsql-statements.md).
