---
title: Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor
description: W tym artykule opisano sposób wysyłania strumieni stanu zadania i zadań elementu Runbook do dzienników Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 4199c5576662eee1dd6cedc388440a71e21f8b74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581215"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor

Azure Automation może wysyłać strumienie zadań elementu Runbook i zadań do obszaru roboczego Log Analytics. Ten proces nie obejmuje łączenia obszarów roboczych i jest całkowicie niezależny. Dzienniki zadań i strumienie zadań są widoczne w Azure Portal lub w programie PowerShell dla poszczególnych zadań. pozwala to na wykonywanie prostych badań. Teraz z dziennikami Azure Monitor można:

* Uzyskaj wgląd w informacje o stanie zadań automatyzacji.
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania elementu Runbook (na przykład Niepowodzenie lub wstrzymane).
* Zapisuj zaawansowane zapytania w strumieniach zadań.
* Skorelowanie zadań na kontach usługi Automation.
* Za pomocą widoków niestandardowych i zapytań wyszukiwania można wizualizować wyniki elementu Runbook, stan zadania elementu Runbook oraz inne powiązane wskaźniki lub metryki kluczy.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wysyłanie dzienników usługi Automation do dzienników Azure Monitor, potrzebne są:

* Najnowsza wersja [Azure PowerShell](/powershell/azure/).

* Obszar roboczy Log Analytics i jego identyfikator zasobu. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../azure-monitor/overview.md).

* Identyfikator zasobu konta Azure Automation.

## <a name="how-to-find-resource-ids"></a>Jak znaleźć identyfikatory zasobów

1. Użyj następującego polecenia, aby znaleźć identyfikator zasobu dla konta Azure Automation:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Skopiuj wartość elementu **ResourceID**.

3. Użyj następującego polecenia, aby znaleźć identyfikator zasobu obszaru roboczego Log Analytics:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Skopiuj wartość elementu **ResourceID**.

Aby zwrócić wyniki z określonej grupy zasobów, Uwzględnij `-ResourceGroupName` parametr. Aby uzyskać więcej informacji, zobacz [Get-AzResource](/powershell/module/az.resources/get-azresource).

