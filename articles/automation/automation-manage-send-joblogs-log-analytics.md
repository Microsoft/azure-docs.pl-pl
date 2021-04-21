---
title: Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor
description: W tym artykule opisano sposób wysyłania stanu zadania i strumieni zadań runbook do dzienników Azure Monitor runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c24c38368ef20dadd0dc19b1804f9d27ad68bd27
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833692"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor

Azure Automation może wysyłać strumienie zadań i stanu zadania runbook do obszaru roboczego usługi Log Analytics. Ten proces nie obejmuje łączenia obszarów roboczych i jest całkowicie niezależny. Dzienniki zadań i strumienie zadań są widoczne w Azure Portal lub za pomocą programu PowerShell dla poszczególnych zadań, co pozwala na wykonywanie prostych badań. Teraz dzięki Azure Monitor można:

* Uzyskaj wgląd w stan zadań automatyzacji.
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania runbook (na przykład niepowodzenie lub wstrzymanie).
* Pisanie zaawansowanych zapytań w strumieniach zadań.
* Korelowanie zadań między kontami usługi Automation.
* Użyj widoków niestandardowych i zapytań wyszukiwania, aby zwizualizować wyniki, stan zadania runbook i inne powiązane kluczowe wskaźniki lub metryki.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wysyłanie dzienników usługi Automation do Azure Monitor, potrzebne są:

* Najnowsza wersja [Azure PowerShell](/powershell/azure/).

* Obszar roboczy usługi Log Analytics i jego identyfikator zasobu. Aby uzyskać więcej informacji, zobacz [Get started with Azure Monitor logs (Rozpoczynanie pracy z Azure Monitor dziennikami).](../azure-monitor/overview.md)

* Identyfikator zasobu twojego Azure Automation konta.

## <a name="how-to-find-resource-ids"></a>Jak znaleźć identyfikatory zasobów

1. Użyj następującego polecenia, aby znaleźć identyfikator zasobu dla Azure Automation konta:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Skopiuj wartość **resourceID**.

3. Użyj następującego polecenia, aby znaleźć identyfikator zasobu obszaru roboczego usługi Log Analytics:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Skopiuj wartość **resourceID**.

Aby zwrócić wyniki z określonej grupy zasobów, uwzględnij `-ResourceGroupName` parametr . Aby uzyskać więcej informacji, zobacz [Get-AzResource](/powershell/module/az.resources/get-azresource).

