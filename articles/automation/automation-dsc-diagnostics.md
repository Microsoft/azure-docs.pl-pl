---
title: Integracja z dziennikami Azure Monitor
description: W tym artykule opisano, jak wysyłać dane o konfiguracji żądanego stanu z konfiguracji stanu Azure Automation do Azure Monitor dzienników.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 34bbf34d53c44dcef7b8e128a93ee64201423c3e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897041"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integracja z dziennikami Azure Monitor

Azure Automation konfiguracja stanu zachowuje dane stanu węzła przez 30 dni. Dane stanu węzła można wysłać do obszaru roboczego Log Analytics, jeśli wolisz zachować te dane przez dłuższy okres. Stan zgodności jest widoczny w Azure Portal lub w programie PowerShell dla węzłów i dla indywidualnych zasobów DSC w konfiguracjach węzłów. 

Dzienniki Azure Monitor zapewniają większą widoczność operacyjną danych konfiguracji stanu usługi Automation i mogą szybciej rozwiązywać problemy. Za pomocą dzienników Azure Monitor można:

- Uzyskaj informacje o zgodności dla węzłów zarządzanych i poszczególnych zasobów.
- Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zgodności.
- Zapisuj zaawansowane zapytania w węzłach zarządzanych.
- Skorelowanie stanu zgodności na kontach usługi Automation.
- Za pomocą widoków niestandardowych i zapytań wyszukiwania można wizualizować wyniki elementu Runbook, stan zadania elementu Runbook oraz inne powiązane wskaźniki lub metryki kluczy.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wysyłanie raportów konfiguracji stanu usługi Automation do dzienników Azure Monitor, potrzebne są:

