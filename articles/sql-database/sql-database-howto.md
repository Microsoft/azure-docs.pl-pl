---
title: Konfigurowanie i zarządzanie
description: Dowiedz się, jak konfigurować Azure SQL Database i zarządzać nimi.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79209417"
---
# <a name="how-to-use-azure-sql-database"></a>Jak używać Azure SQL Database

W tej sekcji znajdują się różne przewodniki, skrypty i wyjaśnienia, które mogą pomóc w zarządzaniu i konfigurowaniu Azure SQL Database. Możesz również znaleźć konkretne przewodniki dotyczące wykonywania [pojedynczych baz danych](sql-database-howto-single-database.md) i [wystąpienia zarządzanego](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Ładowanie danych

- [Kopiowanie pojedynczej bazy danych lub bazy danych w puli na platformie Azure](sql-database-copy.md)
- [Importowanie bazy danych z pliku BACPAC](sql-database-import.md)
- [Eksportowanie bazy danych do pliku BACPAC](sql-database-export.md)
- [Ładowanie danych za pomocą narzędzia BCP](sql-database-load-from-csv-with-bcp.md)
- [Ładowanie danych za pomocą usługi ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Synchronizacja danych

- [SQL Data Sync](sql-database-sync-data.md)
- [Agent synchronizacji danych](sql-database-data-sync-agent.md)
- [Replikowanie zmian schematu](sql-database-update-sync-schema.md)
- [Monitorowanie za pomocą usługi OMS](sql-database-sync-monitor-oms.md)
- [Najlepsze rozwiązania dotyczące funkcji Data Sync](sql-database-best-practices-data-sync.md)
- [Rozwiązywanie problemów z usługą Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitorowanie i dostrajanie

- [Ręczne dostrajanie](sql-database-performance-guidance.md)
- [Korzystanie z widoków DMV do monitorowania wydajności](sql-database-monitoring-with-dmvs.md)
- [Korzystanie z magazynu zapytań do monitorowania wydajności](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Rozwiązywanie problemów z wydajnością przy użyciu funkcji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Korzystanie z dziennika diagnostycznego funkcji Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorowanie miejsca OLTP w pamięci](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Zdarzenia rozszerzone

- [Zdarzenia rozszerzone](sql-database-xevent-db-diff-from-svr.md)
- [Przechowuj zdarzenia rozszerzone w pliku zdarzeń](sql-database-xevent-code-event-file.md)
- [Przechowuj zdarzenia rozszerzone w buforze pierścieniowym](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Konfigurowanie uwierzytelniania usługi Azure AD](sql-database-aad-authentication-configure.md)
- [Konfigurowanie dostępu warunkowego](sql-database-conditional-access.md)
- [Uwierzytelnianie wieloskładnikowe usługi AAD](sql-database-ssms-mfa-authentication.md)
- [Konfigurowanie uwierzytelniania wieloskładnikowego](sql-database-ssms-mfa-authentication-configure.md)
- [Konfigurowanie zasad przechowywania danych czasowych](sql-database-temporal-tables-retention-policy.md)
- [Konfigurowanie szyfrowania TDE przy usługi BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Obracanie kluczy BYOK szyfrowania TDE](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Usuwanie ochrony szyfrowania TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurowanie przetwarzania OLTP w pamięci](sql-database-in-memory-oltp-migration.md)
- [Konfigurowanie Azure Automation](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Tworzenie aplikacji

- [Łączność](sql-database-libraries.md)
- [Korzystanie z łącznika Spark](sql-database-spark-connector.md)
- [Uwierzytelnianie aplikacji](sql-database-client-id-keys.md)
- [Korzystanie z usługi Batch w celu uzyskania lepszej wydajności](sql-database-use-batching-to-improve-performance.md)
- [Wskazówki dotyczące łączności](sql-database-connectivity-issues.md)
- [Aliasy DNS](dns-alias-overview.md)
- [Konfigurowanie aliasu DNS programu PowerShell](dns-alias-powershell.md)
- [Porty — ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C i C++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Projektowanie aplikacji

- [Projektowanie po kątem odzyskiwania po awarii](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Projektowanie pod kątem elastycznych pul](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Projektowanie pod kątem uaktualnień aplikacji](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Projektowanie wielodostępnych aplikacji SaaS

- [Wzorce projektowe SaaS](saas-tenancy-app-design-patterns.md)
- [Indeksator wideo SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpieczenia aplikacji SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat przewodników związanych z wystąpieniami zarządzanymi](sql-database-howto-managed-instance.md).
- Dowiedz się więcej [na temat przewodników dla pojedynczych baz danych](sql-database-howto-single-database.md).
