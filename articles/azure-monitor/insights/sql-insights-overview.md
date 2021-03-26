---
title: Monitoruj wdrożenia SQL za pomocą usługi SQL Insights (wersja zapoznawcza)
description: Omówienie usługi SQL Insights w Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567835"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitoruj wdrożenia SQL za pomocą usługi SQL Insights (wersja zapoznawcza)
SQL Insights to kompleksowe rozwiązanie do monitorowania dowolnego produktu w [rodzinie usługi Azure SQL](../../azure-sql/index.yml). Usługi SQL Insights wykorzystują [dynamiczne widoki zarządzania](../../azure-sql/database/monitoring-with-dmvs.md) , aby uwidocznić dane potrzebne do monitorowania kondycji, diagnozowania problemów i dostrajania wydajności.  

Program SQL Insights wykonuje zdalne monitorowanie. Agenci monitorowania na dedykowanych maszynach wirtualnych nawiązują połączenie z zasobami SQL i zdalnie zbierają dane. Zebrane dane są przechowywane w [dziennikach Azure monitor](../logs/data-platform-logs.md), co umożliwia łatwą agregację, filtrowanie i analizę trendów. Zebrane dane można wyświetlić z [szablonu skoroszytu](../visualize/workbooks-overview.md)usługi SQL Insights lub można je oddzielać bezpośrednio do danych przy użyciu [zapytań dzienników](../logs/get-started-queries.md).

## <a name="pricing"></a>Ceny
Usługi SQL Insights nie mają bezpośredniego kosztu. Wszystkie koszty są naliczane przez maszyny wirtualne, które zbierają dane, Log Analytics obszary robocze, które przechowują dane, oraz wszelkie reguły alertów skonfigurowane dla danych. 

**Maszyny wirtualne**

