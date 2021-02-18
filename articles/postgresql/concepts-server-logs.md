---
title: Dzienniki — Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje konfigurację rejestrowania, magazyn i analizę w Azure Database for PostgreSQL-pojedynczym serwerze
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 621d5a6a91a8c22c52e6febc7c2638571f5bf113
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595807"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Dzienniki w Azure Database for PostgreSQL — pojedynczy serwer

Azure Database for PostgreSQL umożliwia konfigurowanie i dostęp do standardowych dzienników Postgres. Dzienniki mogą służyć do identyfikowania, rozwiązywania problemów i naprawiania błędów konfiguracji oraz nieoptymalnej wydajności. Rejestrowane informacje można skonfigurować i uzyskać do nich dostęp, w tym błędy, informacje o zapytaniach, rekordy autopróżniowe, połączenia i punkty kontrolne. (Dostęp do dzienników transakcji nie jest dostępny).

Rejestrowanie inspekcji jest udostępniane za pomocą rozszerzenia Postgres pgaudit. Aby dowiedzieć się więcej, odwiedź artykuł dotyczący [pojęć związanych z inspekcją](concepts-audit.md) .


## <a name="configure-logging"></a>Konfigurowanie rejestrowania 
W celu skonfigurowania rejestrowania standardowego Postgres na serwerze możesz użyć parametrów serwera rejestrowania. Na każdym serwerze Azure Database for PostgreSQL `log_checkpoints` i `log_connections` są domyślnie włączone. Istnieją dodatkowe parametry, które można dostosować, aby odpowiadały potrzebom rejestrowania: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Azure Database for PostgreSQL — parametry rejestrowania":::

Aby dowiedzieć się więcej na temat parametrów dziennika Postgres, zapoznaj się z sekcją [kiedy rejestrować](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) i [co należy rejestrować](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) w dokumentacji Postgres. W Azure Database for PostgreSQL są dostępne większość parametrów rejestrowania Postgres.

