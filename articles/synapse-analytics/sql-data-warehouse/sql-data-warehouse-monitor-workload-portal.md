---
title: Monitorowanie obciążenia — Azure Portal
description: Monitorowanie Synapse SQL przy użyciu Azure Portal
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4f4c50588a67e2e69d0975c9f4414242ecf23617
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568271"
---
# <a name="monitor-workload---azure-portal"></a>Monitorowanie obciążenia — Azure Portal

W tym artykule opisano sposób używania Azure Portal do monitorowania obciążenia. Obejmuje to konfigurowanie dzienników Azure Monitor w celu badania trendów wykonywania zapytań i obciążeń przy użyciu analizy dzienników [Synapse SQL.](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, przed [rozpoczęciem](https://azure.microsoft.com/free/) utwórz bezpłatne konto.
- Pula SQL: będziemy zbierać dzienniki dla puli SQL. Jeśli nie masz aprowizowanych pul SQL, zobacz instrukcje w tece [Tworzenie puli SQL.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Przejdź do bloku przeglądania obszarów roboczych usługi Log Analytics i utwórz obszar roboczy

![Obszary robocze usługi Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Zrzut ekranu przedstawia obszary robocze usługi Log Analytics, w których można wybrać pozycję Dodaj.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Zrzut ekranu przedstawia obszar roboczy usługi Log Analytics, w którym można wprowadzać wartości.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Aby uzyskać więcej informacji na temat obszarów roboczych, odwiedź następującą [dokumentację.](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-resource-logs"></a>Włączanie dzienników zasobów

Skonfiguruj ustawienia diagnostyczne, aby emitować dzienniki z puli SQL. Dzienniki składają się z widoków telemetrii równoważnych z najczęściej używanymi widokami DMV rozwiązywania problemów z wydajnością. Obecnie obsługiwane są następujące widoki:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Włączanie dzienników zasobów](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Dzienniki mogą być emitowane do usługi Azure Storage, Stream Analytics lub Log Analytics. W tym samouczku wybierz pozycję Log Analytics.

![Określanie dzienników](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Uruchamianie zapytań względem usługi Log Analytics

Przejdź do obszaru roboczego usługi Log Analytics, w którym można wykonać następujące czynności:

- Analizowanie dzienników przy użyciu zapytań dzienników i zapisywanie zapytań do ponownego użycia
- Zapisywanie zapytań do ponownego użycia
- Tworzenie alertów dotyczących dzienników
- Przypinanie wyników zapytania do pulpitu nawigacyjnego

Aby uzyskać szczegółowe informacje na temat możliwości zapytań dziennika, odwiedź następującą [dokumentację.](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)

![Edytor obszarów roboczych usługi Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Zapytania obszaru roboczego usługi Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

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

Teraz, po skonfigurowaniu i skonfigurowaniu dzienników usługi Azure Monitor, dostosuj pulpity nawigacyjne platformy [Azure,](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aby udostępnić je zespołowi.