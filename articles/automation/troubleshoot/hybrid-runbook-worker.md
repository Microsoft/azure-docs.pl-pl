---
title: Rozwiązywanie Azure Automation hybrydowych procesów roboczych runbook
description: W tym artykule opisano sposób rozwiązywania problemów związanych z hybrydowymi Azure Automation runbook.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 906e38b28015bd70cf1c97ba9323094d64a12c94
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830848"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Rozwiązywanie problemów z hybrydowym procesem roboczym elementu runbook

Ten artykuł zawiera informacje na temat rozwiązywania problemów z hybrydowymi Azure Automation runbook. Aby uzyskać ogólne informacje, zobacz [Omówienie hybrydowego procesu roboczego runbook.](../automation-hybrid-runbook-worker.md)

## <a name="general"></a>Ogólne

Hybrydowy proces roboczy runbook zależy od tego, czy agent komunikuje się z kontem Azure Automation w celu zarejestrowania procesu roboczego, odbierania zadań runbook i zgłaszania stanu. W przypadku systemu Windows ten agent jest agentem usługi Log Analytics dla systemu Windows. W przypadku systemu Linux jest to agent usługi Log Analytics dla systemu Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenariusz: Wykonanie runbook kończy się niepowodzeniem

#### <a name="issue"></a>Problem

Wykonanie runbook kończy się niepowodzeniem i pojawia się następujący komunikat o błędzie:

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Twój runbook zostanie wstrzymany wkrótce po trzykrotnej próbie wykonania. Istnieją warunki, które mogą zakłócić ukończenie działania tego runbook. Powiązany komunikat o błędzie może nie zawierać żadnych dodatkowych informacji.

#### <a name="cause"></a>Przyczyna

Możliwe są następujące przyczyny:

* Nie można uwierzytelniać przy użyciu zasobów lokalnych.
* Hybrydowy proces roboczy znajduje się za serwerem proxy lub zaporą.
* Komputer skonfigurowany do uruchamiania hybrydowego procesu roboczego runbook nie spełnia minimalnych wymagań sprzętowych.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp wychodzący do usługi **\* .azure-automation.net** na porcie 443.

Komputery z hybrydowym procesem roboczy elementu Runbook powinny spełniać minimalne wymagania sprzętowe przed skonfigurowaniem procesu roboczego do obsługi tej funkcji. Używanie przez nie podręczników Runbook i procesu w tle może spowodować, że system zostanie przejmowany i spowodować opóźnienia lub przekłynie limity czasu zadania runbook.

Upewnij się, że komputer do uruchomienia funkcji hybrydowego procesu roboczego elementu Runbook spełnia minimalne wymagania sprzętowe. Jeśli tak, monitoruj użycie procesora CPU i pamięci, aby określić dowolną korelację między wydajnością procesów hybrydowych procesów roboczych runbook i systemu Windows. Użycie pamięci lub procesora CPU może wskazywać na konieczność uaktualnienia zasobów. Możesz również wybrać inny zasób obliczeniowy, który spełnia minimalne wymagania i skalować, gdy zapotrzebowanie na obciążenie wskaże, że wzrost jest konieczny.

Sprawdź w **dzienniku zdarzeń microsoft-SMA** odpowiednie zdarzenie z opisem `Win32 Process Exited with code [4294967295]` . Przyczyną tego błędu jest to, że nie skonfigurowano uwierzytelniania w swoich podręcznikach Runbook ani nie określono poświadczeń Uruchom jako dla grupy hybrydowych procesów roboczych runbook. Przejrzyj uprawnienia do runbook w [te tematu Running runbook on a Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) (Uruchamianie podręczników Runbook w hybrydowym procesu roboczego runbook), aby upewnić się, że uwierzytelnianie dla twoich książek runbook jest poprawnie skonfigurowane.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenariusz: Zdarzenie 15011 w hybrydowym procesu roboczego runbook

#### <a name="issue"></a>Problem

