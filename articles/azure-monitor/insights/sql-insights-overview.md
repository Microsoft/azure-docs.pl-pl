---
title: Monitoruj wdrożenia SQL za pomocą usługi SQL Insights (wersja zapoznawcza)
description: Omówienie usługi SQL Insights w Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: b5add466a60bc855e08917d02fecaf60a35deeb1
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889573"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitoruj wdrożenia SQL za pomocą usługi SQL Insights (wersja zapoznawcza)
Usługi SQL Insights monitorują wydajność i kondycję wdrożeń SQL.  Może ona pomóc zapewnić przewidywalną wydajność i dostępność ważnych obciążeń, które zostały utworzone wokół zaplecze SQL, identyfikując wąskie gardła i problemy z wydajnością. Usługi SQL Insights przechowują swoje dane w [dziennikach Azure monitor](../logs/data-platform-logs.md), co pozwala na dostarczanie zaawansowanej agregacji i filtrowania oraz analizowanie trendów danych w miarę upływu czasu. Te Azure Monitor dane można wyświetlić w widokach, które firma Microsoft udostępnia w ramach tej oferty, i można nawiązać do nich udział bezpośrednio w danych dziennika w celu uruchamiania zapytań i analizowania trendów.

Usługa SQL Insights nie instaluje żadnych elementów we wdrożeniach usługi SQL IaaS. Zamiast tego używa dedykowanych maszyn wirtualnych monitorowania do zdalnego zbierania danych dla wdrożeń SQL PaaS i SQL IaaS.  Profil monitorowania usługi SQL Insights umożliwia zarządzanie zestawami danych, które mają być zbierane na podstawie typu SQL, w tym Azure SQL DB, wystąpienia zarządzanego usługi Azure SQL i programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure.

## <a name="pricing"></a>Ceny

Nie ma bezpośredniego kosztu usługi SQL Insights, ale opłaty są naliczane za działanie w obszarze roboczym Log Analytics. W oparciu o Cennik opublikowany na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/)usługi SQL Insights są rozliczane za:

- Dane pozyskane z agentów i przechowywane w obszarze roboczym.
- Reguły alertów na podstawie danych dziennika.
- Powiadomienia wysyłane z reguł alertów.

Rozmiar dziennika jest różny w zależności od długości ciągu zbieranych danych i może zwiększyć się wraz z ilością działania bazy danych. 

## <a name="supported-versions"></a>Obsługiwane wersje 
Usługi SQL Insights obsługują następujące wersje SQL Server:

- SQL Server 2012 i nowsze

Usługi SQL Insights obsługują SQL Server uruchomione w następujących środowiskach:

- Azure SQL Database
- Wystąpienie zarządzane Azure SQL
- Maszyny wirtualne usługi Azure SQL
- Maszyny wirtualne platformy Azure

Usługi SQL Insights nie obsługują ani nie ma ograniczonej obsługi dla następujących elementów:

- SQL Server uruchomione na maszynach wirtualnych poza platformą Azure nie są obecnie obsługiwane.
- Azure SQL Database pule elastyczne: ograniczona pomoc techniczna w publicznej wersji zapoznawczej. Będą w pełni obsługiwane w ogólnej dostępności.  
- Wdrożenia bez usługi Azure SQL Server: podobnie jak Active Geo-DR, bieżąca agenci monitorowania uniemożliwią wdrażanie bezserwerowe w stanie uśpienia. Może to spowodować większe niż oczekiwane koszty wdrożeń bezserwerowych.  
- Możliwe do odczytu serwery pomocnicze: obsługiwane są obecnie tylko typy wdrożeń z pojedynczym dodatkowym punktem końcowym do odczytu (Krytyczne dla działania firmy lub do skalowania). Gdy wdrożenia w ramach skalowania w poziomie obsługują nazwane repliki, będziemy mogli obsługiwać wiele pomocniczych punktów końcowych dla jednej logicznej bazy danych.  
- Azure Active Directory: obecnie obsługujemy tylko nazwy logowania SQL dla agenta monitorowania. Planujemy obsługiwać usługi Azure Active Directory w nadchodzącym wydaniu i nie ma bieżącej obsługi uwierzytelniania maszyn wirtualnych SQL przy użyciu usługi Active Directory na kontrolerze domeny Bespoke.  


