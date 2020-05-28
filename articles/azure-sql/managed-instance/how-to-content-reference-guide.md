---
title: Konfigurowanie & zarządzanie odwołaniem do zawartości
titleSuffix: Azure SQL Managed Instance
description: Przewodnik dotyczący zawartości, który uczy się, jak skonfigurować i zarządzać wystąpieniem zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: bc0c30e234310a4ee1d013d7a11ca556edd071c6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041116"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Informacje o zawartości wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule można znaleźć odwołanie do zawartości do różnych przewodników, skryptów i wyjaśnień, które ułatwiają zarządzanie i Konfigurowanie wystąpienia zarządzanego Azure SQL.

## <a name="load-data"></a>Ładowanie danych

- [Migrowanie do wystąpienia zarządzanego usługi Azure SQL](migrate-to-instance-from-sql-server.md) — informacje na temat zalecanego procesu migracji i narzędzi do migracji do wystąpienia zarządzanego Azure SQL.
- [Migrowanie certyfikatu TDE do wystąpienia zarządzanego usługi Azure SQL](tde-certificate-migrate.md) — Jeśli baza danych SQL Server jest chroniona za pomocą funkcji przezroczystego szyfrowania danych (TDE), należy przeprowadzić migrację certyfikatu, za pomocą którego wystąpienie zarządzane SQL może odszyfrować kopię zapasową, która ma zostać przywrócona na platformie Azure.
- [Importowanie bazy danych z pliku BACPAC](../database/database-import.md)
- [Eksportowanie bazy danych do pliku BACPAC](../database/database-export.md)
- [Ładowanie danych za pomocą narzędzia BCP](../load-from-csv-with-bcp.md)
- [Ładowanie danych za pomocą usługi ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Konfiguracja sieci

- [Określanie rozmiaru podsieci](vnet-subnet-determine-size.md) Ponieważ nie można zmienić rozmiaru podsieci po wdrożeniu wystąpienia zarządzanego SQL, należy obliczyć zakres adresów IP, który jest wymagany dla liczby i typów wystąpień zarządzanych przez usługę SQL, które planujesz wdrożyć w podsieci. 
- [Tworzenie nowej sieci wirtualnej i podsieci](virtual-network-subnet-create-arm-template.md) Skonfiguruj sieć wirtualną i podsieć zgodnie z [wymaganiami dotyczącymi sieci opisanymi tutaj](connectivity-architecture-overview.md#network-requirements) . 
- [Konfigurowanie istniejącej sieci wirtualnej i podsieci](vnet-existing-add-subnet.md) Sprawdź wymagania sieci i skonfiguruj istniejącą sieć wirtualną i podsieć, aby wdrożyć wystąpienie zarządzane SQL. 
- [Konfigurowanie niestandardowego serwera DNS](custom-dns-configure.md) Skonfiguruj niestandardowy serwer DNS, aby przyznać dostęp do zasobów zewnętrznych do domen niestandardowych z wystąpienia zarządzanego SQL za pośrednictwem połączonego serwera profilów poczty bazy danych. 
- [Synchronizuj konfigurację sieci](azure-app-sync-network-configuration.md) Odśwież plan konfiguracji sieci, jeśli nie możesz nawiązać połączenia po [zintegrowaniu aplikacji z usługą Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md)
- [Znajdź adres IP punktu końcowego zarządzania](management-endpoint-find-ip-address.md) Określ publiczny punkt końcowy, którego wystąpienie zarządzane SQL używa do celów zarządzania. 
- [Weryfikowanie wbudowanej ochrony zapory](management-endpoint-verify-built-in-firewall.md) Sprawdź, czy wystąpienie zarządzane SQL zezwala na ruch tylko na niezbędnych portach i innych wbudowanych reguł zapory. 
- [Łączenie aplikacji](connect-application-instance.md) Poznaj różne wzorce łączenia aplikacji z wystąpieniem zarządzanym SQL.

## <a name="feature-configuration"></a>Konfiguracja funkcji

- [Konfigurowanie uwierzytelniania usługi Azure AD](../database/authentication-aad-configure.md)
- [Konfigurowanie dostępu warunkowego](../database/conditional-access-configure.md)
- [Uwierzytelnianie wieloskładnikowe usługi AAD](../database/authentication-mfa-ssms-overview.md)
- [Konfigurowanie uwierzytelniania wieloskładnikowego](../database/authentication-mfa-ssms-configure.md)
- [Konfigurowanie zasad przechowywania danych czasowych](../database/temporal-tables-retention-policy.md)
- [Konfigurowanie szyfrowania TDE przy usługi BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Obracanie kluczy BYOK szyfrowania TDE](../database/transparent-data-encryption-byok-key-rotation.md)
- [Usuwanie ochrony szyfrowania TDE](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurowanie przetwarzania OLTP w pamięci](../in-memory-oltp-configure.md)
- [Konfigurowanie Azure Automation](../database/automation-manage.md)
- [Replikacja transakcyjna](replication-between-two-instances-configure-tutorial.md) umożliwia replikowanie danych między wystąpieniami zarządzanymi usługi Azure SQL lub z SQL Server lokalnych do wystąpienia zarządzanego SQL i na odwrót.
- [Konfigurowanie wykrywania zagrożeń](threat-detection-configure.md) — [wykrywanie zagrożeń](../database/threat-detection-overview.md) to wbudowana funkcja wystąpienia zarządzanego usługi Azure SQL, która wykrywa różne potencjalne ataki, takie jak iniekcja SQL lub dostęp z podejrzanych lokalizacji. 
- [Tworzenie alertów](alerts-create.md) umożliwia Konfigurowanie alertów dotyczących monitorowanych metryk, takich jak użycie procesora CPU, użycie miejsca do magazynowania, liczba operacji we/wy na sekundę i inne dla wystąpienia zarządzanego SQL. 

## <a name="monitoring-and-tuning"></a>Monitorowanie i dostrajanie

- [Ręczne dostrajanie](../database/performance-guidance.md)
- [Korzystanie z widoków DMV do monitorowania wydajności](../database/monitoring-with-dmvs.md)
- [Korzystanie z magazynu zapytań do monitorowania wydajności](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Rozwiązywanie problemów z wydajnością przy użyciu funkcji Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Korzystanie z dziennika diagnostycznego funkcji Intelligent Insights](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitorowanie miejsca OLTP w pamięci](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Zdarzenia rozszerzone

- [Zdarzenia rozszerzone](../database/xevent-db-diff-from-svr.md)
- [Przechowuj zdarzenia rozszerzone w pliku zdarzeń](../database/xevent-code-event-file.md)
- [Przechowuj zdarzenia rozszerzone w buforze pierścieniowym](../database/xevent-code-ring-buffer.md)

## <a name="develop-applications"></a>Tworzenie aplikacji

- [Łączność](../database/connect-query-content-reference-guide.md#libraries)
- [Korzystanie z łącznika Spark](../../cosmos-db/spark-connector.md)
- [Uwierzytelnianie aplikacji](../database/application-authentication-get-client-id-keys.md)
- [Korzystanie z usługi Batch w celu uzyskania lepszej wydajności](../performance-improve-use-batching.md)
- [Wskazówki dotyczące łączności](../database/troubleshoot-common-connectivity-issues.md)
- [Aliasy DNS](../database/dns-alias-overview.md)
- [Konfigurowanie aliasu DNS programu PowerShell](../database/dns-alias-powershell-create.md)
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

Rozpocznij od [wdrożenia wystąpienia zarządzanego SQL](instance-create-quickstart.md)
