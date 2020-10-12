---
title: Azure SQL Analytics rozwiązanie w Azure Monitor | Microsoft Docs
description: Rozwiązanie Azure SQL Analytics ułatwia zarządzanie bazami danych Azure SQL Database
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 0015138f4da9f66e2f9148e468dd1b5543ae0c4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397083"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitoruj Azure SQL Database przy użyciu Azure SQL Analytics (wersja zapoznawcza)

![Symbol Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics to zaawansowane rozwiązanie do monitorowania chmurowego służące do monitorowania wydajności wszystkich baz danych SQL platformy Azure na dużą skalę i wielu subskrypcjach w jednym widoku. Azure SQL Analytics gromadzi i wizualizuje kluczowe metryki wydajności dzięki wbudowanej analizie na potrzeby rozwiązywania problemów z wydajnością.

Korzystając z tych zebranych metryk, można tworzyć niestandardowe reguły monitorowania i alerty. Azure SQL Analytics pomaga identyfikować problemy w każdej warstwie stosu aplikacji. Używa metryk diagnostycznych platformy Azure wraz z Azure Monitor widokami do prezentowania danych o wszystkich bazach danych Azure SQL w jednym Log Analytics obszarze roboczym. Azure Monitor pomaga zbierać, skorelować i wizualizować dane ze strukturą i bez struktury.

Aby dowiedzieć się więcej na temat korzystania z rozwiązania Azure SQL Analytics i dla typowych scenariuszy użycia, zobacz osadzony film wideo:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Połączone źródła

Azure SQL Analytics to rozwiązanie do monitorowania tylko w chmurze obsługujące przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla wszystkich baz Azure SQL Database. Ponieważ Azure SQL Analytics nie używa agentów do łączenia się z Azure Monitor, nie obsługuje monitorowania SQL Server hostowanych lokalnie lub na maszynach wirtualnych.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Ustawienia diagnostyki](../platform/diagnostic-settings.md) | **Tak** | Dane dotyczące metryk i dzienników usługi Azure są wysyłane do dzienników Azure Monitor bezpośrednio przez platformę Azure. |
| [Konto usługi Azure Storage](../platform/resource-logs.md#send-to-log-analytics-workspace) | Nie | Azure Monitor nie odczytuje danych z konta magazynu. |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Nie | Bezpośredni agenci systemu Windows nie są używani przez Azure SQL Analytics. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Agenci bezpośredniego systemu Linux nie są używani przez Azure SQL Analytics. |
| [Grupa zarządzania programu System Center Operations Manager](../platform/om-agents.md) | Nie | Połączenie bezpośrednie od agenta Operations Manager do Azure Monitor nie jest używane przez Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opcje Azure SQL Analytics

W poniższej tabeli przedstawiono opcje obsługiwane przez dwie wersje pulpitu nawigacyjnego Azure SQL Analytics, jedną dla Azure SQL Database i drugą dla baz danych wystąpienia zarządzanego Azure SQL.

| Opcja Azure SQL Analytics | Opis | Obsługa SQL Database | Obsługa usługi SQL Managed Instance |
| --- | ------- | ----- | ----- |
| Zasób według typu | Perspektywa, która zlicza wszystkie monitorowane zasoby. | Tak | Tak |
| Insights | Zapewnia hierarchiczne przechodzenie do szczegółów w Intelligent Insights wydajności. | Tak | Tak |
| Errors | Zawiera hierarchiczne przechodzenie do szczegółów błędów SQL, które wystąpiły w bazach danych. | Tak | Tak |
| Limity czasu | Zawiera hierarchiczne przekroczenia limitów czasu SQL, które wystąpiły w bazach danych. | Tak | Nie |
| Bloki | Zawiera hierarchiczne przechodzenie do szczegółów w blokach SQL, które wystąpiły w bazach danych. | Tak | Nie |
| Oczekiwanie na bazę danych | Zawiera hierarchiczne przechodzenie do szczegółów na poziomie bazy danych. Zawiera podsumowanie łącznego czasu oczekiwania i czasu oczekiwania na typ oczekiwania. |Tak | Nie |
| Czas trwania zapytania | Zawiera hierarchiczne przechodzenie do szczegółów w statystyce wykonywania zapytania, takich jak czas trwania zapytania, użycie procesora CPU, użycie operacji we/wy danych, użycie operacji we/wy dziennika. | Tak | Tak |
| Query waits (Czas oczekiwania na zapytania) | Zawiera hierarchiczne przechodzenie do szczegółów w celu uwzględnienia statystyk oczekiwania na zapytanie według kategorii oczekiwania. | Tak | Tak |

## <a name="configuration"></a>Konfiguracja

Aby dodać Azure SQL Analytics (wersja zapoznawcza) do obszaru roboczego Log Analytics, należy użyć procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](./solutions.md) .

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Konfigurowanie Azure SQL Database do przesyłania danych telemetrycznych diagnostyki