## <a name="open-sql-insights"></a>Otwórz program SQL Insights
Otwórz program SQL Insights, wybierając pozycję **SQL (wersja zapoznawcza)** z sekcji **Insights** w menu **Azure monitor** w Azure Portal. Kliknij kafelek, aby załadować środowisko dla typu monitorowanego serwera SQL.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Usługi SQL Insights w Azure Portal.":::


## <a name="enable-sql-insights"></a>Włącz usługi SQL Insights 
Zobacz temat [Włączanie usługi SQL Insights](sql-insights-enable.md) , aby uzyskać szczegółową procedurę włączania usługi SQL Insights oprócz kroków związanych z rozwiązywaniem problemów.


## <a name="data-collected-by-sql-insights"></a>Dane zbierane przez usługi SQL Insights

Usługi SQL Insights obsługują tylko zdalną metodę monitorowania SQL. Nie instalujemy żadnych agentów na maszynach wirtualnych, na których jest uruchomiony SQL Server. Co najmniej jeden dedykowany maszyn wirtualnych monitorowania jest używany do zdalnego zbierania danych z zasobów SQL. 

Na każdej z tych maszyn wirtualnych monitorowania jest na nich zainstalowany [agent Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) wraz z rozszerzeniem usługi obciążenia szczegółowe informacje (WLI). 

Rozszerzenie WLI zawiera [agenta telegraf](https://www.influxdata.com/time-series-platform/telegraf/)Open Source.  Używamy [reguł zbierania danych](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) w celu skonfigurowania [wtyczki wejściowej SqlServer](https://www.influxdata.com/integration/microsoft-sql-server/) w celu określenia danych do zebrania z usługi Azure SQL DB, wystąpienia zarządzanego usługi Azure SQL i SQL Server uruchomionego na maszynie wirtualnej platformy Azure. 

Poniższe tabele podsumowują następujące elementy:

- Nazwa zapytania w dodatku SqlServer telegraf
- Dynamiczne widoki zarządzane, wywołania zapytania
- Przestrzeń nazw, w której pojawiają się dane w tabeli *InsighstMetrics*
- Czy dane są zbierane domyślnie
- Jak często dane są zbierane domyślnie
 
Podczas tworzenia profilu monitorowania można modyfikować, które zapytania są uruchamiane i częstotliwość zbierania danych. 

### <a name="azure-sql-db-data"></a>Dane usługi Azure SQL DB 

| Nazwa zapytania | DMV | Przestrzeń nazw | Włączona domyślnie | Domyślna częstotliwość zbierania |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | Nie | NA |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Tak | 60 sekund |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Tak | 60 sekund |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | Tak | 60 sekund |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>widoku. 6.5<br>widoku. [database_service_objectives] | sqlserver_server_properties | Tak | 60 sekund |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Tak | 60 sekund |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Tak | 60 sekund |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Tak | 60 sekund |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Nie | NA |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Nie | NA  |

### <a name="azure-sql-managed-instance-data"></a>Dane wystąpienia zarządzanego usługi Azure SQL 

| Nazwa zapytania | DMV | Przestrzeń nazw | Włączona domyślnie | Domyślna częstotliwość zbierania |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Tak | 60 sekund |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Tak | 60 sekund |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Tak | 60 sekund |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Tak | 60 sekund |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Tak | 60 sekund |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Tak | 60 sekund |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Tak | 60 sekund |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Nie | NA |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Nie | NA |

### <a name="sql-server-data"></a>SQL Server dane

| Nazwa zapytania | DMV | Przestrzeń nazw | Włączona domyślnie | Domyślna częstotliwość zbierania |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Tak | 60 sekund |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Tak | 60 sekund | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Tak | 60 sekund |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | Tak | 60 sekund |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Tak | 60 sekund |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Nie | NA |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Nie | NA |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Tak | 60 sekund |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Tak | 60 sekund |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 sekund |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 sekund |




## <a name="next-steps"></a>Następne kroki

Zobacz temat [Włączanie usługi SQL Insights](sql-insights-enable.md) , aby uzyskać szczegółową procedurę włączania usługi SQL Insights.
Zapoznaj się z [często zadawanymi pytaniami](../faq.md#sql-insights-preview) dotyczącymi często zadawanych pytań dotyczących usługi SQL Insights.
