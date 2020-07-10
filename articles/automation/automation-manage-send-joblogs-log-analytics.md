---
title: Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor
description: W tym artykule opisano sposób wysyłania strumieni stanu zadania i zadań elementu Runbook do dzienników Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0c8863615446f8b14043cd7bd13e529b7efa1e46
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186201"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor

Azure Automation może wysyłać strumienie zadań elementu Runbook i zadań do obszaru roboczego Log Analytics. Ten proces nie obejmuje łączenia obszarów roboczych i jest całkowicie niezależny. Dzienniki zadań i strumienie zadań są widoczne w Azure Portal lub w programie PowerShell dla poszczególnych zadań. pozwala to na wykonywanie prostych badań. Teraz z dziennikami Azure Monitor można:

* Uzyskaj wgląd w informacje o stanie zadań automatyzacji.
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania elementu Runbook (na przykład Niepowodzenie lub wstrzymane).
* Zapisuj zaawansowane zapytania w strumieniach zadań.
* Skorelowanie zadań na kontach usługi Automation.
* Za pomocą widoków niestandardowych i zapytań wyszukiwania można wizualizować wyniki elementu Runbook, stan zadania elementu Runbook oraz inne powiązane wskaźniki lub metryki kluczy.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Wymagania wstępne i zagadnienia dotyczące wdrażania

Aby rozpocząć wysyłanie dzienników usługi Automation do dzienników Azure Monitor, potrzebne są:

* Najnowsza wersja [Azure PowerShell](/powershell/azureps-cmdlets-docs/).
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../azure-monitor/overview.md).
* Identyfikator zasobu dla konta Azure Automation.

Użyj następującego polecenia, aby znaleźć identyfikator zasobu dla konta Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Aby znaleźć identyfikator zasobu dla obszaru roboczego Log Analytics, uruchom następujące polecenie programu PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Jeśli masz więcej niż jedno konto usługi Automation lub obszar roboczy w danych wyjściowych powyższych poleceń, możesz znaleźć nazwę i inne powiązane właściwości, które są częścią pełnego identyfikatora zasobu konta usługi Automation, wykonując następujące czynności:

