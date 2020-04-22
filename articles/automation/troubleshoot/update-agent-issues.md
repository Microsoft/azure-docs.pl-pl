---
title: Rozwiązywanie problemów z agentem aktualizacji systemu Windows w usłudze Azure Automation Update Management
description: Dowiedz się, jak rozwiązywać problemy z agentem aktualizacji systemu Windows i rozwiązywać je za pomocą rozwiązania Do zarządzania aktualizacjami.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678976"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Rozwiązywanie problemów z agentem aktualizacji systemu Windows

Może istnieć wiele powodów, dla których komputer nie jest wyświetlany jako gotowy (zdrowy) w zarządzaniu aktualizacjami. W zarządzanie aktualizacjami można sprawdzić kondycję agenta administracyjnego hybrydowego systemu runbook, aby określić podstawowy problem. W tym artykule omówiono sposób uruchamiania narzędzia do rozwiązywania problemów dla komputerów platformy Azure z witryny Azure portal i komputerów innych niż Azure w [scenariuszu offline.](#troubleshoot-offline)

Poniżej przedstawiono trzy stany gotowości maszyny:

* Gotowy — hybrydowy proces roboczy żyła pracy jest wdrażany i był ostatnio widziany mniej niż 1 godzinę temu.
* Rozłączony — hybrydowy proces roboczy uruchomieniu jest wdrożony i był ostatnio widziany ponad 1 godzinę temu.
* Nie skonfigurowano — nie znaleziono lub nie zostało ukończone dołączanie.

> [!NOTE]
> Może wystąpić niewielkie opóźnienie między tym, co pokazuje portal Azure i bieżącego stanu komputera.

## <a name="start-the-troubleshooter"></a>Uruchamianie narzędzia do rozwiązywania problemów

W przypadku komputerów platformy Azure kliknięcie łącza **Rozwiązywanie problemów** w kolumnie **Gotowość agenta aktualizacji** w portalu uruchamia stronę Agent aktualizacji rozwiązywania problemów. W przypadku komputerów innych niż platformy Azure łącze prowadzi do tego artykułu. Zapoznaj się z [instrukcjami offline,](#troubleshoot-offline) aby rozwiązać problem z komputerem nienawiązanym z platformą Azure.

![Aktualizowanie listy zarządzania maszynami wirtualnymi](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Aby sprawdzić kondycję procesu roboczego hybrydowego niu gosna, maszyna wirtualna musi być uruchomiona. Jeśli maszyna wirtualna nie jest uruchomiona, zostanie wyświetlony przycisk **Uruchom maszynę wirtualną.**

Na stronie Agent rozwiązywania problemów z aktualizacją wybierz pozycję **Uruchom kontrole,** aby uruchomić narzędzie do rozwiązywania problemów. Narzędzie do rozwiązywania problemów używa [polecenia Uruchom](../../virtual-machines/windows/run-command.md) do uruchomienia skryptu na komputerze w celu weryfikacji zależności. Po zakończeniu rozwiązywania problemów zwraca wynik kontroli.

![Rozwiązywanie problemów z usługą Agent aktualizacji](../media/update-agent-issues/troubleshoot-page.png)

Wyniki są wyświetlane na stronie, gdy są gotowe. Sekcje kontroli pokazują, co jest zawarte w każdym czeku.

![Rozwiązywanie problemów z sprawdzaniem agenta aktualizacji](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Sprawdzanie wymagań wstępnych

### <a name="operating-system"></a>System operacyjny

Sprawdzanie systemu operacyjnego sprawdza, czy hybrydowy pracownik uruchomieniu jest z jednego z następujących systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2012 i nowsze |.NET Framework 4.6 lub nowsze jest wymagane. (Pobierz[program .NET Framework)](/dotnet/framework/install/guide-for-developers)<br/> Program Windows PowerShell 5.1 jest wymagany.  (Pobierz[program Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

Sprawdzanie .NET Framework sprawdza, czy system ma co najmniej [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) zainstalowany.

### <a name="wmf-51"></a>WMF 5.1

Sprawdzanie WMF sprawdza, czy system ma wymaganą wersję programu Windows Management Framework (WMF) — [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Ten czek określa, czy używasz protokołu TLS 1.2 do szyfrowania komunikacji. TLS 1.0 nie jest już obsługiwany przez platformę. Zaleca się, aby klienci używali protokołu TLS 1.2 do komunikowania się z zarządzaniem aktualizacjami.

## <a name="connectivity-checks"></a>Kontrole łączności

### <a name="registration-endpoint"></a>Punkt końcowy rejestracji

To sprawdzenie określa, czy agent może poprawnie komunikować się z usługą agenta.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego systemu runbook na komunikowanie się z punktem końcowym rejestracji. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla pracowników hybrydowych](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Punkt końcowy operacji

Ten czek określa, czy agent może poprawnie komunikować się z usługą danych środowiska wykonawczego zadania.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego procesu roboczego uruchomieniu na środowisko pracy na komunikowanie się z usługą danych środowiska wykonawczego zadania. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla pracowników hybrydowych](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Kontrole kondycji usługi maszyn wirtualnych

### <a name="monitoring-agent-service-status"></a>Stan usługi agenta monitorowania

Ten czek określa, czy agent`healthservice`usługi Log Analytics dla systemu Windows ( ) jest uruchomiony na komputerze. Aby dowiedzieć się więcej na temat rozwiązywania problemów z usługą, zobacz [Agent analizy dzienników dla systemu Windows nie jest uruchomiony](hybrid-runbook-worker.md#mma-not-running).

Aby ponownie zainstalować agenta analizy dzienników dla systemu Windows, zobacz [Instalowanie i konfigurowanie agenta analizy dzienników dla systemu Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitorowanie zdarzeń usługi agenta

Ten czek określa, czy wszystkie zdarzenia 4502 są wyświetlane w dzienniku usługi Azure Operations Manager na komputerze w ciągu ostatnich 24 godzin.

Aby dowiedzieć się więcej o tym zdarzeniu, zobacz [przewodnik rozwiązywania problemów](hybrid-runbook-worker.md#event-4502) z tym wydarzeniem.

## <a name="access-permissions-checks"></a>Sprawdzanie uprawnień dostępu

### <a name="machinekeys-folder-access"></a>Dostęp do folderu MachineKeys

Sprawdzanie dostępu do folderu Crypto określa, czy lokalne konto systemowe ma dostęp do C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Rozwiązywanie problemów w trybie offline

Narzędzia do rozwiązywania problemów można używać w hybrydowym ustępuje procesowi roboczemu żyjącego w trybie offline, uruchamiając skrypt lokalnie. Skrypt, [Troubleshoot-WindowsUpdateAgentRegistration,](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)można uzyskać w Galerii programu PowerShell. Aby uruchomić skrypt, musisz mieć zainstalowany system WMF 4.0 lub większy. Aby pobrać najnowszą wersję programu PowerShell, zobacz [Instalowanie różnych wersji programu PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

Dane wyjściowe tego skryptu wygląda następująco:

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

Aby rozwiązać więcej problemów z hybrydowymi pracownikami uruchomieniu, zobacz [Rozwiązywanie problemów z hybrydowymi pracownikami wiązki.](hybrid-runbook-worker.md)
