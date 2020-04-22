---
title: Rozwiązywanie problemów z hybrydowymi procesami owymi robotników systemu Azure Automation
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z hybrydowymi procesami owymi workers w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679335"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Rozwiązywanie problemów z hybrydowymi pracownikami uruchomieniu

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z hybrydowymi pracownikami uruchomieniu. wiązaniem.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="general"></a>Ogólne

Hybrydowy proces roboczy uruchomieniu zależy od agenta do komunikowania się z kontem automatyzacji, aby zarejestrować pracownika, odbierać zadania uruchomieniu i stan raportu. W systemie Windows ten agent jest agentem usługi Log Analytics dla systemu Windows. W przypadku systemu Linux jest to agent analizy dzienników dla systemu Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenariusz: Wykonanie uruchomieniu nie powiedzie się

#### <a name="issue"></a>Problem

Wykonanie elementów runbook kończy się niepowodzeniem i pojawia się następujący błąd.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Element runbook jest zawieszony wkrótce po próbie wykonania trzy razy. Istnieją warunki, które mogą przerwać ukończenie śunku. Powiązany komunikat o błędzie może nie zawierać żadnych dodatkowych informacji.

#### <a name="cause"></a>Przyczyna

Oto możliwe przyczyny:

* Programy runbook nie można uwierzytelnić przy użyciu zasobów lokalnych.
* Hybrydowy proces roboczy znajduje się za serwerem proxy lub zaporą.
* Komputer skonfigurowany do uruchamiania hybrydowego procesu roboczego uruchomieniu nie spełnia minimalnych wymagań sprzętowych.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp wychodzący do ***.azure-automation.net** na porcie 443.

Komputery z systemem Hybrydowy proces roboczy elementu runbook powinny spełniać minimalne wymagania sprzętowe, zanim proces roboczy zostanie skonfigurowany do obsługi tej funkcji. Runbooks i proces w tle, którego używają może spowodować system jest nadmiernie używane i spowodować opóźnienia zadania runbook lub limity czasu.

Upewnij się, że komputer do uruchomienia funkcji Hybrydowy proces roboczy elementu runbook spełnia minimalne wymagania sprzętowe. Jeśli tak, należy monitorować użycie procesora CPU i pamięci w celu określenia korelacji między wydajnością procesów hybrydowego procesu roboczego systemu Runbook i systemu Windows. Każda pamięć lub ciśnienie procesora CPU może wskazywać na potrzebę uaktualnienia zasobów. Można również wybrać inny zasób obliczeniowy, który obsługuje minimalne wymagania i skalować, gdy wymagania obciążenia wskazują, że konieczne jest zwiększenie.

Sprawdź dziennik zdarzeń **Microsoft-SMA** dla odpowiedniego `Win32 Process Exited with code [4294967295]`zdarzenia z opisem . Przyczyną tego błędu jest to, że uwierzytelnianie nie zostało skonfigurowane w ykonfetowanych lub określono poświadczenia Uruchom jako dla grupy Hybrydowy proces roboczy systemu runbook. Przejrzyj uprawnienia do uruchomieniu w [uruchomionych uruchomieniu w hybrydowym uzywolnieniu uruchomieniu,](../automation-hrw-run-runbooks.md) aby potwierdzić, że uwierzytelnianie zostało poprawnie skonfigurowane dla uruchomieniu. ścieliń.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenariusz: zdarzenie 15011 w hybrydowym ucho.

#### <a name="issue"></a>Problem

