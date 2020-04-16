---
title: Przesyłanie dalej danych raportowania konfiguracji stanu usługi Azure Automation do dzienników usługi Azure Monitor
description: W tym artykule pokazano, jak wysłać dane raportowania żądanej konfiguracji stanu (DSC) z konfiguracji stanu automatyzacji platformy Azure do dzienników usługi Azure Monitor, aby zapewnić dodatkowe informacje i zarządzanie.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: badd8ba676ef25c33a5034bb04d616faeb4ef1b0
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392094"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Przesyłanie dalej danych raportowania konfiguracji stanu usługi Azure Automation do dzienników usługi Azure Monitor

Konfiguracja stanu automatyzacji platformy Azure zachowuje dane o stanie węzła przez 30 dni. Jeśli wolisz zachować te dane przez dłuższy czas, można wysłać dane o stanie węzła do obszaru roboczego usługi Log Analytics. Stan zgodności jest widoczny w witrynie Azure portal lub z programem PowerShell, dla węzłów i dla poszczególnych zasobów DSC w konfiguracjach węzłów. 

Dzienniki usługi Azure Monitor zapewniają lepszą widoczność operacyjną danych konfiguracji stanu automatyzacji i mogą pomóc w sprawowaniu zdarzeń szybciej. Za pomocą dzienników usługi Azure Monitor można:

- Uzyskaj informacje o zgodności dla węzłów zarządzanych i poszczególnych zasobów.
- Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zgodności.
- Pisanie zaawansowanych zapytań w węzłach zarządzanych.
- Skorelowanie stanu zgodności między kontami automatyzacji.
- Widoki niestandardowe i zapytania wyszukiwania umożliwia wizualizację wyników elementu runbook, stanu zadania elementu runbook i innych powiązanych kluczowych wskaźników lub metryk.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wysyłanie raportów konfiguracji stanu automatyzacji do dzienników usługi Azure Monitor, potrzebujesz:

