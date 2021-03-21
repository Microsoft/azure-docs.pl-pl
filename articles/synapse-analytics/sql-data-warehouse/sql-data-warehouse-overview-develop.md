---
title: Zasoby służące do tworzenia dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics
description: Koncepcje programowania, decyzje projektowe, zalecenia i techniki kodowania dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6b34c70b453c26fe27a51e1aa802564864640cb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96453691"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Decyzje projektowe i techniki kodowania dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics 

 W tym artykule znajdują się dodatkowe zasoby, które ułatwiają lepsze zrozumienie kluczowych decyzji projektowych, zaleceń i technik kodowania dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse.

## <a name="key-design-decisions"></a>Najważniejsze decyzje projektowe

W poniższych artykułach wyróżniono koncepcje i decyzje projektowe dotyczące tworzenia rozproszonego magazynu danych przy użyciu dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse:

* [Licznik](sql-data-warehouse-connect-overview.md)
* [współbieżności](resource-classes-for-workload-management.md)
* [Akcja](sql-data-warehouse-develop-transactions.md)
* [schematy zdefiniowane przez użytkownika](sql-data-warehouse-develop-user-defined-schemas.md)
* [Dystrybucja tabel](sql-data-warehouse-tables-distribute.md)
* [indeksy tabeli](sql-data-warehouse-tables-index.md)
* [partycje tabeli](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Statystyki](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące programowania i techniki kodowania

Poniższe artykuły zawierają charakterystyczne techniki kodowania, porady i zalecenia dotyczące tworzenia dedykowanej puli SQL (dawniej SQL DW):

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