1. W Azure Portal wybierz konto usługi Automation na stronie **konta usługi Automation** . 
2. Na stronie wybranego konta usługi Automation w obszarze **Ustawienia konta**wybierz pozycję **Właściwości**.  
3. Na stronie **Właściwości** Zanotuj wymienione poniżej szczegóły.

    ![Właściwości konta usługi Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

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
| Result |Stan zadania elementu Runbook. Możliwe wartości:<br>-Nowe<br>-Utworzono<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Ukończono |
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
| Result |Stan zadania elementu Runbook. Możliwe wartości:<br>— W toku |
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

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurowanie integracji z dziennikami Azure Monitor

1. Na komputerze Uruchom program Windows PowerShell z ekranu **startowego** .
2. Uruchom następujące polecenia programu PowerShell i edytuj wartości dla `$automationAccountId` i `$workspaceId` z wartościami z poprzedniej sekcji.

   ```powershell-interactive
   $workspaceId = "resource ID of the log analytics workspace"
   $automationAccountId = "resource ID of your Automation account"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po uruchomieniu tego skryptu może upłynąć godzinę przed rozpoczęciem wyświetlania rekordów Azure Monitor nowych `JobLogs` lub `JobStreams` zapisywanych dzienników.

Aby wyświetlić dzienniki, uruchom następujące zapytanie w przeszukiwaniu dzienników usługi log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Weryfikuj konfigurację

Aby upewnić się, że konto usługi Automation wysyła dzienniki do obszaru roboczego Log Analytics, sprawdź, czy na koncie usługi Automation prawidłowo skonfigurowano diagnostykę przy użyciu następującego polecenia programu PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

W danych wyjściowych upewnij się, że:

* W obszarze `Logs` wartość `Enabled` jest równa true.
* `WorkspaceId`ma ustawioną `ResourceId` wartość dla obszaru roboczego log Analytics.

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników usługi Automation w dziennikach Azure Monitor

Teraz, po rozpoczęciu wysyłania dzienników zadań usługi Automation do dzienników Azure Monitor, zobaczmy, co możesz zrobić z tymi dziennikami w Azure Monitor dzienników.

Aby wyświetlić dzienniki, uruchom następujące zapytanie:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Wyślij wiadomość e-mail, gdy zadanie elementu Runbook ulegnie awarii lub zostanie zawieszone

Poniższe kroki pokazują, jak skonfigurować alerty w Azure Monitor, aby powiadomić Cię, gdy coś się nie udaje z zadania elementu Runbook.

Aby utworzyć regułę alertu, Zacznij od utworzenia wyszukiwania w dzienniku dla rekordów zadań elementu Runbook, które powinny wywoływać alert. Kliknij przycisk **alert** , aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd obszaru roboczego Log Analytics kliknij pozycję **Wyświetl dzienniki**.

2. Utwórz zapytanie przeszukiwania dzienników dla alertu, wpisując następujące polecenie wyszukiwania w polu zapytania:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Można również grupować według nazwy elementu Runbook za pomocą:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Jeśli skonfigurujesz dzienniki z więcej niż jednego konta usługi Automation lub subskrypcji w obszarze roboczym, możesz grupować alerty według subskrypcji i konta usługi Automation. Nazwę konta usługi Automation można znaleźć w `Resource` polu wyszukiwania `JobLogs` .

3. Aby otworzyć ekran **Utwórz regułę** , kliknij pozycję **Nowa reguła alertu** u góry strony. Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [alerty dzienników na platformie Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Znajdź wszystkie zadania, które zostały zakończone z błędami

Oprócz alertów dotyczących błędów można znaleźć, kiedy zadanie elementu Runbook ma błąd niepowodujący zakończenia. W takich przypadkach program PowerShell tworzy strumień błędów, ale błędy niepowodujące zakończenia nie powodują zawieszenia zadania lub jego niepowodzenie.

1. W obszarze roboczym Log Analytics kliknij pozycję **dzienniki**.

2. W polu zapytania wpisz `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Kliknij przycisk **Wyszukaj** .

### <a name="view-job-streams-for-a-job"></a>Wyświetlanie strumieni zadań dla zadania

Podczas debugowania zadania warto również zajrzeć do strumienia zadań. Następujące zapytanie wyświetla wszystkie strumienie dla pojedynczego zadania o identyfikatorze GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Wyświetl stan zadania historycznego

Na koniec możesz chcieć wizualizować historię zadań z upływem czasu. To zapytanie służy do wyszukiwania stanu zadań w czasie.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Wykres stanu zadania Log Analytics historycznego](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Usuń ustawienia diagnostyczne

Aby usunąć ustawienie diagnostyczne z konta usługi Automation, uruchom następujące polecenie:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak tworzyć zapytania wyszukiwania i przeglądać dzienniki zadań usługi Automation za pomocą dzienników Azure Monitor, zobacz [Wyszukiwanie w dzienniku w](../azure-monitor/log-query/log-query-overview.md)dziennikach Azure monitor.
* Aby zrozumieć tworzenie i pobieranie komunikatów wyjściowych i błędów z elementów Runbook, zobacz [monitorowanie danych wyjściowych elementu Runbook](automation-runbook-output-and-messages.md).
* Aby dowiedzieć się więcej o wykonywaniu elementów Runbook, sposobie monitorowania zadań elementów Runbook i innych szczegółach technicznych, zobacz [wykonywanie elementów Runbook w Azure Automation](automation-runbook-execution.md).
* Aby dowiedzieć się więcej na temat dzienników Azure Monitor i źródeł zbierania danych, zobacz [zbieranie danych usługi Azure Storage w dziennikach Azure monitor Omówienie](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
* Aby uzyskać pomoc w rozwiązywaniu problemów Log Analytics, zobacz [Rozwiązywanie problemów, dlaczego log Analytics nie zbiera już danych](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
