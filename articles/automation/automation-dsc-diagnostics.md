---
title: Integracja z Azure Monitor dziennikami
description: W tym artykule opisano sposób wysyłania Desired State Configuration danych raportowania z Azure Automation State Configuration do Azure Monitor dzienników.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 8952ea87cfd9317225ecb9e313174f8d1fe8e519
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834808"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integracja z Azure Monitor dziennikami

Azure Automation State Configuration przechowuje dane o stanie węzła przez 30 dni. Dane o stanie węzła można wysłać do obszaru roboczego usługi Log Analytics, jeśli wolisz przechowywać te dane przez dłuższy czas. Stan zgodności jest widoczny w chmurze Azure Portal programie PowerShell dla węzłów i poszczególnych zasobów DSC w konfiguracjach węzłów. 

Azure Monitor zapewniają lepszy wgląd operacyjny w dane usługi Automation State Configuration i mogą pomóc szybciej rozwiązać zdarzenia. Za Azure Monitor dziennikach można:

- Uzyskaj informacje o zgodności dla węzłów zarządzanych i poszczególnych zasobów.
- Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zgodności.
- Pisanie zaawansowanych zapytań w węzłach zarządzanych.
- Skoreluj stan zgodności między kontami usługi Automation.
- Użyj widoków niestandardowych i zapytań wyszukiwania, aby zwizualizować wyniki, stan zadania runbook i inne powiązane kluczowe wskaźniki lub metryki.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wysyłanie raportów usługi Automation State Configuration do Azure Monitor, potrzebne są:

