---
title: Monitorowanie znaczenia obciążenia i zarządzanie nim
description: Dowiedz się, jak zarządzać i monitorować ważność poziomu żądań w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 43006456142728287ddf4adba1fbb9b45f5ccc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85211973"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Zarządzanie i monitorowanie ważności obciążeń w usłudze Azure Synapse Analytics

Zarządzaj i monitoruj Synapse ważność żądania SQL na poziomie na platformie Azure Synapse przy użyciu widoków widoków DMV i wykazu.

## <a name="monitor-importance"></a>Ważność monitora

Monitoruj ważność przy użyciu kolumny Nowa ważność w widoku dynamicznego zarządzania [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .
Poniższe zapytanie monitorowania przedstawia czas przesyłania i godzinę rozpoczęcia dla zapytań. Przejrzyj czas przesyłania i czas rozpoczęcia wraz z ważnością, aby zobaczyć, jak znaczenie ma wpływ na planowanie.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Aby dojrzeć się do sposobu planowania zapytań, użyj widoków wykazu.

## <a name="manage-importance-with-catalog-views"></a>Zarządzanie ważnośćmi przy użyciu widoków wykazu

Widok wykazu sys.workload_management_workload_classifiers zawiera informacje na temat klasyfikatorów. Aby wykluczyć klasyfikatory zdefiniowane przez system, które są mapowane na klasy zasobów, wykonaj następujący kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Widok wykazu, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), zawiera informacje na temat parametrów używanych podczas tworzenia klasyfikatora.  Poniższe zapytanie pokazuje, że ExecReportsClassifier został utworzony na ```membername``` parametrze dla wartości z ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![wyniki zapytania](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Aby uprościć Rozwiązywanie problemów z błędną klasyfikacją, zaleca się usunięcie mapowań ról klasy zasobów podczas tworzenia klasyfikatorów obciążeń. Poniższy kod zwraca istniejące członkostwa ról klasy zasobów. Uruchom sp_droprolemember dla każdej ```membername``` zwróconej klasy zasobów.
Poniżej znajduje się przykład sprawdzania istnienia przed porzuceniem klasyfikatora obciążenia:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat klasyfikacji, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).
- Aby uzyskać więcej informacji na temat ważności, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do konfigurowania ważności obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md)
