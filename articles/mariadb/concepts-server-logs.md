---
title: Dzienniki wolnych zapytań — Azure Database for MariaDB
description: Opisuje dzienniki dostępne w Azure Database for MariaDB i dostępne parametry dotyczące włączania różnych poziomów rejestrowania.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: 2e7e56616300566839fadef762c2165c8d989e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570803"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Wolne dzienniki zapytań w Azure Database for MariaDB
W Azure Database for MariaDB dziennik wolnych zapytań jest dostępny dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwany. Dziennik wolnych zapytań może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów.

Aby uzyskać więcej informacji na temat długiego dziennika zapytań, zobacz dokumentację MariaDB dla [wolnego dziennika zapytań](https://mariadb.com/kb/en/library/slow-query-log-overview/).

Gdy [Magazyn zapytań](concepts-query-store.md) jest włączony na serwerze, mogą pojawić się zapytania takie jak " `CALL mysql.az_procedure_collect_wait_stats (900, 30);` " zarejestrowane w dziennikach wolnych zapytań. To zachowanie jest oczekiwane, ponieważ funkcja magazynu zapytań zbiera dane statystyczne dotyczące zapytań. 

## <a name="configure-slow-query-logging"></a>Skonfiguruj rejestrowanie wolnych zapytań
Domyślnie dziennik wolnych zapytań jest wyłączony. Aby włączyć tę opcję, ustaw wartość `slow_query_log` na na. Tę funkcję można włączyć za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure. 

Inne parametry, które można dostosować, obejmują:

- **long_query_time**: Jeśli zapytanie trwa dłużej niż long_query_time (w sekundach), rejestrowane jest zapytanie. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: Jeśli on zawiera instrukcje administracyjne, takie jak ALTER_TABLE i ANALYZE_TABLE w instrukcjach zapisanych do slow_query_log.
- **log_queries_not_using_indexes**: określa, czy zapytania, które nie używają indeksów są rejestrowane w slow_query_log
- **log_throttle_queries_not_using_indexes**: ten parametr ogranicza liczbę zapytań, które nie są indeksami, które można zapisać w dzienniku wolnych zapytań. Ten parametr zacznie obowiązywać, gdy log_queries_not_using_indexes jest ustawiona na wartość włączone.
- **log_output**: Jeśli "plik" umożliwia zapisanie dziennika wolnych zapytań do lokalnego magazynu serwera i Azure monitor dzienników diagnostycznych. Jeśli jest to opcja „None”, dziennik wolnych zapytań zostanie zapisany wyłącznie w dziennikach diagnostycznych usługi Azure Monitor. 

> [!IMPORTANT]
> Jeśli tabele nie są indeksowane, ustawienie `log_queries_not_using_indexes` parametrów i na wartość `log_throttle_queries_not_using_indexes` on może mieć wpływ na wydajność MariaDB, ponieważ wszystkie zapytania uruchomione względem tych nieindeksowanych tabel zostaną zazapisywane w dzienniku wolnych zapytań.<br><br>
> Jeśli planujesz rejestrowanie wolnych zapytań przez dłuższy czas, zaleca się ustawienie wartości `log_output` "none" (brak). W przypadku wybrania opcji "plik" te dzienniki są zapisywane w magazynie serwera lokalnego i mogą mieć wpływ na wydajność MariaDB. 

Zapoznaj się z [dokumentacją dziennika wolnych zapytań](https://mariadb.com/kb/en/library/slow-query-log-overview/) MariaDB w celu uzyskania pełnych opisów parametrów dziennika wolnych zapytań.

## <a name="access-slow-query-logs"></a>Dostęp do dzienników wolnych zapytań
Dostępne są dwie opcje uzyskiwania dostępu do dzienników wolnych zapytań w Azure Database for MariaDB: lokalnego magazynu serwera lub Azure Monitor dzienników diagnostycznych. Ta wartość jest ustawiana za pomocą `log_output` parametru.

W przypadku magazynu serwera lokalnego można wyświetlić i pobrać wolne dzienniki zapytań przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure. W Azure Portal przejdź do serwera w Azure Portal. W obszarze nagłówek **monitorowania** wybierz stronę **Dzienniki serwera** . Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie i dostęp do dzienników serwera przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-cli.md). 

Azure Monitor dzienników diagnostycznych umożliwia potoki dzienników wolnych zapytań do Azure Monitor dzienników (Log Analytics), Azure Storage lub Event Hubs. Aby uzyskać więcej informacji, zobacz [poniżej](concepts-server-logs.md#diagnostic-logs) .

## <a name="local-server-storage-log-retention"></a>Przechowywanie dzienników magazynu serwera lokalnego
Po zarejestrowaniu do magazynu lokalnego na serwerze dzienniki są dostępne przez maksymalnie siedem dni od ich utworzenia. Jeśli całkowity rozmiar dostępnych dzienników przekracza 7 GB, najstarsze pliki zostaną usunięte do momentu udostępnienia miejsca.

Dzienniki są obracane co 24 godziny lub 7 GB, w zależności od tego, co nastąpi wcześniej.

> [!Note]
> Powyższe przechowywanie dzienników nie dotyczy dzienników, które są potokowe przy użyciu Azure Monitor dzienników diagnostycznych. Można zmienić okres przechowywania dla ujścia danych emitowanych do (np. Azure Storage).

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Azure Database for MariaDB jest zintegrowana z Azure Monitor dzienników diagnostycznych. Po włączeniu wolnych dzienników zapytań na serwerze MariaDB można je wyemitować do dzienników Azure Monitor, Event Hubs lub Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych, zobacz sekcję jak [znaleźć w dokumentacji dzienników diagnostycznych](../azure-monitor/essentials/platform-logs-overview.md).

W poniższej tabeli opisano zawartość poszczególnych dzienników. W zależności od metody Output pola uwzględnione i kolejność ich wyświetlania mogą się różnić.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Stałego `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Stałego `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nazwa serwera |
| `start_time_t` UTC | Godzina rozpoczęcia zapytania |
| `query_time_s` | Łączny czas wykonywania zapytania |
| `lock_time_s` | Łączny czas zablokowania zapytania |
| `user_host_s` | Nazwa użytkownika |
| `rows_sent_s` | Liczba wysłanych wierszy |
| `rows_examined_s` | Liczba badanych wierszy |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Wstaw identyfikator |
| `sql_text_s` | Pełne zapytanie |
| `server_id_s` | Identyfikator serwera |
| `thread_id_s` | Identyfikator wątku |
| `\_ResourceId` | Identyfikator URI zasobu |

> [!Note]
> W przypadku programu `sql_text` Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

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

- Wyświetlaj zapytania dłużej niż 10 sekund na wszystkich serwerach MariaDB z włączonymi dziennikami diagnostycznymi

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować dziennik wolnych zapytań z Azure Portal](howto-configure-server-logs-portal.md)
- [Jak konfigurować dzienniki wolnych zapytań z poziomu interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-cli.md)
