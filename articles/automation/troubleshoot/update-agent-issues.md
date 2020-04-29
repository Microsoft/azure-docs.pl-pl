---
title: Rozwiązywanie problemów z usługą Windows Update Agent w Azure Automation Update Management
description: Informacje na temat rozwiązywania problemów z usługą Windows Update Agent przy użyciu rozwiązania Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678976"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Rozwiązywanie problemów z usługą Windows Update Agent

Może istnieć wiele przyczyn, dla których Twoja maszyna nie jest wyświetlana jako gotowa (dobra kondycja) w Update Management. W Update Management można sprawdzić kondycję agenta hybrydowego procesu roboczego elementu Runbook w celu ustalenia podstawowego problemu. W tym artykule omówiono sposób uruchamiania narzędzia do rozwiązywania problemów dla maszyn platformy Azure z poziomu maszyn Azure Portal i spoza platformy Azure w [scenariuszu w trybie offline](#troubleshoot-offline).

Poniżej przedstawiono trzy Stany gotowości dla maszyny:

* Gotowy — został wdrożony hybrydowy proces roboczy elementu Runbook, który był ostatnio widoczny poniżej 1 godzinę temu.
* Rozłączono — został wdrożony hybrydowy proces roboczy elementu Runbook, który był ostatnio widoczny ponad 1 godzinę temu.
* Nie skonfigurowano — nie można odnaleźć hybrydowego procesu roboczego elementu Runbook lub nie zakończono dołączania.

> [!NOTE]
> Może istnieć niewielkie opóźnienie między elementami Azure Portal a bieżącym stanem maszyny.

## <a name="start-the-troubleshooter"></a>Uruchom narzędzie do rozwiązywania problemów

W przypadku maszyn platformy Azure kliknij link **Rozwiązywanie problemów** w kolumnie **Aktualizacja gotowości agenta** w portalu spowoduje uruchomienie strony Rozwiązywanie problemów z aktualizacją agenta. W przypadku maszyn spoza platformy Azure Link umożliwia przełączenie do tego artykułu. Zapoznaj się z [instrukcjami w trybie offline](#troubleshoot-offline) , aby rozwiązać problem z maszyną spoza platformy Azure.

![Lista zarządzania aktualizacjami dla maszyn wirtualnych](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Aby sprawdzić kondycję hybrydowego procesu roboczego elementu Runbook, maszyna wirtualna musi być uruchomiona. Jeśli maszyna wirtualna nie jest uruchomiona, zostanie wyświetlony przycisk **Uruchom maszynę wirtualną** .

Na stronie Rozwiązywanie problemów z agentem aktualizacji wybierz pozycję **Uruchom testy** , aby uruchomić narzędzie do rozwiązywania problemów. Narzędzie do rozwiązywania problemów używa [polecenia Uruchom](../../virtual-machines/windows/run-command.md) , aby uruchomić skrypt na komputerze w celu sprawdzenia zależności. Po zakończeniu narzędzia do rozwiązywania problemów zwraca wynik kontroli.

![Rozwiązywanie problemów z aktualizacją agenta](../media/update-agent-issues/troubleshoot-page.png)

Wyniki są wyświetlane na stronie, gdy są gotowe. Sekcje checks pokazują, co jest zawarte w każdej kontroli.

![Rozwiązywanie problemów z sprawdzaniem aktualizacji agenta](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Sprawdzanie wymagań wstępnych

### <a name="operating-system"></a>System operacyjny

Sprawdzenie systemu operacyjnego weryfikuje, czy hybrydowy proces roboczy elementu Runbook uruchamia jeden z następujących systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|System Windows Server 2012 lub nowszy |Wymagany jest .NET Framework 4,6 lub nowszy. ([Pobierz .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Wymagany jest program Windows PowerShell 5,1.  ([Pobierz system Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>4.6.2 .NET

.NET Framework sprawdza, czy w systemie jest zainstalowany co najmniej [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) .

### <a name="wmf-51"></a>WMF 5.1

Sprawdzanie plików WMF sprawdza, czy system ma wymaganą wersję programu Windows Management Framework (WMF) — [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Ta kontrola określa, czy używasz protokołu TLS 1,2 do szyfrowania komunikacji. Protokół TLS 1,0 nie jest już obsługiwany przez platformę. Zalecamy, aby klienci używali protokołu TLS 1,2 do komunikowania się z Update Management.

## <a name="connectivity-checks"></a>Sprawdzenia łączności

### <a name="registration-endpoint"></a>Punkt końcowy rejestracji

Ta kontrola określa, czy Agent może prawidłowo komunikować się z usługą agenta.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego procesu roboczego elementu Runbook na komunikowanie się z punktem końcowym rejestracji. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Punkt końcowy operacji

Ta kontrola określa, czy Agent może prawidłowo komunikować się z usługą danych czasu wykonywania zadania.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego procesu roboczego elementu Runbook na komunikowanie się z usługą danych czasu wykonywania zadania. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Kontrole kondycji usługi maszyny wirtualnej

### <a name="monitoring-agent-service-status"></a>Stan usługi Monitoring Agent

Ta kontrola określa, czy Agent Log Analytics dla systemu Windows`healthservice`() jest uruchomiony na komputerze. Aby dowiedzieć się więcej o rozwiązywaniu problemów z usługą, zobacz [agent log Analytics dla systemu Windows nie jest uruchomiony](hybrid-runbook-worker.md#mma-not-running).

Aby ponownie zainstalować agenta Log Analytics dla systemu Windows, zobacz [Instalowanie i konfigurowanie log Analytics agenta dla systemu Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitorowanie zdarzeń usługi Agent

To sprawdzenie decyduje o tym, czy jakiekolwiek zdarzenia 4502 pojawiają się w dzienniku Operations Manager platformy Azure na komputerze w ciągu ostatnich 24 godzin.

Aby dowiedzieć się więcej o tym zdarzeniu, zobacz [Przewodnik rozwiązywania problemów](hybrid-runbook-worker.md#event-4502) dla tego zdarzenia.

## <a name="access-permissions-checks"></a>Sprawdzanie uprawnień dostępu

### <a name="machinekeys-folder-access"></a>Dostęp do folderu MachineKeys

Sprawdzanie dostępu do folderu kryptograficznego określa, czy konto systemu lokalnego ma dostęp do C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Rozwiązywanie problemów w trybie offline

Aby użyć narzędzia do rozwiązywania problemów z hybrydowym procesem roboczym elementu Runbook, należy uruchomić skrypt lokalnie. Możesz uzyskać skrypt, [rozwiązać problemy — WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), w Galeria programu PowerShell. Aby można było uruchomić skrypt, musi być zainstalowany program WMF 4,0 lub nowszy. Aby pobrać najnowszą wersję programu PowerShell, zobacz [Instalowanie różnych wersji programu PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

Dane wyjściowe tego skryptu wyglądają podobnie jak w poniższym przykładzie:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać więcej problemów dotyczących hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów hybrydowych procesów roboczych elementów Runbook](hybrid-runbook-worker.md).