Hybrydowy proces roboczy runbook odbiera zdarzenie 15011 wskazujące, że wynik zapytania jest nieprawidłowy. Następujący błąd pojawia się, gdy proces roboczy próbuje otworzyć połączenie z serwerem [SignalR.](/aspnet/core/signalr/introduction)

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie został poprawnie skonfigurowany do automatycznego wdrażania funkcji, na przykład na Update Management. Wdrożenie zawiera część, która łączy maszynę wirtualną z obszarem roboczym usługi Log Analytics. Skrypt programu PowerShell wyszukuje obszar roboczy w subskrypcji o podanej nazwie. W tym przypadku obszar roboczy usługi Log Analytics znajduje się w innej subskrypcji. Skrypt nie może znaleźć obszaru roboczego i próbuje go utworzyć, ale nazwa jest już zajęte. W związku z tym wdrożenie kończy się niepowodzeniem.

#### <a name="resolution"></a>Rozwiązanie

Dostępne są dwie opcje rozwiązania tego problemu:

* Zmodyfikuj skrypt programu PowerShell, aby szukać obszaru roboczego usługi Log Analytics w innej subskrypcji. Jest to dobre rozwiązanie do użycia, jeśli planujesz wdrożyć w przyszłości wiele maszyn hybrydowych procesów roboczych runbook.

* Ręcznie skonfiguruj maszynę procesu roboczego do uruchamiania w piaskownicy usługi Orchestrator. Następnie uruchom runbook utworzony na koncie Azure Automation procesu roboczego, aby przetestować funkcjonalność.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenariusz: maszyny wirtualne platformy Azure z systemem Windows są automatycznie porzucane z grupy hybrydowych procesów roboczych

#### <a name="issue"></a>Problem

Hybrydowego procesu roboczego runbook ani maszyn wirtualnych nie można zobaczyć, gdy maszyna procesu roboczego została wyłączona przez długi czas.

#### <a name="cause"></a>Przyczyna

Maszyna hybrydowego procesu roboczego runbook nie wysyłała polecenia ping Azure Automation więcej niż 30 dni. W związku z tym automatyzacja przeczyściła grupę hybrydowych procesów roboczych runbook lub grupę systemowych procesów roboczych. 

#### <a name="resolution"></a>Rozwiązanie

Uruchom maszynę procesu roboczego, a następnie ponownie zarejestruj ją przy użyciu Azure Automation. Aby uzyskać instrukcje dotyczące sposobu instalowania środowiska runbook i nawiązywania połączenia z Azure Automation, zobacz Wdrażanie hybrydowego procesu [roboczego runbook systemu Windows.](../automation-windows-hrw-install.md)

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenariusz: w magazynie certyfikatów hybrydowego procesu roboczego runbook nie znaleziono certyfikatu

#### <a name="issue"></a>Problem

