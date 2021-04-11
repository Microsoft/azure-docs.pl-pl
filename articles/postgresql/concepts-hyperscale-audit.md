---
title: Rejestrowanie inspekcji — Azure Database for PostgreSQL-ze skalowaniem (Citus)
description: Pojęcia dotyczące rejestrowania inspekcji pgAudit w Azure Database for PostgreSQL-(Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 8a36062a2d29bcec10279d73211526a0dcba619e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702117"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Rejestrowanie inspekcji w Azure Database for PostgreSQL-Citus

Rejestrowanie inspekcji działań bazy danych w Azure Database for PostgreSQL-Citus) jest dostępne za pomocą rozszerzenia inspekcji PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit zawiera szczegółowe informacje o rejestrowaniu sesji i/lub inspekcji obiektów.

> [!IMPORTANT]
> pgAudit jest w wersji zapoznawczej na Azure Database for PostgreSQL-(Citus)

Jeśli chcesz, aby w dziennikach na poziomie zasobów platformy Azure były wykonywane operacje, takie jak skalowanie obliczeniowe i magazynowe, zobacz [Dziennik aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia
Domyślnie instrukcje rejestrowania rozszerzenia pgAudit są emitowane wraz z normalnymi instrukcjami rejestrowania przy użyciu standardowej funkcji rejestrowania bazy danych Postgres. W Azure Database for PostgreSQL-Citus można skonfigurować wszystkie dzienniki do wysłania do magazynu dzienników Azure Monitor w celu późniejszej analizy w Log Analytics. Po włączeniu rejestrowania zasobów Azure Monitor dzienniki będą automatycznie wysyłane (w formacie JSON) do usługi Azure Storage, Event Hubs i/lub dzienników Azure Monitor, w zależności od wybranej opcji.

## <a name="enabling-pgaudit"></a>Włączanie pgAudit

Rozszerzenie pgAudit jest wstępnie zainstalowane i włączone na wszystkich węzłach grupy serwerów ze wszystkimi skalami (Citus). Nie jest wymagane żadne działanie, aby je włączyć.

## <a name="pgaudit-settings"></a>Ustawienia pgAudit

pgAudit umożliwia skonfigurowanie rejestrowania inspekcji sesji lub obiektu. [Rejestrowanie inspekcji sesji](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emituje szczegółowe dzienniki wykonanych instrukcji. [Rejestrowanie inspekcji obiektów](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) podlega inspekcji w zakresie określonych relacji. Istnieje możliwość skonfigurowania jednego lub obu typów rejestrowania. 

> [!NOTE]
> Ustawienia pgAudit są określane globalnie i nie można ich określić na poziomie bazy danych lub roli.
>
> Ponadto ustawienia pgAudit są określone dla każdego węzła w grupie serwerów. Aby wprowadzić zmiany we wszystkich węzłach, należy zastosować je do każdego węzła osobno.

Aby rozpocząć rejestrowanie, należy skonfigurować parametry pgAudit. [Dokumentacja pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) zawiera definicje każdego z parametrów. Najpierw Przetestuj parametry i upewnij się, że otrzymujesz oczekiwane zachowanie.

> [!NOTE]
> Ustawienie na `pgaudit.log_client` włączone spowoduje przekierowanie dzienników do procesu klienta (na przykład PSQL) zamiast zapisywania do pliku. To ustawienie powinno być zwykle wyłączone. <br> <br>
> Parametr `pgaudit.log_level` jest włączony tylko wtedy, gdy parametr `pgaudit.log_client` jest ustawiony jako włączony.

> [!NOTE]
> W Azure Database for PostgreSQL-Citus) `pgaudit.log` nie można ustawić za pomocą `-` skrótu podpisywania (minus), zgodnie z opisem w dokumentacji pgAudit. Wszystkie wymagane klasy instrukcji (odczyt, zapis itp.) powinny być określone indywidualnie.

## <a name="audit-log-format"></a>Format dziennika inspekcji
Każdy wpis inspekcji jest wskazywany przez `AUDIT:` blisko początku wiersza dziennika. Format reszty wpisu jest szczegółowo opisany w [dokumentacji pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Wprowadzenie
Aby szybko rozpocząć pracę, ustaw `pgaudit.log` wartość `WRITE` i Otwórz Dzienniki serwera, aby przejrzeć dane wyjściowe. 

## <a name="viewing-audit-logs"></a>Wyświetlanie dzienników inspekcji
Sposób dostępu do dzienników zależy od wybranego punktu końcowego. W przypadku usługi Azure Storage zapoznaj się z artykułem [Logs (dzienniki konta magazynu](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) ). Aby uzyskać Event Hubs, zobacz artykuł [przesyłanie strumieniowe dzienników platformy Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

W przypadku dzienników Azure Monitor dzienniki są wysyłane do wybranego obszaru roboczego. Dzienniki Postgres używają trybu zbierania **AzureDiagnostics** , dzięki czemu można wykonywać zapytania z tabeli AzureDiagnostics. Pola w tabeli są opisane poniżej. Więcej informacji o wysyłaniu zapytań i alertach znajduje się w temacie Omówienie [zapytań dotyczących dzienników Azure monitor](../azure-monitor/logs/log-query-overview.md) .

Możesz użyć tego zapytania, aby rozpocząć. Alerty można skonfigurować na podstawie zapytań.

Wyszukaj wszystkie wpisy pgAudit w dziennikach Postgres dla określonego serwera w ostatnim dniu
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak skonfigurować rejestrowanie w Azure Database for PostgreSQL-Citus i jak uzyskać dostęp do dzienników](howto-hyperscale-logging.md)