---
title: Rozwiązywanie problemów dotyczących Azure Automation hybrydowych procesów roboczych elementu Runbook
description: W tym artykule opisano sposób rozwiązywania problemów z Azure Automation hybrydowych procesów roboczych elementów Runbook.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4fcd3d143cf2dbb529a8c9c78a769165621e2e89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400421"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Rozwiązywanie problemów z hybrydowym procesem roboczym elementu runbook

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z Azure Automation hybrydowych procesów roboczych elementów Runbook. Aby uzyskać ogólne informacje, zobacz [Omówienie hybrydowego procesu roboczego elementu Runbook](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Ogólne

Hybrydowy proces roboczy elementu Runbook zależy od agenta, który komunikuje się z kontem Azure Automation, aby zarejestrować proces roboczy, odebrać zadania elementu Runbook i zgłosić stan. Dla systemu Windows ten Agent jest agentem Log Analytics dla systemu Windows. W przypadku systemu Linux jest to Agent Log Analytics dla systemu Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenariusz: wykonanie elementu Runbook nie powiodło się

#### <a name="issue"></a>Problem

Wykonanie elementu Runbook nie powiodło się i zostanie wyświetlony następujący komunikat o błędzie:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Element Runbook zostanie zawieszony wkrótce po ponownym uruchomieniu. Istnieją warunki, które umożliwiają przerwanie wykonywania elementu Runbook. Komunikat o błędzie pokrewny może nie zawierać żadnych dodatkowych informacji.

#### <a name="cause"></a>Przyczyna

Możliwe są następujące przyczyny:

* Elementy Runbook nie mogą uwierzytelniać się za pomocą zasobów lokalnych.
* Hybrydowy proces roboczy jest za serwerem proxy lub zaporą.
* Komputer skonfigurowany do uruchamiania hybrydowego procesu roboczego elementu Runbook nie spełnia minimalnych wymagań sprzętowych.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp wychodzący do ***. Azure-Automation.NET** na porcie 443.

Komputery z uruchomionym hybrydowym procesem roboczym elementu Runbook powinny spełniać minimalne wymagania sprzętowe, zanim proces roboczy zostanie skonfigurowany do obsługi tej funkcji. Elementy Runbook i proces w tle, których używają, mogą spowodować nadużycie systemu i spowodować opóźnienia zadań elementu Runbook lub przekroczenia limitu czasu.

Upewnij się, że komputer, na którym jest uruchomiona funkcja hybrydowego procesu roboczego elementu Runbook, spełnia minimalne wymagania sprzętowe. W takim przypadku należy monitorować użycie procesora i pamięci, aby określić korelację między wydajnością hybrydowych procesów roboczych elementów Runbook i systemu Windows. Każda pamięć lub wykorzystanie procesora CPU może wskazywać potrzebę uaktualnienia zasobów. Możesz również wybrać inny zasób obliczeniowy, który spełnia minimalne wymagania i skalę, gdy wymagania dotyczące obciążenia wskazują, że zwiększenie jest niezbędne.

Sprawdź w dzienniku zdarzeń **Microsoft-SMA** odpowiednie zdarzenie z opisem `Win32 Process Exited with code [4294967295]` . Przyczyną tego błędu jest to, że nie skonfigurowano uwierzytelniania w elementach Runbook ani nie określono poświadczeń Uruchom jako dla grupy hybrydowych procesów roboczych elementu Runbook. Przejrzyj uprawnienia elementu Runbook w [uruchomionych elementach Runbook w hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md) , aby upewnić się, że uwierzytelnianie dla elementów Runbook zostało prawidłowo skonfigurowane.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenariusz: zdarzenie 15011 w hybrydowym procesie roboczym elementu Runbook

#### <a name="issue"></a>Problem

Hybrydowy proces roboczy elementu Runbook odbiera zdarzenie 15011, co oznacza, że wynik zapytania jest nieprawidłowy. Następujący błąd pojawia się, gdy proces roboczy próbuje otworzyć połączenie z [serwerem sygnałów](/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie został poprawnie skonfigurowany do automatycznego wdrażania funkcji, na przykład w przypadku Update Management. Wdrożenie zawiera część, która łączy maszynę wirtualną z obszarem roboczym Log Analytics. Skrypt programu PowerShell szuka obszaru roboczego w subskrypcji o podanej nazwie. W takim przypadku obszar roboczy Log Analytics jest w innej subskrypcji. Skrypt nie może odnaleźć obszaru roboczego i próbuje go utworzyć, ale nazwa jest już zajęta. W związku z tym wdrażanie nie powiedzie się.

#### <a name="resolution"></a>Rozwiązanie

Dostępne są dwie opcje rozwiązania tego problemu:

* Zmodyfikuj skrypt programu PowerShell, aby wyszukać obszar roboczy Log Analytics w innej subskrypcji. Jest to dobre rozwiązanie, które ma być używane, jeśli planujesz wdrożyć wiele hybrydowych maszyn roboczych elementów Runbook w przyszłości.

* Ręcznie skonfiguruj maszynę procesu roboczego do uruchamiania w piaskownicy programu Orchestrator. Następnie Uruchom element Runbook utworzony na koncie Azure Automation w procesie roboczym, aby przetestować jego funkcjonalność.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenariusz: maszyny wirtualne z systemem Windows Azure są automatycznie usuwane z grupy hybrydowych procesów roboczych

#### <a name="issue"></a>Problem

Nie widzisz hybrydowego procesu roboczego elementu Runbook ani maszyn wirtualnych, gdy maszyna procesu roboczego została wyłączona przez długi czas.

#### <a name="cause"></a>Przyczyna

Maszyna hybrydowego procesu roboczego elementu Runbook nie ma polecenia ping Azure Automation przez ponad 30 dni. W związku z tym Automatyzacja przeczyścił grupę hybrydowych procesów roboczych elementu Runbook lub grupę procesów roboczych systemu. 

#### <a name="resolution"></a>Rozwiązanie

Uruchom maszynę proces roboczy, a następnie rereregister ją przy użyciu Azure Automation. Aby uzyskać instrukcje dotyczące sposobu instalowania środowiska Runbook i łączenia się z Azure Automation, zobacz [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenariusz: nie znaleziono certyfikatu w magazynie certyfikatów w hybrydowym procesie roboczym elementu Runbook

#### <a name="issue"></a>Problem

Element Runbook uruchomiony w hybrydowym procesie roboczym elementu Runbook kończy się niepowodzeniem z następującym komunikatem o błędzie:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby użycia [konta Uruchom jako](../manage-runas-account.md) w elemencie Runbook, który działa w hybrydowym procesie roboczym elementu Runbook, gdzie nie jest obecny certyfikat konta Uruchom jako. Hybrydowe procesy robocze elementów Runbook nie mają domyślnie zasobu lokalnego. Konto Uruchom jako wymaga poprawnego działania tego elementu zawartości.

#### <a name="resolution"></a>Rozwiązanie

Jeśli hybrydowy proces roboczy elementu Runbook jest maszyną wirtualną platformy Azure, możesz zamiast tego użyć [uwierzytelniania elementu Runbook z tożsamościami zarządzanymi](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) . Ten scenariusz upraszcza uwierzytelnianie, umożliwiając uwierzytelnianie w zasobach platformy Azure przy użyciu tożsamości zarządzanej maszyny wirtualnej platformy Azure zamiast konta Uruchom jako. Gdy hybrydowy proces roboczy elementu Runbook jest maszyną lokalną, należy zainstalować na tym komputerze certyfikat konta Uruchom jako. Aby dowiedzieć się, jak zainstalować certyfikat, zobacz procedurę uruchamiania programu PowerShell Runbook **Export-RunAsCertificateToHybridWorker** w temacie [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenariusz: Błąd 403 podczas rejestracji hybrydowego procesu roboczego elementu Runbook

#### <a name="issue"></a>Problem

Początkowa faza rejestracji procesu roboczego kończy się niepowodzeniem i pojawia się następujący błąd (403):

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Przyczyna

Możliwe są następujące problemy:

* Istnieje nieprawidłowy typ identyfikatora obszaru roboczego lub klucza obszaru roboczego (podstawowego) w ustawieniach agenta. 
* Hybrydowy proces roboczy elementu Runbook nie może pobrać konfiguracji, co powoduje błąd łączenia konta. Gdy platforma Azure włącza funkcje na maszynach, obsługuje tylko niektóre regiony do łączenia obszaru roboczego Log Analytics i konta usługi Automation. Istnieje również możliwość, że na komputerze jest ustawiona nieprawidłowa data lub godzina. Jeśli czas wynosi +/-15 minut od bieżącego czasu, wdrożenie funkcji kończy się niepowodzeniem.

#### <a name="resolution"></a>Rozwiązanie

##### <a name="mistyped-workspace-id-or-key"></a>Niewpisany identyfikator lub klucz obszaru roboczego
Aby sprawdzić, czy identyfikator obszaru roboczego agenta lub klucz obszaru roboczego został wpisany jako błąd, zobacz [Dodawanie lub usuwanie obszaru roboczego — Agent systemu](../../azure-monitor/platform/agent-manage.md#windows-agent) Windows dla agenta systemu Windows lub [Dodawanie lub usuwanie agenta obszaru roboczego — Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) Agent dla agenta Linux. Upewnij się, że wybrano pełny ciąg z Azure Portal i skopiuj go i wklej uważnie.

##### <a name="configuration-not-downloaded"></a>Nie pobrano konfiguracji

Obszar roboczy Log Analytics i konto usługi Automation muszą znajdować się w połączonym regionie. Listę obsługiwanych regionów można znaleźć w temacie [Azure Automation i log Analytics mapowania obszaru roboczego](../how-to/region-mappings.md).

Może być również konieczne zaktualizowanie daty lub strefy czasowej komputera. W przypadku wybrania niestandardowego zakresu czasu upewnij się, że zakres jest w formacie UTC, który może się różnić od lokalnej strefy czasowej.

## <a name="linux"></a>Linux

Hybrydowy proces roboczy elementu Runbook systemu Linux zależy od [agenta log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md) do komunikowania się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odebrania zadań elementu Runbook i stanu raportu. Jeśli rejestracja procesu roboczego nie powiedzie się, poniżej przedstawiono niektóre możliwe przyczyny błędu.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scenariusz: hybrydowy proces roboczy elementu Runbook systemu Linux odbiera monit o podanie hasła podczas podpisywania elementu Runbook

#### <a name="issue"></a>Problem

Uruchomienie `sudo` polecenia dla hybrydowego procesu roboczego elementu Runbook systemu Linux powoduje pobranie nieoczekiwanego monitu o podanie hasła.

#### <a name="cause"></a>Przyczyna

Konto **nxautomationuser** agenta log Analytics dla systemu Linux nie zostało poprawnie skonfigurowane w pliku **sudo** . Hybrydowy proces roboczy elementu Runbook wymaga odpowiedniej konfiguracji uprawnień konta i innych danych, aby można było podpisywać elementy Runbook w ramach procesu roboczego elementu Runbook systemu Linux.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że hybrydowy proces roboczy elementu Runbook ma plik wykonywalny GnuPG (GPG) na komputerze.

* Sprawdź konfigurację konta **nxautomationuser** w pliku **sudo** . Zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenariusz: Agent Log Analytics dla systemu Linux nie jest uruchomiony

#### <a name="issue"></a>Problem

Agent Log Analytics dla systemu Linux nie jest uruchomiony.

#### <a name="cause"></a>Przyczyna

Jeśli Agent nie jest uruchomiony, uniemożliwia komunikację hybrydowej elementu Runbook systemu Linux z Azure Automation. Agent może nie działać z różnych powodów.

#### <a name="resolution"></a>Rozwiązanie

 Sprawdź, czy Agent jest uruchomiony, wprowadzając polecenie `ps -ef | grep python` . Powinny pojawić się dane wyjściowe podobne do poniższych. Procesy Python są przetwarzane przy użyciu konta użytkownika **nxautomation** . Jeśli funkcja Azure Automation nie jest włączona, żaden z następujących procesów nie jest uruchomiony.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Poniższa lista zawiera procesy uruchomione dla hybrydowego procesu roboczego elementu Runbook systemu Linux. Są one dostępne w katalogu/var/opt/Microsoft/omsagent/State/automationworker/.

* **OMS. conf**: proces Menedżera procesów roboczych. Jest on uruchamiany bezpośrednio z DSC.
* **Worker. conf**: proces samoobsługowego rejestrowania hybrydowego procesu roboczego. Jest on uruchamiany przez Menedżera procesów roboczych. Ten proces jest używany przez Update Management i jest niewidoczny dla użytkownika. Ten proces nie jest obecny, jeśli Update Management nie jest włączona na komputerze.
* **możesz/Worker. conf**: proces hybrydowego procesu roboczego możesz. Proces hybrydowego procesu roboczego możesz służy do wykonywania elementów Runbook użytkownika w hybrydowym procesie roboczym elementu Runbook. Różni się on od automatycznej rejestracji hybrydowego procesu roboczego w kluczowym szczegółzie, który używa innej konfiguracji. Ten proces nie jest obecny, jeśli Azure Automation jest wyłączone i hybrydowy proces roboczy możesz Linux nie jest zarejestrowany.

Jeśli Agent nie jest uruchomiony, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenariusz: określona Klasa nie istnieje

Jeśli zostanie wyświetlony komunikat o błędzie `The specified class does not exist..` w programie **/var/opt/Microsoft/omsconfig/omsconfig.log**, należy zaktualizować agenta log Analytics dla systemu Linux. Uruchom następujące polecenie, aby ponownie zainstalować agenta.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrydowy proces roboczy elementu Runbook systemu Windows zależy od [agenta log Analytics dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md) , który komunikuje się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań elementu Runbook i stanu raportu. Jeśli rejestracja procesu roboczego nie powiedzie się, ta sekcja zawiera pewne możliwe przyczyny.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenariusz: Agent Log Analytics dla systemu Windows nie jest uruchomiony

#### <a name="issue"></a>Problem

`healthservice`Nie działa na maszynie hybrydowej elementu Runbook Worker.

#### <a name="cause"></a>Przyczyna

Jeśli Log Analytics dla usługi systemu Windows nie jest uruchomiona, hybrydowy proces roboczy elementu Runbook nie może komunikować się z Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy Agent jest uruchomiony, wprowadzając następujące polecenie w programie PowerShell: `Get-Service healthservice` . Jeśli usługa jest zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scenariusz: zdarzenie 4502 w dzienniku Operations Manager

#### <a name="issue"></a>Problem

W dzienniku zdarzeń programu **Application and Services Logs\Operations Manager** zobaczysz zdarzenie 4502 i komunikat o zdarzeniu zawierający `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` następujący Opis:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być Microsoft Azure przez serwer proxy lub zaporę sieciową. Sprawdź, czy komputer ma dostęp wychodzący do ***. Azure-Automation.NET** na porcie 443.

#### <a name="resolution"></a>Rozwiązanie

Dzienniki są przechowywane lokalnie na każdym hybrydowym procesie roboczym w witrynie C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Możesz sprawdzić, czy w dziennikach zdarzeń **aplikacji i usług Logs\Microsoft-SMA\Operations** oraz **aplikacji i usług Logs\Operations Manager** znajdują się jakieś zdarzenia ostrzegawcze lub błędy. Te dzienniki wskazują łączność lub inny typ problemu, który wpływa na włączenie roli do Azure Automation lub wystąpił problem w ramach normalnych operacji. Aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z agentem Log Analytics, zobacz [Rozwiązywanie problemów z log Analytics agentem systemu Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybrydowe procesy robocze wysyłają [dane wyjściowe i komunikaty elementu Runbook](../automation-runbook-output-and-messages.md) do Azure Automation w taki sam sposób, jak zadania elementu Runbook działające w chmurze wysyłają dane wyjściowe i komunikaty. Możesz włączyć strumienie pełnych i postępów tak samo jak w przypadku elementów Runbook.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Scenariusz: Orchestrator.Sandbox.exe nie może nawiązać połączenia z Microsoft 365 za poorednictwem serwera proxy

#### <a name="issue"></a>Problem

Skrypt uruchomiony w hybrydowym procesie roboczym elementu Runbook systemu Windows nie może nawiązać połączenia zgodnie z oczekiwaniami, aby Microsoft 365 w piaskownicy programu Orchestrator. Skrypt używa polecenia [Connect-MsolService](/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) w celu nawiązania połączenia. 

W przypadku dostosowania **Orchestrator.Sandbox.exe.config** , aby ustawić serwer proxy i listę pomijania, Piaskownica nadal nie będzie się poprawnie łączyć. Plik **Powershell_ise.exe.config** z tymi samymi ustawieniami serwera proxy i listy obejścia wygląda prawdopodobnie zgodnie z oczekiwaniami. Dzienniki Service Management Automation (SMA) i dzienniki programu PowerShell nie zawierają żadnych informacji dotyczących serwera proxy.

#### <a name="cause"></a>Przyczyna

Połączenie z Active Directory Federation Services (AD FS) na serwerze nie może obejść serwera proxy. Pamiętaj, że piaskownica programu PowerShell jest uruchamiana jako zalogowany użytkownik. Jednak piaskownica programu Orchestrator jest w dużym stopniu dostosowana i może ignorować ustawienia plików **Orchestrator.Sandbox.exe.config** . Ma specjalny kod do obsługi ustawień serwera proxy agenta komputera lub Log Analytics, ale nie do obsługi innych niestandardowych ustawień serwera proxy. 

#### <a name="resolution"></a>Rozwiązanie

Problem związany z piaskownicą programu Orchestrator można rozwiązać przez przeprowadzenie migracji skryptu w celu użycia modułów Azure Active Directory zamiast modułu MSOnline dla poleceń cmdlet programu PowerShell. Aby uzyskać więcej informacji, zobacz [Migrowanie z programu Orchestrator do Azure Automation (beta)](../automation-orchestrator-migration.md).

Jeśli chcesz kontynuować korzystanie z poleceń cmdlet modułu MSOnline, Zmień skrypt, aby używał [polecenia Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Określ wartości `ComputerName` `Credential` parametrów i. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Ta zmiana kodu uruchamia zupełnie nową sesję programu PowerShell w kontekście określonych poświadczeń. Powinien on umożliwić przepływ ruchu przez serwer proxy, który uwierzytelnia aktywnego użytkownika.

>[!NOTE]
>To rozwiązanie nie pozwala na manipulowanie plikiem konfiguracji piaskownicy. Nawet w przypadku pomyślnego wykonania pliku konfiguracji w skrypcie plik zostanie wyczyszczony za każdym razem, gdy zostanie zaktualizowany Agent hybrydowego procesu roboczego elementu Runbook.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenariusz: hybrydowy proces roboczy elementu Runbook nie jest zgłaszany

#### <a name="issue"></a>Problem

Maszyna hybrydowego procesu roboczego elementu Runbook jest uruchomiona, ale nie widzisz danych pulsu dla maszyny w obszarze roboczym.

Poniższe przykładowe zapytanie pokazuje maszyny w obszarze roboczym i ich ostatni puls:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być uszkodzona pamięć podręczna w hybrydowym procesie roboczym elementu Runbook.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, zaloguj się do hybrydowego procesu roboczego elementu Runbook i uruchom poniższy skrypt. Ten skrypt powoduje zatrzymanie Log Analytics agenta dla systemu Windows, usunięcie jego pamięci podręcznej i ponowne uruchomienie usługi. Ta akcja Wymusza ponowne pobranie konfiguracji przez proces hybrydowego elementu Runbook programu z Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenariusz: nie można dodać hybrydowego procesu roboczego elementu Runbook

#### <a name="issue"></a>Problem

Podczas próby dodania hybrydowego procesu roboczego elementu Runbook za pomocą polecenia cmdlet zostanie wyświetlony następujący komunikat `Add-HybridRunbookWorker` :

```error
Machine is already registered
```

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany tym, że maszyna jest już zarejestrowana przy użyciu innego konta usługi Automation lub jeśli spróbujesz ponownie dodać hybrydowy proces roboczy elementu Runbook po usunięciu go z komputera.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, usuń następujący klucz rejestru, uruchom ponownie `HealthService` , a następnie spróbuj `Add-HybridRunbookWorker` ponownie wykonać polecenie cmdlet.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż połączenie z [@AzureSupport](https://twitter.com/azuresupport) kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
