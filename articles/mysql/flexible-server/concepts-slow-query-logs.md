---
title: Dzienniki wolnych zapytań — serwer elastyczny Azure Database for MySQL
description: Zawiera opis wolnych dzienników zapytań dostępnych w Azure Database for MySQL elastycznym serwerze.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d311ea3158e1f9d53c51fe239103039849597d11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579183"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Wolne dzienniki zapytań w Azure Database for MySQL elastycznym serwerze (wersja zapoznawcza)

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

W Azure Database for MySQL elastycznym serwerze, długi dziennik zapytań jest dostępny dla użytkowników w celu skonfigurowania i uzyskania dostępu. Dzienniki wolnych zapytań są domyślnie wyłączone i można je włączyć, aby pomóc w identyfikowaniu wąskich gardeł wydajności podczas rozwiązywania problemów.

Więcej informacji o dzienniku wolnych zapytań programu MySQL znajduje się w [sekcji "wolny dziennik zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) " w dokumentacji aparatu programu MySQL.

## <a name="configure-slow-query-logging"></a>Skonfiguruj rejestrowanie wolnych zapytań 
Domyślnie dziennik wolnych zapytań jest wyłączony. Aby włączyć dzienniki, ustaw `slow_query_log` parametr Server na wartość *on*. Tę konfigurację można skonfigurować przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure <!-- add link to server parameter-->. 

Inne parametry, które można dostosować w celu sterowania zachowaniem rejestrowania wolnych zapytań, obejmują:

- **long_query_time**: Zaloguj zapytanie, jeśli trwa dłużej niż `long_query_time` (w sekundach). Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: określa, czy instrukcje administracyjne (np. `ALTER_TABLE`, `ANALYZE_TABLE` ) są rejestrowane.
- **log_queries_not_using_indexes**: określa, czy rejestrowane są zapytania, które nie używają indeksów.
- **log_throttle_queries_not_using_indexes**: ogranicza liczbę zapytań nieindeksowanych, które można zapisać w dzienniku wolnych zapytań. Ten parametr obowiązuje, gdy `log_queries_not_using_indexes` jest ustawiony na wartość *włączone*

> [!IMPORTANT]
> Jeśli tabele nie są indeksowane, ustawienie `log_queries_not_using_indexes` parametrów i na wartość `log_throttle_queries_not_using_indexes` **on** może wpłynąć na wydajność programu MySQL, ponieważ wszystkie zapytania uruchomione względem tych nieindeksowanych tabel zostaną zazapisywane w dzienniku wolnych zapytań.

Więcej opisów parametrów dziennika wolnych zapytań można znaleźć w [dokumentacji dziennika wolnych zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) programu MySQL.

## <a name="access-slow-query-logs"></a>Dostęp do dzienników wolnych zapytań

Dzienniki wolnych zapytań są zintegrowane z ustawieniami diagnostycznymi Azure Monitor. Po włączeniu dzienników wolnych zapytań na serwerze elastycznym MySQL można je emitować do Azure Monitor dzienników, Event Hubs lub Azure Storage. Aby dowiedzieć się więcej na temat ustawień diagnostycznych, zobacz [dokumentację dzienników diagnostycznych](../../azure-monitor/essentials/platform-logs-overview.md). Aby dowiedzieć się więcej na temat włączania ustawień diagnostycznych w Azure Portal, zobacz [artykuł poświęcony witrynie Portal dziennika wolnych zapytań](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

W poniższej tabeli opisano dane wyjściowe dziennika wolnych zapytań. W zależności od metody Output pola uwzględnione i kolejność ich wyświetlania mogą się różnić.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Stałego `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Stałego `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nazwa serwera |
| `start_time_t` UTC | Godzina rozpoczęcia zapytania |
| `query_time_s` | Łączny czas (w sekundach) wykonywania zapytania |
| `lock_time_s` | Łączny czas w sekundach, przez który zapytanie zostało zablokowane |
| `user_host_s` | Nazwa użytkownika |
| `rows_sent_s` | Liczba wysłanych wierszy |
| `rows_examined_s` | Liczba badanych wierszy |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Wstaw identyfikator |
| `sql_text_s` | Pełne zapytanie |
| `server_id_s` | Identyfikator serwera |
| `thread_id_s` | Identyfikator wątku |
| `\_ResourceId` | Identyfikator URI zasobu |

> [!Note]
> W przypadku programu `sql_text_s` Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizowanie dzienników w dziennikach Azure Monitor

Po powolnych dziennikach zapytań do Azure Monitor dzienników za poorednictwem dzienników diagnostycznych można przeprowadzić dalsze analizy wolnych zapytań. Poniżej przedstawiono kilka przykładowych zapytań, które pomogą Ci rozpocząć pracę. Pamiętaj o zaktualizowaniu poniższej nazwy serwera.

- Zapytania dłuższe niż 10 sekund na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Wyświetlanie 5 najważniejszych zapytań na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Podsumuj wolne zapytania według minimalnej, maksymalnej, średniej i odchylenia standardowego czasu zapytania na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Tworzenie grafu wolnej dystrybucji zapytań na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Wyświetlaj zapytania dłużej niż 10 sekund na wszystkich serwerach MySQL z włączonymi dziennikami diagnostycznymi

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md)
- Konfigurowanie wolnych dzienników zapytań z [Azure Portal](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->