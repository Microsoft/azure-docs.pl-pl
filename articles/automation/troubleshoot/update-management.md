---
title: Rozwiązywanie problemów dotyczących Update Management Azure Automation
description: W tym artykule opisano sposób rozwiązywania problemów z Azure Automation Update Management.
services: automation
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: 3d6a87d9b420ea394baaa21c87dff457e4c908d0
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070337"
---
# <a name="troubleshoot-update-management-issues"></a>Rozwiązywanie problemów z usługą Update Management

W tym artykule omówiono problemy, które można napotkać podczas wdrażania funkcji Update Management na maszynach. Istnieje narzędzie do rozwiązywania problemów z agentem dla agenta hybrydowego procesu roboczego elementu Runbook w celu ustalenia podstawowego problemu. Aby dowiedzieć się więcej na temat narzędzia do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z usługą Windows Update Agent](update-agent-issues.md) i [Rozwiązywanie problemów z usługą Linux Update Agent](update-agent-issues-linux.md). Inne problemy z wdrażaniem funkcji można znaleźć w temacie [Rozwiązywanie problemów z wdrażaniem funkcji](onboarding.md).

>[!NOTE]
>W przypadku napotkania problemów podczas wdrażania Update Management na komputerze z systemem Windows otwórz Podgląd zdarzeń Windows, a następnie sprawdź dziennik zdarzeń **Operations Manager** w obszarze **Dzienniki aplikacji i usług** na komputerze lokalnym. Wyszukaj zdarzenia o IDENTYFIKATORze 4502 i szczegółach zdarzenia, które zawierają `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Scenariusz: zostanie wyświetlony komunikat o błędzie "nie można włączyć rozwiązania aktualizacji"

### <a name="issue"></a>Problem

Podczas próby włączenia Update Management na koncie usługi Automation zostanie wyświetlony następujący błąd:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Wymagania dotyczące zapory sieciowej dla agenta Log Analytics mogą nie być poprawnie skonfigurowane. Ta sytuacja może spowodować niepowodzenie agenta podczas rozpoznawania adresów URL DNS.

* Update Management określania wartości docelowej jest błędnie skonfigurowana, a komputer nie otrzymuje aktualizacji zgodnie z oczekiwaniami.

* Można również zauważyć, że na komputerze jest wyświetlany stan `Non-compliant` pod kątem **zgodności**. W tym samym czasie, **Analiza pulpitu usługi Agent** raportuje agenta jako `Disconnected` .

### <a name="resolution"></a>Rozwiązanie

* Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline), w zależności od systemu operacyjnego.

* Przejdź do pozycji [Konfiguracja sieci](../automation-hybrid-runbook-worker.md#network-planning) , aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Update Management działały.  

* Sprawdź, czy występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../update-management/update-mgmt-scope-configuration.md) określa, które maszyny są skonfigurowane do Update Management. Jeśli Twoja maszyna jest wyświetlana w obszarze roboczym, ale nie w Update Management, musisz ustawić konfigurację zakresu, aby wskazać maszyny docelowe. Aby dowiedzieć się więcej na temat konfiguracji zakresu, zobacz [Włączanie maszyn w obszarze roboczym](../update-management/update-mgmt-enable-automation-account.md#enable-machines-in-the-workspace).

* Usuń konfigurację procesu roboczego, wykonując czynności opisane w sekcji [usuwanie hybrydowego procesu roboczego elementu Runbook z lokalnego komputera z systemem Windows](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) lub [usuwanie hybrydowego procesu roboczego elementu Runbook z lokalnego komputera](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)z systemem Linux.

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenariusz: zastąpiona aktualizacja wskazana jako brakująca w Update Management

### <a name="issue"></a>Problem

Stare aktualizacje są wyświetlane dla konta usługi Automation jako brakujące, mimo że zostały zastąpione. Zastąpiona aktualizacja jest taka, którą nie trzeba instalować, ponieważ dostępna jest nowsza aktualizacja, która rozwiązuje tę samą lukę w zabezpieczeniach. Update Management ignoruje zastąpioną aktualizację i nie ma zastosowania na korzyść zastępowanej aktualizacji. Aby uzyskać informacje o powiązanym problemie, zobacz [Aktualizacja została zastąpiona](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Przyczyna

Zastąpione aktualizacje nie są odrzucane w Windows Server Update Services (WSUS), dzięki czemu można je traktować jako niemające zastosowania.

### <a name="resolution"></a>Rozwiązanie

Gdy zastąpiona aktualizacja nie ma zastosowania do 100%, należy zmienić stan zatwierdzenia tej aktualizacji na `Declined` w programie WSUS. Aby zmienić stan zatwierdzenia dla wszystkich aktualizacji:

1. Na koncie usługi Automation wybierz pozycję **Update Management** , aby wyświetlić stan komputera. Zobacz [Wyświetlanie ocen aktualizacji](../update-management/update-mgmt-view-update-assessments.md).

2. Sprawdź zastąpioną aktualizację, aby upewnić się, że nie ma jej na 100%.

3. Na serwerze programu WSUS, na którym są zgłaszane komputery, [Odrzuć aktualizację](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates).

4. Wybierz pozycję **komputery** , a w kolumnie **zgodność** Wymuś ponowne skanowanie pod kątem zgodności. Zobacz [Zarządzanie aktualizacjami maszyn wirtualnych](../update-management/update-mgmt-manage-updates-for-vm.md).

5. Powtórz powyższe kroki dla innych zastępowanych aktualizacji.

6. W przypadku Windows Server Update Services (WSUS) wyczyść wszystkie zastąpione aktualizacje, aby odświeżyć infrastrukturę za pomocą [Kreatora oczyszczania serwera](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)programu WSUS.

7. Powtórz tę procedurę regularnie, aby poprawić problem z wyświetlaniem i zminimalizować ilość miejsca na dysku używanego do zarządzania aktualizacjami.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenariusz: maszyny nie są wyświetlane w portalu w obszarze Update Management

### <a name="issue"></a>Problem

Maszyny mają następujące objawy:

* Twoja maszyna `Not configured` jest wyświetlana w widoku Update Management maszyny wirtualnej.

* Brak Twoich maszyn w widoku Update Management konta Azure Automation.

* Masz maszyny, które są wyświetlane `Not assessed` jako **zgodne**. Dane pulsu można jednak znaleźć w dziennikach Azure Monitor dla hybrydowego procesu roboczego elementu Runbook, ale nie dla Update Management.

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu mogą być lokalne problemy z konfiguracją lub nieprawidłowo skonfigurowana konfiguracja zakresu. Możliwe przyczyny są następujące:

* Może być konieczne ponowne zarejestrowanie i ponowne zainstalowanie hybrydowego procesu roboczego elementu Runbook.

* Być może zdefiniowano limit przydziału w obszarze roboczym, który został osiągnięty i uniemożliwia dalsze przechowywanie danych.

### <a name="resolution"></a>Rozwiązanie

1. Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline), w zależności od systemu operacyjnego.

2. Upewnij się, że komputer jest raportowany do prawidłowego obszaru roboczego. Aby uzyskać wskazówki dotyczące weryfikacji tego aspektu, zobacz [Weryfikowanie łączności agenta z Azure monitor](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Upewnij się również, że ten obszar roboczy jest połączony z kontem Azure Automation. Aby potwierdzić, przejdź do konta usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

3. Upewnij się, że maszyny są widoczne w obszarze roboczym Log Analytics połączonym z kontem usługi Automation. Uruchom następujące zapytanie w obszarze roboczym Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Jeśli komputer nie jest widoczny w wynikach zapytania, nie został ostatnio zaewidencjonowany. Prawdopodobnie wystąpił problem z konfiguracją lokalną i należy [ponownie zainstalować agenta](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

5. Jeśli komputer jest wyświetlany w wynikach zapytania, sprawdź, czy występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../update-management/update-mgmt-scope-configuration.md) określa, które maszyny są skonfigurowane do Update Management.

6. Jeśli Twoja maszyna jest wyświetlana w obszarze roboczym, ale nie w Update Management, musisz skonfigurować konfigurację zakresu, aby wskazać maszynę docelową. Aby dowiedzieć się, jak to zrobić, zobacz temat [Włączanie maszyn w obszarze roboczym](../update-management/update-mgmt-enable-automation-account.md#enable-machines-in-the-workspace).

7. W obszarze roboczym Uruchom to zapytanie.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Jeśli otrzymasz `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` wynik, zostanie osiągnięty limit przydziału zdefiniowany w obszarze roboczym, w którym zarejestrowano dane. W obszarze roboczym przejdź do pozycji **Zarządzanie ilością danych** w obszarze **użycie i szacowane koszty**, a następnie Zmień lub Usuń przydział.

9. Jeśli problem nadal nie zostanie rozwiązany, wykonaj kroki opisane w sekcji [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](../automation-windows-hrw-install.md) w celu ponownego zainstalowania hybrydowego procesu roboczego dla systemu Windows. W przypadku systemu Linux wykonaj kroki opisane w sekcji [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenariusz: nie można zarejestrować dostawcy zasobów usługi Automation dla subskrypcji

### <a name="issue"></a>Problem

Podczas pracy z wdrożeniami funkcji na koncie usługi Automation wystąpił następujący błąd:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów usługi Automation nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Aby zarejestrować dostawcę zasobów usługi Automation, wykonaj następujące kroki w Azure Portal.

1. Na liście usługi platformy Azure w dolnej części portalu wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **subskrypcje** w grupie usługi ogólne.

2. Wybierz subskrypcję.

3. W obszarze **Ustawienia**wybierz pozycję **dostawcy zasobów**.

4. Z listy dostawców zasobów Sprawdź, czy dostawca zasobów Microsoft. Automation jest zarejestrowany.

5. Jeśli nie ma go na liście, zarejestruj dostawcę Microsoft. Automation, wykonując czynności opisane w sekcji [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenariusz: zaplanowana aktualizacja z harmonogramem dynamicznym nie pominął niektórych maszyn

### <a name="issue"></a>Problem

Maszyny zawarte w wersji zapoznawczej aktualizacji nie są wyświetlane na liście maszyn w trakcie zaplanowanego uruchomienia.

### <a name="cause"></a>Przyczyna

Ten problem może mieć jedną z następujących przyczyn:

* Subskrypcje zdefiniowane w zakresie w zapytaniu dynamicznym nie są skonfigurowane dla zarejestrowanego dostawcy zasobów usługi Automation.

* Maszyny nie były dostępne lub nie miały odpowiednich tagów po wykonaniu harmonogramu.

### <a name="resolution"></a>Rozwiązanie

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Subskrypcje nie zostały skonfigurowane dla zarejestrowanego dostawcy zasobów usługi Automation

Jeśli subskrypcja nie jest skonfigurowana dla dostawcy zasobów usługi Automation, nie można wysyłać zapytań ani pobierać informacji o maszynach w tej subskrypcji. Aby zweryfikować rejestrację subskrypcji, wykonaj następujące czynności.

1. W [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)uzyskaj dostęp do listy usług platformy Azure.

2. Wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **subskrypcje** w grupie usługi ogólne.

3. Znajdź subskrypcję zdefiniowaną w zakresie wdrożenia.

4. W obszarze **Ustawienia**wybierz pozycję **dostawcy zasobów**.

5. Sprawdź, czy dostawca zasobów Microsoft. Automation jest zarejestrowany.

6. Jeśli nie ma go na liście, zarejestruj dostawcę Microsoft. Automation, wykonując czynności opisane w sekcji [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](../../azure-resource-manager/templates/error-register-resource-provider.md).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Maszyny są niedostępne lub niepoprawnie otagowane po wykonaniu harmonogramu

W przypadku skonfigurowania subskrypcji dla dostawcy zasobów usługi Automation należy wykonać poniższą procedurę, ale w przypadku braku niektórych maszyn zostanie uruchomiony harmonogram aktualizacji z określonymi [grupami dynamicznymi](../update-management/update-mgmt-groups.md) .

1. W Azure Portal Otwórz konto usługi Automation i wybierz pozycję **Update Management**.

2. Sprawdź [historię Update Management](../update-management/update-mgmt-deploy-updates.md#view-results-of-a-completed-update-deployment) , aby określić dokładną godzinę uruchomienia wdrożenia aktualizacji.

3. W przypadku komputerów, które prawdopodobnie nie zostały pominięte przez Update Management, użyj grafu zasobów platformy Azure (ARG), aby [zlokalizować zmiany maszyn](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details).

4. Przed uruchomieniem wdrożenia aktualizacji należy wyszukać zmiany w znacznym okresie, na przykład na jednym dniu.

5. Sprawdź wyniki wyszukiwania dla wszelkich zmian systemowych, takich jak usuwanie i aktualizowanie zmian, na maszynach w tym okresie. Te zmiany mogą zmienić stan komputera lub Tagi, dzięki czemu maszyny nie są wybierane na liście maszyn podczas wdrażania aktualizacji.

6. Dostosuj maszyny i ustawienia zasobów odpowiednio do potrzeb w celu poprawienia problemów dotyczących stanu komputera lub tagów.

7. Uruchom ponownie harmonogram aktualizacji, aby upewnić się, że wdrożenie z określonymi grupami dynamicznymi zawiera wszystkie maszyny.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scenariusz: oczekiwane maszyny nie są dostępne w wersji zapoznawczej dla grupy dynamicznej

### <a name="issue"></a>Problem

Maszyny wirtualne dla wybranych zakresów grupy dynamicznej nie są wyświetlane na liście Azure Portal podglądu. Ta lista zawiera wszystkie maszyny pobrane przez zapytanie ARG dla wybranych zakresów. Zakresy są filtrowane dla maszyn, na których zainstalowano hybrydowe procesy robocze elementów Runbook, do których masz uprawnienia dostępu.

### <a name="cause"></a>Przyczyna

Oto możliwe przyczyny tego problemu:

* Nie masz poprawnego dostępu do wybranych zakresów.
* Zapytanie ARG nie pobiera oczekiwanych maszyn.
* Hybrydowy proces roboczy elementu Runbook nie jest zainstalowany na maszynach.

### <a name="resolution"></a>Rozwiązanie 

#### <a name="incorrect-access-on-selected-scopes"></a>Nieprawidłowy dostęp w wybranych zakresach

W Azure Portal są wyświetlane tylko maszyny, dla których masz dostęp do zapisu w danym zakresie. Jeśli nie masz poprawnego dostępu do zakresu, zobacz [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal](../../role-based-access-control/quickstart-assign-role-user-portal.md).

#### <a name="arg-query-doesnt-return-expected-machines"></a>Zapytanie ARG nie zwraca oczekiwanych maszyn

Wykonaj poniższe kroki, aby dowiedzieć się, czy zapytania działają prawidłowo.

1. Uruchom zapytanie ARG sformatowane, jak pokazano poniżej w bloku Eksplorator grafu zasobów w Azure Portal. To zapytanie naśladuje filtry wybrane podczas tworzenia grupy dynamicznej w Update Management. Zobacz [Używanie grup dynamicznych z Update Management](../update-management/update-mgmt-groups.md).

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Oto przykład:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Sprawdź, czy maszyny, których szukasz, są wyświetlane w wynikach zapytania.

3. Jeśli komputery nie znajdują się na liście, prawdopodobnie wystąpił problem z filtrem wybranym w grupie dynamicznej. Dostosuj konfigurację grupy zgodnie z wymaganiami.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrydowy proces roboczy elementu Runbook nie został zainstalowany na maszynach

Maszyny są wyświetlane w polu ARG wyniki zapytania, ale nadal nie są wyświetlane w podglądzie grupy dynamicznej. W takim przypadku maszyny mogą nie być wyznaczeni jako hybrydowe procesy robocze i w ten sposób nie mogą uruchamiać Azure Automation i Update Management zadań. Aby upewnić się, że maszyny, które są widoczne, są skonfigurowane jako hybrydowe procesy robocze elementów Runbook:

1. W Azure Portal przejdź do konta usługi Automation dla komputera, który nie jest wyświetlany poprawnie.

2. Wybierz **grupy hybrydowych procesów roboczych** w obszarze **Automatyzacja procesów**.

3. Wybierz kartę **grupy hybrydowych procesów roboczych systemu** .

4. Sprawdź, czy dla tej maszyny jest obecny hybrydowy proces roboczy.

5. Jeśli maszyna nie jest skonfigurowana jako hybrydowy proces roboczy, wprowadź zmiany przy użyciu instrukcji dotyczących [automatyzowania zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego elementu Runbook](../automation-hybrid-runbook-worker.md).

6. Dołącz maszynę do grupy hybrydowych procesów roboczych elementu Runbook.

7. Powtórz powyższe kroki dla wszystkich komputerów, które nie były wyświetlane w wersji zapoznawczej.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenariusz: składniki Update Management włączone, podczas gdy maszyna wirtualna będzie nadal wyświetlana jako skonfigurowana

### <a name="issue"></a>Problem

Po rozpoczęciu wdrożenia nadal zobaczysz następujący komunikat na maszynie wirtualnej 15 minut:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Trwa blokowanie komunikacji z kontem usługi Automation.

* Istnieje zduplikowana nazwa komputera z różnymi identyfikatorami komputerów źródłowych. Ten scenariusz występuje, gdy maszyna wirtualna z określoną nazwą komputera jest tworzona w różnych grupach zasobów i jest raportowana do tego samego obszaru roboczego agenta logistycznego w ramach subskrypcji.

* Wdrażany obraz maszyny wirtualnej może pochodzić z sklonowanego komputera, który nie został przygotowany przy użyciu przygotowania systemu (Sysprep) z zainstalowanym agentem Log Analytics dla systemu Windows.

### <a name="resolution"></a>Rozwiązanie

Aby pomóc w ustaleniu dokładnego problemu z maszyną wirtualną, uruchom następujące zapytanie w obszarze roboczym Log Analytics połączonym z kontem usługi Automation.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Komunikacja z zablokowanym kontem usługi Automation

Przejdź do pozycji [Planowanie sieci](../update-management/update-mgmt-overview.md#ports) , aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Update Management działały.

#### <a name="duplicate-computer-name"></a>Zduplikowana nazwa komputera

Zmień nazwy maszyn wirtualnych, aby zapewnić unikatowe nazwy w ich środowisku.

#### <a name="deployed-image-from-cloned-machine"></a>Wdrożony obraz ze sklonowanego komputera

Jeśli używasz sklonowanego obrazu, różne nazwy komputerów mają taki sam identyfikator komputera źródłowego. W takim przypadku:

1. W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania dla `MicrosoftDefaultScopeConfig-Updates` konfiguracji zakresu, jeśli jest ona wyświetlana. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.

2. Uruchom następujące polecenie cmdlet.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Uruchom, `Restart-Service HealthService` Aby ponownie uruchomić usługę kondycji. Ta operacja powoduje odtworzenie klucza i wygenerowanie nowego identyfikatora UUID.

4. Jeśli takie podejście nie działa, najpierw należy uruchomić program Sysprep na obrazie, a następnie zainstalować agenta Log Analytics dla systemu Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenariusz: Wystąpił błąd połączonej subskrypcji podczas tworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure

### <a name="issue"></a>Problem

Wystąpił następujący błąd podczas próby utworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas tworzenia wdrożenia aktualizacji z maszynami wirtualnymi platformy Azure w innej dzierżawie uwzględnionej w wdrożeniu aktualizacji.

### <a name="resolution"></a>Rozwiązanie

Aby zaplanowano te elementy, należy wykonać poniższe obejście. Aby utworzyć harmonogram, można użyć polecenia cmdlet [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) z `ForUpdateConfiguration` parametrem. Następnie użyj polecenia cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) i przekaż maszyny w innej dzierżawie do `NonAzureComputer` parametru. Poniższy przykład pokazuje, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenariusz: nie wyjaśniono ponownych uruchomień

### <a name="issue"></a>Problem

Mimo że ustawisz opcję **kontroli ponownego uruchamiania** na **nigdy nie uruchamiaj ponownie**, maszyny nadal są ponownie uruchamiane po zainstalowaniu aktualizacji.

### <a name="cause"></a>Przyczyna

Windows Update mogą być modyfikowane przez kilka kluczy rejestru, z których każde może zmodyfikować zachowanie ponownego uruchamiania.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z kluczami rejestru wymienionymi w obszarze [Konfigurowanie aktualizacji automatycznych, edytując rejestr](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) i [klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , aby upewnić się, że maszyny są prawidłowo skonfigurowane.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenariusz: na komputerze nie można uruchomić programu w ramach wdrożenia aktualizacji

### <a name="issue"></a>Problem

Na komputerze zostanie wyświetlony `Failed to start` stan. Po wyświetleniu szczegółowych informacji o komputerze zostanie wyświetlony następujący błąd:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Przyczyna

Ten błąd może mieć jedną z następujących przyczyn:

* Komputer nie istnieje już.
* Maszyna jest wyłączona i nieosiągalna.
* Komputer ma problem z łącznością sieciową i w związku z tym hybrydowy proces roboczy na komputerze jest nieosiągalny.
* Wystąpił problem z aktualizacją agenta Log Analytics, który zmienił identyfikator komputera źródłowego.
* Działanie przebiegu aktualizacji zostało ograniczone, jeśli osiągnięto limit 200 zadań współbieżnych na koncie usługi Automation. Każde wdrożenie jest uznawane za zadanie, a każdy komputer w ramach wdrożenia aktualizacji liczy się jako zadanie. Wszystkie inne zadania usługi Automation lub wdrożenia, które są aktualnie uruchomione na koncie usługi Automation, są wliczane do współbieżnego limitu zadań.

### <a name="resolution"></a>Rozwiązanie

W razie potrzeby użyj [grup dynamicznych](../update-management/update-mgmt-groups.md) do wdrożeń aktualizacji. Ponadto można wykonać następujące czynności.

1. Sprawdź, czy komputer lub serwer spełnia [wymagania](../update-management/update-mgmt-overview.md#client-requirements).
2. Sprawdź łączność z hybrydowym procesem roboczym elementu Runbook za pomocą narzędzia do rozwiązywania problemów z agentem hybrydowego elementu Runbook. Aby dowiedzieć się więcej na temat narzędzia do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z aktualizowaniem agentów](update-agent-issues.md).

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenariusz: aktualizacje są instalowane bez wdrożenia

### <a name="issue"></a>Problem

Po zarejestrowaniu maszyny z systemem Windows w Update Management są widoczne aktualizacje zainstalowane bez wdrożenia.

### <a name="cause"></a>Przyczyna

W systemie Windows aktualizacje są instalowane automatycznie zaraz po ich udostępnieniu. Takie zachowanie może spowodować pomyłkę, jeśli aktualizacja nie została zaplanowana do wdrożenia na komputerze.

### <a name="resolution"></a>Rozwiązanie

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`Klucz rejestru jest domyślnie ustawiony na wartość 4: `auto download and install` .

