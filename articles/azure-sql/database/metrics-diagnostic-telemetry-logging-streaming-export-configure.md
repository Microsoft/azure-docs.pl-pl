---
title: Konfigurowanie przesyłania strumieniowego eksportu metryk i dzienników zasobów
description: Dowiedz się, jak skonfigurować eksportowanie strumieniowe metryk i dzienników zasobów, w tym inteligentną analizę diagnostyki z Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL do miejsca docelowego w celu przechowywania informacji dotyczących wykorzystania zasobów i statystyk wykonywania zapytań.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 1de2c1ff02c799d04f2ab2c81e83dda5001a531f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592733"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Konfigurowanie eksportu przesyłania strumieniowego Azure SQL Database i diagnostyki wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ten artykuł zawiera informacje na temat metryk wydajności i dzienników zasobów dla Azure SQL Database, które można wyeksportować do jednego z kilku miejsc docelowych na potrzeby analizy. Dowiesz się, jak skonfigurować eksport przesyłania strumieniowego dla tej diagnostyki diagnostycznej za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure oraz szablonów Azure Resource Manager.

Znajdziesz również informacje o miejscach docelowych, do których można przesyłać strumieniowo dane telemetryczne diagnostyki oraz jak wybierać spośród tych opcji. Opcje miejsca docelowego obejmują:

