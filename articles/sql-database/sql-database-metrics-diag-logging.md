---
title: Konfigurowanie eksportu danych i dzienników zasobów przesyłania strumieniowego
description: Dowiedz się, jak skonfigurować eksport strumieniowy metryk i dzienników zasobów, w tym inteligentną analizę diagnostyczną z usługi Azure SQL Database do wybranego miejsca docelowego do przechowywania informacji o wykorzystaniu zasobów i statystykach wykonywania zapytań.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: 288d4e4d0c5faa6bb2b51451fb36bbb6d666c9eb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683177"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>Konfigurowanie eksportu przesyłania strumieniowego danych telemetrycznych diagnostycznych usługi Azure SQL Database

W tym artykule dowiesz się o metryki wydajności i dzienniki zasobów dla usługi Azure SQL Database, które można wyeksportować do jednego z kilku miejsc docelowych do analizy. Dowiesz się, jak skonfigurować eksport przesyłania strumieniowego tej danych telemetrycznych diagnostycznych za pośrednictwem witryny Azure portal, powershell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i szablonów usługi Azure Resource Manager.

Dowiesz się również o miejscach docelowych, do których można przesyłać strumieniowo tę telemetrię diagnostyczną i jak wybrać jedną z tych opcji. Dostępne opcje to:

