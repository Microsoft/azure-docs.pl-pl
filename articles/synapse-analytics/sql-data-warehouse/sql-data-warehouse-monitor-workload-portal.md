---
title: Monitorowanie obciążenia — Azure Portal
description: Monitoruj Synapse SQL przy użyciu Azure Portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: f780d5404d99435a61c4e3f9e90b1acbd9539af9
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118442"
---
# <a name="monitor-workload---azure-portal"></a>Monitorowanie obciążenia — Azure Portal

W tym artykule opisano, jak używać Azure Portal do monitorowania obciążenia. Obejmuje to Konfigurowanie dzienników Azure Monitor, aby zbadać trendy wykonywania i obciążeń zapytań za pomocą usługi log Analytics dla [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
- Pula SQL: będziemy zbierać dzienniki dla puli SQL. Jeśli nie masz zainicjowanej puli SQL, zapoznaj się z instrukcjami w temacie [Tworzenie puli SQL](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Przejdź do bloku Przeglądaj dla obszarów roboczych Log Analytics i Utwórz obszar roboczy

![Obszary robocze usługi Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Zrzut ekranu przedstawia obszary robocze Log Analytics, w których można wybrać opcję Dodaj.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Zrzut ekranu przedstawia obszar roboczy Log Analytics, w którym można wprowadzać wartości.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Aby uzyskać więcej informacji na temat obszarów roboczych, zapoznaj się z poniższą [dokumentacją](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-resource-logs"></a>Włącz dzienniki zasobów

Skonfiguruj ustawienia diagnostyczne, aby emitować dzienniki z puli SQL. Dzienniki składają się z widoków telemetrii równoważnych najczęściej używanym rozwiązywaniu problemów z wydajnością widoków DMV. Obecnie obsługiwane są następujące widoki:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Włączanie dzienników zasobów](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Dzienniki mogą być emitowane do usługi Azure Storage, Stream Analytics lub Log Analytics. Na potrzeby tego samouczka wybierz pozycję Log Analytics.

![Określanie dzienników](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Uruchom zapytania względem Log Analytics

Przejdź do obszaru roboczego Log Analytics, gdzie można wykonać następujące czynności:

- Analizuj dzienniki przy użyciu zapytań dzienników i zapisuj zapytania do ponownego użycia
- Zapisz zapytania do ponownego użycia
- Tworzenie alertów dotyczących dzienników
- Przypinanie wyników zapytania do pulpitu nawigacyjnego

Aby uzyskać szczegółowe informacje na temat możliwości kwerend dzienników, zapoznaj się z poniższą [dokumentacją](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json).

![Log Analytics Edytor obszarów roboczych](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics zapytań obszaru roboczego](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Przykładowe zapytania dziennika

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu i skonfigurowaniu dzienników usługi Azure monitor, [Dostosuj pulpity nawigacyjne platformy Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tak, aby były udostępniane przez zespół.