- [Log Analytics i analiza SQL](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Dane telemetryczne diagnostyki na potrzeby eksportu

Najważniejsze dla danych telemetrycznych diagnostyki, które można eksportować, to dziennik Intelligent Insights (SQLInsights). [Intelligent Insights](intelligent-insights-overview.md) używa wbudowanej analizy do ciągłego monitorowania użycia bazy danych za pomocą sztucznej analizy i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność. Po wykryciu zostanie utworzona Szczegółowa analiza, która generuje dziennik Intelligent Insights z inteligentną oceną problemu. Ta ocena polega na analizie głównej przyczyny problemu z wydajnością bazy danych i, jeśli to możliwe, zalecenia dotyczące ulepszeń wydajności. Należy skonfigurować eksport przesyłania strumieniowego tego dziennika, aby wyświetlić jego zawartość.

Oprócz przesyłania strumieniowego eksportu dziennika Intelligent Insights można również eksportować różne metryki wydajności i dodatkowe dzienniki bazy danych. Poniższa tabela zawiera opis metryk wydajności i dzienników zasobów, które można skonfigurować na potrzeby eksportu strumieniowego do jednego z kilku miejsc docelowych. Dane telemetryczne diagnostyki można skonfigurować dla pojedynczych baz danych, pul elastycznych i baz danych w puli, a także wystąpieniami zarządzanymi i bazami danych wystąpień.

| Dane telemetryczne diagnostyki dla baz danych | Obsługa Azure SQL Database | Obsługa wystąpień zarządzanych Azure SQL |
| :------------------- | ----- | ----- |
| [Metryki podstawowe](#basic-metrics): zawiera wartość procentową jednostek DTU/CPU, limit liczby jednostek DTU/procesora, procent odczytu danych fizycznych, procent zapisu w dzienniku, powodzenie/niepowodzenie/zablokowano przez połączenia zapory, procentową wartość procentową, magazyn, procent magazynu i procent magazynu XTP. | Tak | Nie |
| [Zaawansowane wystąpienie i aplikacja](#advanced-metrics): zawiera bazę danych tempdb system Data i rozmiar pliku dziennika oraz plik dziennika bazy danych tempdb (%). | Tak | Nie |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): zawiera informacje o statystykach środowiska uruchomieniowego zapytań, takich jak użycie procesora CPU i statystyka czasu trwania zapytania. | Tak | Tak |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): zawiera informacje o statystykach oczekiwania na zapytanie (zapytania, w których zarejestrowano), takie jak procesor CPU, dziennik i blokowanie. | Tak | Tak |
| [Błędy](#errors-dataset): zawiera informacje o błędach SQL w bazie danych. | Tak | Tak |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): zawiera informacje o tym, ile czasu baza danych poświęca na oczekiwanie na różne typy oczekiwania. | Tak | Nie |
| [Limity czasu](#time-outs-dataset): zawiera informacje na temat limitów czasu w bazie danych. | Tak | Nie |
| [Bloki](#blockings-dataset): zawiera informacje o blokowaniu zdarzeń w bazie danych. | Tak | Nie |
| [Zakleszczenia](#deadlocks-dataset): zawiera informacje o zdarzeniach zakleszczenia w bazie danych. | Tak | Nie |
| [AutomaticTuning](#automatic-tuning-dataset): zawiera informacje o zaleceniach dostrajania automatycznego dla bazy danych. | Tak | Nie |
| [SQLInsights](#intelligent-insights-dataset): zawiera Intelligent Insights do wydajności bazy danych. Aby dowiedzieć się więcej, zobacz [Intelligent Insights](intelligent-insights-overview.md). | Tak | Tak |

> [!NOTE]
> Nie można skonfigurować ustawień diagnostycznych dla **systemowych baz danych**, takich jak bazy danych Master, msdb, model, Resource i tempdb.

## <a name="streaming-export-destinations"></a>Miejsca docelowe eksportu strumieniowego

Dane telemetryczne diagnostyki mogą być przesyłane strumieniowo do jednego z następujących zasobów platformy Azure w celu przeprowadzenia analizy.

- **[Log Analytics obszar roboczy](#stream-into-sql-analytics)**:

  Dane przesyłane strumieniowo do [obszaru roboczego log Analytics](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) mogą być używane przez usługę [SQL Analytics](../../azure-monitor/insights/azure-sql.md). Analiza SQL to rozwiązanie monitorujące tylko w chmurze, które zapewnia inteligentne monitorowanie baz danych, które obejmują raporty wydajności, alerty i zalecenia dotyczące ograniczenia. Dane przesyłane strumieniowo do obszaru roboczego Log Analytics mogą być analizowane przy użyciu innych zebranych danych monitorowania, a także umożliwiają korzystanie z innych funkcji Azure Monitor, takich jak alerty i wizualizacje
- **[Event Hubs platformy Azure](#stream-into-event-hubs)**:

  Dane przesyłane strumieniowo do [centrum zdarzeń platformy Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)zapewniają następujące funkcje:

  - **Przesyłaj strumieniowo dzienniki do systemów rejestrowania i telemetrii innej firmy**: Prześlij strumieniowo wszystkie metryki i dzienniki zasobów do jednego centrum zdarzeń w celu przełączenia danych dziennika do narzędzia Siem lub log Analytics innej firmy.
  - **Utwórz niestandardową platformę telemetrii i rejestrowania**: wysoce skalowalna natura publikowania/subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwanie metryk i dzienników zasobów na niestandardową platformę telemetrii. Aby uzyskać szczegółowe informacje [, zobacz Projektowanie i ustalanie rozmiaru globalnej platformy telemetrii w usłudze Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
  - **Wyświetl informacje o kondycji usługi przez przesyłanie strumieniowe danych do Power BI**: Użyj Event Hubs, Stream Analytics i Power BI do przekształcania danych diagnostycznych na szczegółowe informacje w czasie rzeczywistym w ramach usług platformy Azure. Zobacz [Stream Analytics i Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym na potrzeby przesyłania strumieniowego danych](../../stream-analytics/stream-analytics-power-bi-dashboard.md) w celu uzyskania szczegółowych informacji dotyczących tego rozwiązania.
- **[Usługa Azure Storage](#stream-into-azure-storage)**:

  Dane przesyłane strumieniowo do [usługi Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) umożliwiają archiwizowanie ogromnych ilości danych telemetrycznych diagnostyki w przypadku części kosztów poprzednich dwóch opcji przesyłania strumieniowego.

To dane telemetryczne diagnostyki przesyłane do jednego z tych miejsc docelowych mogą służyć do oceny użycia zasobów i statystyk wykonywania zapytań w celu ułatwienia monitorowania wydajności.

![Diagram przedstawia wiele baz danych i baz danych SQL w zarządzanych wystąpieniach wysyłających dane telemetryczne do Diagnostyka Azure, które przesyłają informacje do Azure SQL Analytics, centrum zdarzeń i magazyn.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Włącz i skonfiguruj eksport strumieniowy danych telemetrycznych diagnostyki

Za pomocą jednej z następujących metod można włączyć metryki i rejestrowanie telemetrii diagnostyki oraz zarządzać nimi:

- Azure Portal
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Interfejs API REST usługi Azure Monitor
- Szablon usługi Azure Resource Manager

> [!NOTE]
> Aby włączyć przesyłanie strumieniowe danych telemetrycznych usługi Security log w dzienniku inspekcji, zobacz [Konfigurowanie inspekcji dla usługi Database](./auditing-overview.md#setup-auditing) i [dzienników inspekcji w dziennikach Azure Monitor i Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Konfiguruj eksport przesyłania danych telemetrycznych diagnostyki

Możesz użyć menu **Ustawienia diagnostyczne** w Azure Portal, aby włączyć i skonfigurować przesyłanie strumieniowe telemetrii diagnostyki. Ponadto można użyć programu PowerShell, [interfejsu](/rest/api/monitor/diagnosticsettings)wiersza polecenia platformy Azure oraz [szablonów Menedżer zasobów](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md) w celu skonfigurowania przesyłania strumieniowego danych telemetrycznych diagnostyki. Można ustawić następujące miejsca docelowe, aby przesyłać strumieniowo dane telemetryczne diagnostyki: Azure Storage, Azure Event Hubs i Azure Monitor logs.

> [!IMPORTANT]
> Eksport przesyłania strumieniowego diagnostyki danych diagnostycznych nie jest domyślnie włączony.

Wybierz jedną z następujących kart, aby uzyskać wskazówki krok po kroku dotyczące konfigurowania eksportu danych telemetrycznych diagnostyki w Azure Portal i skryptów na potrzeby wykonywania tych samych kroków przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Pule elastyczne w Azure SQL Database

Można skonfigurować zasób puli elastycznej w celu zebrania następujących danych telemetrycznych diagnostyki:

| Zasób | Monitorowanie telemetrii |
| :------------------- | ------------------- |
| **Pula elastyczna** | [Metryki podstawowe](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) zawierają wartości procentowe liczby jednostek EDTU/procesora, wartość EDTU/procesor CPU, procent odczytania danych fizycznych, procent zapisu w dzienniku, procent sesji, procent pracowników, magazyn, procent magazynu, limit magazynu i procent magazynu XTP. |

Aby skonfigurować strumieniowe dane telemetryczne diagnostyki dla pul elastycznych i baz danych w puli, należy oddzielnie skonfigurować osobno:

- Włącz przesyłanie strumieniowe danych telemetrycznych diagnostyki dla puli elastycznej
- Włącz przesyłanie strumieniowe telemetrii diagnostyki dla każdej bazy danych w puli elastycznej

Kontener elastycznej puli ma własne dane telemetryczne oddzielone od poszczególnych danych telemetrycznych w puli.

Aby włączyć przesyłanie strumieniowe danych telemetrycznych diagnostyki dla zasobu puli elastycznej, wykonaj następujące kroki:

1. Przejdź do zasobu **puli elastycznej** w Azure Portal.
2. Wybierz pozycję **Ustawienia diagnostyki**.
3. Wybierz opcję **Włącz diagnostykę** , jeśli nie istnieją poprzednie ustawienia lub wybierz opcję **Edytuj ustawienie** , aby edytować poprzednie ustawienie.

   ![Włączanie diagnostyki dla pul elastycznych](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Wprowadź nazwę ustawienia dla własnego odwołania.
5. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwizuj na koncie magazynu**, **przesyła strumieniowo do centrum zdarzeń** lub **wysyłając do log Analytics**.
6. W przypadku usługi log Analytics wybierz pozycję **Konfiguruj** i Utwórz nowy obszar roboczy, wybierając pozycję **+ Utwórz nowy obszar roboczy** lub wybierz istniejący obszar roboczy.
7. Zaznacz pole wyboru dla telemetrii diagnostyki puli elastycznej: metryki **podstawowe** .
   ![Konfigurowanie diagnostyki dla pul elastycznych](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Wybierz pozycję **Zapisz**.
9. Ponadto należy skonfigurować przesyłanie strumieniowe telemetrii diagnostyki dla każdej bazy danych w puli elastycznej, którą chcesz monitorować, wykonując czynności opisane w następnej sekcji.

> [!IMPORTANT]
> Oprócz konfigurowania telemetrii diagnostyki dla puli elastycznej należy również skonfigurować telemetrię diagnostyki dla każdej bazy danych w puli elastycznej.

### <a name="databases-in-azure-sql-database"></a>Bazy danych w Azure SQL Database

Można skonfigurować zasób bazy danych, aby zebrać następujące dane telemetryczne diagnostyki:

| Zasób | Monitorowanie telemetrii |
| :------------------- | ------------------- |
| **Baza danych z pojedynczym lub pulą** | [Metryki podstawowe](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) obejmują wartość PROCENTową jednostek DTU, użycie jednostek DTU, limit jednostek DTU, wartość procentową procentowej odczytania danych fizycznych, procent zapisu w dzienniku, pomyślne/nieudane/zablokowane przez połączenia zapory, wartość procentowa, procent procesów roboczych, magazyn, procent magazynu, procent magazynu XTP i zakleszczenia. |

Aby włączyć przesyłanie strumieniowe telemetrii diagnostyki dla jednej bazy danych lub w puli, wykonaj następujące kroki:

1. Przejdź do zasobu usługi Azure **SQL Database** .
2. Wybierz pozycję **Ustawienia diagnostyki**.
3. Wybierz opcję **Włącz diagnostykę** , jeśli nie istnieją poprzednie ustawienia lub wybierz opcję **Edytuj ustawienie** , aby edytować poprzednie ustawienie. Do telemetrii diagnostyki strumienia można utworzyć maksymalnie trzy połączenia równoległe.
4. Wybierz pozycję **Dodaj ustawienie diagnostyczne** , aby skonfigurować równoległe przesyłanie strumieniowe danych diagnostycznych do wielu zasobów.

   ![Włącz diagnostykę dla baz danych z pojedynczym i pulą](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Wprowadź nazwę ustawienia dla własnego odwołania.
6. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwizuj na koncie magazynu**, **przesyła strumieniowo do centrum zdarzeń** lub **wysyłając do log Analytics**.
7. W przypadku standardowego środowiska monitorowania opartego na zdarzeniach zaznacz następujące pola wyboru dla telemetrii dzienników diagnostyki bazy danych: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Błędy**, **DatabaseWaitStatistics**, **limity czasu**, **bloki** i **zakleszczenia**.
8. W przypadku zaawansowanego, opartego na minutę środowiska monitorowania zaznacz pole wyboru dla metryk **podstawowych** .

   ![Konfigurowanie diagnostyki dla Azure SQL Database](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Wybierz pozycję **Zapisz**.
10. Powtórz te kroki dla każdej bazy danych, która ma być monitorowana.

> [!TIP]
> Powtórz te kroki dla każdej bazy danych pojedynczej i puli, która ma być monitorowana.

### <a name="instances-in-azure-sql-managed-instance"></a>Wystąpienia w wystąpieniu zarządzanym usługi Azure SQL

Można skonfigurować zasób wystąpienia zarządzanego, aby zebrać następujące dane telemetryczne diagnostyki:

| Zasób | Monitorowanie telemetrii |
| :------------------- | ------------------- |
| **Wystąpienie zarządzane** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) zawiera Licznik rdzeni wirtualnych, średni procent procesora CPU, żądania we/wy, bajty do odczytu/zapisu, zarezerwowane miejsce do magazynowania i miejsce do magazynowania. |

Aby skonfigurować strumieniowe dane telemetryczne diagnostyki dla wystąpień zarządzanych i baz danych wystąpień, należy oddzielnie skonfigurować poszczególne:

- Włącz przesyłanie strumieniowe danych telemetrycznych diagnostyki dla wystąpienia zarządzanego
- Włącz przesyłanie strumieniowe telemetrii diagnostyki dla każdej bazy danych wystąpienia

Kontener wystąpienia zarządzanego ma własne dane telemetryczne oddzielone od danych telemetrycznych z poszczególnych wystąpień.

Aby włączyć przesyłanie strumieniowe danych telemetrycznych diagnostyki dla zasobu wystąpienia zarządzanego, wykonaj następujące czynności:

1. Przejdź do zasobu **wystąpienia zarządzanego** w Azure Portal.
2. Wybierz pozycję **Ustawienia diagnostyki**.
3. Wybierz opcję **Włącz diagnostykę** , jeśli nie istnieją poprzednie ustawienia lub wybierz opcję **Edytuj ustawienie** , aby edytować poprzednie ustawienie.

   ![Włącz diagnostykę dla wystąpienia zarządzanego](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Wprowadź nazwę ustawienia dla własnego odwołania.
5. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwizuj na koncie magazynu**, **przesyła strumieniowo do centrum zdarzeń** lub **wysyłając do log Analytics**.
6. W przypadku usługi log Analytics wybierz pozycję **Konfiguruj** i Utwórz nowy obszar roboczy, wybierając pozycję **+ Utwórz nowy obszar roboczy** lub korzystając z istniejącego obszaru roboczego.
7. Zaznacz pole wyboru dla telemetrii diagnostyki wystąpienia: **ResourceUsageStats**.

   ![Konfigurowanie diagnostyki dla wystąpienia zarządzanego](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Wybierz pozycję **Zapisz**.
9. Ponadto należy skonfigurować przesyłanie strumieniowe telemetrii diagnostyki dla każdej bazy danych wystąpienia w zarządzanym wystąpieniu, które chcesz monitorować, wykonując kroki opisane w następnej sekcji.

> [!IMPORTANT]
> Oprócz konfigurowania telemetrii diagnostyki dla wystąpienia zarządzanego należy również skonfigurować telemetrię diagnostyki dla każdej bazy danych wystąpienia.

### <a name="databases-in-azure-sql-managed-instance"></a>Bazy danych w wystąpieniu zarządzanym Azure SQL

Można skonfigurować zasób bazy danych wystąpienia, aby zebrać następujące dane telemetryczne diagnostyki:

| Zasób | Monitorowanie telemetrii |
| :------------------- | ------------------- |
| **Baza danych wystąpienia** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) zawiera Licznik rdzeni wirtualnych, średni procent procesora CPU, żądania we/wy, bajty do odczytu/zapisu, zarezerwowane miejsce do magazynowania i miejsce do magazynowania. |

Aby włączyć przesyłanie strumieniowe telemetrii diagnostyki dla bazy danych wystąpienia, wykonaj następujące kroki:

1. Przejdź do zasobu **wystąpienia bazy danych** w ramach wystąpienia zarządzanego.
2. Wybierz pozycję **Ustawienia diagnostyki**.
3. Wybierz opcję **Włącz diagnostykę** , jeśli nie istnieją poprzednie ustawienia lub wybierz opcję **Edytuj ustawienie** , aby edytować poprzednie ustawienie.
   - Można utworzyć maksymalnie trzy (3) połączenia równoległe do telemetrii diagnostyki strumienia.
   - Wybierz pozycję **+ Dodaj ustawienie diagnostyczne** , aby skonfigurować równoległe przesyłanie strumieniowe danych diagnostycznych do wielu zasobów.

   ![Włącz diagnostykę baz danych wystąpień](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Wprowadź nazwę ustawienia dla własnego odwołania.
5. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwizuj na koncie magazynu**, **przesyła strumieniowo do centrum zdarzeń** lub **wysyłając do log Analytics**.
6. Zaznacz pola wyboru dla telemetrii diagnostyki bazy danych: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** i **Errors**.
   ![Konfigurowanie diagnostyki dla baz danych wystąpień](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Wybierz pozycję **Zapisz**.
8. Powtórz te kroki dla każdej bazy danych wystąpienia, która ma być monitorowana.

> [!TIP]
> Powtórz te kroki dla każdej bazy danych wystąpienia, która ma być monitorowana.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Można włączyć funkcję rejestrowania metryk i diagnostyki przy użyciu programu PowerShell.

- Aby włączyć magazyn metryk i dzienników zasobów na koncie magazynu, użyj tego polecenia:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  Identyfikator konta magazynu jest IDENTYFIKATORem zasobu dla docelowego konta magazynu.

- Aby włączyć przesyłanie strumieniowe metryk i dzienników zasobów do centrum zdarzeń, użyj tego polecenia:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Identyfikator reguły usługi Azure Service Bus jest ciągiem o następującym formacie:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Aby włączyć wysyłanie metryk i dzienników zasobów do obszaru roboczego Log Analytics, użyj tego polecenia:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Można połączyć te parametry, aby uaktywnić wiele opcji danych wyjściowych.

**Aby skonfigurować wiele zasobów platformy Azure**

Aby obsługiwać wiele subskrypcji, Użyj skryptu programu PowerShell z okna [Włączanie rejestrowania metryk zasobów platformy Azure przy użyciu programu PowerShell](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell).

Podaj identyfikator zasobu obszaru roboczego \<$WSID\> jako parametr podczas wykonywania skryptu `Enable-AzureRMDiagnostics.ps1` w celu wysyłania danych diagnostycznych z wielu zasobów do obszaru roboczego.

- Aby uzyskać identyfikator obszaru roboczego \<$WSID\> dla danych diagnostycznych, użyj następującego skryptu:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Zamień na \<subID\> Identyfikator subskrypcji, \<RG_NAME\> nazwę grupy zasobów i \<WS_NAME\> nazwę obszaru roboczego.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz włączyć funkcję rejestrowania metryk i diagnostyki przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Skrypty umożliwiające włączenie rejestrowania diagnostycznego są obsługiwane dla interfejsu wiersza polecenia platformy Azure w wersji 1.0. Interfejs wiersza polecenia platformy Azure w wersji 2.0 jest w tej chwili nieobsługiwany.

- Aby włączyć magazyn metryk i dzienników zasobów na koncie magazynu, użyj tego polecenia:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  Identyfikator konta magazynu jest IDENTYFIKATORem zasobu dla docelowego konta magazynu.

- Aby włączyć przesyłanie strumieniowe metryk i dzienników zasobów do centrum zdarzeń, użyj tego polecenia:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  Identyfikator reguły Service Bus jest ciągiem o tym formacie:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Aby włączyć wysyłanie metryk i dzienników zasobów do obszaru roboczego Log Analytics, użyj tego polecenia:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Można połączyć te parametry, aby uaktywnić wiele opcji danych wyjściowych.

---

## <a name="stream-into-sql-analytics"></a>Przesyłanie strumieniowe do usługi SQL Analytics

Azure SQL Database i metryki wystąpienia zarządzanego usługi Azure SQL oraz dzienniki zasobów przesyłane strumieniowo do obszaru roboczego Log Analytics mogą być używane przez Azure SQL Analytics. Azure SQL Analytics to rozwiązanie w chmurze, które służy do monitorowania wydajności pojedynczych baz danych, pul elastycznych i baz danych w puli oraz wystąpień zarządzanych i baz danych wystąpień na dużą skalę i w wielu subskrypcjach. Może pomóc zbierać i wizualizować metryki wydajności oraz wbudowaną analizę na potrzeby rozwiązywania problemów z wydajnością.

![Przegląd Azure SQL Analytics](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Omówienie instalacji

Kolekcję baz danych i kolekcji baz danych można monitorować za pomocą Azure SQL Analytics, wykonując następujące czynności:

1. Utwórz rozwiązanie Azure SQL Analytics w portalu Azure Marketplace.
2. Utwórz obszar roboczy Log Analytics w rozwiązaniu.
3. Skonfiguruj bazy danych do przesyłania strumieniowego telemetrii diagnostyki do obszaru roboczego.

Eksport przesyłania strumieniowego tej diagnostyki diagnostycznej można skonfigurować przy użyciu wbudowanej opcji **Wyślij do log Analytics** na karcie Ustawienia diagnostyki w Azure Portal. Możesz również włączyć przesyłanie strumieniowe do obszaru roboczego Log Analytics przy użyciu ustawień diagnostycznych za pośrednictwem [poleceń cmdlet programu PowerShell](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), interfejsu [wiersza polecenia platformy Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), [Azure Monitor API REST](/rest/api/monitor/diagnosticsettings)lub [szablonów Menedżer zasobów](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Tworzenie zasobu Azure SQL Analytics

1. Wyszukaj Azure SQL Analytics w witrynie Azure Marketplace i wybierz ją.

   ![Wyszukaj Azure SQL Analytics w portalu](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Wybierz pozycję **Utwórz** na ekranie przegląd rozwiązania.

3. Wypełnij formularz Azure SQL Analytics informacjami o dodatkowych informacjach, które są wymagane: Nazwa obszaru roboczego, subskrypcja, Grupa zasobów, lokalizacja i warstwa cenowa.

   ![Konfigurowanie Azure SQL Analytics w portalu](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Wybierz **przycisk OK** , aby potwierdzić, a następnie wybierz pozycję **Utwórz**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Konfigurowanie zasobu do rejestrowania metryk i dzienników zasobów

Należy oddzielnie skonfigurować przesyłanie strumieniowe telemetrii diagnostyki dla jednej i puli baz danych, pul elastycznych, wystąpień zarządzanych i baz danych wystąpień. Najprostszym sposobem skonfigurowania miejsca, w którym rejestrowane są metryki zasobów, jest użycie Azure Portal. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie przesyłania strumieniowego danych telemetrycznych diagnostyki](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Używanie Azure SQL Analytics do monitorowania i generowania alertów

Usługi SQL Analytics można używać jako hierarchicznego pulpitu nawigacyjnego do wyświetlania zasobów bazy danych.

- Aby dowiedzieć się, jak używać Azure SQL Analytics, zobacz [monitorowanie za pomocą usługi SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Aby dowiedzieć się, jak skonfigurować alerty dla usługi SQL Analytics, zobacz [tworzenie alertów dotyczących baz danych, pul elastycznych i wystąpień zarządzanych](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Przesyłanie strumieniowe do usługi Event Hubs

Metryki i dzienniki zasobów wystąpienia zarządzanego usługi Azure SQL można przesyłać Azure SQL Database strumieniowo do Event Hubs przy użyciu wbudowanego **strumienia do opcji centrum zdarzeń** w Azure Portal. Identyfikator reguły Service Bus można również włączyć przy użyciu ustawień diagnostycznych za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST Azure Monitor. Upewnij się, że centrum zdarzeń znajduje się w tym samym regionie, w którym znajduje się baza danych i serwer.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Co zrobić z metrykami i dziennikami zasobów w Event Hubs

Gdy wybrane dane zostaną przesłane do Event Hubs, jest to jeden krok bliższy do włączenia zaawansowanych scenariuszy monitorowania. Event Hubs działa jako drzwi czołowe dla potoku zdarzeń. Po zebraniu danych do centrum zdarzeń można je przekształcać i przechowywać za pomocą dostawcy analiz w czasie rzeczywistym lub karty magazynu. Event Hubs oddziela produkcję strumienia zdarzeń od użycia tych zdarzeń. W ten sposób odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z ich własnymi harmonogramami. Aby uzyskać więcej informacji na temat Event Hubs, zobacz:

- [Co to jest platforma Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Metryki przesyłane strumieniowo można używać w Event Hubs do:

- **Wyświetl kondycję usługi przez przesyłanie strumieniowe danych na gorąco do Power BI**

  Za pomocą Event Hubs, Stream Analytics i Power BI można łatwo przekształcać metryki i dane diagnostyczne na szczegółowe informacje w czasie rzeczywistym w ramach usług platformy Azure. Aby zapoznać się z omówieniem sposobu konfigurowania centrum zdarzeń, przetwarzania danych przy użyciu Stream Analytics i używania Power BI jako danych wyjściowych, zobacz [Stream Analytics i Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Strumieniowe przesyłanie dzienników do strumieni danych i danych telemetrycznych innych firm**

  Za pomocą Event Hubs Streaming można uzyskać metryki i dzienniki zasobów w różnych rozwiązaniach do monitorowania i analizy dzienników innych firm.

- **Tworzenie niestandardowej platformy telemetrii i rejestrowania**

  Czy masz już utworzoną niestandardową platformę telemetrii lub rozważamy kompilację? Wysoce skalowalny charakter publikowania/subskrybowania Event Hubs umożliwia elastyczne pozyskiwanie metryk i dzienników zasobów. Zobacz [Rosanova, aby korzystać z Event Hubs na platformie telemetrii na skalę globalną](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Przesyłanie strumieniowe do usługi Azure Storage

Metryki i dzienniki zasobów można przechowywać w usłudze Azure Storage przy użyciu wbudowanego Archiwum w ramach opcji **konta magazynu** w Azure Portal. Magazyn można również włączyć przy użyciu ustawień diagnostycznych za pośrednictwem poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub usługi Azure Monitor API REST.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schemat metryk i dzienników zasobów na koncie magazynu

Po skonfigurowaniu metryk i kolekcji dzienników zasobów kontener magazynu jest tworzony na koncie magazynu wybranym podczas udostępniania pierwszego wiersza danych. Struktura obiektów BLOB jest:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Lub, po prostu:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład nazwa obiektu BLOB dla podstawowych metryk może być:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Nazwa obiektu BLOB do przechowywania danych z puli elastycznej wygląda następująco:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Zasady przechowywania danych i Cennik

W przypadku wybrania Event Hubs lub konta magazynu można określić zasady przechowywania. Te zasady usuwają dane, które są starsze niż wybrany okres. W przypadku określenia Log Analytics zasady przechowywania są zależne od wybranej warstwy cenowej. W takim przypadku zapewnione bezpłatne jednostki pozyskiwania danych umożliwiają bezpłatne monitorowanie kilku baz danych w każdym miesiącu. Każde użycie danych telemetrycznych diagnostyki przekraczające wolne jednostki może ponieść koszty.

> [!IMPORTANT]
> Aktywne bazy danych z większymi obciążeniami pobierają więcej danych niż bezczynne bazy danych. Aby uzyskać więcej informacji, zobacz [Cennik usługi log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Jeśli używasz Azure SQL Analytics, możesz monitorować użycie pozyskiwania danych, wybierając **obszar roboczy OMS** w menu nawigacji Azure SQL Analytics, a następnie wybierając pozycję **użycie** i **szacowane koszty**.

## <a name="metrics-and-logs-available"></a>Dostępne metryki i dzienniki

Dane telemetryczne monitorowania dostępne dla pojedynczych baz danych, baz danych w puli, pul elastycznych, wystąpienia zarządzanego i baz danych wystąpień zostały udokumentowane w tej części artykułu. Zebrane dane telemetryczne monitorowania w usłudze SQL Analytics mogą służyć do własnej niestandardowej analizy i opracowywania aplikacji przy użyciu języka [zapytań dzienników Azure monitor](../../azure-monitor/logs/get-started-queries.md) .

### <a name="basic-metrics"></a>Metryki podstawowe

Szczegółowe informacje na temat podstawowych metryk według zasobów można znaleźć w poniższych tabelach.

> [!NOTE]
> Podstawowa opcja metryk była wcześniej znana jako wszystkie metryki. Wprowadzona zmiana dotyczyła tylko nazewnictwa i nie wprowadzono żadnych zmian w monitorowanych metrykach. Ta zmiana została zainicjowana, aby umożliwić wprowadzenie dodatkowych kategorii metryk w przyszłości.

#### <a name="basic-metrics-for-elastic-pools"></a>Podstawowe metryki dla pul elastycznych

|**Zasób**|**Metryki**|
|---|---|
|Pula elastyczna|procentowa wartość eDTU, użyta wartość eDTU, limit liczby jednostek eDTU, procent użycia procesora, procent odczytu danych fizycznych, procent zapisu w dzienniku, procent sesji, procent procesów roboczych, magazyn, procent magazynu, limit magazynu, procent magazynu XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Podstawowe metryki dla pojedynczych i w puli baz danych

|**Zasób**|**Metryki**|
|---|---|
|Baza danych z pojedynczym i pulą|Procent jednostek DTU, użytych jednostek DTU, limit jednostek DTU, procent użycia procesora, procent odczytu danych fizycznych, procent zapisu w dzienniku, pomyślne/nieudane/zablokowane przez połączenia zapory |

### <a name="advanced-metrics"></a>Metryki zaawansowane

Szczegółowe informacje na temat zaawansowanych metryk można znaleźć w poniższej tabeli.

|**Metryka**|**Nazwa wyświetlana metryki**|**Opis**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|Podstawowy procent procesu SQL|Procent użycia procesora CPU dla procesu SQL, mierzony przez system operacyjny.|
|sqlserver_process_memory_percent<sup>1</sup> |Procent pamięci procesu SQL|Procent użycia pamięci dla procesu SQL, mierzony przez system operacyjny.|
|tempdb_data_size<sup>2</sup>| Rozmiar pliku danych tempdb kilobajtów |Rozmiar pliku danych tempdb kilobajtów.|
|tempdb_log_size<sup>2</sup>| Rozmiar pliku dziennika bazy danych tempdb kilobajtów |Rozmiar pliku dziennika bazy danych tempdb kilobajtów.|
|tempdb_log_used_percent<sup>2</sup>| Użyto dziennika% tempdb |Użyto dziennika bazy danych tempdb.|

<sup>1</sup> ta Metryka jest dostępna dla baz danych korzystających z modelu zakupów rdzeń wirtualny o wartości 2 rdzeni wirtualnych i wyższych lub 200 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU.

<sup>Ta</sup> Metryka jest dostępna dla baz danych korzystających z modelu zakupów rdzeń wirtualny o wartości 2 rdzeni wirtualnych i wyższych lub 200 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU. Ta Metryka jest obecnie niedostępna dla baz danych lub magazynów danych w skali.

### <a name="basic-logs"></a>Dzienniki podstawowe

Szczegółowe informacje o telemetrii dostępne dla wszystkich dzienników zostały udokumentowane w poniższych tabelach. Aby uzyskać więcej informacji, zobacz [obsługiwane dane telemetryczne diagnostyki](#diagnostic-telemetry-for-export).

#### <a name="resource-usage-stats-for-managed-instances"></a>Statystyka użycia zasobów dla wystąpień zarządzanych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: ResourceUsageStats |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: MANAGEDINSTANCES |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa wystąpienia zarządzanego |
|ResourceId|Identyfikator URI zasobu |
|SKU_s|Jednostka SKU produktu wystąpienia zarządzanego SQL |
|virtual_core_count_s|Liczba dostępnych rdzeni wirtualnych |
|avg_cpu_percent_s|Średni procent procesora CPU |
|reserved_storage_mb_s|Zarezerwowana pojemność magazynu w wystąpieniu zarządzanym |
|storage_space_used_mb_s|Używany magazyn w wystąpieniu zarządzanym |
|io_requests_s|Liczba operacji we/wy |
|io_bytes_read_s|Odczytane bajty IOPS |
|io_bytes_written_s|Zapisano bajtów IOPS |

#### <a name="query-store-runtime-statistics"></a>Statystyka środowiska uruchomieniowego magazynu zapytań

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreRuntimeStatistics |
|OperationName|Nazwa operacji. Zawsze: QueryStoreRuntimeStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|query_hash_s|Skrót zapytania |
|query_plan_hash_s|Wartość skrótu planu zapytania |
|statement_sql_handle_s|Dojście SQL instrukcji |
|interval_start_time_d|Rozpocznij DateTimeOffset dla interwału w liczbie taktów od 1900-1-1 |
|interval_end_time_d|Końcowa wartość DateTimeOffset interwału wyrażona w liczbie taktów od 1900-1-1 |
|logical_io_writes_d|Łączna liczba operacji zapisu logicznych operacji we/wy |
|max_logical_io_writes_d|Maksymalna liczba logicznych zapisów we/wy na wykonanie |
|physical_io_reads_d|Łączna liczba odczytów fizycznych operacji we/wy |
|max_physical_io_reads_d|Maksymalna liczba logicznych odczytów we/wy na wykonanie |
|logical_io_reads_d|Łączna liczba odczytów logicznych operacji we/wy |
|max_logical_io_reads_d|Maksymalna liczba logicznych odczytów we/wy na wykonanie |
|execution_type_d|Typ wykonywania |
|count_executions_d|Liczba wykonań zapytania |
|cpu_time_d|Łączny czas procesora CPU zużyty przez zapytanie w mikrosekundach |
|max_cpu_time_d|Maksymalny czas użycia procesora CPU przez pojedyncze wykonanie w mikrosekundach |
|dop_d|Suma stopni równoległości |
|max_dop_d|Maksymalny stopień równoległości używany do pojedynczego wykonywania |
|rowcount_d|Łączna liczba zwróconych wierszy |
|max_rowcount_d|Maksymalna liczba wierszy zwróconych w ramach pojedynczego wykonania |
|query_max_used_memory_d|Łączna ilość pamięci używanej w KB |
|max_query_max_used_memory_d|Maksymalna ilość pamięci używanej przez pojedyncze wykonanie w KB |
|duration_d|Łączny czas wykonywania w mikrosekundach |
|max_duration_d|Maksymalny czas wykonywania pojedynczego wykonania |
|num_physical_io_reads_d|Łączna liczba odczytów fizycznych |
|max_num_physical_io_reads_d|Maksymalna liczba odczytów fizycznych na wykonanie |
|log_bytes_used_d|Łączna ilość użytych bajtów dziennika |
|max_log_bytes_used_d|Maksymalna ilość bajtów dziennika użyta na wykonanie |
|query_id_d|Identyfikator zapytania w magazynie zapytań |
|plan_id_d|Identyfikator planu w magazynie zapytań |

Dowiedz się więcej o [danych statystyk środowiska uruchomieniowego magazynu zapytań](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Statystyka oczekiwania magazynu zapytań

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreWaitStatistics |
|OperationName|Nazwa operacji. Zawsze: QueryStoreWaitStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|wait_category_s|Kategoria oczekiwania |
|is_parameterizable_s|Czy zapytanie jest można sparametryzować |
|statement_type_s|Typ instrukcji |
|statement_key_hash_s|Skrót klucza instrukcji |
|exec_type_d|Typ wykonywania |
|total_query_wait_time_ms_d|Łączny czas oczekiwania zapytania w określonej kategorii oczekiwania |
|max_query_wait_time_ms_d|Maksymalny czas oczekiwania zapytania w poszczególnym wykonaniu dla określonej kategorii oczekiwania |
|query_param_type_d|0 |
|query_hash_s|Skrót zapytania w magazynie zapytań |
|query_plan_hash_s|Wartość skrótu planu zapytania w magazynie zapytań |
|statement_sql_handle_s|Dojście instrukcji w magazynie zapytań |
|interval_start_time_d|Rozpocznij DateTimeOffset dla interwału w liczbie taktów od 1900-1-1 |
|interval_end_time_d|Końcowa wartość DateTimeOffset interwału wyrażona w liczbie taktów od 1900-1-1 |
|count_executions_d|Liczba wykonań zapytania |
|query_id_d|Identyfikator zapytania w magazynie zapytań |
|plan_id_d|Identyfikator planu w magazynie zapytań |

Dowiedz się więcej na temat [danych statystycznych oczekiwania magazynu zapytań](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Zestaw danych błędów

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: Błędy |
|OperationName|Nazwa operacji. Zawsze: ErrorEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|Komunikat|Komunikat o błędzie w postaci zwykłego tekstu |
|user_defined_b|Jest błędem bitowym zdefiniowanym przez użytkownika |
|error_number_d|Kod błędu |
|Ważność|Ważność błędu |
|state_d|Stan błędu |
|query_hash_s|Wartość skrótu zapytania zakończonego niepowodzeniem, jeśli jest dostępna |
|query_plan_hash_s|Wartość skrótu planu zapytania zakończonego niepowodzeniem, jeśli jest dostępna |

Dowiedz się więcej o [komunikatach o błędach SQL](/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Zestaw danych statystyki oczekiwania bazy danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: DatabaseWaitStatistics |
|OperationName|Nazwa operacji. Zawsze: DatabaseWaitStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|wait_type_s|Nazwa typu oczekiwania |
|start_utc_date_t [UTC]|Mierzony czas rozpoczęcia okresu |
|end_utc_date_t [UTC]|Czas zakończenia okresu mierzoną |
|delta_max_wait_time_ms_d|Maksymalny czas oczekiwania na wykonanie |
|delta_signal_wait_time_ms_d|Łączny czas oczekiwania sygnałów |
|delta_wait_time_ms_d|Łączny czas oczekiwania w okresie |
|delta_waiting_tasks_count_d|Liczba oczekujących zadań |

Dowiedz się więcej na temat [statystyk oczekiwania bazy danych](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Zestaw danych przekroczenia czasu

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: przekroczenia limitu czasu |
|OperationName|Nazwa operacji. Zawsze: TimeoutEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|error_state_d|Kod stanu błędu |
|query_hash_s|Skrót zapytania, jeśli jest dostępny |
|query_plan_hash_s|Wartość skrótu planu zapytania, jeśli jest dostępna |

#### <a name="blockings-dataset"></a>Zestaw danych blokad

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: bloki |
|OperationName|Nazwa operacji. Zawsze: BlockEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|lock_mode_s|Tryb blokowania używany przez zapytanie |
|resource_owner_type_s|Właściciel blokady |
|blocked_process_filtered_s|Zablokowany plik XML raportu procesu |
|duration_d|Czas trwania blokady w mikrosekundach |

#### <a name="deadlocks-dataset"></a>Zestaw danych zakleszczenii

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC] |Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: zakleszczenia |
|OperationName|Nazwa operacji. Zawsze: DeadlockEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|deadlock_xml_s|Zakleszczony raport XML |

#### <a name="automatic-tuning-dataset"></a>Zestaw danych dostrajania automatycznego

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Server |
|Kategoria|Nazwa kategorii. Zawsze: AutomaticTuning |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/bazy danych |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|LogicalDatabaseName_s|Nazwa bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej dla bazy danych (jeśli istnieje) |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|RecommendationHash_s|Unikatowy skrót zalecenia automatycznego dostrajania |
|OptionName_s|Operacja dostrajania automatycznego |
|Schema_s|Schemat bazy danych |
|Table_s|Tabela, której dotyczy |
|IndexName_s|Nazwa indeksu |
|IndexColumns_s|Nazwa kolumny |
|IncludedColumns_s|Uwzględnione kolumny |
|EstimatedImpact_s|Szacowany wpływ JSON zalecenia dostrajania automatycznego |
|Event_s|Typ zdarzenia dostrajania automatycznego |
|Timestamp_t|Ostatnia aktualizacja sygnatury czasowej |

#### <a name="intelligent-insights-dataset"></a>Zestaw danych Intelligent Insights

Dowiedz się więcej o [formacie dziennika Intelligent Insights](intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć rejestrowanie i zrozumieć metryki i kategorie dzienników obsługiwane przez różne usługi platformy Azure, zobacz:

- [Przegląd metryk w Microsoft Azure](../../azure-monitor/data-platform.md)
- [Omówienie dzienników platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md)

Aby dowiedzieć się więcej na temat Event Hubs, Przeczytaj:

- [Co to jest usługa Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Aby dowiedzieć się, jak skonfigurować alerty na podstawie danych telemetrycznych z usługi log Analytics, zobacz:

- [Tworzenie alertów dla Azure SQL Database i wystąpienia zarządzanego Azure SQL](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)