Hybrydowy proces roboczy uruchomieniu odbiera zdarzenie 15011, wskazując, że wynik kwerendy jest nieprawidłowy. Następujący błąd pojawia się, gdy pracownik próbuje otworzyć połączenie z [serwerem SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy uruchomieniu nie został poprawnie skonfigurowany dla rozwiązania do wdrażania automatycznego. To rozwiązanie zawiera część, która łączy maszynę wirtualną z obszarem roboczym usługi Log Analytics. Skrypt programu PowerShell wyszukuje obszar roboczy w ramach subskrypcji o podanej nazwie. W takim przypadku obszar roboczy usługi Log Analytics jest w innej subskrypcji. Skrypt nie może znaleźć obszaru roboczego i próbuje go utworzyć, ale nazwa jest już zajęta. W związku z tym wdrożenie kończy się niepowodzeniem.

#### <a name="resolution"></a>Rozwiązanie

Istnieją dwie opcje rozwiązania tego problemu:

* Zmodyfikuj skrypt programu PowerShell, aby wyszukać obszar roboczy usługi Log Analytics w innej subskrypcji. Jest to dobre rozwiązanie, jeśli planujesz wdrożyć wiele hybrydowych maszyn roboczych systemu runbook w przyszłości.

* Ręcznie skonfiguruj komputer roboczy do pracy w piaskownicy programu Orchestrator. Następnie uruchom system runbook utworzony na koncie usługi Azure Automation w usłudze worker, aby przetestować funkcje.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenariusz: maszyny wirtualne systemu Windows Azure automatycznie porzucone z hybrydowej grupy roboczej

#### <a name="issue"></a>Problem

Nie widać hybrydowego procesu roboczego lub maszyn wirtualnych, gdy komputer roboczy został wyłączony przez długi czas.

#### <a name="cause"></a>Przyczyna

Hybrydowy komputer roboczy systemu żyjącego nie pingował usługi Azure Automation od ponad 30 dni. W rezultacie automatyzacja oczyściła grupę Hybrydowy proces roboczy lub grupę Proces roboczy systemu. 

#### <a name="resolution"></a>Rozwiązanie

Uruchom komputer roboczy, a następnie ponownie zarejestrować go za pomocą usługi Azure Automation. Zobacz instrukcje dotyczące instalowania środowiska zestawu runbook i łączenia się z usługą Azure Automation w [przypadku wdrażania procesu roboczego hybrydowego żyli systemu Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenariusz: w magazynie certyfikatów w hybrydowym uichu jako pracownika uruchomieniu nie znaleziono certyfikatu

#### <a name="issue"></a>Problem

System runbook uruchomiony w hybrydowym ustępuje procesowi roboczemu systemu runbook z następującym komunikatem o błędzie.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby użycia [konta Uruchom jako](../manage-runas-account.md) w bieśpieci runbook, który działa w hybrydowym przebiegu roboczego życinicy, gdzie nie ma certyfikatu uruchom jako konto. Hybrydowe środowiska Runbook Workers domyślnie nie mają zasobu certyfikatu lokalnie. Konto Uruchom jako wymaga prawidłowego działania tego zasobu.

#### <a name="resolution"></a>Rozwiązanie

Jeśli proces roboczy hybrydowego niu gozytu jest maszyną wirtualną platformy Azure, można użyć [tożsamości zarządzanych dla zasobów platformy Azure.](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) W tym scenariuszu upraszcza uwierzytelnianie, umożliwiając uwierzytelnianie zasobów platformy Azure przy użyciu tożsamości zarządzanej maszyny Wirtualnej platformy Azure zamiast konta Uruchom jako. Gdy hybrydowy proces roboczy niu gosna jest komputerem lokalnym, należy zainstalować certyfikat uruchom jako konto na komputerze. Aby dowiedzieć się, jak zainstalować certyfikat, zobacz kroki uruchamiania programu Runbook **Export-RunAsCertificateToHybridWorker** w [uruchomionych systemach runbook w hybrydowym uliczce uruchomieniu. .](../automation-hrw-run-runbooks.md)

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenariusz: Błąd 403 podczas rejestracji hybrydowego pracownika uruchomieniu

#### <a name="issue"></a>Problem

Początkowa faza rejestracji pracownika kończy się niepowodzeniem i pojawia się następujący błąd (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Przyczyna

Oto możliwe przyczyny:

* W ustawieniach agenta znajduje się błędnie wpisany identyfikator obszaru roboczego lub klucz obszaru roboczego (podstawowy). 

* Hybrydowy pracownik ujeżdna nie może pobrać konfiguracji, powodując błąd łączenia konta. Gdy platforma Azure włącza rozwiązania, obsługuje tylko niektóre regiony do łączenia obszaru roboczego usługi Log Analytics i konta automatyzacji. Jest również możliwe, że na komputerze ustawiona jest nieprawidłowa data i/lub godzina. Jeśli czas wynosi +/-15 minut od bieżącego czasu, dołączanie kończy się niepowodzeniem.

#### <a name="resolution"></a>Rozwiązanie

##### <a name="mistyped-workspace-idkey"></a>Błędnie wpisany identyfikator/klucz obszaru roboczego
Aby sprawdzić, czy identyfikator obszaru roboczego agenta lub klucz obszaru roboczego został błędnie wpisane, zobacz [Dodawanie lub usuwanie obszaru roboczego — agenta systemu Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) lub Dodawanie lub usuwanie obszaru [roboczego — agenta Systemu Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) dla agenta systemu Linux.  Upewnij się, aby wybrać pełny ciąg z witryny Azure portal i skopiować i wkleić go ostrożnie.

##### <a name="configuration-not-downloaded"></a>Konfiguracja nie została pobrana

Obszar roboczy usługi Log Analytics i konto automatyzacji muszą znajdować się w regionie połączonym. Aby uzyskać listę obsługiwanych regionów, zobacz [Mapowania obszarów roboczych usługi Azure Automation i Log Analytics](../how-to/region-mappings.md).

Może być również konieczne zaktualizowanie daty i/lub strefy czasowej komputera. Jeśli wybierzesz niestandardowy zakres czasu, upewnij się, że zakres jest w czasie UTC, który może się różnić od lokalnej strefy czasowej.

## <a name="linux"></a>Linux

Proces roboczy hybrydowego systemu Linux jest zależny od [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md) do komunikowania się z kontem automatyzacji, aby zarejestrować pracownika, odbierać zadania uruchomieniu i stan raportu. Jeśli rejestracja pracownika nie powiedzie się, oto kilka możliwych przyczyn błędu:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenariusz: Agent analizy dzienników dla systemu Linux nie jest uruchomiony

#### <a name="issue"></a>Problem

Agent analizy dzienników dla systemu Linux nie działa

#### <a name="cause"></a>Przyczyna

Jeśli agent nie jest uruchomiony, uniemożliwia procesowi roboczemu hybrydowego funkcjonowania systemu Linux komunikowanie się z usługą Azure Automation. Agent może nie być uruchomiony z różnych powodów.

#### <a name="resolution"></a>Rozwiązanie

 Sprawdź, czy agent jest uruchomiony, wprowadzając polecenie `ps -ef | grep python`. Powinny być widoczne dane wyjściowe podobne do następujących procesów Języka Python z kontem użytkownika **nxautomation.** Jeśli usługa Zarządzanie aktualizacjami lub rozwiązanie usługi Azure Automation nie jest włączone, żaden z następujących procesów nie jest uruchomiony.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Na poniższej liście przedstawiono procesy, które są uruchamiane dla procesu roboczego hybrydowego systemu Linux. Wszystkie znajdują się w katalogu **/var/opt/microsoft/omsagent/state/automationworker/.**

* **oms.conf** — proces menedżera pracowników. To jest rozpoczęty bezpośrednio z DSC.

* **worker.conf** — proces automatycznego rejestrowania hybrydowego. Jest on akcesarz pracownika. Ten proces jest używany przez zarządzanie aktualizacjami i jest niewidoczny dla użytkownika. Ten proces nie jest obecny, jeśli rozwiązanie do zarządzania aktualizacjami nie jest włączone na komputerze.

* **diy/worker.conf** - Proces hybrydowego pracownika DIY. Proces hybrydowego procesu roboczego diy jest używany do wykonywania śmięty użytkowników w hybrydowym procesie żyła pracy. Różni się tylko od procesu automatycznego rejestrowania hybrydowego procesu roboczego w szczegółach klucza, który używa innej konfiguracji. Ten proces nie jest obecny, jeśli rozwiązanie usługi Azure Automation jest wyłączone, a hybrydowy proces roboczy systemu Roboczy systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego systemu Roboczego

Jeśli agent nie jest uruchomiony, uruchom następujące polecenie, `sudo /opt/microsoft/omsagent/bin/service_control restart`aby uruchomić usługę: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenariusz: Określona klasa nie istnieje

Jeśli widzisz błąd `The specified class does not exist..` w **/var/opt/microsoft/omsconfig/omsconfig.log,** agent usługi Log Analytics dla systemu Linux musi zostać zaktualizowany. Uruchom następujące polecenie, aby ponownie zainstalować agenta:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrydowy proces roboczy systemu Windows do żyjącego uruchomieniu zależy [na agencie usługi Log Analytics dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md) do komunikowania się z kontem automatyzacji w celu zarejestrowania pracownika, odbierania zadań uruchomieniu i stanu raportu. Jeśli rejestracja pracownika nie powiedzie się, ta sekcja zawiera kilka możliwych przyczyn.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenariusz: Agent usługi Log Analytics dla systemu Windows nie jest uruchomiony

#### <a name="issue"></a>Problem

Nie `healthservice` działa na komputerze hybrydowego procesu roboczego egoistry.

#### <a name="cause"></a>Przyczyna

Jeśli usługa Log Analytics dla systemu Windows nie jest uruchomiona, hybrydowy proces roboczy niubratów nie może komunikować się z usługą Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy agent jest uruchomiony, wprowadzając następujące polecenie `Get-Service healthservice`w programie PowerShell: . Jeśli usługa zostanie zatrzymana, wprowadź następujące polecenie w programie `Start-Service healthservice`PowerShell, aby uruchomić usługę: .

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scenariusz: zdarzenie 4502 w dzienniku programu Operations Manager

#### <a name="issue"></a>Problem

W dzienniku zdarzeń **Application and Services Logs\Operations Manager** zostanie wyświetlone zdarzenie 4502 i EventMessage zawierające `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` następujący opis:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany przez serwer proxy lub zaporę sieciową blokującą komunikację z platformą Microsoft Azure. Sprawdź, czy komputer ma dostęp wychodzący do ***.azure-automation.net** na porcie 443. 

#### <a name="resolution"></a>Rozwiązanie

Dzienniki są przechowywane lokalnie na każdym hybrydowym uczynku roboczym w **witrynie C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. Można sprawdzić, czy w dziennikach zdarzeń aplikacji i usług występują zdarzenia ostrzegawcze lub zdarzenia **błędów\Microsoft-SMA\Operacje** i **dzienniki aplikacji i usług\Dzienniki operacji programu Operations Manager.** Te dzienniki wskazują łączność lub inny typ problemu, który wpływa na dołączanie roli do usługi Azure Automation lub problem napotkany w normalnych operacjach. Aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów z agentem usługi Log Analytics, zobacz [Rozwiązywanie problemów z agentem systemu Windows usługi Log Analytics](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybrydowe procesy pracy procesowe wysyłają [dane wyjściowe i wiadomości](../automation-runbook-output-and-messages.md) w usłudze Azure Automation w taki sam sposób, w jaki zadania uruchomieniu uruchomione w chmurze wysyłają dane wyjściowe i wiadomości. Strumienie pełne i postępowe można włączyć tak samo, jak w przypadku śmigieł.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scenariusz: Orchestrator.Sandbox.exe nie może połączyć się z usługą Office 365 za pośrednictwem serwera proxy

#### <a name="issue"></a>Problem

Skrypt uruchomiony w hybrydowym urzędzie roboczym systemu Windows nie może połączyć się zgodnie z oczekiwaniami z pakietem Office 365 w piaskownicy programu Orchestrator. Skrypt używa [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) dla połączenia. 

Jeśli dostosujesz **Orchestrator.Sandbox.exe.config,** aby ustawić serwer proxy i listę pomijania, piaskownica nadal nie łączy się poprawnie. Plik **Powershell_ise.exe.config** z tymi samymi ustawieniami listy proxy i bypass wydaje się działać zgodnie z oczekiwaniami. Dzienniki automatyzacji zarządzania usługami (SMA) i dzienniki programu PowerShell nie zawierają żadnych informacji dotyczących serwera proxy.

#### <a name="cause"></a>Przyczyna

Połączenie z usługami federacyjnymi Active Directory (ADFS) na serwerze nie może ominąć serwera proxy. Należy pamiętać, że piaskownica programu PowerShell działa jako zalogowany użytkownik. Jednak piaskownica programu Orchestrator jest mocno dostosowana i może ignorować ustawienia pliku **Orchestrator.Sandbox.exe.config.** Posiada specjalny kod do obsługi komputera lub ustawienia serwera proxy MMA, ale nie do obsługi innych niestandardowych ustawień serwera proxy. 

#### <a name="resolution"></a>Rozwiązanie

Można rozwiązać ten problem dla piaskownicy programu Orchestrator, migrując skrypt, aby użyć modułów usługi Azure AD zamiast modułu MSOnline dla poleceń cmdlet programu PowerShell. Zobacz [Migrowanie z programu Orchestrator do usługi Azure Automation (Beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Jeśli chcesz nadal korzystać z poleceń cmdlet modułu MSOnline, zmień skrypt, aby użyć [invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Określ wartości `ComputerName` `Credential` i parametry. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Ta zmiana kodu rozpoczyna całkowicie nową sesję programu PowerShell w kontekście określonych poświadczeń. Powinien włączyć ruch do przepływu przez serwer proxy, który uwierzytelnia aktywnego użytkownika.

>[!NOTE]
>To rozwiązanie sprawia, że nie ma potrzeby manipulowania plikiem konfiguracji piaskownicy. Nawet jeśli uda ci się, aby plik konfiguracji działał ze skryptem, plik zostanie wymazany za każdym razem, gdy agent hybrydowego procesu roboczego uruchomieniu zostanie zaktualizowany.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenariusz: Hybrydowy pracownik ujeżdny nieraportujący

#### <a name="issue"></a>Problem

Komputer hybrydowy proces roboczy niustrego jest uruchomiony, ale nie są widoczne dane pulsu dla komputera w obszarze roboczym.

Poniższa przykładowa kwerenda przedstawia maszyny w obszarze roboczym i ich ostatnie bicie serca:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany przez uszkodzoną pamięć podręczną w hybrydowym ustępuje procesowi roboczemu podręcznika runbooka.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, zaloguj się do hybrydowego procesu roboczego elementów runbook i uruchom następujący skrypt. Ten skrypt zatrzymuje agenta usługi Log Analytics dla systemu Windows, usuwa jego pamięć podręczną i ponownie uruchamia usługę. Ta akcja wymusza hybrydowy proces roboczy niubratów do ponownego pobrania jego konfiguracji z usługi Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenariusz: nie można dodać hybrydowego pracownika uruchomieniu

#### <a name="issue"></a>Problem

Podczas próby dodania hybrydowego procesu roboczego systemu `Add-HybridRunbookWorker` runbook przy użyciu polecenia cmdlet zostanie wyświetlony następujący komunikat.

```error
Machine is already registered
```

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany, jeśli komputer jest już zarejestrowany przy innym koncie automatyzacji lub jeśli spróbujesz odczytać hybrydowy proces roboczy likemi po usunięciu go z komputera.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, usuń następujący `HealthService`klucz rejestru, `Add-HybridRunbookWorker` uruchom ponownie program i spróbuj ponownie wykonać polecenie cmdlet.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz powyższego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.