Jeśli masz więcej niż jedno konto usługi Automation lub obszar roboczy w danych wyjściowych powyższych poleceń, możesz znaleźć nazwę i inne powiązane właściwości, które są częścią pełnego identyfikatora zasobu konta usługi Automation, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W Azure Portal wybierz konto usługi Automation na stronie **konta usługi Automation** .
1. Na stronie wybranego konta usługi Automation w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.
1. Na stronie **Właściwości** Zanotuj wymienione poniżej szczegóły.

    ![Właściwości konta usługi Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Ustawienia diagnostyki automatyzacji obsługują przesyłanie dalej następujących dzienników platformy i danych metryk:

* JobLogs
* JobStreams
* DSCNodeStatus
* Metryki — całkowita liczba zadań, liczba uruchomień komputerów wdrożenia aktualizacji, Łączna liczba przebiegów wdrożenia aktualizacji

Aby rozpocząć wysyłanie dzienników usługi Automation do dzienników Azure Monitor, zapoznaj się z tematem [Tworzenie ustawień diagnostycznych](../azure-monitor/essentials/diagnostic-settings.md) , aby poznać funkcję i metody dostępne w celu skonfigurowania ustawień diagnostycznych w celu wysyłania dzienników platformy.

## <a name="azure-monitor-log-records"></a>Azure Monitor rekordy dziennika

Azure Automation Diagnostics Utwórz dwa typy rekordów w dziennikach Azure Monitor, otagowane jako `AzureDiagnostics` . Tabele w następnych sekcjach to przykłady rekordów, które Azure Automation generować i typy danych, które są wyświetlane w wynikach przeszukiwania dzienników.

### <a name="job-logs"></a>Dzienniki zadań

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Obiekt wywołujący, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawcę dla obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który identyfikuje zadanie elementu Runbook. |
| ResultType |Stan zadania elementu Runbook. Możliwe wartości:<br>-Nowe<br>-Utworzono<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Ukończono |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs. |
| OperationName | Typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie. |
| Zasób | Nazwa konta usługi Automation |
| SourceSystem | System, który Azure Monitor dzienników służy do zbierania danych. Ta wartość jest zawsze na platformie Azure dla diagnostyki Azure. |
| ResultDescription |Stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone |
| CorrelationId |Identyfikator GUID korelacji zadania elementu Runbook. |
| ResourceId |Identyfikator zasobu konta Azure Automation elementu Runbook. |
| SubscriptionId | Identyfikator GUID subskrypcji platformy Azure dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Dostawca zasobów. Wartość to MICROSOFT. Automatyzacji. |
| ResourceType | Typ zasobu. Wartość to AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Strumienie zadania
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Obiekt wywołujący, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| StreamType_s |Typ strumienia zadania. Możliwe wartości:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawcę dla obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który identyfikuje zadanie elementu Runbook. |
| ResultType |Stan zadania elementu Runbook. Możliwe wartości:<br>— W toku |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams. |
| OperationName | Typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie. |
| Zasób | Nazwa konta usługi Automation. |
| SourceSystem | System, który Azure Monitor dzienników służy do zbierania danych. Ta wartość jest zawsze na platformie Azure dla diagnostyki Azure. |
| ResultDescription |Opis obejmujący strumień danych wyjściowych z elementu Runbook. |
| CorrelationId |Identyfikator GUID korelacji zadania elementu Runbook. |
| ResourceId |Identyfikator zasobu konta Azure Automation elementu Runbook. |
| SubscriptionId | Identyfikator GUID subskrypcji platformy Azure dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Dostawca zasobów. Wartość to MICROSOFT. Automatyzacji. |
| ResourceType | Typ zasobu. Wartość to AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników usługi Automation w dziennikach Azure Monitor

Teraz po rozpoczęciu wysyłania strumieni zadań usługi Automation i dzienników do dzienników Azure Monitor Zobaczmy, co możesz zrobić z tymi dziennikami w Azure Monitor dzienników.

Aby wyświetlić dzienniki, uruchom następujące zapytanie: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Wyślij wiadomość e-mail, gdy zadanie elementu Runbook ulegnie awarii lub zostanie zawieszone

Poniższe kroki pokazują, jak skonfigurować alerty w Azure Monitor, aby powiadomić Cię, gdy coś się nie udaje z zadania elementu Runbook.

Aby utworzyć regułę alertu, Zacznij od utworzenia wyszukiwania w dzienniku dla rekordów zadań elementu Runbook, które powinny wywoływać alert. Kliknij przycisk **alert** , aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd obszaru roboczego Log Analytics kliknij pozycję **Wyświetl dzienniki**.

2. Utwórz zapytanie przeszukiwania dzienników dla alertu, wpisując następujące polecenie wyszukiwania w polu zapytania: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Można również grupować według nazwy elementu Runbook za pomocą: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Jeśli skonfigurujesz dzienniki z więcej niż jednego konta usługi Automation lub subskrypcji w obszarze roboczym, możesz grupować alerty według subskrypcji i konta usługi Automation. Nazwę konta usługi Automation można znaleźć w `Resource` polu wyszukiwania `JobLogs` .

3. Aby otworzyć ekran **Utwórz regułę** , kliknij pozycję **Nowa reguła alertu** u góry strony. Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [alerty dzienników na platformie Azure](../azure-monitor/alerts/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Znajdź wszystkie zadania, które zostały zakończone z błędami

Oprócz alertów dotyczących błędów można znaleźć, kiedy zadanie elementu Runbook ma błąd niepowodujący zakończenia. W takich przypadkach program PowerShell tworzy strumień błędów, ale błędy niepowodujące zakończenia nie powodują zawieszenia zadania lub jego niepowodzenie.

1. W obszarze roboczym Log Analytics kliknij pozycję **dzienniki**.

2. W polu zapytania wpisz `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Kliknij przycisk **Wyszukaj** .

### <a name="view-job-streams-for-a-job"></a>Wyświetlanie strumieni zadań dla zadania

Podczas debugowania zadania warto również zajrzeć do strumienia zadań. Następujące zapytanie wyświetla wszystkie strumienie dla pojedynczego zadania o identyfikatorze GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Wyświetl stan zadania historycznego

Na koniec możesz chcieć wizualizować historię zadań z upływem czasu. To zapytanie służy do wyszukiwania stanu zadań w czasie.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Wykres stanu zadania Log Analytics historycznego](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtrowanie danych wyjściowych stanu zadania konwertowane na obiekt JSON

Ostatnio zmieniono zachowanie sposobu zapisywania danych dziennika usługi Automation w `AzureDiagnostics` tabeli w usłudze log Analytics, gdzie nie powoduje już podziału właściwości JSON na osobne pola. Jeśli element Runbook został skonfigurowany do formatowania obiektów w strumieniu danych wyjściowych w formacie JSON jako oddzielnych kolumn, należy ponownie skonfigurować zapytania, aby przeanalizować to pole do obiektu JSON w celu uzyskania dostępu do tych właściwości. Jest to realizowane za pomocą [parseJSON](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) , aby uzyskać dostęp do określonego elementu JSON w znanej ścieżce.

Na przykład element Runbook formatuje Właściwość *ResultDescription* w strumieniu danych wyjściowych w formacie JSON z wieloma polami. Aby wyszukać stan zadań, które znajdują się w stanie niepowodzenia określonym w polu o nazwie **status**, użyj tego przykładowego zapytania, aby przeszukać *ResultDescription* ze stanem **Niepowodzenie**:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics historyczny format JSON strumienia zadań](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak tworzyć zapytania wyszukiwania i przeglądać dzienniki zadań usługi Automation za pomocą dzienników Azure Monitor, zobacz [Wyszukiwanie w dzienniku w](../azure-monitor/logs/log-query-overview.md)dziennikach Azure monitor.
* Aby zrozumieć tworzenie i pobieranie komunikatów wyjściowych i błędów z elementów Runbook, zobacz [monitorowanie danych wyjściowych elementu Runbook](automation-runbook-output-and-messages.md).
* Aby dowiedzieć się więcej o wykonywaniu elementów Runbook, sposobie monitorowania zadań elementów Runbook i innych szczegółach technicznych, zobacz [wykonywanie elementów Runbook w Azure Automation](automation-runbook-execution.md).
* Aby dowiedzieć się więcej na temat dzienników Azure Monitor i źródeł zbierania danych, zobacz [zbieranie danych usługi Azure Storage w dziennikach Azure monitor Omówienie](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
* Aby uzyskać pomoc w rozwiązywaniu problemów Log Analytics, zobacz [Rozwiązywanie problemów, dlaczego log Analytics nie zbiera już danych](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).