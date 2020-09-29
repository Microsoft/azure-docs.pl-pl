---
title: Konfigurowanie & zarządzanie odwołaniem do zawartości
description: Znajdź odwołanie do zawartości, która uczy się skonfigurować Azure SQL Databaseę i zarządzać nią.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 9b151e566f0cc3e086277c101a796e7dde059ef9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442570"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Konfigurowanie i zarządzanie odwołaniem do zawartości — Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera informacje dotyczące różnych przewodników, skryptów i wyjaśnień, które mogą pomóc w zarządzaniu i konfigurowaniu Azure SQL Database. 

## <a name="load-data"></a>Ładowanie danych

- [Migracja do bazy danych SQL](migrate-to-database-from-sql-server.md)
- Dowiedz się, jak [zarządzać SQL Database po migracji](manage-data-after-migrating-to-database.md).
- [Kopiowanie bazy danych](database-copy.md)
- [Importowanie bazy danych z pliku BACPAC](database-import.md)
- [Eksportowanie bazy danych do pliku BACPAC](database-export.md)
- [Ładowanie danych za pomocą narzędzia BCP](../load-from-csv-with-bcp.md)
- [Ładowanie danych za pomocą usługi ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Konfigurowanie uwierzytelniania Azure Active Directory (Azure AD)](authentication-aad-configure.md)
- [Konfigurowanie dostępu warunkowego](conditional-access-configure.md)
- [Uwierzytelnianie wieloskładnikowe w usłudze Azure AD](authentication-mfa-ssms-overview.md)
- [Konfigurowanie Multi-Factor Authentication](authentication-mfa-ssms-configure.md)
- [Konfigurowanie zasad przechowywania danych czasowych](temporal-tables-retention-policy.md)
- [Konfigurowanie szyfrowania TDE przy usługi BYOK](transparent-data-encryption-byok-configure.md)
- [Obracanie kluczy BYOK szyfrowania TDE](transparent-data-encryption-byok-key-rotation.md)
- [Usuwanie ochrony szyfrowania TDE](transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurowanie przetwarzania OLTP w pamięci](../in-memory-oltp-configure.md)
- [Konfigurowanie Azure Automation](automation-manage.md)
- [Skonfiguruj replikację transakcyjną](replication-to-sql-database.md) , aby replikować datę między bazami danych.
- [Skonfiguruj wykrywanie zagrożeń](threat-detection-configure.md) , aby umożliwić Azure SQL Database identyfikowanie podejrzanych działań, takich jak iniekcja SQL lub dostęp z podejrzanych lokalizacji.
- [Skonfiguruj Dynamiczne maskowanie danych](dynamic-data-masking-configure-portal.md) , aby chronić poufne dane.
- [Skonfiguruj przechowywanie kopii zapasowych](long-term-backup-retention-configure.md) bazy danych, aby zachować kopie zapasowe na platformie Azure Blob Storage. 
- [Skonfiguruj replikację geograficzną](active-geo-replication-overview.md) , aby zachować replikę bazy danych w innym regionie.
- [Skonfiguruj zabezpieczenia dla replik geograficznych](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Monitorowanie i dostrajanie bazy danych

- [Ręczne dostrajanie](performance-guidance.md)
- [Korzystanie z widoków DMV do monitorowania wydajności](monitoring-with-dmvs.md)
- [Korzystanie z magazynu zapytań do monitorowania wydajności](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Włącz automatyczne dostrajanie](automatic-tuning-enable.md) , aby umożliwić Azure SQL Database optymalizację wydajności obciążeń.
- [Włącz powiadomienia e-mail na potrzeby dostrajania automatycznego](automatic-tuning-email-notifications-configure.md) , aby uzyskać informacje o zaleceniach dostrajania.
- [Stosuj zalecenia dotyczące wydajności](database-advisor-find-recommendations-portal.md) i Optymalizuj bazę danych.
- [Utwórz alerty](alerts-insights-configure-portal.md) , aby otrzymywać powiadomienia z Azure SQL Database.
- [Rozwiązywanie problemów z](troubleshoot-common-errors-issues.md) łącznością, jeśli zauważysz problemy z łącznością między aplikacjami i bazą danych. Resource Health można także użyć [do problemów z łącznością](resource-health-to-troubleshoot-connectivity.md).
- [Rozwiązywanie problemów z wydajnością przy użyciu funkcji Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Zarządzaj miejscem w pliku](file-space-manage.md) , aby monitorować użycie magazynu w bazie danych programu.
- [Korzystanie z dziennika diagnostycznego funkcji Intelligent Insights](intelligent-insights-use-diagnostics-log.md)
- [Monitorowanie miejsca OLTP w pamięci](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Zdarzenia rozszerzone

- [Zdarzenia rozszerzone](xevent-db-diff-from-svr.md)
- [Przechowuj zdarzenia rozszerzone w pliku zdarzeń](xevent-code-event-file.md)
- [Przechowuj zdarzenia rozszerzone w buforze pierścieniowym](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Wykonywanie zapytań względem danych rozproszonych

- [Wykonywanie zapytań o dane partycjonowane w pionie](elastic-query-getting-started-vertical.md) w wielu bazach danych.
- [Raport w warstwie danych skalowanych w poziomie](elastic-query-horizontal-partitioning.md).
- [Zapytania między tabelami z różnymi schematami](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Synchronizacja danych

- [SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
- [Agent synchronizacji danych](sql-data-sync-agent-overview.md)
- [Replikowanie zmian schematu](sql-data-sync-update-sync-schema.md)
- [Monitorowanie za pomocą usługi OMS](sql-data-sync-monitor-sync.md)
- [Najlepsze rozwiązania dotyczące funkcji Data Sync](sql-data-sync-best-practices.md)
- [Rozwiązywanie problemów z usługą Data Sync](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Zadania Elastic Database

- [Tworzenie i zarządzanie](elastic-jobs-powershell-create.md) Elastic Database zadań przy użyciu programu PowerShell.
- [Tworzenie i zarządzanie](elastic-jobs-tsql-create-manage.md) Elastic Database zadania przy użyciu języka Transact-SQL.
- [Migruj ze starego zadania elastycznego](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Dzielenie bazy danych na fragmenty

- [Uaktualnij bibliotekę klienta Elastic Database](elastic-scale-upgrade-client-library.md).
- [Utwórz aplikację podzielonej na fragmenty](elastic-scale-get-started.md).
- [Zapytanie w poziomie podzielonej na fragmenty dane](elastic-query-getting-started.md).
- Uruchom [zapytania fragmentu](elastic-scale-multishard-querying.md).
- [Przenieś dane podzielonej na fragmenty](elastic-scale-configure-deploy-split-and-merge.md).
- [Skonfiguruj zabezpieczenia](elastic-scale-split-merge-security-configuration.md) w bazie danych fragmentów.
- [Dodaj fragmentu](elastic-scale-add-a-shard.md) do bieżącego zestawu bazy danych fragmentów.
- [Rozwiąż problemy dotyczące mapy fragmentu](elastic-database-recovery-manager.md).
- [Migrowanie bazy danych podzielonej na fragmenty DB](elastic-convert-to-use-elastic-tools.md).
- [Utwórz liczniki](elastic-database-perf-counters.md).
- [Użyj programu Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) do wykonywania zapytań dotyczących danych podzielonej na fragmenty.
- [Użyj platformy Dapper Framework](elastic-scale-working-with-dapper.md) , aby wykonywać zapytania dotyczące danych podzielonej na fragmenty.

## <a name="develop-applications"></a>Tworzenie aplikacji

- [Łączność](connect-query-content-reference-guide.md#libraries)
- [Korzystanie z łącznika Spark](spark-connector.md)
- [Uwierzytelnianie aplikacji](application-authentication-get-client-id-keys.md)
- [Korzystanie z usługi Batch w celu uzyskania lepszej wydajności](../performance-improve-use-batching.md)
- [Wskazówki dotyczące łączności](troubleshoot-common-connectivity-issues.md)
- [Aliasy DNS](dns-alias-overview.md)
- [Konfigurowanie aliasu DNS programu PowerShell](dns-alias-powershell-create.md)
- [Porty — ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C i C++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Projektowanie aplikacji

- [Projektowanie po kątem odzyskiwania po awarii](designing-cloud-solutions-for-disaster-recovery.md)
- [Projektowanie pod kątem elastycznych pul](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Projektowanie pod kątem uaktualnień aplikacji](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Projektowanie aplikacji wielodostępnego oprogramowania jako usługi (SaaS)

- [Wzorce projektowe SaaS](saas-tenancy-app-design-patterns.md)
- [Indeksator wideo SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpieczenia aplikacji SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat przewodników dla wystąpienia zarządzanego usługi Azure SQL](../managed-instance/how-to-content-reference-guide.md)