- Wersja [programu Azure PowerShell](/powershell/azure/overview) z listopada 2016 r. lub nowsza (wersja 2.3.0).
- Konto usługi Azure Automation. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Automation](automation-intro.md).
- Obszar roboczy usługi Log Analytics z ofertą usługi Automation & Control. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Co najmniej jeden węzeł konfiguracji stanu automatyzacji platformy Azure. Aby uzyskać więcej informacji, zobacz [Maszyny dołączania do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md).
- Moduł [xDscDiagnostics,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) wersja 2.7.0.0 lub większa. Aby zapoznać się z instrukcjami instalacji, zobacz [Rozwiązywanie problemów z konfiguracją żądanego stanu automatyzacji platformy Azure](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurowanie integracji z dziennikami usługi Azure Monitor

Aby rozpocząć importowanie danych z konfiguracji stanu automatyzacji platformy Azure do dzienników usługi Azure Monitor, wykonaj następujące kroki:

1. Zaloguj się do swojego konta platformy Azure w programie PowerShell. Zobacz [Logowanie się za pomocą programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Pobierz identyfikator zasobu konta automatyzacji, uruchamiając następujące polecenie cmdlet programu PowerShell. Jeśli masz więcej niż jedno konto automatyzacji, wybierz identyfikator zasobu dla konta, które chcesz skonfigurować.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Pobierz identyfikator zasobu obszaru roboczego usługi Log Analytics, uruchamiając następujące polecenie cmdlet programu PowerShell. Jeśli masz więcej niż jeden obszar roboczy, wybierz identyfikator zasobu dla obszaru roboczego, który chcesz skonfigurować.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Uruchom następujące polecenie cmdlet programu `<AutomationResourceId>` `<WorkspaceResourceId>` PowerShell, zastępując i `ResourceId` wartości z każdego z poprzednich kroków.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Jeśli chcesz zatrzymać importowanie danych z konfiguracji stanu automatyzacji platformy Azure do dzienników usługi Azure Monitor, uruchom następujące polecenie cmdlet programu PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Wyświetlanie dzienników konfiguracji stanu

Po skonfigurowaniu integracji z dziennikami usługi Azure Monitor dla danych konfiguracji stanu automatyzacji można je wyświetlić, wybierając **dzienniki** w sekcji **Monitorowanie** w lewym okienku strony konfiguracji stanu (DSC).

![Dzienniki](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Zostanie otwarte okienko wyszukiwania dzienników z regionem kwerendy o zakresie do zasobu konta automatyzacji. Można przeszukiwać dzienniki konfiguracji stanu dla operacji DSC, wyszukując w dziennikach usługi Azure Monitor. Rekordy dla operacji DSC są `AzureDiagnostics` przechowywane w tabeli. Na przykład, aby znaleźć węzły, które nie są zgodne, wpisz następującą kwerendę.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Szczegóły filtrowania:

* Filtruj, `DscNodeStatusData` aby zwrócić operacje dla każdego węzła konfiguracji stanu.
* Filtruj, `DscResourceStatusData` aby zwrócić operacje dla każdego zasobu DSC wywoływane w konfiguracji węzła zastosowanego do tego zasobu. 
* Filtruj, `DscResourceStatusData` aby zwrócić informacje o błędzie dla wszystkich zasobów DSC, które nie powiodą się.

Aby dowiedzieć się więcej na temat konstruowania zapytań dziennika w celu znalezienia danych, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Wysyłanie wiadomości e-mail po awarii sprawdzania zgodności konfiguracji stanu

Jednym z naszych najlepszych żądań klientów jest możliwość wysyłania wiadomości e-mail lub tekstu, gdy coś pójdzie nie tak z konfiguracją DSC.

Aby utworzyć regułę alertu, należy rozpocząć od utworzenia wyszukiwania dziennika dla rekordów raportu konfiguracji stanu, które powinny wywoływać alert. Kliknij przycisk **Nowa reguła alertu,** aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Omówienie obszaru roboczego usługi Log Analytics kliknij pozycję **Dzienniki**.
1. Utwórz zapytanie wyszukiwania dziennika dla alertu, wpisując następujące wyszukiwanie w polu kwerendy:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Jeśli skonfigurowano dzienniki z więcej niż jednego konta automatyzacji lub subskrypcji do obszaru roboczego, można pogrupować alerty według subskrypcji i konta automatyzacji. Wykierowuj `Resource` nazwę konta automatyzacji `DscNodeStatusData` z pola w wyszukiwaniu rekordów.
1. Aby otworzyć ekran **Reguła tworzenia,** kliknij pozycję **Nowa reguła alertu** u góry strony. 

Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [Tworzenie reguły alertu](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Znajdowanie nieudanych zasobów DSC we wszystkich węzłach

Jedną z zalet korzystania z dzienników usługi Azure Monitor jest to, że można wyszukiwać nieudanych kontroli w węzłach. Aby znaleźć wszystkie wystąpienia zasobów DSC, które nie powiodły się:

1. Na stronie Omówienie obszaru roboczego usługi Log Analytics kliknij pozycję **Dzienniki**.
1. Utwórz zapytanie wyszukiwania dziennika dla alertu, wpisując następujące wyszukiwanie w polu kwerendy:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Wyświetlanie historycznego stanu węzła DSC

Aby wizualizować historię stanu węzła DSC w czasie, można użyć tej kwerendy:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Ta kwerenda wyświetla wykres stanu węzła w czasie.

## <a name="azure-monitor-logs-records"></a>Usługa Azure Monitor rejestruje rekordy

Diagnostyka usługi Azure Automation tworzy dwie kategorie rekordów w dziennikach usługi Azure Monitor:

* Dane o`DscNodeStatusData`stanie węzła ( )
* Dane o`DscResourceStatusData`stanie zasobu ( )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina, kiedy uruchomiono kontrolę zgodności. |
| OperationName |`DscNodeStatusData`. |
| Resulttype |Wartość, która wskazuje, czy węzeł jest zgodny. |
| NodeName_s |Nazwa węzła zarządzanego. |
| NodeComplianceStatus_s |Wartość stanu określająca, czy węzeł jest zgodny. |
| DscReportStatus (Stan dscreport) |Wartość stanu wskazująca, czy sprawdzanie zgodności przebiega pomyślnie. |
| Tryb konfiguracji | Tryb używany do stosowania konfiguracji do węzła. Możliwe wartości: <ul><li>`ApplyOnly`: DSC stosuje konfigurację i nie robi nic dalej, chyba że nowa konfiguracja jest wypychany do węzła docelowego lub gdy nowa konfiguracja jest pobierana z serwera. Po początkowym zastosowaniu nowej konfiguracji DSC nie sprawdza, czy nie ma dryfu z wcześniej skonfigurowanego stanu. DSC próbuje zastosować konfigurację, dopóki `ApplyOnly` nie zakończy się pomyślnie, zanim wartość zacznie obowiązywać. </li><li>`ApplyAndMonitor`: Jest to wartość domyślna. LCM stosuje wszystkie nowe konfiguracje. Po początkowym zastosowaniu nowej konfiguracji, jeśli węzeł docelowy dryfuje od żądanego stanu, DSC zgłasza rozbieżności w dziennikach. DSC próbuje zastosować konfigurację, dopóki `ApplyAndMonitor` nie zakończy się pomyślnie, zanim wartość zacznie obowiązywać.</li><li>`ApplyAndAutoCorrect`: DSC stosuje wszystkie nowe konfiguracje. Po początkowym zastosowaniu nowej konfiguracji, jeśli węzeł docelowy odpędnie się od żądanego stanu, DSC zgłasza rozbieżność w dziennikach, a następnie ponownie wykorzystuje bieżącą konfigurację.</li></ul> |
| HostName_s | Nazwa węzła zarządzanego. |
| IPAddress | Adres IPv4 węzła zarządzanego. |
| Kategoria | `DscNodeStatus`. |
| Zasób | Nazwa konta usługi Azure Automation. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla wywołującego. |
| NodeId_g | Identyfikator GUID identyfikujący węzeł zarządzany. |
| DscReportId_g | Identyfikator GUID identyfikujący raport. |
| LastSeenTime_t | Data i godzina ostatniego wyświetlenia raportu. |
| ReportStartTime_t | Data i godzina rozpoczęcia raportu. |
| ReportEndTime_t | Data i godzina zakończenia raportu. |
| NumberOfResources_d | Liczba zasobów DSC wywoływanych w konfiguracji zastosowanej do węzła. |
| SourceSystem | System źródłowy identyfikujący sposób dzienników usługi Azure Monitor zebrał dane. Zawsze `Azure` dla diagnostyki platformy Azure. |
| ResourceId |Identyfikator zasobu konta usługi Azure Automation. |
| Opis wyników | Opis zasobu dla tej operacji. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | Microsoft. Automatyzacji. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Identyfikator GUID, który jest identyfikatorem korelacji raportu zgodności. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina, kiedy uruchomiono kontrolę zgodności. |
| OperationName |`DscResourceStatusData`.|
| Resulttype |Czy zasób jest zgodny. |
| NodeName_s |Nazwa węzła zarządzanego. |
| Kategoria | DscNodeStatus. |
| Zasób | Nazwa konta usługi Azure Automation. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla wywołującego. |
| NodeId_g |Identyfikator GUID identyfikujący węzeł zarządzany. |
| DscReportId_g |Identyfikator GUID identyfikujący raport. |
| DscResourceId_s |Nazwa wystąpienia zasobu DSC. |
| DscResourceName_s |Nazwa zasobu DSC. |
| DscResourceStatus_s |Czy zasób DSC jest zgodny. |
| DscModuleName_s |Nazwa modułu programu PowerShell zawierającego zasób DSC. |
| DscModuleVersion_s |Wersja modułu programu PowerShell zawierająca zasób DSC. |
| DscConfigurationName_s |Nazwa konfiguracji zastosowanej do węzła. |
| ErrorCode_s | Kod błędu, jeśli zasób nie powiódł się. |
| ErrorMessage_s |Komunikat o błędzie, jeśli zasób nie powiódł się. |
| DscResourceDuration_d |Czas w sekundach, który uruchomiono zasób DSC. |
| SourceSystem | Jak dzienniki usługi Azure Monitor zebrane dane. Zawsze `Azure` dla diagnostyki platformy Azure. |
| ResourceId |Identyfikator konta usługi Azure Automation. |
| Opis wyników | Opis tej operacji. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta automatyzacji. |
| ResourceGroup | Nazwa grupy zasobów dla konta automatyzacji. |
| ResourceProvider | Microsoft. Automatyzacji. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji raportu zgodności. |


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Konfiguracja stanu automatyzacji platformy Azure](automation-dsc-overview.md).
- Aby uzyskać informacje o tym, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Aby zobaczyć przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md).
- Aby dowiedzieć się więcej na temat konstruowania różnych zapytań wyszukiwania i przejrzenia dzienników konfiguracji stanu automatyzacji za pomocą dzienników usługi Azure Monitor, zobacz [Dzienniki wyszukiwania w dziennikach usługi Azure Monitor.](../log-analytics/log-analytics-log-searches.md)
- Aby dowiedzieć się więcej o dziennikach i źródłach zbierania danych usługi Azure Monitor, zobacz [Zbieranie danych magazynu platformy Azure w omówieniu dzienników usługi Azure Monitor.](../azure-monitor/platform/collect-azure-metrics-logs.md)