W przypadku maszyn wirtualnych opłata jest naliczana zgodnie z cennikiem opublikowanym na [stronie cennika usługi Virtual Machines](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/). Liczba wymaganych maszyn wirtualnych zależy od liczby parametrów połączenia, które mają być monitorowane. Zalecamy przydzielenie 1 maszyny wirtualnej o rozmiarze Standard_B2s dla każdego ciągu połączenia 100. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące maszyny wirtualnej platformy Azure](sql-insights-enable.md#azure-virtual-machine-requirements) .

**Obszary robocze usługi Log Analytics**

W przypadku obszarów roboczych Log Analytics opłaty są naliczone zgodnie z cennikiem opublikowanym na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Obszary robocze Log Analytics używane przez usługi SQL Insights będą powodować naliczanie opłat za pozyskiwanie i przechowywanie danych oraz (opcjonalnie) eksportowanie danych. Dokładne opłaty będą się różnić w zależności od ilości danych, które zostały odebrane, zachowane i wyeksportowane. Ilość tych danych będzie się różnić w zależności od aktywności bazy danych i ustawień kolekcji zdefiniowanych w [profilach monitorowania](sql-insights-enable.md#create-sql-monitoring-profile).

**Zasady alertów**

W przypadku reguł alertów w Azure Monitor opłaty są naliczone zgodnie z cennikiem opublikowanym na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Jeśli zdecydujesz się na [tworzenie alertów za pomocą usługi SQL Insights](sql-insights-alerts.md), nastąpi naliczanie opłat za wszystkie utworzone reguły alertów i powiadomienia.

## <a name="supported-versions"></a>Obsługiwane wersje 
Usługi SQL Insights obsługują następujące wersje SQL Server:
- SQL Server 2012 i nowsze

Usługi SQL Insights obsługują SQL Server uruchomione w następujących środowiskach:
- Azure SQL Database
- Wystąpienie zarządzane Azure SQL
- SQL Server na platformie Azure Virtual Machines (SQL Server uruchomione na maszynach wirtualnych zarejestrowanych w dostawcy [maszyny wirtualnej SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )
- Maszyny wirtualne platformy Azure (SQL Server uruchomione na maszynach wirtualnych, które nie są zarejestrowane w dostawcy [maszyny wirtualnej SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )

Usługi SQL Insights nie obsługują ani nie ma ograniczonej obsługi dla następujących elementów:
- **Wystąpienia spoza platformy Azure**: SQL Server uruchomione na maszynach wirtualnych poza platformą Azure nie są obsługiwane
- **Azure SQL Database pule elastyczne**: nie można zbierać metryk dla pul elastycznych. Nie można zbierać metryk dla baz danych w ramach pul elastycznych.
- **Azure SQL Database niskie warstwy usług**: nie można zbierać metryk dla baz danych w [warstwach usług](../../azure-sql/database/resource-limits-dtu-single-databases.md) podstawowa, S0, S1 i S2
- **Azure SQL Database warstwa bezserwerowa**: metryki można zbierać dla baz danych przy użyciu warstwy obliczeń bezserwerowych. Jednak proces zbierania metryk zresetuje czasomierz autopauzy, uniemożliwiając przechodzenie bazy danych do stanu autowstrzymania
- **Repliki pomocnicze**: metryki można zbierać tylko dla jednej repliki pomocniczej na bazę danych. Jeśli baza danych ma więcej niż 1 replikę pomocniczą, można monitorować tylko 1.
- **Uwierzytelnianie za pomocą Azure Active Directory**: jedyną obsługiwaną metodą [uwierzytelniania](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) dla monitorowania jest uwierzytelnianie SQL. W przypadku SQL Server na maszynie wirtualnej platformy Azure uwierzytelnianie przy użyciu Active Directory na niestandardowym kontrolerze domeny nie jest obsługiwane.  

## <a name="open-sql-insights"></a>Otwórz program SQL Insights
Otwórz program SQL Insights, wybierając pozycję **SQL (wersja zapoznawcza)** z sekcji **Insights** w menu **Azure monitor** w Azure Portal. Kliknij kafelek, aby załadować środowisko dla typu monitorowanego serwera SQL.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Usługi SQL Insights w Azure Portal.":::

## <a name="enable-sql-insights"></a>Włącz usługi SQL Insights 
Zobacz temat [Włączanie usługi SQL Insights](sql-insights-enable.md) , aby uzyskać instrukcje dotyczące włączania usługi SQL Insights.

## <a name="troubleshoot-sql-insights"></a>Rozwiązywanie problemów z usługą SQL Insights 
Zobacz temat [Rozwiązywanie problemów z usługą SQL Insights](sql-insights-troubleshoot.md) , aby uzyskać instrukcje dotyczące rozwiązywania problemów z usługą SQL Insights.

## <a name="data-collected-by-sql-insights"></a>Dane zbierane przez usługi SQL Insights
Program SQL Insights wykonuje zdalne monitorowanie. Nie instalujemy żadnych agentów na maszynach wirtualnych z systemem SQL Server. 

Usługi SQL Insights wykorzystują dedykowane maszyny wirtualne monitorowania do zdalnego zbierania danych z zasobów SQL. Dla każdej maszyny wirtualnej monitorowania zostanie zainstalowany [agent Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) i rozszerzenie usługi obciążenie Insights (WLI). Rozszerzenie WLI zawiera [agenta telegraf](https://www.influxdata.com/time-series-platform/telegraf/)Open Source. Usługi SQL Insights wykorzystują [reguły zbierania danych](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) w celu określenia ustawień zbierania danych dla [wtyczki SQL Server](https://www.influxdata.com/integration/microsoft-sql-server/)telegraf.

Różne zestawy danych są dostępne dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i SQL Server. W poniższych tabelach opisano dostępne dane. Można dostosować, które zestawy danych mają być zbierane, oraz częstotliwość zbierania podczas [tworzenia profilu monitorowania](sql-insights-enable.md#create-sql-monitoring-profile).

Poniższe tabele zawierają następujące kolumny:
- **Przyjazna nazwa**: Nazwa zapytania, jak pokazano na Azure Portal podczas tworzenia profilu monitorowania
- **Nazwa konfiguracji**: Nazwa zapytania, jak pokazano na Azure Portal podczas edytowania profilu monitorowania
- **Przestrzeń nazw**: Nazwa zapytania, która została znaleziona w obszarze roboczym log Analytics. Ten identyfikator jest wyświetlany w tabeli **InsighstMetrics** we `Namespace` właściwości w kolumnie. `Tags`
- **Widoków DMV**: dynamiczne widoki zarządzane używane do tworzenia zestawu danych
- **Domyślnie włączone**: niezależnie od tego, czy dane są zbierane domyślnie
- **Częstotliwość zbierania domyślnego**: jak często dane są zbierane domyślnie

### <a name="data-for-azure-sql-database"></a>Dane dla Azure SQL Database 
| Przyjazna nazwa | Nazwa konfiguracji | Przestrzeń nazw | Dynamiczne widoki zarządzania | Włączona domyślnie | Domyślna częstotliwość zbierania |
|:---|:---|:---|:---|:---|:---|
| Statystyka oczekiwania bazy danych | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | Nie | NA |
| Statystyka oczekiwania DBO | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Tak | 60 sekund |
| Nadzorcy pamięci | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Tak | 60 sekund |
| Operacje we/wy bazy danych | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Tak | 60 sekund |
| Właściwości serwera | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>widoku. 6.5<br>widoku. [database_service_objectives] | Tak | 60 sekund |
| Liczniki wydajności | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Tak | 60 sekund |
| Statystyka zasobów | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Tak | 60 sekund |
| Nadzór nad zasobami | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Tak | 60 sekund |
| Żądania | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Nie | NA |
| Planiści| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Nie | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Dane dla wystąpienia zarządzanego usługi Azure SQL 

| Przyjazna nazwa | Nazwa konfiguracji | Przestrzeń nazw | Dynamiczne widoki zarządzania | Włączona domyślnie | Domyślna częstotliwość zbierania |
|:---|:---|:---|:---|:---|:---|
| Statystyki oczekiwania | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Tak | 60 sekund |
| Nadzorcy pamięci | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Tak | 60 sekund |
| Operacje we/wy bazy danych | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Tak | 60 sekund |
| Właściwości serwera | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Tak | 60 sekund |
| Liczniki wydajności | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Tak | 60 sekund |
| Statystyka zasobów | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Tak | 60 sekund |
| Nadzór nad zasobami | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Tak | 60 sekund |
| Żądania | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Nie | NA |
| Planiści | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Nie | NA |

### <a name="data-for-sql-server"></a>Dane dla SQL Server

| Przyjazna nazwa | Nazwa konfiguracji | Przestrzeń nazw | Dynamiczne widoki zarządzania | Włączona domyślnie | Domyślna częstotliwość zbierania |
|:---|:---|:---|:---|:---|:---|
| Statystyki oczekiwania | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Tak | 60 sekund | 
| Nadzorcy pamięci | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Tak | 60 sekund |
| Operacje we/wy bazy danych | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Tak | 60 sekund |
| Właściwości serwera | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Tak | 60 sekund |
| Liczniki wydajności | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Tak | 60 sekund |
| Miejsce na woluminie | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Tak | 60 sekund |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Tak | 60 sekund |
| Planiści | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Nie | NA |
| Żądania | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Nie | NA |
| Stany repliki dostępności | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | Nie | 60 sekund |
| Repliki bazy danych dostępności | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | Nie | 60 sekund |

## <a name="next-steps"></a>Następne kroki

- Zobacz [Włączanie usługi SQL Insights](sql-insights-enable.md) , aby uzyskać instrukcje dotyczące włączania usługi SQL Insights
- Zapoznaj się z [często zadawanymi pytaniami](../faq.md#sql-insights-preview) dotyczącymi często zadawanych pytań dotyczących usługi SQL Insights