Aby dowiedzieć się, jak skonfigurować parametry w Azure Database for PostgreSQL, zapoznaj się z [dokumentacją portalu](howto-configure-server-parameters-using-portal.md) lub [dokumentacją interfejsu wiersza polecenia](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Skonfigurowanie dużej liczby dzienników na przykład rejestrowania instrukcji może zwiększyć znaczne obciążenie wydajności. 

## <a name="access-log-files"></a>Dostęp do plików dziennika
Domyślny format dziennika w Azure Database for PostgreSQL to. log. Przykładowa linia z tego dziennika wygląda następująco:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL zapewnia krótkoterminową lokalizację przechowywania plików. log. Nowy plik zaczyna się co 1 godzina lub 100 MB, w zależności od tego, co nastąpi wcześniej. Dzienniki są dołączane do bieżącego pliku, ponieważ są emitowane z Postgres.  

Można ustawić okres przechowywania dla tego krótkoterminowego magazynu dzienników przy użyciu `log_retention_period` parametru. Wartość domyślna to 3 dni, a maksymalna to 7 dni. Krótkoterminowa lokalizacja przechowywania może zawierać maksymalnie 1 GB plików dziennika. Po 1 GB najstarsze pliki, niezależnie od okresu przechowywania, zostaną usunięte, aby zwolnić miejsce na nowe dzienniki. 

Aby okresowo przechowywać dzienniki i analizę dzienników, można pobrać pliki dziennika. log i przenieść je do usługi innej firmy. Pliki można pobrać przy użyciu [Azure Portal](howto-configure-server-logs-in-portal.md), [interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md). Alternatywnie można skonfigurować ustawienia diagnostyczne Azure Monitor, które automatycznie emitują dzienniki (w formacie JSON) do dłuższych lokalizacji. Więcej informacji na temat tej opcji można znaleźć w sekcji poniżej. 

Można zatrzymać generowanie plików. log, ustawiając parametr `logging_collector` na off. Wyłączenie generowania plików dziennika jest zalecane, jeśli używasz ustawień diagnostycznych Azure Monitor. Ta konfiguracja zmniejszy wpływ dodatkowego rejestrowania na wydajność.

## <a name="resource-logs"></a>Dzienniki zasobów

Azure Database for PostgreSQL jest zintegrowany z Azure Monitor ustawień diagnostycznych. Ustawienia diagnostyczne umożliwiają wysyłanie dzienników Postgres w formacie JSON do Azure Monitor dzienników na potrzeby analiz i alertów, Event Hubs do przesyłania strumieniowego i usługi Azure Storage w celu archiwizacji. 

> [!IMPORTANT]
> Ta funkcja diagnostyczna dla dzienników serwera jest dostępna tylko w [warstwach cenowych](concepts-pricing-tiers.md)ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.


### <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Ustawienia diagnostyczne dla serwera Postgres można włączyć za pomocą Azure Portal, interfejsu wiersza polecenia i środowiska API REST oraz programu PowerShell. Kategoria dziennika do wybrania to **PostgreSQLLogs**. (Istnieją inne dzienniki, które można skonfigurować w przypadku korzystania z [magazynu zapytań](concepts-query-store.md)).

Aby włączyć dzienniki zasobów przy użyciu Azure Portal:

   1. W portalu przejdź do pozycji *Ustawienia diagnostyczne* w menu nawigacji serwera Postgres.
   2. Wybierz pozycję *Dodaj ustawienie diagnostyczne*.
   3. Nadaj nazwę temu ustawieniu. 
   4. Wybierz preferowany punkt końcowy (konto magazynu, centrum zdarzeń, Analiza dzienników). 
   5. Wybierz typ dziennika **PostgreSQLLogs**.
   7. Zapisz ustawienie.

Aby włączyć dzienniki zasobów przy użyciu programu PowerShell lub interfejsu wiersza polecenia, przejdź do artykułu [ustawień diagnostycznych](../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Dostęp do dzienników zasobów

Sposób dostępu do dzienników zależy od wybranego punktu końcowego. W przypadku usługi Azure Storage zapoznaj się z artykułem [Logs (dzienniki konta magazynu](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) ). Aby uzyskać Event Hubs, zobacz artykuł [przesyłanie strumieniowe dzienników platformy Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

W przypadku dzienników Azure Monitor dzienniki są wysyłane do wybranego obszaru roboczego. Dzienniki Postgres używają trybu zbierania **AzureDiagnostics** , dzięki czemu można wykonywać zapytania z tabeli AzureDiagnostics. Pola w tabeli są opisane poniżej. Więcej informacji o wysyłaniu zapytań i alertach znajduje się w temacie Omówienie [zapytań dotyczących dzienników Azure monitor](../azure-monitor/logs/log-query-overview.md) .

Poniżej przedstawiono zapytania, w których można spróbować rozpocząć pracę. Alerty można skonfigurować na podstawie zapytań.

Wyszukaj wszystkie dzienniki Postgres dla określonego serwera w ostatnim dniu
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Wyszukaj wszystkie próby połączenia niebędące hostem lokalnym
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
W powyższym zapytaniu zostaną wyświetlone wyniki w ciągu ostatnich 6 godzin dla dowolnego rejestrowania serwera Postgres w tym obszarze roboczym.

### <a name="log-format"></a>Format dziennika

W poniższej tabeli opisano pola dla typu **PostgreSQLLogs** . W zależności od wybranego wyjściowego punktu końcowego pola zawarte i kolejność ich wyświetlania mogą się różnić. 

|**Pole** | **Opis** |
|---|---|
| TenantId | Identyfikator dzierżawy |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| Typ | Typ dziennika. Stałego `AzureDiagnostics` |
| SubscriptionId | Identyfikator GUID subskrypcji, do której należy serwer |
| ResourceGroup | Nazwa grupy zasobów, do której należy serwer |
| ResourceProvider | Nazwa dostawcy zasobów. Stałego `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identyfikator URI zasobu |
| Zasób | Nazwa serwera |
| Kategoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Zmienna | Poziom rejestrowania, przykład: LOG, błąd, Uwaga |
| Komunikat | Podstawowy komunikat dziennika | 
| Domena | Wersja serwera, przykład: Postgres-10 |
| Szczegóły | Dodatkowy komunikat dziennika (jeśli dotyczy) |
| nazwa_kolumny | Nazwa kolumny (jeśli dotyczy) |
| SchemaName | Nazwa schematu (jeśli dotyczy) |
| Typ danych | Nazwa typu danych (jeśli dotyczy) |
| LogicalServerName | Nazwa serwera | 
| _ResourceId | Identyfikator URI zasobu |
| Prefiks | Prefiks wiersza dziennika |


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o uzyskiwaniu dostępu do dzienników z [Azure Portal](howto-configure-server-logs-in-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md).
- Dowiedz się więcej o [cenach Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit.md)