- Wersja z listopada 2016 r. lub Azure PowerShell [(wersja](/powershell/azure/) 2.3.0).
- Konto usługi Azure Automation. Aby uzyskać więcej informacji, [zobacz An introduction to Azure Automation (Wprowadzenie do Azure Automation](automation-intro.md)).
- Obszar roboczy usługi Log Analytics z ofertą & Automation. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Log Analytics w Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).
- Co najmniej jeden Azure Automation State Configuration węzła. Aby uzyskać więcej informacji, zobacz [Dołączanie maszyn do zarządzania przez Azure Automation State Configuration](automation-dsc-onboarding.md).
- Moduł [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) w wersji 2.7.0.0 lub większej. Aby uzyskać instrukcje instalacji, zobacz [Rozwiązywanie Azure Automation Desired State Configuration](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurowanie integracji z Azure Monitor dziennikami

Aby rozpocząć importowanie danych z Azure Automation State Configuration do Azure Monitor, wykonaj następujące kroki:

1. Zaloguj się do konta platformy Azure w programie PowerShell. Zobacz [Logowanie za pomocą Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Pobierz identyfikator zasobu konta usługi Automation, uruchamiając następujące polecenie cmdlet programu PowerShell. Jeśli masz więcej niż jedno konto automatyzacji, wybierz identyfikator zasobu dla konta, które chcesz skonfigurować.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Pobierz identyfikator zasobu obszaru roboczego usługi Log Analytics, uruchamiając następujące polecenie cmdlet programu PowerShell. Jeśli masz więcej niż jeden obszar roboczy, wybierz identyfikator zasobu dla obszaru roboczego, który chcesz skonfigurować.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Uruchom następujące polecenie cmdlet programu PowerShell, zastępując elementy i wartościami `<AutomationResourceId>` `<WorkspaceResourceId>` z `ResourceId` poszczególnych poprzednich kroków.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Jeśli chcesz zatrzymać importowanie danych z Azure Automation State Configuration do Azure Monitor, uruchom następujące polecenie cmdlet programu PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Wyświetlanie dzienników State Configuration użytkownika

Po skonfigurowaniu integracji z dziennikami Azure Monitor usługi Automation State Configuration można je  wyświetlić,  wybierając pozycję Dzienniki w sekcji Monitorowanie w lewym okienku strony Konfiguracja stanu (DSC).

![Dzienniki](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Zostanie otwarte okienko Przeszukiwanie dzienników z regionem zapytania w zakresie zasobu konta usługi Automation. Możesz przeszukiwać dzienniki State Configuration dsc, wyszukując je w Azure Monitor danych. Rekordy dla operacji DSC są przechowywane w `AzureDiagnostics` tabeli. Aby na przykład znaleźć niezgodne węzły, wpisz następujące zapytanie.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Szczegóły filtrowania:

* `DscNodeStatusData`Odfiltruj , aby zwrócić operacje dla State Configuration węzła.
* `DscResourceStatusData`Odfiltruj , aby zwrócić operacje dla każdego zasobu DSC o nazwie w konfiguracji węzła zastosowanej do tego zasobu. 
* `DscResourceStatusData`Odfiltruj, aby zwrócić informacje o błędzie dla wszystkich zasobów DSC, które się nie powiodły.

Aby dowiedzieć się więcej na temat tworzenia zapytań dziennika w celu znalezienia danych, zobacz Overview of log queries in Azure Monitor (Omówienie zapytań [dziennika w programie Azure Monitor](../azure-monitor/logs/log-query-overview.md)).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Wysyłanie wiadomości e-mail w przypadku State Configuration sprawdzania zgodności aplikacji

Jednym z naszych żądań klientów jest możliwość wysyłania wiadomości e-mail lub tekstu, gdy coś pójdzie nie tak z konfiguracją DSC.

Aby utworzyć regułę alertu, rozpocznij od utworzenia przeszukiwania dzienników dla rekordów State Configuration, które powinny wywoływać alert. Kliknij przycisk **Nowa reguła alertu,** aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd obszaru roboczego usługi Log Analytics kliknij pozycję **Dzienniki**.
1. Utwórz zapytanie przeszukiwania dzienników dla alertu, wpisując następujące wyszukiwanie w polu zapytania:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Jeśli masz ustawione dzienniki z więcej niż jednego konta usługi Automation lub subskrypcji do obszaru roboczego, możesz grupować alerty według subskrypcji i konta usługi Automation. Wyprowadz nazwę konta usługi Automation `Resource` z pola w wyszukiwaniu `DscNodeStatusData` rekordów.
1. Aby otworzyć ekran **Tworzenie reguły,** kliknij pozycję Nowa reguła **alertu** w górnej części strony. 

Aby uzyskać więcej informacji na temat opcji konfigurowania alertu, zobacz [Tworzenie reguły alertu](../azure-monitor/alerts/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Znajdowanie nieudanych zasobów DSC we wszystkich węzłach

Jedną z zalet dzienników Azure Monitor jest możliwość wyszukiwania testów, które zakończyły się niepowodzeniem w węzłach. Aby znaleźć wszystkie wystąpienia zasobów DSC, które zakończyły się niepowodzeniem:

1. Na stronie Przegląd obszaru roboczego usługi Log Analytics kliknij pozycję **Dzienniki.**
1. Utwórz zapytanie przeszukiwania dzienników dla alertu, wpisując następujące wyszukiwanie w polu zapytania:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Wyświetlanie historycznego stanu węzła DSC

Aby zwizualizować historię stanu węzła DSC w czasie, możesz użyć tego zapytania:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

To zapytanie wyświetla wykres stanu węzła w czasie.

## <a name="azure-monitor-logs-records"></a>Azure Monitor rejestruje rekordy

Azure Automation diagnostyki utwórz dwie kategorie rekordów w Azure Monitor dziennikach:

* Dane stanu węzła ( `DscNodeStatusData` )
* Dane stanu zasobu ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina rozpoczęcia sprawdzania zgodności. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Wartość wskazująca, czy węzeł jest zgodny. |
| NodeName_s |Nazwa węzła zarządzanego. |
| NodeComplianceStatus_s |Wartość stanu określająca, czy węzeł jest zgodny. |
| DscReportStatus |Wartość stanu wskazująca, czy sprawdzanie zgodności zakończyło się pomyślnie. |
| ConfigurationMode | Tryb używany do stosowania konfiguracji do węzła. Możliwe wartości: <ul><li>`ApplyOnly`: DSC stosuje konfigurację i nie robi nic dalej, chyba że nowa konfiguracja zostanie wypchniętą do węzła docelowego lub gdy nowa konfiguracja zostanie ściągnięta z serwera. Po początkowym zastosowaniu nowej konfiguracji dsc nie sprawdza dryfu od wcześniej skonfigurowanego stanu. DsC próbuje zastosować konfigurację, dopóki nie powiedzie się przed `ApplyOnly` zastosowaniem wartości. </li><li>`ApplyAndMonitor`: jest to wartość domyślna. LcM stosuje wszelkie nowe konfiguracje. Jeśli po początkowym zastosowaniu nowej konfiguracji węzeł docelowy oddala się od żądanego stanu, dsc zgłasza rozbieżności w dziennikach. DsC próbuje zastosować konfigurację, dopóki nie powiedzie się, `ApplyAndMonitor` zanim wartość zaimplikuje.</li><li>`ApplyAndAutoCorrect`: dsc stosuje wszystkie nowe konfiguracje. Po początkowym zastosowaniu nowej konfiguracji, jeśli węzeł docelowy oddala się od żądanego stanu, dsc zgłasza rozbieżności w dziennikach, a następnie ponownie rejestruje bieżącą konfigurację.</li></ul> |
| HostName_s | Nazwa węzła zarządzanego. |
| IPAddress | Adres IPv4 węzła zarządzanego. |
| Kategoria | `DscNodeStatus`. |
| Zasób | Nazwa konta Azure Automation magazynu. |
| Tenant_g | Identyfikator GUID identyfikujący dzierżawę dla wywołującego. |
| NodeId_g | Identyfikator GUID identyfikujący węzeł zarządzany. |
| DscReportId_g | Identyfikator GUID identyfikujący raport. |
| LastSeenTime_t | Data i godzina ostatniego wyświetlenia raportu. |
| ReportStartTime_t | Data i godzina rozpoczęcia raportu. |
| ReportEndTime_t | Data i godzina ukończenia raportu. |
| NumberOfResources_d | Liczba zasobów DSC o nazwie w konfiguracji zastosowanej do węzła. |
| SourceSystem | System źródłowy identyfikujący sposób Azure Monitor zbierał dane. Zawsze `Azure` w przypadku diagnostyki platformy Azure. |
| ResourceId |Identyfikator zasobu Azure Automation konta. |
| ResultDescription | Opis zasobu dla tej operacji. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Microsoft. Automatyzacji. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Identyfikator GUID, który jest identyfikatorem korelacji raportu zgodności. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina rozpoczęcia sprawdzania zgodności. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Czy zasób jest zgodny. |
| NodeName_s |Nazwa węzła zarządzanego. |
| Kategoria | DscNodeStatus. |
| Zasób | Nazwa konta Azure Automation użytkownika. |
| Tenant_g | Identyfikator GUID identyfikujący dzierżawę dla wywołującego. |
| NodeId_g |Identyfikator GUID identyfikujący węzeł zarządzany. |
| DscReportId_g |Identyfikator GUID identyfikujący raport. |
| DscResourceId_s |Nazwa wystąpienia zasobu DSC. |
| DscResourceName_s |Nazwa zasobu DSC. |
| DscResourceStatus_s |Czy zasób DSC jest zgodny. |
| DscModuleName_s |Nazwa modułu programu PowerShell, który zawiera zasób DSC. |
| DscModuleVersion_s |Wersja modułu programu PowerShell, który zawiera zasób DSC. |
| DscConfigurationName_s |Nazwa konfiguracji zastosowanej do węzła. |
| ErrorCode_s | Kod błędu, jeśli zasób zakończył się niepowodzeniem. |
| ErrorMessage_s |Komunikat o błędzie, jeśli zasób zakończył się niepowodzeniem. |
| DscResourceDuration_d |Czas( w sekundach) uruchomiono zasób DSC. |
| SourceSystem | Jak Azure Monitor zbierały dane. Zawsze `Azure` w przypadku diagnostyki platformy Azure. |
| ResourceId |Identyfikator konta Azure Automation użytkownika. |
| ResultDescription | Opis tej operacji. |
| SubscriptionId | Identyfikator subskrypcji platformy Azure (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | Microsoft. Automatyzacji. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji raportu zgodności. |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Azure Automation State Configuration omówienie.](automation-dsc-overview.md)
- Aby rozpocząć pracę, zobacz [Wprowadzenie do Azure Automation State Configuration](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC w celu przypisania ich do węzłów docelowych, zobacz Compile [DSC configurations in Azure Automation State Configuration](automation-dsc-compile.md).
- Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
- Aby uzyskać informacje o cenach, [zobacz Azure Automation State Configuration cennik.](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation State Configuration w potoku ciągłego wdrażania, zobacz Konfigurowanie ciągłego wdrażania [za pomocą narzędzia Chocolatey.](automation-dsc-cd-chocolatey.md)
- Aby dowiedzieć się więcej na temat tworzenia różnych zapytań wyszukiwania i przejrzeć dzienniki usługi Automation State Configuration z Azure Monitor, zobacz Przeszukiwanie dzienników w Azure Monitor [dziennikach.](../azure-monitor/logs/log-query-overview.md)
- Aby dowiedzieć się więcej o Azure Monitor danych i źródłach zbierania danych, zobacz Collecting Azure storage data in Azure Monitor logs overview (Zbieranie danych usługi Azure Storage w [dziennikach Azure Monitor omówienie).](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