Po utworzeniu rozwiązania Azure SQL Analytics w obszarze roboczym należy **skonfigurować wszystkie** zasoby, które mają być monitorowane, aby przesyłać strumieniowo dane telemetryczne diagnostyki do Azure SQL Analytics. Postępuj zgodnie ze szczegółowymi instrukcjami na tej stronie:

- Włącz Diagnostyka Azure dla bazy danych, aby [przesyłać dane telemetryczne diagnostyki do Azure SQL Analytics](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Powyższa Strona zawiera również instrukcje dotyczące włączania obsługi monitorowania wielu subskrypcji platformy Azure z jednego obszaru roboczego Azure SQL Analytics jako pojedynczego okienka.

## <a name="using-azure-sql-analytics"></a>Używanie Azure SQL Analytics

Po dodaniu Azure SQL Analytics do obszaru roboczego kafelek Azure SQL Analytics zostanie dodany do obszaru roboczego i zostanie wyświetlony w temacie Omówienie. Wybierz łącze Wyświetl podsumowanie, aby załadować zawartość kafelka.

![Kafelek podsumowania Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po załadowaniu kafelek pokazuje liczbę baz danych i pul elastycznych w SQL Database i wystąpieniach oraz bazach danych wystąpień w wystąpieniu zarządzanym SQL, z którego Azure SQL Analytics otrzymuje dane telemetryczne diagnostyki.

![Kafelek Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics oferuje dwa osobne widoki — jeden do monitorowania SQL Database i drugi widok monitorowania wystąpienia zarządzanego SQL.

Aby wyświetlić pulpit nawigacyjny monitorowania Azure SQL Analytics dla SQL Database, kliknij górną część kafelka. Aby wyświetlić pulpit nawigacyjny monitorowania Azure SQL Analytics dla wystąpienia zarządzanego SQL, kliknij dolną część kafelka.

### <a name="viewing-azure-sql-analytics-data"></a>Wyświetlanie danych Azure SQL Analytics

Pulpit nawigacyjny zawiera przegląd wszystkich baz danych, które są monitorowane przy użyciu różnych perspektyw. Aby różne perspektywy działały, należy włączyć odpowiednie metryki lub dzienniki w zasobach SQL, aby były przesyłane strumieniowo do Log Analytics obszaru roboczego.

Jeśli niektóre metryki lub dzienniki nie są przesyłane strumieniowo do Azure Monitor, kafelki w Azure SQL Analytics nie są wypełnione informacjami monitorowania.

### <a name="sql-database-view"></a>Widok SQL Database

Po wybraniu Azure SQL Analytics kafelka bazy danych zostanie wyświetlony pulpit nawigacyjny monitorowanie.

![Zrzut ekranu przedstawiający pulpit nawigacyjny monitorowania.](./media/azure-sql/azure-sql-sol-overview.png)

Wybranie dowolnego kafelka spowoduje otwarcie raportu przechodzenia do szczegółów w określonej perspektywie. Po wybraniu perspektywy zostanie otwarty raport przechodzenia do szczegółów.

![Zrzut ekranu, który pokazuje raport przechodzenia do szczegółów w określonej perspektywie.](./media/azure-sql/azure-sql-sol-metrics.png)

Każda perspektywa w tym widoku zawiera podsumowania na poziomach subskrypcji, serwera, elastycznej puli i bazy danych. Ponadto każda perspektywa pokazuje perspektywę specyficzną dla raportu po prawej stronie. Wybieranie subskrypcji, serwera, puli lub bazy danych z listy kontynuuje przechodzenie do szczegółów.

### <a name="sql-managed-instance-view"></a>Widok wystąpienia zarządzanego SQL

Po wybraniu Azure SQL Analytics kafelka dla baz danych zostanie wyświetlony pulpit nawigacyjny monitorowanie.

![Przegląd Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Wybranie dowolnego kafelka spowoduje otwarcie raportu przechodzenia do szczegółów w określonej perspektywie. Po wybraniu perspektywy zostanie otwarty raport przechodzenia do szczegółów.

Wybór widoku wystąpienia zarządzanego SQL zawiera szczegółowe informacje o wykorzystaniu wystąpienia, bazach danych wystąpień i telemetrii w zapytaniach wykonywanych w ramach wystąpienia zarządzanego.

![Limity czasu Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Raport Intelligent Insights

Azure SQL Database [Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) pozwala wiedzieć, co dzieje się z wydajnością wszystkich baz danych SQL Azure. Wszystkie zebrane Intelligent Insights można wizualizować i uzyskiwać do nich dostęp za pomocą perspektywy wglądu w szczegółowe dane.

![Azure SQL Analytics szczegółowych informacji](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Pule elastyczne i raporty bazy danych

W przypadku pul elastycznych i baz danych są używane własne raporty pokazujące wszystkie dane zbierane dla zasobu w określonym czasie.

![Baza danych Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Elastyczna Pula usługi Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty zapytań

Przez czas trwania zapytania i zapytanie czekają na perspektywy, można skorelować wydajność dowolnego zapytania za pomocą raportu zapytania. Ten raport porównuje wydajność zapytań między różnymi bazami danych i ułatwia lokalizowanie baz danych, które wykonują wybrane zapytanie w zależności od tego, które są powolne.

![Zapytania Azure SQL Analytics](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Uprawnienia

Aby korzystać z Azure SQL Analytics, użytkownicy muszą mieć przyznane minimalne uprawnienia roli czytelnik na platformie Azure. Ta rola nie zezwala jednak użytkownikom na wyświetlanie tekstu zapytania ani wykonywanie żadnych akcji dostrajania automatycznego. Bardziej ograniczając role na platformie Azure, które zezwalają na używanie Azure SQL Analytics do pełnego zakresu są właścicielami, współautorem, współautorem bazy danych SQL lub współautorem SQL Server. Warto również rozważyć utworzenie roli niestandardowej w portalu z określonymi uprawnieniami wymaganymi tylko Azure SQL Analytics i bez dostępu do zarządzania innymi zasobami.

### <a name="creating-a-custom-role-in-portal"></a>Tworzenie roli niestandardowej w portalu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Uznając, że niektóre organizacje wymuszają ścisłe kontrole uprawnień na platformie Azure, znajdź następujący skrypt programu PowerShell umożliwiający utworzenie roli niestandardowej "operator monitorowania usługi SQL Analytics" w Azure Portal z minimalnymi uprawnieniami do odczytu i zapisu wymaganymi do użycia Azure SQL Analytics do jego pełnego zakresu.

Zastąp element "{Subscription}" w poniższym skrypcie IDENTYFIKATORem subskrypcji platformy Azure i uruchom skrypt zalogowany jako rola właściciela lub współautora na platformie Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Po utworzeniu nowej roli Przypisz tę rolę każdemu użytkownikowi, który będzie musiał udzielić uprawnień niestandardowych do korzystania z Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analizowanie danych i tworzenie alertów

Analiza danych w Azure SQL Analytics opiera się na [log Analytics języku](../log-query/get-started-queries.md) dla niestandardowych zapytań i raportowania. Znajdź opis dostępnych danych zebranych z zasobu bazy danych na potrzeby niestandardowych zapytań w [dostępnych metrykach i dziennikach](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available).

Automatyczne generowanie alertów w Azure SQL Analytics opiera się na pisaniu Log Analytics kwerendy, która wyzwala alert po spełnieniu warunku. Znajdź poniżej kilka przykładów na Log Analytics zapytaniach, na których można skonfigurować alerty w Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Tworzenie alertów dla Azure SQL Database

Możesz łatwo [tworzyć alerty](../platform/alerts-metric.md) z danymi pochodzącymi z zasobów Azure SQL Database. Oto kilka przydatnych [zapytań dzienników](../log-query/log-query-overview.md) , których można użyć w przypadku alertu dziennika:

#### <a name="high-cpu"></a>Wysokie wykorzystanie procesora CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Wymagania wstępne dotyczące konfigurowania tego alertu to, że monitorowane bazy danych przesyłają Podstawowe metryki do Azure SQL Analytics.
> - Zastąp wartość Metricname cpu_percent z dtu_consumption_percent, aby zamiast tego uzyskać wysokie wyniki jednostek DTU.

#### <a name="high-cpu-on-elastic-pools"></a>Duże użycie procesora CPU w elastycznych pulach

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Wymagania wstępne dotyczące konfigurowania tego alertu to, że monitorowane bazy danych przesyłają Podstawowe metryki do Azure SQL Analytics.
> - Zastąp wartość Metricname cpu_percent z dtu_consumption_percent, aby zamiast tego uzyskać wysokie wyniki jednostek DTU.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Średnia ilość miejsca w magazynie powyżej 95% w ostatnich 1 godz.

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - Wymagania wstępne dotyczące konfigurowania tego alertu to, że monitorowane bazy danych przesyłają Podstawowe metryki do Azure SQL Analytics.
> - To zapytanie wymaga skonfigurowania reguły alertu do wyzwolenia alertu, gdy istnieją wyniki (> 0 wyników) z zapytania, co oznacza, że warunek istnieje w niektórych bazach danych. Dane wyjściowe to lista zasobów bazy danych, które znajdują się powyżej storage_threshold w ramach zdefiniowanego time_range.
> - Dane wyjściowe to lista zasobów bazy danych, które znajdują się powyżej storage_threshold w ramach zdefiniowanego time_range.

#### <a name="alert-on-intelligent-insights"></a>Alert dotyczący usługi Intelligent Insights

> [!IMPORTANT]
> Jeśli baza danych działa prawidłowo i nie Wygenerowano żadnych Intelligent Insights, to zapytanie zakończy się niepowodzeniem z komunikatem o błędzie: nie można rozpoznać wyrażenia skalarnego o nazwie "rootCauseAnalysis_s". To zachowanie jest oczekiwane dla wszystkich przypadków, w których nie ma żadnych inteligentnych szczegółowych informacji dotyczących bazy danych.

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - Przed rozpoczęciem konfigurowania tego alertu jest to, że monitorowane bazy danych SQLInsightsą dziennik diagnostyczny przesyłania strumieniowego do Azure SQL Analytics.
> - To zapytanie wymaga skonfigurowania reguły alertu tak, aby uruchamiała się z tą samą częstotliwością co alert_run_interval, aby uniknąć zduplikowanych wyników. Reguła powinna być skonfigurowana w taki sposób, aby nie wyłączać alertu, gdy istnieją wyniki (> 0 wyników) z zapytania.
> - Dostosuj alert_run_interval, aby określić zakres czasu, aby sprawdzić, czy warunek wystąpił w bazach danych skonfigurowanych do przesyłania strumieniowego dziennika SQLInsights do Azure SQL Analytics.
> - Dostosuj insights_string, aby przechwytywać dane wyjściowe analizy głównych przyczyn analiz. Jest to ten sam tekst wyświetlany w interfejsie użytkownika Azure SQL Analytics, którego możesz użyć z istniejących szczegółowych informacji. Możesz też użyć poniższego zapytania, aby zobaczyć tekst wszystkich szczegółowych informacji generowanych w ramach subskrypcji. Użyj danych wyjściowych zapytania, aby zebrać unikatowe ciągi do konfigurowania alertów w usłudze Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>Tworzenie alertów dla wystąpienia zarządzanego SQL

#### <a name="storage-is-above-90"></a>Magazyn jest powyżej 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Przed ustawieniem tego alertu w monitorowanym wystąpieniu zarządzanym jest włączone przesyłanie strumieniowe dziennika ResourceUsageStats Azure SQL Analytics.
> - To zapytanie wymaga skonfigurowania reguły alertu do wyzwolenia alertu, gdy istnieją wyniki (> 0 wyników) z zapytania, co oznacza, że warunek istnieje w wystąpieniu zarządzanym. Dane wyjściowe to użycie procentowe magazynu dla wystąpienia zarządzanego.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Średnie użycie procesora CPU jest powyżej 95% w ostatnich 1 HR

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Przed ustawieniem tego alertu w monitorowanym wystąpieniu zarządzanym jest włączone przesyłanie strumieniowe dziennika ResourceUsageStats Azure SQL Analytics.
> - To zapytanie wymaga skonfigurowania reguły alertu do wyzwolenia alertu, gdy istnieją wyniki (> 0 wyników) z zapytania, co oznacza, że warunek istnieje w wystąpieniu zarządzanym. Dane wyjściowe są średnim zużyciem użycia procesora CPU w określonym okresie w wystąpieniu zarządzanym.

### <a name="pricing"></a>Cennik

Gdy usługa Azure SQL Analytics jest bezpłatna, użycie danych telemetrycznych diagnostyki powyżej bezpłatnych jednostek, które są przystosowane do pozyskiwania w każdym miesiącu, znajduje się w temacie [log Analytics Cennik](https://azure.microsoft.com/pricing/details/monitor). Bezpłatne jednostki pozyskiwania danych zapewniają bezpłatne monitorowanie kilku baz danych miesięcznie. Więcej aktywnych baz danych z większymi obciążeniami zwiększają ilości danych i bezczynnych baz danych. Możesz łatwo monitorować użycie pozyskiwania danych w Azure SQL Analytics, wybierając obszar roboczy OMS w menu nawigacji Azure SQL Analytics, a następnie wybierając pozycję użycie i szacowane koszty.

## <a name="next-steps"></a>Następne kroki

- Użyj [zapytań dzienników](../log-query/log-query-overview.md) w Azure monitor, aby wyświetlić szczegółowe dane SQL platformy Azure.
- [Utwórz własne pulpity nawigacyjne](../learn/tutorial-logs-dashboards.md) pokazujące dane usługi Azure SQL.
- [Utwórz alerty](../platform/alerts-overview.md) w przypadku wystąpienia określonych zdarzeń SQL platformy Azure.

