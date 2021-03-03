---
title: Dzienniki inspekcji — Azure Database for MySQL — elastyczny serwer
description: Zawiera opis dzienników inspekcji dostępnych w Azure Database for MySQL elastycznym serwerze.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 1232a0753c988f5a28ebba28f9819aa67ce28603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718747"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Śledź aktywność bazy danych za pomocą dzienników inspekcji w Azure Database for MySQL elastycznym serwerze

> [!IMPORTANT] 
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

Azure Database for MySQL elastyczny serwer zapewnia użytkownikom możliwość konfigurowania dzienników inspekcji. Dzienniki inspekcji mogą służyć do śledzenia aktywności na poziomie bazy danych, w tym połączeń, administratorów, DDL i zdarzeń DML. Te typy dzienników są często używane na potrzeby zapewniania zgodności.

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

>[!IMPORTANT]
> Zaleca się tylko rejestrowanie typów zdarzeń i użytkowników wymaganych do celów inspekcji, aby upewnić się, że wydajność serwera nie jest w dużym stopniu zagrożona.

Domyślnie dzienniki inspekcji są wyłączone. Aby je włączyć, ustaw `audit_log_enabled` parametr serwer na wartość *włączone*. Tę konfigurację można skonfigurować przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure <!-- add link to server parameter-->. 

Inne parametry, które można dostosować do kontroli inspekcji, obejmują:

- `audit_log_events`: kontroluje zdarzenia, które mają być rejestrowane. Szczegółowe zdarzenia inspekcji można znaleźć w poniższej tabeli.
- `audit_log_include_users`: Użytkownicy programu MySQL mają być włączeni do rejestrowania. Wartość domyślna tego parametru jest pusta, co spowoduje uwzględnienie wszystkich użytkowników do rejestrowania. Ma wyższy priorytet niż `audit_log_exclude_users` . Maksymalna długość parametru to 512 znaków.
- `audit_log_exclude_users`: Użytkownicy programu MySQL mają być wykluczeni z rejestrowania. Maksymalna długość parametru to 512 znaków.

> [!NOTE]
> `audit_log_include_users` ma wyższy priorytet niż `audit_log_exclude_users` . Na przykład jeśli `audit_log_include_users`  =  `demouser` i `audit_log_exclude_users`  =  `demouser` , użytkownik zostanie uwzględniony w dziennikach inspekcji, ponieważ `audit_log_include_users` ma wyższy priorytet.

| **Zdarzenie** | **Opis** |
|---|---|
| `CONNECTION` | -Inicjacja połączenia (powodzenie lub niepowodzenie) <br> — Ponowne uwierzytelnianie użytkownika przy użyciu innego użytkownika/hasła podczas sesji <br> -Zakończenie połączenia |
| `DML_SELECT`| Wybieranie zapytań |
| `DML_NONSELECT` | Wstawianie/usuwanie/aktualizowanie zapytań |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Zapytania, takie jak "DROP DATABASE" |
| `DCL` | Zapytania, takie jak "Udziel uprawnień" |
| `ADMIN` | Zapytania, takie jak "Pokaż stan" |
| `GENERAL` | Wszystkie w DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administrator |
| `TABLE_ACCESS` | — Instrukcje odczytu tabeli, takie jak SELECT lub INSERT INTO... ZAZNACZENIA <br> -Instrukcji DELETE tabeli, takich jak DELETE lub TRUNCATE TABLE <br> — Instrukcje INSERT tabeli, takie jak INSERT lub REPLACE <br> — Instrukcje aktualizacji tabeli, takie jak aktualizacja |

## <a name="access-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji

Dzienniki inspekcji są zintegrowane z ustawieniami diagnostycznymi Azure Monitor. Po włączeniu dzienników inspekcji na serwerze elastycznym MySQL można je emitować do Azure Monitor dzienników, Event Hubs lub Azure Storage. Aby dowiedzieć się więcej na temat ustawień diagnostycznych, zobacz [dokumentację dzienników diagnostycznych](../../azure-monitor/essentials/platform-logs-overview.md). Aby dowiedzieć się więcej na temat włączania ustawień diagnostycznych w Azure Portal, zobacz [artykuł Portal dziennika inspekcji](how-to-configure-audit-logs-portal.md#set-up-diagnostics).

W poniższych sekcjach opisano dane wyjściowe dzienników inspekcji programu MySQL na podstawie typu zdarzenia. W zależności od metody Output pola uwzględnione i kolejność ich wyświetlania mogą się różnić.

### <a name="connection"></a>Połączenie

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Stałego `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Stałego `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` |
| `connection_id_d` | Unikatowy identyfikator połączenia wygenerowany przez MySQL |
| `host_s` | Pusty |
| `ip_s` | Adres IP klienta nawiązującego połączenie z bazą danych MySQL |
| `user_s` | Nazwa użytkownika wykonującego zapytanie |
| `db_s` | Nazwa bazy danych połączonej z |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="general"></a>Ogólne

Poniższy schemat dotyczy typów zdarzeń GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administrator.

> [!NOTE]
> W przypadku programu `sql_text_s` Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Stałego `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Stałego `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (dostępne tylko dla MySQL 5,6) |
| `event_time` | Godzina rozpoczęcia zapytania w sygnaturze czasowej UTC |
| `error_code_d` | Kod błędu, jeśli zapytanie nie powiodło się. `0` oznacza brak błędu |
| `thread_id_d` | Identyfikator wątku, który wykonał zapytanie |
| `host_s` | Pusty |
| `ip_s` | Adres IP klienta nawiązującego połączenie z bazą danych MySQL |
| `user_s` | Nazwa użytkownika wykonującego zapytanie |
| `sql_text_s` | Pełny tekst zapytania |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="table-access"></a>Dostęp do tabeli

> [!NOTE]
> W przypadku programu `sql_text_s` Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Stałego `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Stałego `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` , `UPDATE` lub `DELETE` |
| `connection_id_d` | Unikatowy identyfikator połączenia wygenerowany przez MySQL |
| `db_s` | Nazwa bazy danych, do której uzyskano dostęp |
| `table_s` | Nazwa tabeli, do której można uzyskać dostęp |
| `sql_text_s` | Pełny tekst zapytania |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizowanie dzienników w dziennikach Azure Monitor

Po przeprowadzeniu potoku dzienników inspekcji do Azure Monitor dzienników za poorednictwem dzienników diagnostycznych można przeprowadzić dalsze analizy zdarzeń inspekcji. Poniżej przedstawiono kilka przykładowych zapytań, które pomogą Ci rozpocząć pracę. Pamiętaj o zaktualizowaniu poniższej nazwy serwera.

- Wyświetl listę ogólnych zdarzeń na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Wyświetlanie listy zdarzeń połączeń na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Podsumuj zdarzenia inspekcji na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Tworzenie grafu dystrybucji typów zdarzeń inspekcji na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Wyświetl listę zdarzeń inspekcji na wszystkich serwerach MySQL z włączonymi dziennikami diagnostycznymi dla dzienników inspekcji

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [wolnych dziennikach zapytań](concepts-slow-query-logs.md)
- Konfigurowanie dzienników zapytań inspekcji z [Azure Portal](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->
