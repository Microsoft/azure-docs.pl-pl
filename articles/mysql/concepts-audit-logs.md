---
title: Dzienniki inspekcji — Azure Database for MySQL
description: Opisuje dzienniki inspekcji dostępne w Azure Database for MySQL i dostępne parametry umożliwiające włączenie poziomów rejestrowania.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: fa845e7c402073a64f51d75b1da51f56142eee2e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630504"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Inspekcja dzienników w Azure Database for MySQL

W Azure Database for MySQL dziennik inspekcji jest dostępny dla użytkowników. Dziennik inspekcji może służyć do śledzenia aktywności na poziomie bazy danych i jest często używany w celu zapewnienia zgodności.

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

>[!IMPORTANT]
> Zaleca się tylko rejestrowanie typów zdarzeń i użytkowników wymaganych do celów inspekcji, aby upewnić się, że wydajność serwera nie jest w dużym stopniu zagrożona.

Domyślnie dziennik inspekcji jest wyłączony. Aby włączyć tę opcję, ustaw wartość `audit_log_enabled` na na.

Inne parametry, które można dostosować, obejmują:

- `audit_log_events`: kontroluje zdarzenia, które mają być rejestrowane. Szczegółowe zdarzenia inspekcji można znaleźć w poniższej tabeli.
- `audit_log_include_users`: Użytkownicy programu MySQL mają być włączeni do rejestrowania. Wartość domyślna tego parametru jest pusta, co spowoduje uwzględnienie wszystkich użytkowników do rejestrowania. Ma wyższy priorytet niż `audit_log_exclude_users` . Maksymalna długość parametru to 512 znaków.
- `audit_log_exclude_users`: Użytkownicy programu MySQL mają być wykluczeni z rejestrowania. Maksymalna długość parametru to 512 znaków.

> [!NOTE]
> `audit_log_include_users` ma wyższy priorytet niż `audit_log_exclude_users` . Na przykład jeśli `audit_log_include_users`  =  `demouser` i `audit_log_exclude_users`  =  `demouser` , użytkownik zostanie uwzględniony w dziennikach inspekcji, ponieważ `audit_log_include_users` ma wyższy priorytet.

| **Zdarzenie** | **Opis** |
|---|---|
| `CONNECTION` | -Inicjacja połączenia (powodzenie lub niepowodzenie) <br> -Uwierzytelnianie użytkownika przy użyciu innego użytkownika/hasła podczas sesji <br> -Zakończenie połączenia |
| `DML_SELECT`| Wybieranie zapytań |
| `DML_NONSELECT` | Wstawianie/usuwanie/aktualizowanie zapytań |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Zapytania, takie jak "DROP DATABASE" |
| `DCL` | Zapytania, takie jak "Udziel uprawnień" |
| `ADMIN` | Zapytania, takie jak "Pokaż stan" |
| `GENERAL` | Wszystkie w DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administrator |
| `TABLE_ACCESS` | -Dostępne dla MySQL 5,7 i MySQL 8,0 <br> — Instrukcje odczytu tabeli, takie jak SELECT lub INSERT INTO... ZAZNACZENIA <br> -Instrukcji DELETE tabeli, takich jak DELETE lub TRUNCATE TABLE <br> — Instrukcje INSERT tabeli, takie jak INSERT lub REPLACE <br> — Instrukcje aktualizacji tabeli, takie jak aktualizacja |

## <a name="access-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji

Dzienniki inspekcji są zintegrowane z dziennikami diagnostycznymi usługi Azure Monitor. Gdy włączysz dzienniki inspekcji na serwerze MySQL, możesz je emitować do dzienników usługi Azure Monitor, Event Hubs i Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych w Azure Portal, zobacz [artykuł Portal dziennika inspekcji](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schematy dzienników diagnostycznych

W poniższych sekcjach opisano dane wyjściowe dzienników inspekcji MySQL na podstawie typu zdarzenia. W zależności od metody Output pola uwzględnione i kolejność ich wyświetlania mogą się różnić.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (dostępne tylko dla MySQL 5,7) |
| `connection_id_d` | Unikatowy identyfikator połączenia wygenerowany przez MySQL |
| `host_s` | Pusty |
| `ip_s` | Adres IP klienta nawiązującego połączenie z bazą danych MySQL |
| `user_s` | Nazwa użytkownika wykonującego zapytanie |
| `db_s` | Nazwa bazy danych połączonej z |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="general"></a>Ogólne

Poniższy schemat dotyczy typów zdarzeń GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administrator.

> [!NOTE]
> W przypadku programu `sql_text` Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

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
> Dzienniki dostępu do tabel są wyprowadzane wyłącznie dla programu MySQL 5,7.<br>W przypadku programu `sql_text` Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

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

- [Jak skonfigurować dzienniki inspekcji w Azure Portal](howto-configure-audit-logs-portal.md)