- Wydanie z listopada 2016 lub nowszą [Azure PowerShell](/powershell/azure/) (v 2.3.0).
- Konto usługi Azure Automation. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Automation](automation-intro.md).
- Obszar roboczy Log Analytics z ofertą usługi Automation & Control. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z log Analytics w Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md).
- Co najmniej jeden węzeł konfiguracji stanu Azure Automation. Aby uzyskać więcej informacji, zobacz sekcję dołączanie [maszyn w celu zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).
- Moduł [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) w wersji 2.7.0.0 lub nowszej. Kroki instalacji znajdują się w temacie [Rozwiązywanie problemów z konfiguracją żądanego stanu Azure Automation](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurowanie integracji z dziennikami Azure Monitor

Aby rozpocząć importowanie danych z konfiguracji stanu Azure Automation do dzienników Azure Monitor, wykonaj następujące czynności:

1. Zaloguj się do konta platformy Azure w programie PowerShell. Zobacz [Logowanie za pomocą Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Aby uzyskać identyfikator zasobu konta usługi Automation, należy uruchomić następujące polecenie cmdlet programu PowerShell. Jeśli masz więcej niż jedno konto usługi Automation, wybierz identyfikator zasobu dla konta, które chcesz skonfigurować.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Pobierz identyfikator zasobu obszaru roboczego Log Analytics, uruchamiając następujące polecenie cmdlet programu PowerShell. Jeśli masz więcej niż jeden obszar roboczy, wybierz identyfikator zasobu dla obszaru roboczego, który chcesz skonfigurować.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Uruchom następujące polecenie cmdlet programu PowerShell, `<AutomationResourceId>` zastępując `<WorkspaceResourceId>` wartości i `ResourceId` wartościami z każdego z poprzednich kroków.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Jeśli chcesz zatrzymać importowanie danych z konfiguracji stanu Azure Automation do dzienników Azure Monitor, uruchom następujące polecenie cmdlet programu PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Wyświetlanie dzienników konfiguracji stanu

Po skonfigurowaniu integracji z dziennikami Azure Monitor dla danych konfiguracji stanu usługi Automation można je wyświetlić, wybierając pozycję **dzienniki** w sekcji **monitorowanie** w lewym okienku na stronie Konfiguracja stanu (DSC).

![Dzienniki](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Zostanie otwarte okienko przeszukiwania dzienników z regionem zapytania objętym zakresem zasobów konta usługi Automation. Dzienniki konfiguracji stanu dla operacji DSC można wyszukać, wyszukując w dziennikach Azure Monitor. Rekordy dla operacji DSC są przechowywane w `AzureDiagnostics` tabeli. Na przykład aby znaleźć węzły, które nie są zgodne, wpisz następujące zapytanie.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Szczegóły filtrowania:

* Odfiltruj w `DscNodeStatusData` celu zwrócenia operacji dla każdego węzła konfiguracji stanu.
* Odfiltruj w `DscResourceStatusData` celu zwrócenia operacji dla każdego zasobu DSC o nazwie w konfiguracji węzła zastosowanej do tego zasobu. 
* Odfiltruj, `DscResourceStatusData` aby zwracać informacje o błędzie dla wszystkich zasobów DSC, które nie powiodą się.

Aby dowiedzieć się więcej na temat konstruowania zapytań dzienników do znajdowania danych, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Wyślij wiadomość e-mail, gdy sprawdzanie zgodności konfiguracji stanu nie powiedzie się

Jednym z naszych najpopularniejszych żądań klientów jest możliwość wysyłania wiadomości e-mail lub tekstu, gdy coś się nie stało z konfiguracją DSC.

Aby utworzyć regułę alertu, Zacznij od utworzenia w dzienniku wyszukiwania rekordów raportów o konfiguracji stanu, które powinny wywoływać alert. Kliknij przycisk **Nowa reguła alertu** , aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd obszaru roboczego Log Analytics kliknij pozycję **dzienniki**.
1. Utwórz kwerendę przeszukiwania dzienników dla alertu, wpisując następujące polecenie wyszukiwania w polu zapytania:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Jeśli skonfigurowano dzienniki z więcej niż jednego konta usługi Automation lub subskrypcji w obszarze roboczym, można grupować alerty według subskrypcji i konta usługi Automation. Utwórz nazwę konta usługi Automation na podstawie `Resource` pola wyszukiwania `DscNodeStatusData` rekordów.
1. Aby otworzyć ekran **Utwórz regułę** , kliknij pozycję **Nowa reguła alertu** u góry strony. 

Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [Tworzenie reguły alertu](../azure-monitor/platform/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Znajdź niepowodzenie zasobów DSC we wszystkich węzłach

Jedną z zalet korzystania z dzienników Azure Monitor jest możliwość wyszukiwania testów zakończonych niepowodzeniem między węzłami. Aby znaleźć wszystkie wystąpienia zasobów DSC, które zakończyły się niepowodzeniem:

1. Na stronie Przegląd obszaru roboczego Log Analytics kliknij pozycję **dzienniki**.
1. Utwórz zapytanie przeszukiwania dzienników dla alertu, wpisując następujące polecenie wyszukiwania w polu zapytania:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Wyświetlanie historycznego stanu węzła DSC

Aby wizualizować historię stanu węzła DSC w czasie, możesz użyć tego zapytania:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

To zapytanie wyświetla wykres stanu węzła w czasie.

## <a name="azure-monitor-logs-records"></a>Rekordy dzienników Azure Monitor

Azure Automation Diagnostics Utwórz dwie kategorie rekordów w dziennikach Azure Monitor:

* Dane stanu węzła ( `DscNodeStatusData` )
* Dane stanu zasobu ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina uruchomienia sprawdzania zgodności. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Wartość wskazująca, czy węzeł jest zgodny. |
| NodeName_s |Nazwa zarządzanego węzła. |
| NodeComplianceStatus_s |Wartość stanu określająca, czy węzeł jest zgodny. |
| DscReportStatus |Wartość stanu wskazująca, czy sprawdzanie zgodności zostało pomyślnie wykonane. |
| ConfigurationMode | Tryb służący do zastosowania konfiguracji do węzła. Możliwe wartości: <ul><li>`ApplyOnly`: Konfiguracja DSC stosuje konfigurację i nie wykonuje żadnych dalszych operacji, chyba że nowa konfiguracja jest wypychana do węzła docelowego lub gdy nowa konfiguracja zostanie pościągnięta z serwera. Po początkowej aplikacji nowej konfiguracji DSC nie sprawdza dryfu od wcześniej skonfigurowanego stanu. Konfiguracja DSC próbuje zastosować konfigurację do momentu jego pomyślnego zakończenia `ApplyOnly` . </li><li>`ApplyAndMonitor`: Jest to wartość domyślna. LCM stosuje wszelkie nowe konfiguracje. Po początkowej aplikacji nowej konfiguracji, jeśli węzeł docelowy zostanie przedryfem z żądanego stanu, DSC zgłosi niezgodność w dziennikach. Konfiguracja DSC próbuje zastosować konfigurację do momentu jego pomyślnego zakończenia `ApplyAndMonitor` .</li><li>`ApplyAndAutoCorrect`: Konfiguracja DSC stosuje wszelkie nowe konfiguracje. Po początkowym zastosowaniu nowej konfiguracji, jeśli węzeł docelowy zostanie przedryfem z żądanego stanu, DSC zgłosi niezgodność w dziennikach, a następnie ponownie zastosuje bieżącą konfigurację.</li></ul> |
| HostName_s | Nazwa zarządzanego węzła. |
| IPAddress | Adres IPv4 węzła zarządzanego. |
| Kategoria | `DscNodeStatus`. |
| Zasób | Nazwa konta Azure Automation. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawcę dla obiektu wywołującego. |
| NodeId_g | Identyfikator GUID, który identyfikuje zarządzany węzeł. |
| DscReportId_g | Identyfikator GUID, który identyfikuje raport. |
| LastSeenTime_t | Data i godzina ostatniego wyświetlenia raportu. |
| ReportStartTime_t | Data i godzina uruchomienia raportu. |
| ReportEndTime_t | Data i godzina zakończenia raportu. |
| NumberOfResources_d | Liczba zasobów DSC wywoływanych w konfiguracji zastosowanej do węzła. |
| SourceSystem | System źródłowy identyfikujący, w jaki sposób dzienniki Azure Monitor zostały zebrane. Zawsze `Azure` na potrzeby diagnostyki platformy Azure. |
| ResourceId |Identyfikator zasobu konta Azure Automation. |
| ResultDescription | Opis zasobu dla tej operacji. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Programu. Automatyzacji. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Identyfikator GUID, który jest identyfikatorem korelacji raportu zgodności. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina uruchomienia sprawdzania zgodności. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Czy zasób jest zgodny. |
| NodeName_s |Nazwa zarządzanego węzła. |
| Kategoria | DscNodeStatus. |
| Zasób | Nazwa konta Azure Automation. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawcę dla obiektu wywołującego. |
| NodeId_g |Identyfikator GUID, który identyfikuje zarządzany węzeł. |
| DscReportId_g |Identyfikator GUID, który identyfikuje raport. |
| DscResourceId_s |Nazwa wystąpienia zasobu DSC. |
| DscResourceName_s |Nazwa zasobu DSC. |
| DscResourceStatus_s |Czy zasób DSC jest zgodny. |
| DscModuleName_s |Nazwa modułu programu PowerShell zawierającego zasób DSC. |
| DscModuleVersion_s |Wersja modułu programu PowerShell, która zawiera zasób DSC. |
| DscConfigurationName_s |Nazwa konfiguracji zastosowanej do węzła. |
| ErrorCode_s | Kod błędu, jeśli zasób nie powiódł się. |
| ErrorMessage_s |Komunikat o błędzie, jeśli zasób nie powiódł się. |
| DscResourceDuration_d |Czas (w sekundach) uruchomienia zasobu DSC. |
| SourceSystem | Jak dzienniki Azure Monitor zbierane dane. Zawsze `Azure` na potrzeby diagnostyki platformy Azure. |
| ResourceId |Identyfikator konta Azure Automation. |
| ResultDescription | Opis tej operacji. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Programu. Automatyzacji. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID, który jest IDENTYFIKATORem korelacji raportu zgodności. |

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [Omówienie konfiguracji stanu Azure Automation](automation-dsc-overview.md).
- Aby rozpocząć, zobacz Wprowadzenie do [konfiguracji stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
- Aby dowiedzieć się więcej na temat tworzenia różnych zapytań wyszukiwania i przeglądania dzienników konfiguracji stanu usługi Automation za pomocą dzienników Azure Monitor, zobacz [Wyszukiwanie w dzienniku w](../azure-monitor/log-query/log-query-overview.md)dziennikach Azure monitor.
- Aby dowiedzieć się więcej na temat dzienników Azure Monitor i źródeł zbierania danych, zobacz [zbieranie danych usługi Azure Storage w dziennikach Azure monitor Omówienie](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).