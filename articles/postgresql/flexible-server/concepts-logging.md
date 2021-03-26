---
title: Dzienniki — Azure Database for PostgreSQL — elastyczny serwer
description: Opisuje konfigurację rejestrowania, magazyn i analizę w Azure Database for PostgreSQL-elastycznym serwerze
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e09c01fcfb9c4725ac169151e85c8b030d8bb18c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606389"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Dzienniki na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Azure Database for PostgreSQL umożliwia konfigurowanie i dostęp do dzienników standardowych Postgres. Dzienniki mogą służyć do identyfikowania, rozwiązywania problemów i naprawiania błędów konfiguracji oraz nieoptymalnej wydajności. Rejestrowane informacje można skonfigurować i uzyskać do nich dostęp, w tym błędy, informacje o zapytaniach, rekordy autopróżniowe, połączenia i punkty kontrolne. (Dostęp do dzienników transakcji nie jest dostępny).

Rejestrowanie inspekcji jest udostępniane za pomocą rozszerzenia Postgres `pgaudit` . Aby dowiedzieć się więcej, odwiedź artykuł dotyczący [pojęć związanych z inspekcją](concepts-audit.md) .

## <a name="configure-logging"></a>Konfigurowanie rejestrowania

W celu skonfigurowania rejestrowania standardowego Postgres na serwerze możesz użyć parametrów serwera rejestrowania. Aby dowiedzieć się więcej na temat parametrów dziennika Postgres, zapoznaj się z sekcją [kiedy rejestrować](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) i [co należy rejestrować](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) w dokumentacji Postgres. W Azure Database for PostgreSQL są dostępne większość parametrów rejestrowania Postgres.

Aby dowiedzieć się, jak skonfigurować parametry w Azure Database for PostgreSQL, zapoznaj się z [dokumentacją portalu](howto-configure-server-parameters-using-portal.md) lub [dokumentacją interfejsu wiersza polecenia](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> Skonfigurowanie dużej liczby dzienników na przykład rejestrowania instrukcji może zwiększyć znaczne obciążenie wydajności. 

## <a name="accessing-logs"></a>Uzyskiwanie dostępu do dzienników

Azure Database for PostgreSQL jest zintegrowany z Azure Monitor ustawień diagnostycznych. Ustawienia diagnostyczne umożliwiają wysyłanie dzienników Postgres w formacie JSON do Azure Monitor dzienników na potrzeby analiz i alertów, Event Hubs do przesyłania strumieniowego i usługi Azure Storage w celu archiwizacji. 

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

- Dowiedz się więcej na temat sposobu [konfigurowania dzienników i uzyskiwania do nich dostępu](howto-configure-and-access-logs.md).
- Dowiedz się więcej o [cenach Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit.md)
