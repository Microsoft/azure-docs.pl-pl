---
title: Zasoby służące do tworzenia puli SQL Synapse w usłudze Azure Synapse Analytics
description: Pojęcia dotyczące programowania, decyzje projektowe, zalecenia i techniki kodowania dla usługi Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 95f712f196c37650b52220c9e34f6cb6b50bff23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460613"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Projektowanie decyzji i technik kodowania dla puli SQL Synapse w usłudze Azure Synapse Analytics 
 W tym artykule znajdziesz dodatkowe zasoby, które pomogą lepiej zrozumieć najważniejsze decyzje projektowe, zalecenia i techniki kodowania dla puli SQL w usłudze Azure Synapse.

## <a name="key-design-decisions"></a>Najważniejsze decyzje projektowe
W poniższych artykułach wyróżniono koncepcje i decyzje projektowe dotyczące tworzenia rozproszonego magazynu danych przy użyciu możliwości puli SQL w usłudze Azure Synapse:

* [Licznik](../sql/connect-overview.md)
* [współbieżności](resource-classes-for-workload-management.md)
* [Akcja](sql-data-warehouse-develop-transactions.md)
* [schematy zdefiniowane przez użytkownika](sql-data-warehouse-develop-user-defined-schemas.md)
* [Dystrybucja tabel](sql-data-warehouse-tables-distribute.md)
* [indeksy tabeli](sql-data-warehouse-tables-index.md)
* [partycje tabeli](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Statystyki](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące programowania i techniki kodowania
Poniższe artykuły zawierają charakterystyczne techniki kodowania, porady i zalecenia dotyczące tworzenia puli SQL:

* [procedury składowane](sql-data-warehouse-develop-stored-procedures.md)
* [Etykieta](sql-data-warehouse-develop-label.md)
* [Widoki](performance-tuning-materialized-views.md)
* [tabele tymczasowe](sql-data-warehouse-tables-temporary.md)
* [dynamiczny SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [pętli](sql-data-warehouse-develop-loops.md)
* [Grupuj według opcji](sql-data-warehouse-develop-group-by-options.md)
* [przypisanie zmiennej](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [instrukcje języka T-SQL](sql-data-warehouse-reference-tsql-statements.md).