W przypadku klientów Update Management zalecamy ustawienie tego klucza na 3: `auto download but do not auto install` .

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aktualizacji automatycznych](/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenariusz: maszyna jest już zarejestrowana na innym koncie

### <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Przyczyna

Maszyna została już wdrożona w innym obszarze roboczym dla Update Management.

### <a name="resolution"></a>Rozwiązanie

1. Wykonaj kroki opisane w sekcji [maszyny nie są wyświetlane w portalu w obszarze Update Management](#nologs) , aby upewnić się, że komputer jest raportowany do prawidłowego obszaru roboczego.
2. Wyczyść artefakty na maszynie, [usuwając grupę hybrydowych elementów Runbook](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group), a następnie spróbuj ponownie.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenariusz: maszyna nie może komunikować się z usługą

### <a name="issue"></a>Problem

Zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Przyczyna

Serwer proxy, Brama lub Zapora mogą blokować komunikację sieciową.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj sieć i upewnij się, że odpowiednie porty i adresy są dozwolone. Aby uzyskać listę portów i adresów wymaganych przez Update Management i hybrydowych procesów roboczych elementów Runbook, zobacz [wymagania sieciowe](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenariusz: nie można utworzyć certyfikatu z podpisem własnym

### <a name="issue"></a>Problem

Zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie może wygenerować certyfikatu z podpisem własnym.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy konto systemowe ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** , i spróbuj ponownie.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenariusz: zaplanowana aktualizacja nie powiodła się z powodu błędu MaintenanceWindowExceeded

### <a name="issue"></a>Problem

Domyślne okno obsługi aktualizacji to 120 minut. Można zwiększyć okno obsługi do maksymalnie 6 godzin lub 360 minut.

### <a name="resolution"></a>Rozwiązanie

Aby zrozumieć, dlaczego ten błąd wystąpił podczas przebiegu aktualizacji po pomyślnym uruchomieniu, [Sprawdź dane wyjściowe zadania](../update-management/update-mgmt-deploy-updates.md#view-results-of-a-completed-update-deployment) z danego komputera w przebiegu. Możesz znaleźć określone komunikaty o błędach z maszyn, które można zbadać i podjąć odpowiednie działania.  

Edytuj wszystkie błędy zaplanowanej aktualizacji, a następnie zwiększ okno obsługi.

Aby uzyskać więcej informacji o oknach obsługi, zobacz [Install Updates](../update-management/update-mgmt-deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenariusz: maszyna jest wyświetlana jako "nieoceniona" i pokazuje wyjątek HRESULT

### <a name="issue"></a>Problem

* Masz maszyny, które są wyświetlane w `Not assessed` obszarze **zgodność**, i zobaczysz komunikat o wyjątku poniżej.
* W portalu zostanie wyświetlony kod błędu HRESULT.

### <a name="cause"></a>Przyczyna

Agent aktualizacji (Agent Windows Update w systemie Windows; Menedżer pakietów dla dystrybucji systemu Linux) nie jest prawidłowo skonfigurowany. Update Management polega na agencie aktualizacji maszyny w celu zapewnienia wymaganych aktualizacji, stanu poprawki oraz wyników wdrożonych poprawek. Bez tych informacji Update Management nie może poprawnie zgłosić poprawek wymaganych lub zainstalowanych.

### <a name="resolution"></a>Rozwiązanie

Spróbuj przeprowadzić aktualizacje lokalnie na komputerze. Jeśli ta operacja nie powiedzie się, zazwyczaj oznacza to, że wystąpił błąd konfiguracji agenta aktualizacji.

Ten problem jest często spowodowany przez problemy z konfiguracją sieci i zaporą. Aby rozwiązać ten problem, wykonaj następujące testy.

* W przypadku systemu Linux zapoznaj się z odpowiednią dokumentacją, aby upewnić się, że można uzyskać dostęp do punktu końcowego sieci repozytorium pakietu.

* W przypadku systemu Windows sprawdź konfigurację agenta wymienioną w temacie [aktualizacje nie są pobierane z punktu końcowego intranetu (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Jeśli maszyny są skonfigurowane do Windows Update, upewnij się, że można uzyskać dostęp do punktów końcowych opisanych w [problemach związanych z protokołem HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Jeśli na komputerach skonfigurowano program Windows Server Update Services (WSUS), upewnij się, że można nawiązać połączenie z serwerem WSUS skonfigurowanym za pomocą [klucza rejestru WUServer](/windows/deployment/update/waas-wu-settings).

Jeśli zobaczysz wynik HRESULT, kliknij dwukrotnie wyjątek wyświetlany na czerwono, aby wyświetlić cały komunikat o wyjątku. Zapoznaj się z poniższą tabelą w celu uzyskania potencjalnych rozwiązań lub zalecanych akcji.

|Wyjątek  |Rozwiązanie lub Akcja  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Przeszukaj odpowiedni kod błędu na [liście kodów błędów w usłudze Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) , aby znaleźć dodatkowe szczegóły dotyczące przyczyny wyjątku.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Wskazują one problemy z łącznością sieciową. Upewnij się, że maszyna ma łączność sieciową z Update Management. Lista wymaganych portów i adresów znajduje się w sekcji [Planowanie sieci](../update-management/update-mgmt-overview.md#ports) .        |
|`0x8024001E`| Operacja aktualizacji nie została ukończona, ponieważ trwa zamykanie usługi lub systemu.|
|`0x8024002E`| Usługa Windows Update jest wyłączona.|
|`0x8024402C`     | W przypadku korzystania z serwera programu WSUS upewnij się, że wartości rejestru dla `WUServer` i `WUStatusServer` w  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` kluczu rejestru określają prawidłowy serwer WSUS.        |
|`0x80072EE2`|Występuje problem z łącznością sieciową lub problem podczas rozmowy ze skonfigurowanym serwerem WSUS. Sprawdź ustawienia usług WSUS i upewnij się, że usługa jest dostępna z poziomu klienta.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, że usługa Windows Update (wuauserv) jest uruchomiona i nie jest wyłączona.        |
|`0x80070005`| Błąd odmowy dostępu może być spowodowany jedną z następujących czynności:<br> Zainfekowany komputer<br> Ustawienia Windows Update niepoprawnie skonfigurowane<br> Błąd uprawnień pliku w folderze%WinDir%\SoftwareDistribution<br> Za mało miejsca na dysku systemowym (C:).
|Każdy inny wyjątek ogólny     | Uruchom wyszukiwanie w Internecie, aby zapoznać się z możliwymi rozwiązaniami, i pracuj z lokalną pomocą techniczną IT.         |

Przeglądanie pliku **%windir%\WindowsUpdate.log** może również pomóc w ustaleniu możliwych przyczyn. Aby uzyskać więcej informacji na temat odczytywania dziennika, zobacz [jak odczytać plik WindowsUpdate. log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Możesz również pobrać i uruchomić narzędzie do [rozwiązywania problemów Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , aby sprawdzić, czy występują problemy z Windows Update na komputerze.

> [!NOTE]
> Dokumentacja [Windows Update narzędzia do rozwiązywania problemów](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) wskazuje, że jest ona używana na klientach z systemem Windows, ale działa również w systemie Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenariusz: przebieg aktualizacji zwraca stan niepowodzenia (Linux)

### <a name="issue"></a>Problem

Przebieg aktualizacji rozpocznie się, ale napotka błędy podczas przebiegu.

### <a name="cause"></a>Przyczyna

Możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji.
* Program Update Agent (WUA for Windows, dystrybucji — konkretny pakiet Manager dla systemu Linux) jest nieprawidłowo skonfigurowany.
* Określone pakiety zakłócają stosowanie poprawek opartych na chmurze.
* Komputer jest nieosiągalny.
* Aktualizacje mają zależności, które nie zostały rozpoznane.

### <a name="resolution"></a>Rozwiązanie

Jeśli wystąpią błędy podczas przebiegu aktualizacji po pomyślnym uruchomieniu, [Sprawdź dane wyjściowe zadania](../update-management/update-mgmt-deploy-updates.md#view-results-of-a-completed-update-deployment) z danego komputera w przebiegu. Możesz znaleźć określone komunikaty o błędach z maszyn, które można zbadać i podjąć odpowiednie działania. Update Management wymaga, aby Menedżer pakietów był w dobrej kondycji w przypadku pomyślnych wdrożeń aktualizacji.

Jeśli określone poprawki, pakiety lub aktualizacje są widoczne bezpośrednio przed zakończeniem zadania, można [wypróbować te elementy z](../update-management/update-mgmt-deploy-updates.md#schedule-an-update-deployment) kolejnego wdrożenia aktualizacji. Aby zebrać informacje dziennika z Windows Update, zobacz [pliki dziennika Windows Update](/windows/deployment/update/windows-update-logs).

Jeśli nie możesz rozwiązać problemu z poprawkami, Utwórz kopię pliku **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** i Zachowaj ją w celu rozwiązywania problemów przed rozpoczęciem następnego wdrożenia aktualizacji.

## <a name="patches-arent-installed"></a>Nie zainstalowano poprawek

### <a name="machines-dont-install-updates"></a>Maszyny nie instalują aktualizacji

Spróbuj uruchomić aktualizacje bezpośrednio na maszynie. Jeśli na komputerze nie można zastosować aktualizacji, zapoznaj się z [listą potencjalnych błędów w przewodniku rozwiązywania problemów](#hresult).

Jeśli aktualizacje są uruchamiane lokalnie, spróbuj usunąć i ponownie zainstalować agenta na komputerze, postępując zgodnie ze wskazówkami w temacie [usuwanie maszyny wirtualnej z Update Management](../update-management/update-mgmt-remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Wiemy, że aktualizacje są dostępne, ale nie są wyświetlane jako dostępne na moich maszynach

Często zdarza się to, gdy maszyny są skonfigurowane do pobierania aktualizacji z usług WSUS lub Microsoft Endpoint Configuration Manager ale WSUS i Configuration Manager nie zaakceptowały aktualizacji.

Możesz sprawdzić, czy maszyny są skonfigurowane pod kątem usług WSUS i SCCM, przez krzyżowe odwołanie `UseWUServer` klucza rejestru do kluczy rejestru w [sekcji Konfigurowanie aktualizacji automatycznych przez edytowanie rejestru w tym artykule](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Jeśli aktualizacje nie są zatwierdzone w programie WSUS, nie są one zainstalowane. Niezatwierdzone aktualizacje można sprawdzić w Log Analytics, uruchamiając następujące zapytanie.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizacje są wyświetlane jako zainstalowane, ale nie mogę ich znaleźć na swojej maszynie

Aktualizacje są często zastępowane przez inne aktualizacje. Aby uzyskać więcej informacji, zobacz [Aktualizacja została zastąpiona](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) w przewodniku rozwiązywania problemów Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalowanie aktualizacji według klasyfikacji w systemie Linux

Wdrażanie aktualizacji w systemie Linux według klasyfikacji („Aktualizacje krytyczne i zabezpieczeń”) ma ważne zastrzeżenia, szczególnie w przypadku systemu CentOS. Te ograniczenia są udokumentowane na [stronie przegląd Update Management](../update-management/update-mgmt-overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 jest ciągle nieobecny

KB2267602 to [aktualizacja definicji usługi Windows Defender](https://www.microsoft.com/wdsi/definitions). Jest ona aktualizowana codziennie.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz swojego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną.

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż połączenie z [@AzureSupport](https://twitter.com/azuresupport) kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