Działanie runbook uruchomionego w hybrydowym procesów roboczych runbook kończy się niepowodzeniem z następującym komunikatem o błędzie:

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy [](../automation-security-overview.md#run-as-accounts) próbujesz użyć konta Uruchom jako w podręczniku Runbook uruchomionym w hybrydowym procesie roboczy runbook, w którym nie ma certyfikatu konta Uruchom jako. Hybrydowe pracownicy runbook domyślnie nie mają zasobu certyfikatu lokalnie. Konto Uruchom jako wymaga prawidłowego działania tego zasobu.

#### <a name="resolution"></a>Rozwiązanie

Jeśli hybrydowy proces roboczy runbook jest maszyną wirtualną platformy Azure, zamiast tego możesz użyć uwierzytelniania [element runbook przy użyciu tożsamości zarządzanych.](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) Ten scenariusz upraszcza uwierzytelnianie, umożliwiając uwierzytelnianie w zasobach platformy Azure przy użyciu tożsamości zarządzanej maszyny wirtualnej platformy Azure zamiast konta Uruchom jako. Jeśli hybrydowy proces roboczy runbook jest maszyną lokalną, należy zainstalować certyfikat konta Uruchom jako na maszynie. Aby dowiedzieć się, jak zainstalować certyfikat, zobacz kroki uruchamiania runbook programu PowerShell **Export-RunAsCertificateToHybridWorker** w tece Runbook w hybrydowym procesu roboczego [runbook.](../automation-hrw-run-runbooks.md)

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenariusz: Błąd 403 podczas rejestracji hybrydowego procesu roboczego runbook

#### <a name="issue"></a>Problem

Faza rejestracji początkowej procesu roboczego kończy się niepowodzeniem i pojawia się następujący błąd (403):

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>Przyczyna

Możliwe przyczyny następujących problemów:

* W ustawieniach agenta znajduje się nieprawidłowo wpisany identyfikator obszaru roboczego lub klucz obszaru roboczego (podstawowy). 
* Hybrydowy proces roboczy runbook nie może pobrać konfiguracji, co powoduje błąd łączenia konta. Gdy platforma Azure włącza funkcje na maszynach, obsługuje tylko niektóre regiony w celu łączenia obszaru roboczego usługi Log Analytics i konta usługi Automation. Istnieje również możliwość, że na komputerze ustawiono nieprawidłową datę lub datę. Jeśli czas wynosi +/- 15 minut od bieżącej godziny, wdrożenie funkcji kończy się niepowodzeniem.

#### <a name="resolution"></a>Rozwiązanie

##### <a name="mistyped-workspace-id-or-key"></a>Nieprawidłowo wpisany identyfikator lub klucz obszaru roboczego
Aby sprawdzić, czy identyfikator obszaru roboczego lub klucz obszaru roboczego agenta został błędnie wpisany, zobacz Dodawanie lub usuwanie obszaru roboczego — agent systemu Windows dla agenta systemu [Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) lub Dodawanie lub usuwanie obszaru roboczego — agent systemu [Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) dla agenta systemu Linux. Pamiętaj, aby wybrać pełny ciąg z Azure Portal, a następnie skopiować i wkleić go uważnie.

##### <a name="configuration-not-downloaded"></a>Nie pobrano konfiguracji

Obszar roboczy usługi Log Analytics i konto usługi Automation muszą znajdować się w połączonym regionie. Aby uzyskać listę obsługiwanych regionów, zobacz [Azure Automation i log Analytics workspace mappings](../how-to/region-mappings.md)(Mapowania obszarów roboczych usługi Log Analytics).

Może być również konieczne zaktualizowanie daty lub strefy czasowej komputera. Jeśli wybierzesz niestandardowy zakres czasu, upewnij się, że zakres jest w czasie UTC, który może różnić się od lokalnej strefy czasowej.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>Scenariusz: Set-AzStorageBlobContent kończy się niepowodzeniem w hybrydowym procesu roboczego runbook 

#### <a name="issue"></a>Problem

Uruchomienie runbook kończy się niepowodzeniem, gdy próbuje wykonać polecenie `Set-AzStorageBlobContent` i jest wyświetlany następujący komunikat o błędzie:

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>Przyczyna

 Ten błąd jest spowodowany przez długie zachowanie nazwy pliku `[System.IO.Path]::GetFullPath()` wywołań, do których są dodaje ścieżki UNC.

#### <a name="resolution"></a>Rozwiązanie

Aby obejść ten problem, można utworzyć plik konfiguracji o nazwie `OrchestratorSandbox.exe.config` z następującą zawartością:

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

Umieść ten plik w tym samym folderze co plik wykonywalny `OrchestratorSandbox.exe` . Na przykład

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> Jeśli uaktualnisz agenta, ten plik konfiguracji zostanie usunięty i konieczne będzie jego ponowne utworzenie.

## <a name="linux"></a>Linux

Hybrydowy proces roboczy runbook systemu Linux zależy od agenta [usługi Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) dla systemu Linux w celu komunikowania się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań runbook i zgłaszania stanu. Jeśli rejestracja procesu roboczego zakończy się niepowodzeniem, poniżej znajdują się niektóre możliwe przyczyny błędu.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scenariusz: Hybrydowy proces roboczy runbook systemu Linux otrzymuje monit o hasło podczas podpisywania runbook

#### <a name="issue"></a>Problem

Uruchomienie polecenia `sudo` dla hybrydowego procesu roboczego runbook systemu Linux pobiera nieoczekiwany monit o hasło.

#### <a name="cause"></a>Przyczyna

Konto **nxautomationuser** dla agenta usługi Log Analytics dla systemu Linux nie jest poprawnie skonfigurowane w pliku **sudoers.** Hybrydowy proces roboczy runbook wymaga odpowiedniej konfiguracji uprawnień konta i innych danych, aby można było podpisywać je w procesach roboczych runbook systemu Linux.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że hybrydowy proces roboczy runbook ma plik wykonywalny GnuPG (GPG) na maszynie.

* Sprawdź konfigurację konta **nxautomationuser** w pliku **sudoers.** Zobacz Running runbooks on a Hybrid Runbook Worker (Uruchamianie [podręczników Runbook w hybrydowym procesu roboczego runbook).](../automation-hrw-run-runbooks.md)

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenariusz: Agent usługi Log Analytics dla systemu Linux nie jest uruchomiony

#### <a name="issue"></a>Problem

Agent usługi Log Analytics dla systemu Linux nie jest uruchomiony.

#### <a name="cause"></a>Przyczyna

Jeśli agent nie jest uruchomiony, uniemożliwia hybrydowemu procesowi roboczemu runbook systemu Linux komunikację z Azure Automation. Agent może nie być uruchomiony z różnych powodów.

#### <a name="resolution"></a>Rozwiązanie

 Sprawdź, czy agent jest uruchomiony, wprowadzając polecenie `ps -ef | grep python` . Powinny pojawić się dane wyjściowe podobne do poniższych. Język Python przetwarza przy użyciu **konta użytkownika nxautomation.** Jeśli Azure Automation nie jest włączona, żaden z następujących procesów nie jest uruchomiony.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Na poniższej liście przedstawiono procesy, które zostały uruchomione dla hybrydowego procesu roboczego runbook systemu Linux. Wszystkie znajdują się w katalogu /var/opt/microsoft/omsagent/state/automationworker/.

* **oms.conf:** proces menedżera procesu roboczego. Jest ona rozpoczynana bezpośrednio z dsc.
* **worker.conf:** proces automatycznie zarejestrowanego hybrydowego procesu roboczego. Jest on rozpoczynany przez menedżera procesu roboczego. Ten proces jest używany przez Update Management i jest niewidoczny dla użytkownika. Ten proces nie występuje, jeśli Update Management nie jest włączona na maszynie.
* **diy/worker.conf:** proces hybrydowy procesu roboczego diy. Hybrydowy proces roboczy typu "diy" służy do wykonywania podręczników runbook użytkownika w hybrydowym procesie roboczym runbook. Różni się on tylko od procesu automatycznie zarejestrowanego hybrydowego procesu roboczego pod kluczowymi szczegółami, że używa innej konfiguracji. Ten proces nie występuje, jeśli Azure Automation jest wyłączona, a hybrydowy proces roboczy systemu Linux diy nie jest zarejestrowany.

Jeśli agent nie jest uruchomiony, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenariusz: Określona klasa nie istnieje

Jeśli w pliku `The specified class does not exist..` **/var/opt/microsoft/omsconfig/omsconfig.log** zostanie wyświetlony komunikat o błędzie, należy zaktualizować agenta usługi Log Analytics dla systemu Linux. Uruchom następujące polecenie, aby ponownie zainstalować agenta.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrydowy proces roboczy runbook systemu Windows zależy od tego, czy agent usługi [Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) dla systemu Windows komunikuje się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań runbook i zgłaszania stanu. Jeśli rejestracja procesu roboczego zakończy się niepowodzeniem, ta sekcja zawiera kilka możliwych przyczyn.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenariusz: Agent usługi Log Analytics dla systemu Windows nie jest uruchomiony

#### <a name="issue"></a>Problem

Nie `healthservice` jest uruchomiony na maszynie hybrydowego procesu roboczego runbook.

#### <a name="cause"></a>Przyczyna

Jeśli usługa Log Analytics dla systemu Windows nie jest uruchomiona, hybrydowy proces roboczy runbook nie może komunikować się z Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy agent jest uruchomiony, wprowadzając następujące polecenie w programie PowerShell: `Get-Service healthservice` . Jeśli usługa została zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scenariusz: Zdarzenie 4502 w Operations Manager danych

#### <a name="issue"></a>Problem

W dzienniku **zdarzeń Application and Services Logs\Operations Manager** zostanie wyświetlony komunikat zdarzenia 4502 i komunikat o zdarzeniu zawierający następujący `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` opis:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany tym, że serwer proxy lub zapora sieciowa blokuje komunikację Microsoft Azure. Sprawdź, czy komputer ma dostęp wychodzący do usługi **\* .azure-automation.net** na porcie 443.

#### <a name="resolution"></a>Rozwiązanie

Dzienniki są przechowywane lokalnie dla każdego hybrydowego procesu roboczego w folderze C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. W dziennikach aplikacji i **usług\Microsoft-SMA\Operations** i **Application and Services Logs\Operations Manager** zdarzeń można sprawdzić, czy istnieją jakiekolwiek zdarzenia ostrzeżeń lub błędów. Te dzienniki wskazują na łączność lub inny typ problemu, który ma wpływ na włączanie roli do Azure Automation lub problem napotykany w normalnych operacjach. Aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z agentem usługi Log Analytics, zobacz Rozwiązywanie problemów z agentem [systemu Windows usługi Log Analytics.](../../azure-monitor/agents/agent-windows-troubleshoot.md)

Hybrydowe pracownicy [wysyłają](../automation-runbook-output-and-messages.md) dane wyjściowe i komunikaty runbook do Azure Automation w taki sam sposób, jak zadania runbook uruchomione w chmurze wysyłają dane wyjściowe i komunikaty. Strumienie Pełne i Postęp można włączyć tak samo jak w przypadku podręczników Runbook.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Scenariusz: Orchestrator.Sandbox.exe nie można nawiązać połączenia z usługą Microsoft 365 pośrednictwem serwera proxy

#### <a name="issue"></a>Problem

Skrypt uruchomiony w hybrydowym procesu roboczego runbook systemu Windows nie może nawiązać połączenia zgodnie z oczekiwaniami Microsoft 365 w piaskownicy programu Orchestrator. Skrypt używa polecenia [Connect-MsolService do](/powershell/module/msonline/connect-msolservice) nawiązania połączenia. 

Jeśli **dostosujemyOrchestrator.Sandbox.exe.config,** aby ustawić serwer proxy i listę obejść, piaskownica nadal nie nawiąze prawidłowego połączenia. Plik **Powershell_ise.exe.config** z tym samym serwerem proxy i ustawieniami listy pomijania wydaje się działać zgodnie z oczekiwaniami. Service Management Automation (SMA) i dzienniki programu PowerShell nie zawierają żadnych informacji dotyczących serwera proxy.

#### <a name="cause"></a>Przyczyna

Połączenie z Active Directory Federation Services (AD FS) na serwerze nie może pominąć serwera proxy. Pamiętaj, że piaskownica programu PowerShell jest uruchamiana jako zalogowany użytkownik. Piaskownica programu Orchestrator jest jednak w dużym stopniu dostosowana i może ignorować **Orchestrator.Sandbox.exe.config** ustawień pliku. Zawiera ona specjalny kod do obsługi ustawień serwera proxy agenta usługi Log Analytics, ale nie do obsługi innych niestandardowych ustawień serwera proxy. 

#### <a name="resolution"></a>Rozwiązanie

Problem z piaskownicą programu Orchestrator można rozwiązać, migrując skrypt w celu używania modułów Azure Active Directory zamiast modułu MSOnline dla poleceń cmdlet programu PowerShell. Aby uzyskać więcej informacji, [zobacz Migrowanie z programu Orchestrator do Azure Automation (beta).](../automation-orchestrator-migration.md)

Jeśli chcesz nadal używać poleceń cmdlet modułu MSOnline, zmień skrypt, aby użyć [polecenia Invoke-Command.](/powershell/module/microsoft.powershell.core/invoke-command) Określ wartości parametrów `ComputerName` `Credential` i . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Ta zmiana kodu uruchamia całkowicie nową sesję programu PowerShell w kontekście określonych poświadczeń. Powinno to umożliwić przepływ ruchu przez serwer proxy, który uwierzytelnia aktywnego użytkownika.

>[!NOTE]
>Takie rozwiązanie sprawia, że manipulowanie plikiem konfiguracji piaskownicy nie jest konieczne. Nawet jeśli plik konfiguracji będzie działać ze skryptem, plik zostanie wyczyszany przy każdej aktualizacji agenta hybrydowego procesu roboczego runbook.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenariusz: hybrydowy proces roboczy runbook nie raportuje

#### <a name="issue"></a>Problem

Maszyna hybrydowego procesu roboczego runbook jest uruchomiona, ale nie widzisz danych pulsu dla maszyny w obszarze roboczym.

Następujące przykładowe zapytanie przedstawia maszyny w obszarze roboczym i ich ostatni puls:

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany uszkodzeniem pamięci podręcznej hybrydowego procesu roboczego runbook.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, zaloguj się do hybrydowego procesu roboczego runbook i uruchom następujący skrypt. Ten skrypt zatrzymuje agenta usługi Log Analytics dla systemu Windows, usuwa jego pamięć podręczną i ponownie uruchamia usługę. Ta akcja wymusza ponowne pobranie konfiguracji hybrydowego procesu roboczego runbook z Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenariusz: nie można dodać hybrydowego procesu roboczego runbook systemu Windows

#### <a name="issue"></a>Problem

Podczas próby dodania hybrydowego procesu roboczego runbook za pomocą polecenia cmdlet zostanie wyświetlony `Add-HybridRunbookWorker` następujący komunikat:

`Machine is already registered`

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany tym, że maszyna jest już zarejestrowana przy użyciu innego konta usługi Automation lub jeśli spróbujesz ponownie dodać hybrydowy proces roboczy runbook po usunięciu go z maszyny.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, usuń następujący klucz rejestru, uruchom ponownie polecenie `HealthService` i spróbuj ponownie użyć polecenia `Add-HybridRunbookWorker` cmdlet.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenariusz: nie można dodać hybrydowego procesu roboczego runbook systemu Linux

#### <a name="issue"></a>Problem

Podczas próby dodania hybrydowego procesu roboczego runbook przy użyciu skryptu języka Python zostanie wyświetlony następujący `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` komunikat:

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

Ponadto próba wyrejestrowania hybrydowego procesu roboczego runbook przy użyciu skryptu `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` języka Python:

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli maszyna jest już zarejestrowana przy użyciu innego konta usługi Automation, grupa hybrydowych procesów roboczych platformy Azure została usunięta lub jeśli spróbujesz ponownie dodać hybrydowy proces roboczy runbook po usunięciu go z maszyny.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem:

1. Usuń agenta `sudo sh onboard_agent.sh --purge` .

1. Uruchom następujące polecenia:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Ponownie dokań agenta `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` .

1. Poczekaj na `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` wypełnienie folderu.

1. Spróbuj ponownie `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` użyć skryptu języka Python.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz swojego problemu w tym miejscu lub nie możesz go rozwiązać, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów z platformy Azure na [forach platformy Azure.](https://azure.microsoft.com/support/forums/)
* Połącz się z usługą , [@AzureSupport](https://twitter.com/azuresupport) oficjalnym kontem Microsoft Azure w celu poprawy jakości obsługi klienta. pomoc techniczna platformy Azure łączy społeczność platformy Azure z odpowiedziami, pomocą techniczną i ekspertami.
* Zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną.**
