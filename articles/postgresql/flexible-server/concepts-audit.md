---
title: Rejestrowanie inspekcji — serwer elastyczny Azure Database for PostgreSQL
description: Pojęcia dotyczące rejestrowania inspekcji pgAudit na serwerze elastycznym Azure Database for PostgreSQL.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b344e2a845a9da8333860599bd4ff9041108202f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588264"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Rejestrowanie inspekcji na serwerze elastycznym Azure Database for PostgreSQL

Rejestrowanie inspekcji działań bazy danych w Azure Database for PostgreSQL-elastycznym serwerze jest dostępne za pomocą rozszerzenia inspekcji PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit zawiera szczegółowe informacje o rejestrowaniu sesji i/lub inspekcji obiektów.

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Jeśli chcesz, aby w dziennikach na poziomie zasobów platformy Azure były wykonywane operacje, takie jak skalowanie obliczeniowe i magazynowe, zobacz [Dziennik aktywności platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia
Domyślnie instrukcje rejestrowania rozszerzenia pgAudit są emitowane wraz z normalnymi instrukcjami rejestrowania przy użyciu standardowej funkcji rejestrowania bazy danych Postgres. Na serwerze elastycznym Azure Database for PostgreSQL można skonfigurować wszystkie dzienniki do wysłania do magazynu dzienników Azure Monitor w celu późniejszej analizy w Log Analytics. Po włączeniu rejestrowania zasobów Azure Monitor dzienniki będą automatycznie wysyłane (w formacie JSON) do usługi Azure Storage, Event Hubs i/lub dzienników Azure Monitor, w zależności od wybranej opcji.

Aby dowiedzieć się, jak skonfigurować rejestrowanie do usługi Azure Storage, Event Hubs lub dzienników Azure Monitor, odwiedź sekcję dzienniki zasobów w [artykule Dzienniki serwera](concepts-logging.md).

## <a name="enabling-pgaudit"></a>Włączanie pgAudit

Aby włączyć pgAudit, musisz nawiązać połączenie z serwerem za pomocą klienta (na przykład PSQL) i włączyć rozszerzenie pgAudit, uruchamiając następujące polecenie:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>Ustawienia pgAudit

pgAudit umożliwia skonfigurowanie rejestrowania inspekcji sesji lub obiektu. [Rejestrowanie inspekcji sesji](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emituje szczegółowe dzienniki wykonanych instrukcji. [Rejestrowanie inspekcji obiektów](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) podlega inspekcji w zakresie określonych relacji. Istnieje możliwość skonfigurowania jednego lub obu typów rejestrowania. 

> [!NOTE]
> Ustawienia pgAudit są określane globalnie i nie można ich określić na poziomie bazy danych lub roli.

Po [włączeniu pgAudit](#enabling-pgaudit)można skonfigurować jego parametry, aby rozpocząć rejestrowanie. [Dokumentacja pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) zawiera definicje każdego z parametrów. Najpierw Przetestuj parametry i upewnij się, że otrzymujesz oczekiwane zachowanie.

> [!NOTE]
> Ustawienie na `pgaudit.log_client` włączone spowoduje przekierowanie dzienników do procesu klienta (na przykład PSQL) zamiast zapisywania do pliku. To ustawienie powinno być zwykle wyłączone. <br> <br>
> `pgaudit.log_level` jest włączona tylko wtedy, gdy `pgaudit.log_client` jest włączona.

> [!NOTE]
> W Azure Database for PostgreSQL-elastycznym serwerze `pgaudit.log` nie można ustawić za pomocą `-` skrótu podpisywania (minus), zgodnie z opisem w dokumentacji pgAudit. Wszystkie wymagane klasy instrukcji (odczyt, zapis itp.) powinny być określone indywidualnie.

## <a name="audit-log-format"></a>Format dziennika inspekcji
Każdy wpis inspekcji jest wskazywany przez `AUDIT:` blisko początku wiersza dziennika. Format reszty wpisu jest szczegółowo opisany w [dokumentacji pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Wprowadzenie
Aby szybko rozpocząć pracę, ustaw `pgaudit.log` wartość `WRITE` i Otwórz Dzienniki serwera, aby przejrzeć dane wyjściowe. 

## <a name="viewing-audit-logs"></a>Wyświetlanie dzienników inspekcji
Sposób dostępu do dzienników zależy od wybranego punktu końcowego. W przypadku usługi Azure Storage zapoznaj się z artykułem [Logs (dzienniki konta magazynu](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) ). Aby uzyskać Event Hubs, zobacz artykuł [przesyłanie strumieniowe dzienników platformy Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

W przypadku dzienników Azure Monitor dzienniki są wysyłane do wybranego obszaru roboczego. Dzienniki Postgres używają trybu zbierania **AzureDiagnostics** , dzięki czemu można wykonywać zapytania z tabeli AzureDiagnostics. Pola w tabeli są opisane poniżej. Więcej informacji o wysyłaniu zapytań i alertach znajduje się w temacie Omówienie [zapytań dotyczących dzienników Azure monitor](../../azure-monitor/logs/log-query-overview.md) .

Możesz użyć tego zapytania, aby rozpocząć. Alerty można skonfigurować na podstawie zapytań.

Wyszukaj wszystkie wpisy pgAudit w dziennikach Postgres dla określonego serwera w ostatnim dniu
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Następne kroki
- [Informacje o rejestrowaniu w Azure Database for PostgreSQL-elastycznym serwerze](concepts-logging.md)
- [Dowiedz się, jak skonfigurować rejestrowanie na serwerze elastycznym Azure Database for PostgreSQL i jak uzyskać dostęp do dzienników](howto-configure-and-access-logs.md)