---
title: Konfigurowanie & zarządzanie odwołaniem do zawartości
titleSuffix: Azure SQL Managed Instance
description: Przewodnik dotyczący zawartości, który uczy się, jak skonfigurować i zarządzać wystąpieniem zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779768"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Informacje o zawartości wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule można znaleźć odwołanie do zawartości do różnych przewodników, skryptów i wyjaśnień, które ułatwiają zarządzanie i Konfigurowanie wystąpienia zarządzanego Azure SQL.

## <a name="load-data"></a>Ładowanie danych

- [Migrowanie do wystąpienia zarządzanego usługi Azure SQL](migrate-to-instance-from-sql-server.md): informacje na temat zalecanego procesu migracji i narzędzi do migracji do wystąpienia zarządzanego Azure SQL.
- [Migruj certyfikat TDE do wystąpienia zarządzanego usługi Azure SQL](tde-certificate-migrate.md): Jeśli baza danych SQL Server jest chroniona za pomocą funkcji transparent Data Encryption (TDE), należy przeprowadzić migrację certyfikatu, za pomocą którego wystąpienie zarządzane SQL może odszyfrować kopię zapasową, która ma zostać przywrócona na platformie Azure.
- [Importowanie bazy danych z pliku BACPAC](../database/database-import.md)
- [Eksportowanie bazy danych do pliku BACPAC](../database/database-export.md)
- [Ładowanie danych za pomocą narzędzia BCP](../load-from-csv-with-bcp.md)
- [Ładowanie danych za pomocą Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Konfiguracja sieci

- [Określ rozmiar podsieci](vnet-subnet-determine-size.md): ponieważ nie można zmienić rozmiaru podsieci po wdrożeniu wystąpienia zarządzanego SQL, należy obliczyć zakres adresów IP, który jest wymagany dla liczby i typów zarządzanych wystąpień, które planujesz wdrożyć w podsieci. 
- [Tworzenie nowej sieci wirtualnej i podsieci](virtual-network-subnet-create-arm-template.md): Skonfiguruj sieć wirtualną i podsieć zgodnie z [wymaganiami sieci](connectivity-architecture-overview.md#network-requirements). 
- [Konfigurowanie istniejącej sieci wirtualnej i podsieci](vnet-existing-add-subnet.md): Sprawdź wymagania dotyczące sieci i skonfiguruj istniejącą sieć wirtualną i podsieć do wdrożenia wystąpienia zarządzanego SQL. 
- [Skonfiguruj niestandardowy serwer DNS](custom-dns-configure.md): Skonfiguruj niestandardowy serwer DNS, aby przyznać dostęp do zasobów zewnętrznych do domen niestandardowych z wystąpienia zarządzanego SQL za pośrednictwem połączonego serwera profilów poczty bazy danych. 
- [Synchronizuj konfigurację sieci](azure-app-sync-network-configuration.md): Odśwież plan konfiguracji sieci, jeśli nie możesz nawiązać połączenia po [zintegrowaniu aplikacji z siecią wirtualną platformy Azure](../../app-service/web-sites-integrate-with-vnet.md).
- [Znajdź adres IP punktu końcowego zarządzania](management-endpoint-find-ip-address.md): Określ publiczny punkt końcowy używany przez wystąpienie zarządzane przez program SQL do celów zarządzania. 
- [Sprawdź wbudowaną ochronę zapory](management-endpoint-verify-built-in-firewall.md): Sprawdź, czy wystąpienie zarządzane SQL zezwala na ruch tylko na niezbędnych portach i innych wbudowanych reguł zapory. 
- [Połącz aplikacje](connect-application-instance.md): Poznaj różne wzorce łączenia aplikacji z wystąpieniem zarządzanym SQL.

## <a name="feature-configuration"></a>Konfiguracja funkcji

- [Konfigurowanie uwierzytelniania usługi Azure AD](../database/authentication-aad-configure.md)
- [Konfigurowanie zasad dostępu warunkowego](../database/conditional-access-configure.md)
- [Uwierzytelnianie wieloskładnikowe w usłudze Azure AD](../database/authentication-mfa-ssms-overview.md)
- [Konfigurowanie uwierzytelniania wieloskładnikowego](../database/authentication-mfa-ssms-configure.md)
- [Konfigurowanie zasad przechowywania danych czasowych](../database/temporal-tables-retention-policy.md)
- [Konfigurowanie szyfrowania TDE przy usługi BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Obracanie kluczy BYOK szyfrowania TDE](../database/transparent-data-encryption-byok-key-rotation.md)
- [Usuń ochronę TDE](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurowanie przetwarzania OLTP w pamięci](../in-memory-oltp-configure.md)
- [Konfigurowanie Azure Automation](../database/automation-manage.md)
- [Replikacja transakcyjna](replication-between-two-instances-configure-tutorial.md) umożliwia replikowanie danych między wystąpieniami zarządzanymi, a także z SQL Server lokalnego do wystąpienia zarządzanego SQL i na odwrót.
- [Konfigurowanie wykrywania zagrożeń](threat-detection-configure.md) — [wykrywanie zagrożeń](../database/threat-detection-overview.md) to wbudowana funkcja wystąpienia zarządzanego usługi Azure SQL, która wykrywa różne potencjalne ataki, takie jak iniekcja SQL lub dostęp z podejrzanych lokalizacji. 
- [Tworzenie alertów](alerts-create.md) umożliwia Konfigurowanie alertów dotyczących monitorowanych metryk, takich jak użycie procesora CPU, użycie miejsca do magazynowania, liczba operacji we/wy na sekundę i inne dla wystąpienia zarządzanego SQL. 

## <a name="monitoring-and-tuning"></a>Monitorowanie i dostrajanie

- [Ręczne dostrajanie](../database/performance-guidance.md)
- [Korzystanie z widoków DMV do monitorowania wydajności](../database/monitoring-with-dmvs.md)
- [Monitorowanie wydajności za pomocą magazynu zapytań](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Rozwiązywanie problemów z wydajnością przy użyciu funkcji Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Korzystanie z dziennika diagnostyki Intelligent Insights](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitoruj In-Memory miejsce OLTP](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Zdarzenia rozszerzone

- [Zdarzenia rozszerzone](../database/xevent-db-diff-from-svr.md)
- [Przechowywanie zdarzeń rozszerzonych w pliku zdarzeń](../database/xevent-code-event-file.md)
- [Przechowywanie zdarzeń rozszerzonych w buforze pierścieniowym](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Generowanie alertów

- [Tworzenie alertów dla wystąpienia zarządzanego](alerts-create.md)

## <a name="operations"></a>Operacje

- [Zainicjowane przez użytkownika ręczne przejście w tryb failover w usłudze SQL Managed Instance](user-initiated-failover.md)

## <a name="develop-applications"></a>Tworzenie aplikacji

- [Połączenia](../database/connect-query-content-reference-guide.md#libraries)
- [Korzystanie z łącznika Spark](../../cosmos-db/spark-connector.md)
- [Uwierzytelnianie aplikacji](../database/application-authentication-get-client-id-keys.md)
- [Korzystanie z usługi Batch w celu uzyskania lepszej wydajności](../performance-improve-use-batching.md)
- [Wskazówki dotyczące łączności](../database/troubleshoot-common-connectivity-issues.md)
- [Aliasy DNS](../database/dns-alias-overview.md)
- [Konfigurowanie aliasu DNS przy użyciu programu PowerShell](../database/dns-alias-powershell-create.md)
- [Porty — ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C i C++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Projektowanie aplikacji

- [Projektowanie po kątem odzyskiwania po awarii](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Projektowanie pod kątem elastycznych pul](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Projektowanie pod kątem uaktualnień aplikacji](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Projektowanie wielodostępnych aplikacji SaaS

- [Wzorce projektowe SaaS](../database/saas-tenancy-app-design-patterns.md)
- [Indeksator wideo SaaS](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpieczenia aplikacji SaaS](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Następne kroki

Rozpocznij od [wdrożenia wystąpienia zarządzanego SQL](instance-create-quickstart.md).