Jeśli w danych wyjściowych poprzednich poleceń masz więcej niż jedno konto usługi Automation lub obszar roboczy, możesz znaleźć nazwę i inne powiązane właściwości, które są częścią pełnego identyfikatora zasobu konta usługi Automation, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W Azure Portal wybierz swoje konto usługi Automation na stronie **Konta usługi Automation.**
1. Na stronie wybranego konta usługi Automation w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.
1. Na **stronie Właściwości** zanotuj szczegóły pokazane poniżej.

    ![Właściwości konta usługi Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Ustawienia diagnostyczne usługi Automation obsługują przekazywanie następujących dzienników platformy i danych metryk:

* JobLogs (Listy zadań)
* JobStreams
* DSCNodeStatus
* Metryki — łączna liczba zadań, łączna liczba przebiegów maszyn wdrażania aktualizacji, łączna liczba przebiegów wdrożeń aktualizacji

Aby rozpocząć wysyłanie dzienników usługi Automation do Azure Monitor, zapoznaj się z tematem Create [diagnostic settings](../azure-monitor/essentials/diagnostic-settings.md) (Tworzenie ustawień diagnostycznych), aby poznać dostępne funkcje i metody konfigurowania ustawień diagnostycznych w celu wysyłania dzienników platformy.

## <a name="azure-monitor-log-records"></a>Azure Monitor rekordów dziennika

Azure Automation diagnostyki utwórz dwa typy rekordów w dziennikach Azure Monitor, oznaczonych jako `AzureDiagnostics` . Tabele w następnych sekcjach to przykłady rekordów, które Azure Automation, oraz typy danych, które pojawiają się w wynikach przeszukiwania dzienników.

### <a name="job-logs"></a>Dzienniki zadań

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Wywołujący, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| Tenant_g | Identyfikator GUID identyfikujący dzierżawę dla wywołującego. |
| JobId_g |Identyfikator GUID identyfikujący zadanie runbook. |
| ResultType |Stan zadania elementu Runbook. Możliwe wartości:<br>— Nowy<br>— Utworzono<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Ukończono |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs. |
| OperationName | Typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest Job( Zadanie). |
| Zasób | Nazwa konta usługi Automation |
| SourceSystem | System, Azure Monitor do zbierania danych za pomocą dzienników. Wartość to zawsze Azure na potrzeby diagnostyki platformy Azure. |
| ResultDescription |Stan wyniku zadania runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone |
| CorrelationId |Identyfikator GUID korelacji zadania runbook. |
| ResourceId |Identyfikator Azure Automation zasobu konta runbook. |
| SubscriptionId | Identyfikator GUID subskrypcji platformy Azure dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Dostawca zasobów. Wartość to MICROSOFT. Automatyzacji. |
| ResourceType | Typ zasobu. Wartość to AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Strumienie zadania
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Wywołujący, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| StreamType_s |Typ strumienia zadania. Możliwe wartości:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne |
| Tenant_g | Identyfikator GUID identyfikujący dzierżawę dla wywołującego. |
| JobId_g |Identyfikator GUID identyfikujący zadanie runbook. |
| ResultType |Stan zadania elementu Runbook. Możliwe wartości:<br>— W toku |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams. |
| OperationName | Typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest Job( Zadanie). |
| Zasób | Nazwa konta usługi Automation. |
| SourceSystem | System, Azure Monitor do zbierania danych za pomocą dzienników. Wartość to zawsze Azure na potrzeby diagnostyki platformy Azure. |
| ResultDescription |Opis, który zawiera strumień wyjściowy z runbook. |
| CorrelationId |Identyfikator GUID korelacji zadania runbook. |
| ResourceId |Identyfikator Azure Automation zasobu konta runbook. |
| SubscriptionId | Identyfikator GUID subskrypcji platformy Azure dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Dostawca zasobów. Wartość to MICROSOFT. Automatyzacji. |
| ResourceType | Typ zasobu. Wartość to AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników automatyzacji w Azure Monitor danych

Teraz, gdy rozpoczęto wysyłanie strumieni zadań i dzienników usługi Automation do dzienników usługi Azure Monitor, zobaczmy, co można zrobić z tymi dziennikami w Azure Monitor dziennikach.

Aby wyświetlić dzienniki, uruchom następujące zapytanie: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Wysyłanie wiadomości e-mail w przypadku niepowodzenia lub wstrzymania zadania runbook

Poniższe kroki pokazują, jak skonfigurować alerty w programie Azure Monitor powiadamiania o błędzie zadania runbook.

Aby utworzyć regułę alertu, rozpocznij od utworzenia przeszukiwania dzienników rekordów zadań runbook, które powinny wywoływać alert. Kliknij przycisk **Alert,** aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd obszaru roboczego usługi Log Analytics kliknij pozycję **Wyświetl dzienniki.**

2. Utwórz zapytanie przeszukiwania dzienników dla alertu, wpisując następujące wyszukiwanie w polu zapytania: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Można również grupować według nazwy runbook przy użyciu: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Jeśli skonfigurujemy dzienniki z więcej niż jednego konta usługi Automation lub subskrypcji do obszaru roboczego, możesz pogrupować alerty według subskrypcji i konta usługi Automation. Nazwę konta usługi Automation można znaleźć w `Resource` polu wyszukiwania `JobLogs` .

3. Aby otworzyć ekran **Tworzenie reguły,** kliknij pozycję Nowa reguła **alertu** w górnej części strony. Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [Alerty dzienników na platformie Azure.](../azure-monitor/alerts/alerts-unified-log.md)

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Znajdź wszystkie zadania, które zostały zakończone z błędami

Oprócz alertów o błędach można sprawdzić, kiedy zadanie runbook ma błąd niepowodujący zakończenia. W takich przypadkach program PowerShell generuje strumień błędów, ale błędy niepowiązywające zakończenia nie powodują wstrzymania lub zakończenia zadania.

1. W obszarze roboczym usługi Log Analytics kliknij pozycję **Dzienniki.**

2. W polu zapytania wpisz `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Kliknij **przycisk** Wyszukaj.

### <a name="view-job-streams-for-a-job"></a>Wyświetlanie strumieni zadań dla zadania

Podczas debugowania zadania warto również przyjrzeć się strumieniom zadań. Następujące zapytanie przedstawia wszystkie strumienie dla pojedynczego zadania z identyfikatorem GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Wyświetlanie historycznego stanu zadania

Na koniec warto zwizualizować historię zadań w czasie. To zapytanie umożliwia wyszukiwanie stanu zadań w czasie.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Wykres stanu historycznego zadania usługi Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtrowanie danych wyjściowych stanu zadania przekonwertowanych na obiekt JSON

Ostatnio zmieniliśmy zachowanie sposobu, w jaki dane dziennika usługi Automation są zapisywane w tabeli w usłudze Log Analytics, gdzie nie dzieli ona już właściwości `AzureDiagnostics` JSON na oddzielne pola. Jeśli skonfigurowano obiekt Runbook do formatowania obiektów w strumieniu wyjściowym w formacie JSON jako oddzielnych kolumn, konieczne jest ponowne skonfigurowanie zapytań w celu przeanalizowania tego pola do obiektu JSON w celu uzyskania dostępu do tych właściwości. Jest to realizowane przy użyciu [pliku parsejson](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) w celu uzyskania dostępu do określonego elementu JSON w znanej ścieżce.

Na przykład obiekt Runbook formatuje właściwość *ResultDescription* w strumieniu wyjściowym w formacie JSON z wieloma polami. Aby wyszukać stan zadań, które są w stanie niepowodzeniem określonym w polu o nazwie **Status**, użyj tego przykładowego zapytania, aby wyszukać w obszarze *ResultDescription* stan **Niepowodzenie:**

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Format JSON strumienia zadań historycznych usługi Log Analytics](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak tworzyć zapytania wyszukiwania i przeglądać dzienniki zadań usługi Automation przy użyciu dzienników Azure Monitor, zobacz Przeszukiwanie dzienników w [Azure Monitor dziennikach.](../azure-monitor/logs/log-query-overview.md)
* Aby zrozumieć tworzenie i pobieranie danych wyjściowych i komunikatów o błędach z runbook, zobacz [Monitorowanie danych wyjściowych runbook.](automation-runbook-output-and-messages.md)
* Aby dowiedzieć się więcej na temat wykonywania runbook, sposobu monitorowania zadań runbook i innych szczegółów technicznych, zobacz Wykonywanie runbook w [programie Azure Automation](automation-runbook-execution.md).
* Aby dowiedzieć się więcej o dziennikach Azure Monitor i źródłach zbierania danych, zobacz Collecting Azure storage data in Azure Monitor logs overview (Zbieranie danych usługi Azure Storage w dziennikach [zarządzania).](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
* Aby uzyskać pomoc w rozwiązywaniu problemów z usługą Log Analytics, zobacz [Troubleshooting why Log Analytics is no longer collecting data](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)(Rozwiązywanie problemów z tym, dlaczego usługa Log Analytics nie zbiera już danych).
