---
title: Konfigurowanie i dostęp do dzienników — elastyczny serwer Azure Database for PostgreSQL
description: Jak uzyskać dostęp do dzienników bazy danych dla Azure Database for PostgreSQL-elastyczny serwer
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e52f0f22065d89788d08659476d14af0351cc493
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590590"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Dzienniki PostgreSQL są dostępne w każdym węźle elastycznego serwera. Dzienniki można przesłać na serwer magazynu lub do usługi analizy. Dzienniki mogą służyć do identyfikowania, rozwiązywania problemów i naprawiania błędów konfiguracji oraz nieoptymalnej wydajności.

## <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Ustawienia diagnostyczne dla serwera Postgres można włączyć za pomocą Azure Portal, interfejsu wiersza polecenia i środowiska API REST oraz programu PowerShell. Kategoria dziennika do wybrania to **PostgreSQLLogs**.

Aby włączyć dzienniki zasobów przy użyciu Azure Portal:

1. W portalu przejdź do pozycji *Ustawienia diagnostyczne* w menu nawigacji serwera Postgres.
   
2. Wybierz pozycję *Dodaj ustawienie diagnostyczne*.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Przycisk dodawania ustawień diagnostycznych":::

3. Nadaj nazwę temu ustawieniu. 

4. Wybierz preferowany punkt końcowy (konto magazynu, centrum zdarzeń, Analiza dzienników). 

5. Wybierz typ dziennika **PostgreSQLLogs**.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="Wybieranie dzienników PostgreSQL":::

7. Zapisz ustawienie.

Aby włączyć dzienniki zasobów przy użyciu programu PowerShell lub interfejsu wiersza polecenia, przejdź do artykułu [ustawień diagnostycznych](../../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Dostęp do dzienników zasobów

Sposób dostępu do dzienników zależy od wybranego punktu końcowego. W przypadku usługi Azure Storage zapoznaj się z artykułem [Logs (dzienniki konta magazynu](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) ). Aby uzyskać Event Hubs, zobacz artykuł [przesyłanie strumieniowe dzienników platformy Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

W przypadku dzienników Azure Monitor dzienniki są wysyłane do wybranego obszaru roboczego. Dzienniki Postgres używają trybu zbierania **AzureDiagnostics** , dzięki czemu można wykonywać zapytania z tabeli AzureDiagnostics. Pola w tabeli są opisane poniżej. Więcej informacji o wysyłaniu zapytań i alertach znajduje się w temacie Omówienie [zapytań dotyczących dzienników Azure monitor](../../azure-monitor/logs/log-query-overview.md) .

Poniżej przedstawiono zapytania, w których można spróbować rozpocząć pracę. Alerty można skonfigurować na podstawie zapytań.

Wyszukaj wszystkie dzienniki Postgres dla określonego serwera w ostatnim dniu

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Wyszukaj wszystkie próby połączenia niebędące hostem lokalnym

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

W powyższym zapytaniu zostaną wyświetlone wyniki w ciągu ostatnich 6 godzin dla dowolnego rejestrowania serwera Postgres w tym obszarze roboczym.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do zapytań usługi log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)
- Informacje o [centrach zdarzeń platformy Azure](../../event-hubs/event-hubs-about.md)