- [Analiza dzienników i analiza SQL](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>Dane telemetryczne diagnostyczne do eksportowania bazy danych SQL usługi Azure

Najważniejsze wśród danych telemetrycznych diagnostycznych, które można wyeksportować, jest dziennik intelligent insights (SQLInsights). [Usługa Intelligent Insights](sql-database-intelligent-insights.md) wykorzystuje wbudowaną inteligencję do ciągłego monitorowania użycia bazy danych za pomocą sztucznej inteligencji i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność. Po wykryciu przeprowadzana jest szczegółowa analiza, która generuje dziennik inteligentnej analizy z inteligentną oceną problemu. Ocena ta składa się z analizy głównej przyczyny problemu z wydajnością bazy danych i, w miarę możliwości, zalecenia dotyczące poprawy wydajności. Należy skonfigurować eksport przesyłania strumieniowego tego dziennika, aby wyświetlić jego zawartość.

Oprócz przesyłania strumieniowego eksportu dziennika inteligentnej usługi Insights można również wyeksportować różne metryki wydajności i dodatkowe dzienniki bazy danych SQL. W poniższej tabeli opisano metryki wydajności i dzienniki zasobów, które można skonfigurować do eksportowania przesyłania strumieniowego do jednego z kilku miejsc docelowych. Tę dane telemetryczne diagnostyczne można skonfigurować dla pojedynczych baz danych, pul elastycznych i puli baz danych oraz zarządzanych wystąpień i baz danych wystąpień.

| Dane telemetryczne diagnostyczne dla baz danych | Obsługa pojedynczej bazy danych i puli baz danych | Obsługa bazy danych wystąpienia zarządzanego |
| :------------------- | ----- | ----- |
| [Podstawowe metryki:](#basic-metrics)Zawiera procent jednostki DTU/CPU, limit jednostki DTU/CPU, procent odczytu danych fizycznych, procent zapisu dziennika, pomyślny/nieudany/zablokowany przez połączenia zapory, procent sesji, procent pracowników, procent pamięci masowej, procent pamięci masowej i procent pamięci XTP. | Tak | Nie |
| [Wystąpienie i aplikacja zaawansowane:](#advanced-metrics)Zawiera dane bazy danych systemu tempdb i rozmiar pliku dziennika i plik dziennika tempdb procent używane. | Tak | Nie |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Zawiera informacje o statystykach środowiska wykonawczego kwerendy, takich jak użycie procesora CPU i statystyki czasu trwania kwerendy. | Tak | Tak |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Zawiera informacje o statystykach oczekiwania na kwerendy (na co czekały zapytania), takich jak procesor, dziennik i blokowanie. | Tak | Tak |
| [Błędy](#errors-dataset): Zawiera informacje o błędach SQL w bazie danych. | Tak | Tak |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Zawiera informacje o tym, ile czasu bazy danych spędził oczekiwania na różnych typach oczekiwania. | Tak | Nie |
| [Limity czasu:](#time-outs-dataset)Zawiera informacje o przesiąkłach w bazie danych. | Tak | Nie |
| [Bloki](#blockings-dataset): Zawiera informacje o blokowaniu zdarzeń w bazie danych. | Tak | Nie |
| [Zakleszczenia:](#deadlocks-dataset)Zawiera informacje o zdarzeniach zakleszczenia w bazie danych. | Tak | Nie |
| [AutomaticTuning](#automatic-tuning-dataset): Zawiera informacje o zaleceniach automatycznego dostrajania bazy danych. | Tak | Nie |
| [SQLInsights:](#intelligent-insights-dataset)Zawiera inteligentne wgląd w wydajność bazy danych. Aby dowiedzieć się więcej, zobacz [Inteligentna statystyka](sql-database-intelligent-insights.md). | Tak | Tak |

> [!NOTE]
> Nie można skonfigurować ustawień diagnostycznych dla **systemowych baz danych**, takich jak główne, msdb, model, zasób i bazy danych tempdb.

## <a name="streaming-export-destinations"></a>Przesyłanie strumieniowe miejsc docelowych eksportu

Ta telemetria diagnostyczna może być przesyłana strumieniowo do jednego z następujących zasobów platformy Azure do analizy.

- **[Obszar roboczy analizy dzienników:](#stream-into-sql-analytics)**

  Dane przesyłane strumieniowo do [obszaru roboczego usługi Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) mogą być używane przez usługę SQL [Analytics.](../azure-monitor/insights/azure-sql.md) SQL Analytics to rozwiązanie do monitorowania tylko w chmurze, które zapewnia inteligentne monitorowanie baz danych, które zawiera raporty wydajności, alerty i zalecenia dotyczące ograniczania ryzyka. Dane przesyłane strumieniowo do obszaru roboczego usługi Log Analytics mogą być analizowane przy użyciu innych zebranych danych monitorowania, a także umożliwia korzystanie z innych funkcji usługi Azure Monitor, takich jak alerty i wizualizacje
- **[Usługi Azure Event Hubs:](#stream-into-event-hubs)**

  Dane przesyłane strumieniowo do [usługi Azure Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md)zapewniają następujące funkcje:

  - **Dzienniki strumienia do systemów rejestrowania i telemetrii innych firm:** przesyłaj strumieniowo wszystkie metryki i dzienniki zasobów do centrum pojedynczych zdarzeń, aby potokować dane dziennika do narzędzia SIEM lub analizy dzienników innej firmy.
  - **Tworzenie niestandardowej platformy telemetrycznej i rejestrowania:** wysoce skalowalny charakter publikowania i subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwanie metryk i dzienników zasobów na niestandardowej platformie telemetrycznej. Zobacz [projektowanie i tworzenie rozmiaru globalnej platformy telemetrii skali w usłudze Azure Event Hubs, aby](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) uzyskać szczegółowe informacje.
  - **Wyświetlanie kondycji usługi przez przesyłanie strumieniowe danych do usługi Power BI:** korzystanie z centrów zdarzeń, usługi Stream Analytics i usługi Power BI w celu przekształcenia danych diagnostycznych w niemal w czasie rzeczywistym w szczegółowych informacje o usługach platformy Azure. Zobacz [Usługa Stream Analytics i Usługa Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym, aby uzyskać](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) szczegółowe informacje na temat tego rozwiązania.
- **[Usługa Azure Storage](#stream-into-azure-storage)**:

  Dane przesyłane strumieniowo do [usługi Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) umożliwia archiwizowanie ogromnych ilości danych telemetrycznych diagnostycznych za ułamek kosztu poprzednich dwóch opcji przesyłania strumieniowego.

Ta telemetria diagnostyczna przesyłana strumieniowo do jednego z tych miejsc docelowych może służyć do pomiaru wykorzystania zasobów i statystyki wykonywania zapytań w celu łatwiejszego monitorowania wydajności.

![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Włączanie i konfigurowanie eksportu danych diagnostycznych danych telemetrycznych w trybie przesyłania strumieniowego

Można włączyć metryki i rejestrowanie danych telemetrycznych i zarządzać nimi przy użyciu jednej z następujących metod:

- Azure Portal
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Interfejs API REST usługi Azure Monitor
- Szablon usługi Azure Resource Manager

> [!NOTE]
> Aby włączyć przesyłanie strumieniowe danych telemetrycznych zabezpieczeń w dzienniku dziennika inspekcji, zobacz [Konfigurowanie inspekcji bazy danych](sql-database-auditing.md#setup-auditing) i [dzienników inspekcji w dziennikach usługi Azure Monitor i usłudze Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Konfigurowanie eksportu danych telemetrycznych w trybie przesyłania strumieniowego

Można użyć menu **Ustawienia diagnostyki** w witrynie Azure portal, aby włączyć i skonfigurować przesyłanie strumieniowe danych telemetrycznych diagnostycznych. Ponadto można użyć programu PowerShell, interfejsu wiersza polecenia platformy Azure, [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)i [szablonów Menedżera zasobów](../azure-monitor/platform/diagnostic-settings-template.md) do konfigurowania przesyłania strumieniowego danych telemetrycznych diagnostycznych. Można ustawić następujące miejsca docelowe do przesyłania strumieniowego danych telemetrycznych diagnostycznych: Usługi Azure Storage, usługi Azure Event Hubs i dzienniki usługi Azure Monitor.

> [!IMPORTANT]
> Eksport danych przesyłanych strumieniowo danych telemetrycznych diagnostycznych nie jest domyślnie włączony.

Wybierz jedną z następujących kart, aby uzyskać wskazówki krok po kroku dotyczące konfigurowania eksportu danych logowania do przesyłania strumieniowego danych telemetrycznych w portalu Azure i skryptów do wykonania tego samego za pomocą programu PowerShell i interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="elastic-pools"></a>Pule elastyczne

Można skonfigurować zasób puli elastycznej, aby zebrać następujące dane telemetryczne diagnostyczne:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Basen elastyczny** | [Podstawowe dane](sql-database-metrics-diag-logging.md#basic-metrics) zawierają wartość procentową eDTU/CPU, limit eDTU/CPU, procent odczytu danych fizycznych, procent zapisu dziennika, procent sesji, procent pracowników, wartość magazynową, wartość procentową magazynu, limit pamięci masowej i procent pamięci XTP. |

Aby skonfigurować przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla pul elastycznych i puli baz danych, należy oddzielnie skonfigurować każdą z nich oddzielnie:

- Włącz przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla puli elastycznej
- Włącz przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla każdej bazy danych w puli elastycznej

Kontener puli elastycznej ma własne dane telemetryczne oddzielone od danych telemetrycznych poszczególnych puli bazy danych.

Aby włączyć przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla zasobu puli elastycznej, wykonaj następujące kroki:

1. Przejdź do zasobu **puli elastycznej** w witrynie Azure portal.
2. Wybierz **ustawienia diagnostyki**.
3. Wybierz **pozycję Włącz diagnostykę,** jeśli nie istnieją poprzednie ustawienia, lub wybierz **pozycję Edytuj ustawienie,** aby edytować poprzednie ustawienie.

   ![Włącz diagnostykę basenów elastycznych](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Wprowadź nazwę ustawienia dla własnego odwołania.
5. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwum do konta magazynu,** **Strumień do centrum zdarzeń**lub Wyślij do usługi Log **Analytics**.
6. W przypadku analizy dzienników wybierz pozycję **Konfiguruj** i utwórz nowy obszar roboczy, wybierając **pozycję +Utwórz nowy obszar roboczy**lub wybierz istniejący obszar roboczy.
7. Zaznacz pole wyboru dla danych telemetrycznych diagnostycznych puli elastycznej: **Podstawowe** metryki.
   ![Konfigurowanie diagnostyki dla basenów elastycznych](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. Wybierz pozycję **Zapisz**.
9. Ponadto należy skonfigurować przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla każdej bazy danych w puli elastycznej, którą chcesz monitorować, wykonując kroki opisane w następnej sekcji.

> [!IMPORTANT]
> Oprócz konfigurowania danych telemetrycznych diagnostycznych dla puli elastycznej należy również skonfigurować dane telemetryczne diagnostyczne dla każdej bazy danych w puli elastycznej.

### <a name="single-or-pooled-database"></a>Pojedyncza lub buforowana baza danych

Można skonfigurować pojedynczy lub buforowany zasób bazy danych w celu zebrania następujących danych telemetrycznych diagnostycznych:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Pojedyncza lub buforowana baza danych** | [Podstawowe metryki](sql-database-metrics-diag-logging.md#basic-metrics) zawierają procent jednostki DTU, używany obiekt DTU, limit jednostki DTU, procent odczytu danych fizycznych, procent zapisu dziennika, wartość błędu/Niepowodzenie/Zablokowana przez połączenia zapory, procent sesji, procent pracowników, wartość magazynowania, procent magazynowania, procent magazynu XTP i zakleszczenia. |

Aby włączyć przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla pojedynczej lub puli bazy danych, wykonaj następujące kroki:

1. Przejdź do zasobu **bazy danych** SQL platformy Azure.
2. Wybierz **ustawienia diagnostyki**.
3. Wybierz **pozycję Włącz diagnostykę,** jeśli nie istnieją poprzednie ustawienia, lub wybierz **pozycję Edytuj ustawienie,** aby edytować poprzednie ustawienie. Można utworzyć maksymalnie trzy połączenia równoległe do przesyłania strumieniowego danych telemetrycznych diagnostycznych.
4. Wybierz **dodaj ustawienie diagnostyczne,** aby skonfigurować równoległe przesyłanie strumieniowe danych diagnostycznych do wielu zasobów.

   ![Włączanie diagnostyki dla pojedynczych i puli baz danych](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. Wprowadź nazwę ustawienia dla własnego odwołania.
6. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwum do konta magazynu,** **Strumień do centrum zdarzeń**lub Wyślij do usługi Log **Analytics**.
7. W przypadku standardowego środowiska monitorowania opartego na zdarzeniach zaznacz następujące pola wyboru dla danych telemetrycznych dziennika diagnostyki bazy danych: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**i **Deadlocks**.
8. Aby uzyskać zaawansowane, jednominutowe środowisko monitorowania, zaznacz pole wyboru **metryki podstawowe.**

   ![Konfigurowanie diagnostyki dla pojedynczych, pulowych lub wystąpień baz danych](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. Wybierz pozycję **Zapisz**.
10. Powtórz te kroki dla każdej bazy danych, którą chcesz monitorować.

> [!TIP]
> Powtórz te kroki dla każdej pojedynczej i puli bazy danych, którą chcesz monitorować.

### <a name="managed-instance"></a>Wystąpienie zarządzane

Zasób wystąpienia zarządzanego można skonfigurować w celu zebrania następujących danych telemetrycznych diagnostycznych:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Wystąpienie zarządzane** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) zawiera liczbę rdzeni wirtualnych, średnią wartość procentową procesora CPU, żądania we/wy, bajty odczytu/zapisu, zarezerwowane miejsce do magazynowania i używane miejsce do magazynowania. |

Aby skonfigurować przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla zarządzanych baz danych wystąpień i wystąpień, należy oddzielnie skonfigurować każdą z nich:

- Włącz przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla wystąpienia zarządzanego
- Włącz przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla każdej bazy danych wystąpień

Kontener wystąpienia zarządzanego ma własne dane telemetryczne oddzielne od danych telemetrycznych każdej bazy wystąpień.

Aby włączyć przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla zasobu wystąpienia zarządzanego, wykonaj następujące kroki:

1. Przejdź do zasobu **wystąpienia zarządzanego** w witrynie Azure Portal.
2. Wybierz **ustawienia diagnostyki**.
3. Wybierz **pozycję Włącz diagnostykę,** jeśli nie istnieją poprzednie ustawienia, lub wybierz **pozycję Edytuj ustawienie,** aby edytować poprzednie ustawienie.

   ![Włącz diagnostykę dla wystąpienia zarządzanego](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Wprowadź nazwę ustawienia dla własnego odwołania.
5. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwum do konta magazynu,** **Strumień do centrum zdarzeń**lub Wyślij do usługi Log **Analytics**.
6. W przypadku analizy dzienników wybierz pozycję **Konfiguruj** i utwórz nowy obszar roboczy, wybierając **pozycję +Utwórz nowy obszar roboczy**lub użyj istniejącego obszaru roboczego.
7. Zaznacz pole wyboru dla danych telemetrycznych diagnostycznych: **ResourceUsageStats**.

   ![Konfigurowanie diagnostyki dla wystąpienia zarządzanego](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. Wybierz pozycję **Zapisz**.
9. Ponadto należy skonfigurować przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla każdej bazy danych wystąpień w wystąpieniu zarządzanym, które chcesz monitorować, wykonując kroki opisane w następnej sekcji.

> [!IMPORTANT]
> Oprócz konfigurowania danych telemetrycznych diagnostycznych dla wystąpienia zarządzanego należy również skonfigurować dane telemetryczne diagnostyczne dla każdej bazy danych wystąpień.

### <a name="instance-database"></a>Baza danych wystąpień

Zasób bazy danych wystąpienia można skonfigurować w celu zebrania następujących danych telemetrycznych diagnostycznych:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Baza danych wystąpień** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) zawiera liczbę rdzeni wirtualnych, średnią wartość procentową procesora CPU, żądania we/wy, bajty odczytu/zapisu, zarezerwowane miejsce do magazynowania i używane miejsce do magazynowania. |

Aby włączyć przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla bazy danych wystąpienia, wykonaj następujące kroki:

1. Przejdź do zasobu **bazy danych wystąpienia** w wystąpieniu zarządzanym.
2. Wybierz **ustawienia diagnostyki**.
3. Wybierz **pozycję Włącz diagnostykę,** jeśli nie istnieją poprzednie ustawienia, lub wybierz **pozycję Edytuj ustawienie,** aby edytować poprzednie ustawienie.
   - Można utworzyć maksymalnie trzy (3) połączenia równoległe do przesyłania strumieniowego danych telemetrycznych diagnostycznych.
   - Wybierz **+Dodaj ustawienie diagnostyczne,** aby skonfigurować równoległe przesyłanie strumieniowe danych diagnostycznych do wielu zasobów.

   ![Włączanie diagnostyki dla baz danych](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Wprowadź nazwę ustawienia dla własnego odwołania.
5. Wybierz zasób docelowy dla danych diagnostycznych przesyłania strumieniowego: **Archiwum do konta magazynu,** **Strumień do centrum zdarzeń**lub Wyślij do usługi Log **Analytics**.
6. Zaznacz pola wyboru danych telemetrycznych diagnostycznych bazy danych: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**i **Errors**.
   ![Konfigurowanie diagnostyki dla baz danych instancji](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. Wybierz pozycję **Zapisz**.
8. Powtórz te kroki dla każdej bazy danych wystąpień, którą chcesz monitorować.

> [!TIP]
> Powtórz te kroki dla każdej bazy danych wystąpień, którą chcesz monitorować.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Rejestrowanie metryk i diagnostyki można włączyć przy użyciu programu PowerShell.

- Aby włączyć przechowywanie metryk i dzienników zasobów na koncie magazynu, użyj tego polecenia:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  Identyfikator konta magazynu jest identyfikatorem zasobu dla docelowego konta magazynu.

- Aby włączyć przesyłanie strumieniowe metryk i dzienników zasobów do centrum zdarzeń, użyj tego polecenia:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Identyfikator reguły usługi Azure Service Bus jest ciągiem o następującym formacie:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Aby włączyć wysyłanie metryk i dzienników zasobów do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Można połączyć te parametry, aby uaktywnić wiele opcji danych wyjściowych.

**Aby skonfigurować wiele zasobów platformy Azure**

Aby obsługiwać wiele subskrypcji, użyj skryptu programu PowerShell z [rejestrowania metryk zasobów platformy Azure przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Podaj identyfikator zasobu obszaru roboczego \<$WSID\> jako `Enable-AzureRMDiagnostics.ps1` parametr podczas wykonywania skryptu w celu wysyłania danych diagnostycznych z wielu zasobów do obszaru roboczego.

- Aby uzyskać \<identyfikator obszaru\> roboczego $WSID miejsca docelowego dla danych diagnostycznych, użyj następującego skryptu:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  \<Zastąp podd\> identyfikatorem subskrypcji,\> \<\> \<RG_NAME nazwą grupy zasobów i WS_NAME nazwą obszaru roboczego.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rejestrowanie metryk i diagnostyki można włączyć przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Skrypty umożliwiające rejestrowanie diagnostyki są obsługiwane dla interfejsu wiersza polecenia platformy Azure w wersji 1.0. Narzędzie CLI platformy Azure w wersji 2.0 nie jest obecnie obsługiwane.

- Aby włączyć przechowywanie metryk i dzienników zasobów na koncie magazynu, użyj tego polecenia:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  Identyfikator konta magazynu jest identyfikatorem zasobu dla docelowego konta magazynu.

- Aby włączyć przesyłanie strumieniowe metryk i dzienników zasobów do centrum zdarzeń, użyj tego polecenia:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  Identyfikator reguły usługi Service Bus jest ciągiem o tym formacie:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Aby włączyć wysyłanie metryk i dzienników zasobów do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Można połączyć te parametry, aby uaktywnić wiele opcji danych wyjściowych.

---

## <a name="stream-into-sql-analytics"></a>Przesyłanie strumieniowe do usługi SQL Analytics

Metryki bazy danych SQL i dzienniki zasobów, które są przesyłane strumieniowo do obszaru roboczego usługi Log Analytics mogą być używane przez usługę Azure SQL Analytics. Usługa Azure SQL Analytics to rozwiązanie w chmurze, które monitoruje wydajność pojedynczych baz danych, pul elastycznych i puli baz danych oraz zarządzanych wystąpień i baz danych wystąpień na dużą skalę i w wielu subskrypcjach. Może pomóc w zbieraniu i wizualizowaniu metryk wydajności usługi Azure SQL Database i ma wbudowaną inteligencję do rozwiązywania problemów z wydajnością.

![Omówienie usługi Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Omówienie instalacji

Możesz monitorować kolekcję baz danych SQL platformy Azure za pomocą usługi Azure SQL Analytics, wykonując następujące kroki:

1. Utwórz rozwiązanie usługi Azure SQL Analytics z portalu Azure Marketplace.
2. Utwórz obszar roboczy usługi Log Analytics w rozwiązaniu.
3. Konfigurowanie baz danych do przesyłania strumieniowego danych telemetrycznych diagnostycznych do obszaru roboczego.

Eksport danych strumieniowych tej telemetrii diagnostycznej można skonfigurować przy użyciu wbudowanej opcji **Wyślij do usługi Log Analytics** na karcie ustawienia diagnostyki w witrynie Azure portal. Można również włączyć przesyłanie strumieniowe do obszaru roboczego usługi Log Analytics przy użyciu ustawień diagnostycznych za pomocą [poleceń cmdlet programu PowerShell,](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry) [interfejsu wiersza polecenia platformy Azure,](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry) [interfejsu API REST monitora azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)lub [szablonów Menedżera zasobów.](../azure-monitor/platform/diagnostic-settings-template.md)

### <a name="create-an-azure-sql-analytics-resource"></a>Tworzenie zasobu usługi Azure SQL Analytics

1. Wyszukaj usługę Azure SQL Analytics w portalu Azure Marketplace i wybierz ją.

   ![Wyszukiwanie usługi Azure SQL Analytics w portalu](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Wybierz **pozycję Utwórz** na ekranie przeglądu rozwiązania.

3. Wypełnij formularz usługi Azure SQL Analytics dodatkowymi informacjami, które są wymagane: nazwa obszaru roboczego, subskrypcja, grupa zasobów, lokalizacja i warstwa cenowa.

   ![Konfigurowanie usługi Azure SQL Analytics w portalu](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Wybierz **przycisk OK,** aby potwierdzić, a następnie wybierz pozycję **Utwórz**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Konfigurowanie zasobu do rejestrowania metryk i dzienników zasobów

Należy oddzielnie skonfigurować diagnostyczne dane strumieniowe telemetryczne dla pojedynczych i pul pul, wystąpień zarządzanych i baz danych wystąpień. Najprostszym sposobem skonfigurowania, gdzie rekordy zasobów metryki jest przy użyciu witryny Azure portal. Aby uzyskać szczegółowe kroki, zobacz [Konfigurowanie eksportu danych przesyłanych strumieniowo danych telemetrycznych diagnostycznych](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Używanie usługi Azure SQL Analytics do monitorowania i ostrzegania

Usługi SQL Analytics można używać jako hierarchicznego pulpitu nawigacyjnego do wyświetlania zasobów bazy danych SQL.

- Aby dowiedzieć się, jak korzystać z usługi Azure SQL Analytics, zobacz [Monitorowanie bazy danych SQL przy użyciu usługi SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
- Aby dowiedzieć się, jak skonfigurować alerty w usłudze SQL Analytics, zobacz [Tworzenie alertów dla bazy danych, pul elastycznych i wystąpień zarządzanych.](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)

## <a name="stream-into-event-hubs"></a>Przesyłanie strumieniowe do usługi Event Hubs

Można przesyłać strumieniowo metryki bazy danych SQL i dzienniki zasobów do centrów zdarzeń przy użyciu wbudowanej opcji **Usługi Stream do centrum zdarzeń** w witrynie Azure portal. Można również włączyć identyfikator reguły usługi Service Bus przy użyciu ustawień diagnostyki za pośrednictwem poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor. Upewnij się, że centrum zdarzeń znajduje się w tym samym regionie co baza danych i serwer.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Co zrobić z metrykami i dziennikami zasobów w Centrach zdarzeń

Po strumieniowo przesyłaniu wybranych danych do centrum zdarzeń, jesteś o krok bliżej do włączenia zaawansowanych scenariuszy monitorowania. Centra zdarzeń działa jako drzwi wejściowe dla potoku zdarzeń. Po zebraniu danych w centrum zdarzeń można je przekształcić i przechowywać za pomocą dostawcy analizy w czasie rzeczywistym lub karty magazynu. Centra zdarzeń oddziela produkcję strumienia zdarzeń od użycia tych zdarzeń. W ten sposób konsumenci zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z własnym harmonogramem. Aby uzyskać więcej informacji na temat Centrów zdarzeń, zobacz:

- [Co to są usługi Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Metryki przesyłane strumieniowo w Centrach zdarzeń można użyć do:

- **Wyświetlanie kondycji usługi przez przesyłanie strumieniowe danych ścieżki gorącej do usługi Power BI**

  Korzystając z centrów zdarzeń, usługi Stream Analytics i usługi Power BI, można łatwo przekształcić dane metryki i dane diagnostyczne w niemal w czasie rzeczywistym szczegółowe informacje na temat usług platformy Azure. Aby zapoznać się z omówieniem konfigurowania centrum zdarzeń, przetwarzania danych za pomocą usługi Stream Analytics i używania usługi Power BI jako danych wyjściowych, zobacz [Usługa Stream Analytics i usługa Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Przesyłanie strumieniowe dzienników do rejestrowania i strumieni telemetrycznych innych firm**

  Korzystając z usługi przesyłania strumieniowego w centrach zdarzeń, można uzyskać metryki i dzienniki zasobów do różnych rozwiązań do monitorowania i analizy dzienników innych firm.

- **Tworzenie niestandardowej platformy telemetrycznej i rejestrowania**

  Czy masz już niestandardową platformę telemetryczną lub rozważasz jej budowę? Wysoce skalowalny charakter publikowania i subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwania metryk i dzienników zasobów. Zobacz [przewodnik dan Rosanova dotyczący korzystania z centrów zdarzeń na globalnej platformie telemetrycznej.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="stream-into-azure-storage"></a>Przesyłanie strumieniowe do usługi Azure Storage

Metryki i dzienniki zasobów można przechowywać w usłudze Azure Storage przy użyciu wbudowanej opcji **archiwum do konta magazynu** w witrynie Azure portal. Magazyn można również włączyć przy użyciu ustawień diagnostyki za pośrednictwem poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schemat metryk i dzienników zasobów na koncie magazynu

Po skonfigurowaniu metryk i kolekcji dzienników zasobów kontener magazynu jest tworzony na koncie magazynu wybranym, gdy dostępne są pierwsze wiersze danych. Struktura obiektów blob jest:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Albo, po prostu:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład nazwa obiektu blob dla metryk podstawowych może być:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Nazwa obiektu blob do przechowywania danych z puli elastycznej wygląda następująco:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Zasady przechowywania danych i ustalanie cen

Jeśli wybierzesz Centra zdarzeń lub konto magazynu, możesz określić zasady przechowywania. Ta zasada usuwa dane starsze niż wybrany okres. Jeśli określisz usługi Log Analytics, zasady przechowywania zależy od wybranej warstwy cenowej. W takim przypadku podane wolne jednostki pozyskiwania danych mogą umożliwić bezpłatne monitorowanie kilku baz danych każdego miesiąca. Wszelkie zużycie danych telemetrycznych diagnostycznych przekraczających wolne jednostki może ponieść koszty.

> [!IMPORTANT]
> Aktywne bazy danych z większymi obciążeniami poławiają więcej danych niż bezczynne bazy danych. Aby uzyskać więcej informacji, zobacz [Ceny analizy dzienników](https://azure.microsoft.com/pricing/details/monitor/).

Jeśli korzystasz z usługi Azure SQL Analytics, możesz monitorować zużycie pozyskiwania danych, wybierając **obszar roboczy usługi OMS** w menu nawigacyjnym usługi Azure SQL Analytics, a następnie wybierając **opcję Użycie** i **koszty szacunkowe**.

## <a name="metrics-and-logs-available"></a>Dostępne dane i dzienniki

Monitorowanie danych telemetrycznych dostępnych dla pojedynczych baz danych, puli baz danych, pul elastycznych, wystąpień zarządzanych i baz danych wystąpień jest udokumentowane w tej sekcji artykułu. Zebrane dane telemetryczne monitorowania wewnątrz usługi SQL Analytics mogą być używane do własnej analizy niestandardowej i tworzenia aplikacji przy użyciu języka [zapytań dziennika usługi Azure Monitor.](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)

### <a name="basic-metrics"></a>Podstawowe dane

Szczegółowe informacje na temat metryk podstawowych według zasobów można znaleźć w poniższych tabelach.

> [!NOTE]
> Opcja Metryki podstawowe była wcześniej znana jako Wszystkie metryki. Wniesienia zmiany była tylko do nazewnictwa i nie było żadnych zmian w metryki monitorowane. Ta zmiana została zainicjowana, aby umożliwić wprowadzenie dodatkowych kategorii metryk w przyszłości.

#### <a name="basic-metrics-for-elastic-pools"></a>Podstawowe metryki dla basenów elastycznych

|**Zasób**|**Metryki**|
|---|---|
|Elastyczna pula|Procent eDTU, używany eDTU, limit eDTU, procent procesora, procent odczytu danych fizycznych, procent zapisu dziennika, procent zapisu sesji, procent pracowników, procent pamięci masowej, procent pamięci masowej, limit pamięci masowej, procent pamięci XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Podstawowe dane dla pojedynczych i puli baz danych

|**Zasób**|**Metryki**|
|---|---|
|Pojedyncza i zpulowana baza danych|Procent jednostki DTU, używany protokół DTU, limit jednostki DTU, procent procesora CPU, procent odczytu danych fizycznych, procent zapisu dziennika, pomyślny/nieudany/zablokowany przez połączenia zapory, procent sesji, procent pracowników, procent pamięci masowej, procent pamięci XTP i zakleszczenia |

### <a name="advanced-metrics"></a>Zaawansowane metryki

Szczegółowe informacje na temat zaawansowanych metryk można znaleźć w poniższej tabeli.

|**Metryka**|**Metryka Nazwa wyświetlana**|**Opis**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|Procent rdzenia procesu programu SQL Server|Procent użycia procesora CPU dla procesu programu SQL Server, mierzony przez system operacyjny.|
|sqlserver_process_memory_percent<sup>1</sup> |Procent pamięci procesu programu SQL Server|Procent użycia pamięci dla procesu programu SQL Server, mierzony przez system operacyjny.|
|tempdb_data_size<sup>2</sup>| Kilobajty rozmiaru pliku danych tempdb |Kilobajty o rozmiarze pliku danych tempdb.|
|tempdb_log_size<sup>2</sup>| Kilobajty rozmiaru pliku dziennika tempdb |Kilobajty o rozmiarze pliku dziennika tempdb.|
|tempdb_log_used_percent<sup>2</sup>| Używany dziennik procentu tempdb |Używany dziennik procentu tempdb.|

<sup>1</sup> Ta metryka jest dostępna dla baz danych przy użyciu modelu zakupu vCore z 2 vCore i wyższymi lub 200 DTU i wyższymi dla modeli zakupów opartych na UDW. 

<sup>2</sup> Ta metryka jest dostępna dla baz danych przy użyciu modelu zakupu vCore z 2 vCore i wyższym lub 200 DTU i wyższym dla modeli zakupów opartych na DTU. Ta metryka nie jest obecnie dostępna dla baz danych hiperskali lub magazynów danych.

### <a name="basic-logs"></a>Podstawowe dzienniki

Szczegóły danych telemetrycznych dostępnych dla wszystkich dzienników są udokumentowane w poniższych tabelach. Zobacz [obsługiwane dane telemetryczne diagnostyczne,](#diagnostic-telemetry-for-export-for-azure-sql-database) aby zrozumieć, które dzienniki są obsługiwane dla określonego smaku bazy danych — azure SQL pojedynczej, puli lub bazy danych wystąpień.

#### <a name="resource-usage-stats-for-managed-instances"></a>Statystyki użycia zasobów dla wystąpień zarządzanych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure|
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: ResourceUsageStats |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: MANAGEDINSTANCES |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa wystąpienia zarządzanego |
|ResourceId|Identyfikator URI zasobu |
|SKU_s|Identyfikator SKU produktu wystąpienia zarządzanego |
|virtual_core_count_s|Liczba dostępnych liczb wirtualnych |
|avg_cpu_percent_s|Średni procent procesora |
|reserved_storage_mb_s|Zarezerwowana pojemność magazynu w wystąpieniu zarządzanym |
|storage_space_used_mb_s|Używany magazyn w wystąpieniu zarządzanym |
|io_requests_s|Liczba we/wy |
|io_bytes_read_s|Odczyt bajtów we/wy |
|io_bytes_written_s|Bajty we/wy odpowiedzi zapisane |

#### <a name="query-store-runtime-statistics"></a>Statystyki środowiska wykonawczego magazynu kwerend

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreRuntimeStatistics |
|OperationName|Nazwa operacji. Zawsze: QueryStoreRuntimeStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|query_hash_s|Skrót kwerendy |
|query_plan_hash_s|Skrót planu kwerend |
|statement_sql_handle_s|Instrukcja sql dojście |
|interval_start_time_d|Początek data-początek interwału pod względem liczby znaczników z 1900-1-1 |
|interval_end_time_d|Koniec dataczasu interwału w liczbie znaczników z 1900-1-1 |
|logical_io_writes_d|Całkowita liczba zapisów we/wy logicznej |
|max_logical_io_writes_d|Maksymalna liczba zapisów we/wy logicznej na wykonanie |
|physical_io_reads_d|Całkowita liczba fizycznych odczytów we/wy |
|max_physical_io_reads_d|Maksymalna liczba logicznych odczytów we/wy na wykonanie |
|logical_io_reads_d|Całkowita liczba logicznych odczytów we/wy |
|max_logical_io_reads_d|Maksymalna liczba logicznych odczytów we/wy na wykonanie |
|execution_type_d|Typ wykonania |
|count_executions_d|Liczba wykonań kwerendy |
|cpu_time_d|Całkowity czas procesora cpu zużyty przez kwerendę w mikrosekundach |
|max_cpu_time_d|Maksymalna liczba jednostek procentowych czasu procesora w trybie pojedynczej w mikrosekundach |
|dop_d|Suma stopni równoległości |
|max_dop_d|Maksymalny stopień równoległości używany do pojedynczego wykonania |
|rowcount_d|Całkowita liczba zwróconych wierszy |
|max_rowcount_d|Maksymalna liczba wierszy zwróconych w pojedynczym wykonaniu |
|query_max_used_memory_d|Całkowita ilość pamięci używanej w KB |
|max_query_max_used_memory_d|Maksymalna ilość pamięci używanej przez pojedyncze wykonanie w KB |
|duration_d|Całkowity czas wykonania w mikrosekundach |
|max_duration_d|Maksymalny czas wykonania pojedynczego wykonania |
|num_physical_io_reads_d|Całkowita liczba odczytów fizycznych |
|max_num_physical_io_reads_d|Maksymalna liczba odczytów fizycznych na wykonanie |
|log_bytes_used_d|Całkowita ilość użytych bajtów dziennika |
|max_log_bytes_used_d|Maksymalna ilość bajtów dziennika używanych na wykonanie |
|query_id_d|Identyfikator kwerendy w Magazynie zapytań |
|plan_id_d|Identyfikator planu w Magazynie zapytań |

Dowiedz się więcej o [danych statystycznych środowiska wykonawczego Magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Statystyki oczekiwania magazynu zapytań

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreWaitStatistics |
|OperationName|Nazwa operacji. Zawsze: QueryStoreWaitStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|wait_category_s|Kategoria oczekiwania |
|is_parameterizable_s|Czy kwerenda jest parametryzowalna |
|statement_type_s|Typ instrukcji |
|statement_key_hash_s|Skrót klucza zestawienia |
|exec_type_d|Rodzaj wykonania |
|total_query_wait_time_ms_d|Całkowity czas oczekiwania kwerendy w określonej kategorii oczekiwania |
|max_query_wait_time_ms_d|Maksymalny czas oczekiwania kwerendy w wykonaniu indywidualnym dla określonej kategorii oczekiwania |
|query_param_type_d|0 |
|query_hash_s|Skrót kwerendy w Magazynie kwerend |
|query_plan_hash_s|Skrót planu kwerend w Magazynie kwerend |
|statement_sql_handle_s|Dojście do instrukcji w Magazynie zapytań |
|interval_start_time_d|Początek data-początek interwału pod względem liczby znaczników z 1900-1-1 |
|interval_end_time_d|Koniec dataczasu interwału w liczbie znaczników z 1900-1-1 |
|count_executions_d|Liczba wykonań kwerendy |
|query_id_d|Identyfikator kwerendy w Magazynie zapytań |
|plan_id_d|Identyfikator planu w Magazynie zapytań |

Dowiedz się więcej o [danych statystycznych oczekiwania magazynu query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)store .

#### <a name="errors-dataset"></a>Zestaw danych Błędy

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: Błędy |
|OperationName|Nazwa operacji. Zawsze: BłądWentnik |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|Komunikat|Komunikat o błędzie w postaci zwykłego tekstu |
|user_defined_b|Czy bit zdefiniowany przez użytkownika jest |
|error_number_d|Kod błędu |
|Ważność|Ważność błędu |
|state_d|Stan błędu |
|query_hash_s|Skrót kwerendy kwerendy, która nie powiodła się, jeśli jest dostępna |
|query_plan_hash_s|Skrót planu kwerendy kwerendy, jeśli jest dostępny, jeśli jest dostępny |

Dowiedz się więcej o [komunikatach o błędach programu SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

#### <a name="database-wait-statistics-dataset"></a>Zestaw danych statystyk oczekiwania bazy danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: DatabaseWaitStatistics |
|OperationName|Nazwa operacji. Zawsze: DatabaseWaitStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|wait_type_s|Nazwa typu oczekiwania |
|start_utc_date_t [UTC]|Zmierzony czas rozpoczęcia okresu |
|end_utc_date_t [UTC]|Zmierzony czas zakończenia okresu |
|delta_max_wait_time_ms_d|Maksymalny czas oczekiwania na wykonanie |
|delta_signal_wait_time_ms_d|Całkowity czas oczekiwania na sygnały |
|delta_wait_time_ms_d|Całkowity czas oczekiwania w okresie |
|delta_waiting_tasks_count_d|Liczba oczekujących zadań |

Dowiedz się więcej o [statystykach oczekiwania na bazę danych](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Limity czasu — zestaw danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: Limity czasu |
|OperationName|Nazwa operacji. Zawsze: TimeoutEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|error_state_d|Kod stanu błędu |
|query_hash_s|Skrót kwerendy, jeśli jest dostępny |
|query_plan_hash_s|Skrót planu kwerend, jeśli jest dostępny |

#### <a name="blockings-dataset"></a>Zestaw danych blokowania

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: Bloki |
|OperationName|Nazwa operacji. Zawsze: BlockEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|lock_mode_s|Tryb blokady używany przez kwerendę |
|resource_owner_type_s|Właściciel zamka |
|blocked_process_filtered_s|Kod XML raportu o zablokowanym procesie |
|duration_d|Czas trwania blokady w mikrosekundach |

#### <a name="deadlocks-dataset"></a>Zestaw danych zakleszczenia

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC] |Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: Zakleszczenia |
|OperationName|Nazwa operacji. Zawsze: Zakleszczenie |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|deadlock_xml_s|Raport zakleszczenia XML |

#### <a name="automatic-tuning-dataset"></a>Zestaw danych automatycznego dostrajania

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Platforma Azure |
|Czasgenerowany [UTC]|Sygnatura czasowa podczas rejestrowania dziennika |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. Sql |
|Kategoria|Nazwa kategorii. Zawsze: Automatyczne dostrajanie |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZY DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji bazy danych |
|ResourceGroup|Nazwa grupy zasobów bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|LogicalDatabaseName_s|Nazwa bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|RecommendationHash_s|Unikatowy skrót zalecenia automatycznego strojenia |
|OptionName_s|Automatyczna regulacja |
|Schema_s|Schemat bazy danych |
|Table_s|Tabela, którego dotyczy problem |
|IndexName_s|Nazwa indeksu |
|IndexColumns_s|Nazwa kolumny |
|IncludedColumns_s|Kolumny w zestawie |
|EstimatedImpact_s|Szacowany wpływ zalecenia automatycznego strojenia JSON |
|Event_s|Typ zdarzenia automatycznego strojenia |
|Timestamp_t|Ostatnia aktualizacja sygnatury czasowe |

#### <a name="intelligent-insights-dataset"></a>Zestaw danych inteligentnej usługi Insights

Dowiedz się więcej o [formacie dziennika inteligentnego wglądu w dane](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć rejestrowanie i zrozumieć metryki i kategorie dzienników obsługiwane przez różne usługi platformy Azure, zobacz:

- [Omówienie metryk na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Omówienie dzienników platformy Azure](../azure-monitor/platform/platform-logs-overview.md)

Aby dowiedzieć się więcej o Centrach zdarzeń, przeczytaj:

- [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Aby dowiedzieć się, jak skonfigurować alerty na podstawie danych telemetrycznych z analizy dzienników, zobacz:

- [Tworzenie alertów dla bazy danych SQL i wystąpienia zarządzanego](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
