---
title: Rozwiązywanie Azure Automation Update Management problemów
description: W tym artykule opisano sposób rozwiązywania problemów z Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 04/16/2021
ms.topic: troubleshooting
ms.openlocfilehash: f23632ba6a6b83f92b2bfc90beb4c1a8613c090a
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587367"
---
# <a name="troubleshoot-update-management-issues"></a>Rozwiązywanie problemów z usługą Update Management

W tym artykule omówiono problemy, które mogą wystąpić podczas wdrażania Update Management na maszynach. Istnieje narzędzie do rozwiązywania problemów z agentem hybrydowego procesu roboczego runbook, które pozwala określić podstawowy problem. Aby dowiedzieć się więcej na temat narzędzia do rozwiązywania problemów, zobacz Rozwiązywanie problemów z agentem [usługi Windows Update](update-agent-issues.md) i Rozwiązywanie problemów z [agentem aktualizacji systemu Linux.](update-agent-issues-linux.md) Aby uzyskać informacje o innych problemach z wdrażaniem funkcji, zobacz [Rozwiązywanie problemów z wdrażaniem funkcji.](onboarding.md)

>[!NOTE]
>Jeśli podczas wdrażania programu Update Management na maszynie z systemem Windows występują problemy, otwórz program Windows Podgląd zdarzeń i  sprawdź dziennik zdarzeń programu **Operations Manager** w obszarze Dzienniki aplikacji i usług na komputerze lokalnym. Poszukaj zdarzeń o identyfikatorze 4502 i szczegółów zdarzenia, które zawierają `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Scenariusz: aktualizacje systemu Linux wyświetlane jako oczekujące, a zainstalowane aktualizacje różnią się

### <a name="issue"></a>Problem

W przypadku maszyny z systemem Linux Update Management określone aktualizacje dostępne w obszarze **klasyfikacji Zabezpieczenia** i **inne.** Jednak w przypadku uruchomienia harmonogramu aktualizacji na maszynie, na  przykład w celu zainstalowania tylko aktualizacji pasujących do klasyfikacji Zabezpieczeń, zainstalowane aktualizacje różnią się od lub podzestawem aktualizacji pokazanych wcześniej, pasujących do tej klasyfikacji.

### <a name="cause"></a>Przyczyna

Po ocenie oczekujących aktualizacji systemu operacyjnego dla maszyny z systemem Linux pliki open [vulnerability and assessment language](https://oval.mitre.org/) (OVAL) dostarczone przez dostawcę dystrybucji systemu Linux są używane przez usługę Update Management do klasyfikacji. Kategoryzacja jest wykonywana  w przypadku aktualizacji systemu Linux jako zabezpieczeń lub innych na podstawie plików OVAL, które stwierdzają, że aktualizacje są związane z problemami z zabezpieczeniami lub lukami w zabezpieczeniach. Jednak po uruchomieniu harmonogramu aktualizacji jest on wykonywany na maszynie z systemem Linux przy użyciu odpowiedniego menedżera pakietów, takiego jak YUM, APT lub ZYPPER, aby je zainstalować. Menedżer pakietów dla dystrybucji systemu Linux może mieć inny mechanizm klasyfikowania aktualizacji, gdzie wyniki mogą różnić się od wyników uzyskanych z plików OVAL przez Update Management.

### <a name="resolution"></a>Rozwiązanie

Możesz ręcznie sprawdzić maszynę z systemem Linux, odpowiednie aktualizacje i ich klasyfikację zgodnie z menedżerem pakietów dystrybucji. Aby zrozumieć, które aktualizacje są klasyfikowane jako **zabezpieczenia** przez menedżera pakietów, uruchom następujące polecenia.

W przypadku aplikacji YUM następujące polecenie zwraca niezerową listę aktualizacji **skategoryzowanych** jako Zabezpieczenia według systemu Red Hat. Należy pamiętać, że w przypadku systemu CentOS zawsze zwraca pustą listę i nie występuje klasyfikacja zabezpieczeń.

```bash
sudo yum -q --security check-update
```

W przypadku programu ZYPPER następujące polecenie zwraca niezerową listę aktualizacji **skategoryzowanych** jako Zabezpieczenia według systemu SUSE.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

W przypadku apt następujące polecenie zwraca niezerową listę aktualizacji sklasyfikowanych jako Security **by** Canonical Ubuntu Linux dystrybucji.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

Z tej listy uruchom polecenie , `grep ^Inst` aby pobrać wszystkie oczekujące aktualizacje zabezpieczeń.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Scenariusz: Zostanie wyświetlony błąd "Nie można włączyć rozwiązania aktualizacji"

### <a name="issue"></a>Problem

Podczas próby włączenia Update Management na koncie usługi Automation występuje następujący błąd:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Wymagania dotyczące zapory sieciowej dla agenta usługi Log Analytics mogą nie być poprawnie skonfigurowane. Taka sytuacja może spowodować niepowodzenie agenta podczas rozpoznawania adresów URL DNS.

* Update Management określania wartości docelowej jest błędnie skonfigurowana, a maszyna nie otrzymuje aktualizacji zgodnie z oczekiwaniami.

* Można również zauważyć, że stan maszyny jest w obszarze `Non-compliant` **Zgodność.** W tym samym czasie agent **Desktop Analytics** raportuje agenta jako `Disconnected` .

### <a name="resolution"></a>Rozwiązanie

* Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) [lub Linux](update-agent-issues-linux.md#troubleshoot-offline)w zależności od systemu operacyjnego.

* Przejdź do [opcji Konfiguracja sieci,](../automation-hybrid-runbook-worker.md#network-planning) aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Update Management działać.  

* Sprawdź, czy występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../update-management/scope-configuration.md) określa, które maszyny są skonfigurowane dla Update Management. Jeśli maszyna jest wyświetlona w obszarze roboczym, ale nie w Update Management, musisz ustawić konfigurację zakresu na maszyny docelowe. Aby dowiedzieć się więcej o konfiguracji zakresu, zobacz [Włączanie maszyn w obszarze roboczym](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

* Usuń konfigurację procesu roboczego, korzystając z procedury Usuwania hybrydowego procesu roboczego [runbook](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) z lokalnego komputera z systemem Windows lub Usuń hybrydowy proces roboczy runbook z lokalnego komputera z [systemem Linux.](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenariusz: supersadowana aktualizacja wskazana jako brakująca w Update Management

### <a name="issue"></a>Problem

Stare aktualizacje są wyświetlane dla konta usługi Automation jako brakujące, mimo że zostały one nadsyłane. Aktualizacja, która została nadsyłana, jest taka, że nie trzeba jej instalować, ponieważ dostępna jest późniejsza aktualizacja, która poprawia tę samą lukę w zabezpieczeniach. Update Management ignoruje aktualizację, która została nadsyłana, i sprawia, że nie ma ona zastosowania na rzecz aktualizacji, która została nadsyłana. Aby uzyskać informacje o powiązanym problemie, [zobacz Update is supersed .](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="cause"></a>Przyczyna

Aktualizacje, które zostały nadsyłane, nie są odrzucane w programie Windows Server Update Services (WSUS), aby można je było uznać za nie dotyczy.

### <a name="resolution"></a>Rozwiązanie

Gdy aktualizacja, która została już w 100 procentach, nie ma zastosowania, należy zmienić stan zatwierdzenia tej aktualizacji na w `Declined` programie WSUS. Aby zmienić stan zatwierdzenia dla wszystkich aktualizacji:

1. Na koncie usługi Automation wybierz pozycję **Update Management,** aby wyświetlić stan maszyny. Zobacz [Wyświetlanie ocen aktualizacji.](../update-management/view-update-assessments.md)

2. Sprawdź aktualizację, która została nadsyłana, aby upewnić się, że nie ma ona zastosowania w 100 procentach.

3. Na serwerze WSUS, do których są raporty maszyn, [odrzuć aktualizację](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates).

4. Wybierz **pozycję Komputery** i w kolumnie **Zgodność** wymusz ponowne skanowanie pod celu zapewnienia zgodności. Zobacz [Manage updates for VMs (Zarządzanie aktualizacjami maszyn wirtualnych).](../update-management/manage-updates-for-vm.md)

5. Powtórz powyższe kroki dla innych aktualizacji, które są już nadsyłane.

6. W Windows Server Update Services (WSUS) wyczyść wszystkie aktualizacje, które są już wsadowe, aby odświeżyć infrastrukturę przy użyciu Kreatora oczyszczania [serwera](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)WSUS.

7. Powtarzaj tę procedurę regularnie, aby rozwiązać problem z wyświetlaniem i zminimalizować ilość miejsca na dysku używanego do zarządzania aktualizacjami.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenariusz: Maszyny nie są wyświetlane w portalu w obszarze Update Management

### <a name="issue"></a>Problem

Maszyny mają następujące objawy:

* Maszyna jest `Not configured` pokazywna Update Management widoku maszyny wirtualnej.

* Brak maszyn w widoku Update Management konta Azure Automation.

* Masz maszyny, które są wyświetlane jako `Not assessed` w obszarze **Zgodność.** Jednak dane pulsu są w dziennikach Azure Monitor dla hybrydowego procesu roboczego runbook, ale nie dla Update Management.

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany problemami z konfiguracją lokalną lub nieprawidłowo skonfigurowaną konfiguracją zakresu. Możliwe konkretne przyczyny:

* Może być konieczne ponowne zarejestrowanie i ponowne zainstalowanie hybrydowego procesu roboczego runbook.

* Być może zdefiniowano limit przydziału w obszarze roboczym, który został osiągnięty, co uniemożliwia dalsze przechowywanie danych.

### <a name="resolution"></a>Rozwiązanie

1. Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) [lub Linux](update-agent-issues-linux.md#troubleshoot-offline)w zależności od systemu operacyjnego.

2. Upewnij się, że maszyna raportuje do poprawnego obszaru roboczego. Aby uzyskać wskazówki dotyczące weryfikowania tego aspektu, zobacz [Verify agent connectivity to Azure Monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)(Weryfikowanie łączności agenta z Azure Monitor ). Upewnij się również, że ten obszar roboczy jest połączony Azure Automation kontem. Aby potwierdzić, przejdź do konta usługi Automation i wybierz pozycję **Połączony obszar roboczy w** obszarze Powiązane **zasoby.**

3. Upewnij się, że maszyny są wyświetlane w obszarze roboczym usługi Log Analytics połączonym z Twoim kontem usługi Automation. Uruchom następujące zapytanie w obszarze roboczym usługi Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Jeśli nie widzisz maszyny w wynikach zapytania, nie została ona ostatnio zaewidencjonowana. Prawdopodobnie występuje problem z konfiguracją lokalną i należy [ponownie zainstalować agenta](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows).

    Jeśli maszyna znajduje się na liście wyników zapytania, sprawdź, czy w obszarze **właściwości Rozwiązania** jest **na liście** aktualizacji. W ten sposób można sprawdzić, czy jest on zarejestrowany w Update Management. Jeśli tak nie jest, sprawdź, czy występują problemy z konfiguracją zakresu. Konfiguracja [zakresu określa,](../update-management/scope-configuration.md) które maszyny są skonfigurowane dla Update Management. Aby skonfigurować konfigurację zakresu dla maszyny docelowej, zobacz [Włączanie maszyn w obszarze roboczym](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

4. W obszarze roboczym uruchom to zapytanie.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Jeśli otrzymasz wynik, osiągnięto limit przydziału zdefiniowany w obszarze roboczym, co zatrzymało `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` zapisanie danych. W obszarze roboczym przejdź do obszaru **Zarządzanie woluminami** danych w obszarze Użycie i **szacowane koszty** i zmień lub usuń limit przydziału.

5. Jeśli problem nadal nie został rozwiązany, wykonaj kroki opisane w te tematu Deploy a Windows Hybrid Runbook Worker (Wdrażanie hybrydowego procesu [roboczego runbook](../automation-windows-hrw-install.md) systemu Windows), aby ponownie zainstalować hybrydowy proces roboczy dla systemu Windows. W przypadku systemu Linux wykonaj kroki opisane w te tematu Deploy a Linux Hybrid Runbook Worker (Wdrażanie hybrydowego procesu [roboczego runbook systemu Linux).](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenariusz: Nie można zarejestrować dostawcy zasobów usługi Automation dla subskrypcji

### <a name="issue"></a>Problem

Podczas pracy z wdrożeniami funkcji na koncie usługi Automation występuje następujący błąd:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów usługi Automation nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Aby zarejestrować dostawcę zasobów usługi Automation, wykonaj następujące kroki w Azure Portal.

1. Na liście usług platformy Azure w dolnej części portalu wybierz pozycję **Wszystkie** usługi, a następnie wybierz pozycję **Subskrypcje** w grupie Usługi ogólne.

2. Wybierz subskrypcję.

3. W **obszarze Ustawienia** wybierz pozycję Dostawcy **zasobów.**

4. Z listy dostawców zasobów sprawdź, czy dostawca zasobów Microsoft.Automation jest zarejestrowany.

5. Jeśli nie ma go na liście, zarejestruj dostawcę Microsoft.Automation, korzystając z procedury opisanej w tece Rozwiązywanie błędów związanych z [rejestracją dostawcy zasobów.](../../azure-resource-manager/templates/error-register-resource-provider.md)

## <a name="scenario-scheduled-update-did-not-patch-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenariusz: Zaplanowana aktualizacja nie poprawia niektórych maszyn

### <a name="issue"></a>Problem

Maszyny uwzględnione w wersji zapoznawczej aktualizacji nie wszystkie są wyświetlane na liście maszyn poprawionych podczas zaplanowanego uruchomienia lub maszyny wirtualne dla wybranych zakresów grupy dynamicznej nie są wyświetlane na liście podglądu aktualizacji w portalu.

Lista podglądu aktualizacji składa się ze wszystkich maszyn pobranych przez Azure Resource Graph [zapytania](../../governance/resource-graph/overview.md) dla wybranych zakresów. Zakresy są filtrowane w przypadku maszyn z zainstalowanym hybrydowym procesem roboczy runbook systemu, do których masz uprawnienia dostępu.

### <a name="cause"></a>Przyczyna

Ten problem może mieć jedną z następujących przyczyn:

* Subskrypcje zdefiniowane w zakresie w zapytaniu dynamicznym nie są konfigurowane dla zarejestrowanego dostawcy zasobów usługi Automation.

* Maszyny były niedostępne lub nie miały odpowiednich tagów podczas wykonywania harmonogramu.

* Nie masz poprawnego dostępu do wybranych zakresów.

* Zapytanie Azure Resource Graph nie pobiera oczekiwanych maszyn.

* Systemowy hybrydowy proces roboczy runbook nie jest zainstalowany na maszynach.

### <a name="resolution"></a>Rozwiązanie

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Subskrypcje nieskonfigurowane dla zarejestrowanego dostawcy zasobów usługi Automation

Jeśli twoja subskrypcja nie jest skonfigurowana dla dostawcy zasobów usługi Automation, nie możesz odpytować ani pobrać informacji na maszynach w tej subskrypcji. Aby zweryfikować rejestrację subskrypcji, należy wykonać następujące czynności.

1. W [witrynie Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)uzyskać dostęp do listy usług platformy Azure.

2. Wybierz **pozycję Wszystkie usługi,** a następnie wybierz **pozycję Subskrypcje** w grupie usługi Ogólne.

3. Znajdź subskrypcję zdefiniowaną w zakresie wdrożenia.

4. W **obszarze Ustawienia** wybierz pozycję Dostawcy **zasobów.**

5. Sprawdź, czy dostawca zasobów Microsoft.Automation jest zarejestrowany.

6. Jeśli nie ma go na liście, zarejestruj dostawcę Microsoft.Automation, korzystając z procedury opisanej w tece Rozwiązywanie błędów związanych z [rejestracją dostawcy zasobów.](../../azure-resource-manager/templates/error-register-resource-provider.md)

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Maszyny są niedostępne lub nie są poprawnie oznaczane podczas wykonywania harmonogramu

Użyj poniższej procedury, jeśli subskrypcja została skonfigurowana dla dostawcy zasobów usługi Automation, ale uruchomienie harmonogramu aktualizacji z określonymi grupami dynamicznymi [pominięto](../update-management/configure-groups.md) niektóre maszyny.

1. W Azure Portal otwórz konto usługi Automation i wybierz pozycję **Update Management**.

2. Sprawdź [Update Management,](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) aby określić dokładny czas uruchomienia wdrożenia aktualizacji.

3. W przypadku maszyn, które podejrzewasz, że zostały pominięte przez Update Management, użyj Azure Resource Graph (ARG), aby zlokalizować [zmiany maszyny.](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)

4. Wyszukaj zmiany w ciągu znacznego okresu, takiego jak jeden dzień, przed uruchomieniem wdrożenia aktualizacji.

5. Sprawdź w wynikach wyszukiwania wszelkie zmiany systemowe, takie jak usuwanie lub aktualizowanie zmian, na maszynach w tym okresie. Te zmiany mogą zmieniać stan maszyny lub tagi, dzięki czemu maszyny nie są wybierane na liście maszyn podczas wdrażania aktualizacji.

6. Dostosuj ustawienia maszyn i zasobów w razie potrzeby, aby rozwiązać problemy ze stanem maszyny lub tagiem.

7. Ponownie należy uruchomić harmonogram aktualizacji, aby upewnić się, że wdrożenie z określonymi grupami dynamicznymi obejmuje wszystkie maszyny.

#### <a name="incorrect-access-on-selected-scopes"></a>Nieprawidłowy dostęp do wybranych zakresów

W Azure Portal są wyświetlane tylko maszyny, dla których masz dostęp do zapisu w danym zakresie. Jeśli nie masz poprawnego dostępu do zakresu, zobacz [Samouczek:](../../role-based-access-control/quickstart-assign-role-user-portal.md)udzielanie użytkownikowi dostępu do zasobów platformy Azure przy użyciu Azure Portal .

#### <a name="resource-graph-query-doesnt-return-expected-machines"></a>Resource Graph nie zwraca oczekiwanych maszyn

Wykonaj poniższe kroki, aby dowiedzieć się, czy zapytania działają prawidłowo.

1. Uruchom zapytanie Azure Resource Graph sformatowane w sposób pokazany poniżej w bloku eksploratora Resource Graph w Azure Portal. Jeśli jesteś nowym użytkownikiem Azure Resource Graph, zobacz ten przewodnik Szybki [start,](../../governance/resource-graph/first-query-portal.md) aby dowiedzieć się, jak pracować z Resource Graph Explorer. To zapytanie naśladuje filtry wybrane podczas tworzenia grupy dynamicznej w Update Management. Zobacz [Używanie grup dynamicznych z Update Management](../update-management/configure-groups.md).

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

3. Jeśli maszyn nie ma na liście, prawdopodobnie występuje problem z filtrem wybranym w grupie dynamicznej. Dostosuj konfigurację grupy zgodnie z potrzebami.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrydowy proces roboczy runbook nie jest zainstalowany na maszynach

Maszyny są wyświetlane w Azure Resource Graph zapytania, ale nadal nie są wyświetlane w podglądzie grupy dynamicznej. W takim przypadku maszyny mogą nie być wyznaczone jako systemowe hybrydowe środowiska Runbook workers i w związku z tym nie mogą uruchamiać zadań Azure Automation i Update Management runbook. Aby upewnić się, że maszyny, których oczekujesz, są ustawione jako hybrydowe pracownicy runbook systemu:

1. W Azure Portal przejdź do konta usługi Automation dla maszyny, która nie jest wyświetlana poprawnie.

2. Wybierz **pozycję Grupy hybrydowych procesów roboczych** w obszarze **Automatyzacja procesów.**

3. Wybierz **kartę Grupy hybrydowych procesów roboczych** systemu.

4. Sprawdź, czy hybrydowy proces roboczy jest obecny dla tej maszyny.

5. Jeśli maszyna nie jest ustawiona jako systemowy hybrydowy proces roboczy runbook, zapoznaj się z metodami włączania maszyny w sekcji Włączanie Update Management artykułu Update Management Runbook. [](../update-management/overview.md#enable-update-management) Metoda włączania jest oparta na środowisku, w którym jest uruchomiona maszyna.

6. Powtórz powyższe kroki dla wszystkich maszyn, które nie były wyświetlane w wersji zapoznawczej.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenariusz: Update Management włączone, podczas gdy maszyna wirtualna jest nadal pokazywana jako skonfigurowana

### <a name="issue"></a>Problem

Po 15 minutach od rozpoczęciem wdrażania na maszynie wirtualnej będzie nadal wyświetlany następujący komunikat:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Komunikacja z kontem usługi Automation jest blokowana.

* Istnieje zduplikowana nazwa komputera z różnymi identyfikatorami komputerów źródłowych. Ten scenariusz występuje, gdy maszyna wirtualna o określonej nazwie komputera jest tworzona w różnych grupach zasobów i raportuje do tego samego obszaru roboczego agenta logistyki w subskrypcji.

* Wdrażany obraz maszyny wirtualnej może pochodzić ze sklonowanej maszyny, która nie została przygotowana przy użyciu narzędzia Przygotowywania systemu (sysprep) z zainstalowanym agentem usługi Log Analytics dla systemu Windows.

### <a name="resolution"></a>Rozwiązanie

Aby ułatwić określenie dokładnego problemu z maszyną wirtualną, uruchom następujące zapytanie w obszarze roboczym usługi Log Analytics połączonym z kontem usługi Automation.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Komunikacja z kontem usługi Automation zablokowana

Przejdź do [tematu Planowanie sieci,](../update-management/overview.md#ports) aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Update Management działać.

#### <a name="duplicate-computer-name"></a>Zduplikowana nazwa komputera

Zmień nazwy maszyn wirtualnych, aby zapewnić unikatowe nazwy w ich środowisku.

#### <a name="deployed-image-from-cloned-machine"></a>Wdrożony obraz ze sklonowanej maszyny

Jeśli używasz sklonowanego obrazu, różne nazwy komputerów mają ten sam identyfikator komputera źródłowego. W takim przypadku:

1. W obszarze roboczym usługi Log Analytics usuń maszynę wirtualną z zapisanego wyszukiwania dla `MicrosoftDefaultScopeConfig-Updates` konfiguracji zakresu, jeśli jest wyświetlana. Zapisane wyszukiwania można znaleźć w obszarze **Ogólne** w obszarze roboczym.

2. Uruchom następujące polecenie cmdlet.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Uruchom `Restart-Service HealthService` program , aby ponownie uruchomić usługę kondycji. Ta operacja ponownie tworzy klucz i generuje nowy UUID.

4. Jeśli to podejście nie działa, najpierw uruchom narzędzie sysprep na obrazie, a następnie zainstaluj agenta usługi Log Analytics dla systemu Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenariusz: Podczas tworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure jest wyświetlany błąd połączonej subskrypcji

### <a name="issue"></a>Problem

Podczas próby utworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure występuje następujący błąd:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas tworzenia wdrożenia aktualizacji, które zawiera maszyny wirtualne platformy Azure w innej dzierżawie uwzględnionej we wdrożeniu aktualizacji.

### <a name="resolution"></a>Rozwiązanie

Aby zaplanować te elementy, skorzystaj z poniższego obejścia. Aby utworzyć harmonogram, można użyć polecenia cmdlet [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) z `ForUpdateConfiguration` parametrem . Następnie użyj polecenia cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) i przekaż maszyny w innej dzierżawie do `NonAzureComputer` parametru . W poniższym przykładzie pokazano, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenariusz: Niewyjaśnione ponowne uruchomienia

### <a name="issue"></a>Problem

Mimo że opcja Ponowne  uruchamianie kontrolki jest ustawiona na Nigdy nie uruchamiaj **ponownie,** maszyny są nadal ponownie uruchamiane po zainstalowaniu aktualizacji.

### <a name="cause"></a>Przyczyna

Windows Update można modyfikować za pomocą kilku kluczy rejestru, z których każdy może modyfikować zachowanie ponownego uruchamiania.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj klucze rejestru wymienione w obszarze [Konfigurowanie](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) aktualizacji automatycznych, edytując klucze rejestru i rejestru używane do zarządzania ponownym uruchamianiem, aby upewnić się, że maszyny są prawidłowo skonfigurowane. [](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenariusz: na maszynie jest przedstawiany błąd "Nie można uruchomić" we wdrożeniu aktualizacji

### <a name="issue"></a>Problem

Maszyna wyświetla `Failed to start` stan. Podczas wyświetlania szczegółowych informacji o maszynie jest wyświetlany następujący błąd:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Przyczyna

Ten błąd może mieć jedną z następujących przyczyn:

* Maszyna już nie istnieje.
* Maszyna jest wyłączona i nieosiągalna.
* Maszyna ma problem z łącznością sieciową, dlatego hybrydowy proces roboczy na maszynie jest nieosiągalny.
* Aktualizacja agenta usługi Log Analytics zmieniła identyfikator komputera źródłowego.
* Przebieg aktualizacji został ograniczany po przekroczeniu limitu 200 współbieżnych zadań na koncie usługi Automation. Każde wdrożenie jest uznawane za zadanie, a każda maszyna we wdrożeniu aktualizacji liczy się jako zadanie. Każde inne zadanie automatyzacji lub wdrożenie aktualizacji aktualnie uruchomione na koncie usługi Automation wlicza się do limitu współbieżnych zadań.

### <a name="resolution"></a>Rozwiązanie

Jeśli ma to zastosowanie, [użyj grup dynamicznych](../update-management/configure-groups.md) dla wdrożeń aktualizacji. Ponadto możesz wykonać następujące czynności.

1. Sprawdź, czy maszyna lub serwer spełnia [wymagania](../update-management/overview.md#system-requirements).
2. Sprawdź łączność z hybrydowym procesem roboczym runbook przy użyciu narzędzia do rozwiązywania problemów z agentem hybrydowego procesu roboczego runbook. Aby dowiedzieć się więcej na temat narzędzia do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z agentem aktualizacji.](update-agent-issues.md)

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenariusz: aktualizacje są instalowane bez wdrożenia

### <a name="issue"></a>Problem

Po zarejestrowaniu maszyny z systemem Windows w Update Management są instalowane aktualizacje bez wdrożenia.

### <a name="cause"></a>Przyczyna

W systemie Windows aktualizacje są instalowane automatycznie, gdy tylko będą dostępne. Takie zachowanie może powodować dezorientację, jeśli nie zaplanowano wdrożenia aktualizacji na maszynie.

### <a name="resolution"></a>Rozwiązanie

Wartość  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` domyślna klucza rejestru to 4: `auto download and install` .

W Update Management klientów zalecamy ustawienie tego klucza na 3: `auto download but do not auto install` .

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aktualizacji automatycznych.](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenariusz: Maszyna jest już zarejestrowana na innym koncie

### <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Przyczyna

Maszyna została już wdrożona w innym obszarze roboczym dla Update Management.

### <a name="resolution"></a>Rozwiązanie

1. Wykonaj kroki opisane w obszarze Maszyny nie są wyświetlane w portalu w obszarze [Update Management,](#nologs) aby upewnić się, że maszyna raportuje do poprawnego obszaru roboczego.
2. Wyczyść artefakty na maszynie, usuwając [grupę hybrydowych elementów Runbook,](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)a następnie spróbuj ponownie.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenariusz: Maszyna nie może komunikować się z usługą

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

Serwer proxy, brama lub zapora mogą blokować komunikację sieciową.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj sieć i upewnij się, że są dozwolone odpowiednie porty i adresy. Zobacz [wymagania dotyczące sieci,](../automation-hybrid-runbook-worker.md#network-planning) aby uzyskać listę portów i adresów, które są wymagane przez Update Management i hybrydowe środowiska runbook.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenariusz: Nie można utworzyć certyfikatu z podpisem własnym

### <a name="issue"></a>Problem

Zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy runbook nie mógł wygenerować certyfikatu z podpisem własnym.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy konto systemowe ma dostęp do odczytu do **folderu C:\ProgramData\Microsoft\Crypto\RSA,** a następnie spróbuj ponownie.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenariusz: Zaplanowana aktualizacja nie powiodła się z błędem MaintenanceWindowExceeded

### <a name="issue"></a>Problem

Domyślne okno obsługi aktualizacji to 120 minut. Okno obsługi można zwiększyć do maksymalnie 6 godzin lub 360 minut.

### <a name="resolution"></a>Rozwiązanie

Aby zrozumieć, dlaczego wystąpiło to podczas [](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) uruchamiania aktualizacji po pomyślnym uruchomieniu, sprawdź dane wyjściowe zadania z maszyny, której dotyczy problem, w uruchomieniu. Możesz znaleźć określone komunikaty o błędach z maszyn, które możesz zbadać i podjąć działania.  

Edytuj wszelkie nieudane zaplanowane wdrożenia aktualizacji i zwiększ okno obsługi.

Aby uzyskać więcej informacji na temat okien obsługi, zobacz [Instalowanie aktualizacji](../update-management/deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenariusz: maszyna jest pokazywana jako "Nie oceniono" i pokazuje wyjątek HRESULT

### <a name="issue"></a>Problem

* Masz maszyny, które są wyświetlane jako w obszarze Zgodność i poniżej nich jest `Not assessed` wyświetlany komunikat o wyjątku. 
* W portalu zostanie wyświetlony kod błędu HRESULT.

### <a name="cause"></a>Przyczyna

Agent aktualizacji (Windows Update w systemie Windows; menedżer pakietów dla dystrybucji systemu Linux) nie jest poprawnie skonfigurowany. Update Management od agenta aktualizacji maszyny w celu zapewnienia wymaganych aktualizacji, stanu poprawki i wyników wdrożonych poprawek. Bez tych informacji Update Management prawidłowo zgłosić poprawek, które są potrzebne lub zainstalowane.

### <a name="resolution"></a>Rozwiązanie

Spróbuj wykonać aktualizacje lokalnie na maszynie. Jeśli ta operacja zakończy się niepowodzeniem, zazwyczaj oznacza to, że wystąpił błąd konfiguracji agenta aktualizacji.

Ten problem jest często spowodowany przez problemy z konfiguracją sieci i zaporą. Aby rozwiązać ten problem, użyj następujących testów.

* W przypadku systemu Linux sprawdź odpowiednią dokumentację, aby upewnić się, że możesz uzyskać dostęp do punktu końcowego sieci repozytorium pakietów.

* W przypadku systemu Windows sprawdź konfigurację agenta wymienioną w tece Aktualizacje nie są pobierane z intranetowego punktu końcowego [(WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)

  * Jeśli maszyny są skonfigurowane pod Windows Update, upewnij się, że możesz uzyskać dostęp do punktów końcowych opisanych w te tematu Issues related to HTTP/proxy (Problemy [związane z serwerem HTTP/serwerem proxy).](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)
  * Jeśli maszyny są skonfigurowane do Windows Server Update Services (WSUS), upewnij się, że można uzyskać dostęp do serwera WSUS skonfigurowanego przez klucz rejestru [WUServer.](/windows/deployment/update/waas-wu-settings)

Jeśli zostanie wyświetlony komunikat HRESULT, kliknij dwukrotnie wyjątek wyświetlany na czerwono, aby wyświetlić cały komunikat o wyjątku. Przejrzyj następującą tabelę, aby zapoznać się z potencjalnymi rozwiązaniami lub zalecanymi akcjami.

|Wyjątek  |Rozwiązanie lub akcja  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Wyszukaj odpowiedni kod błędu na liście [kodów błędów](https://support.microsoft.com/help/938205/windows-update-error-code-list) aktualizacji systemu Windows, aby znaleźć dodatkowe szczegóły dotyczące przyczyny wyjątku.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Wskazują one problemy z łącznością sieciową. Upewnij się, że maszyna ma łączność sieciową z Update Management. Listę [wymaganych](../update-management/overview.md#ports) portów i adresów można znaleźć w sekcji dotyczącej planowania sieci.        |
|`0x8024001E`| Operacja aktualizacji nie została ukończona, ponieważ usługa lub system został zamknięty.|
|`0x8024002E`| Windows Update usługa jest wyłączona.|
|`0x8024402C`     | Jeśli używasz serwera WSUS, upewnij się, że wartości rejestru dla i w kluczu rejestru określają `WUServer` `WUStatusServer` prawidłowy serwer  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` WSUS.        |
|`0x80072EE2`|Występuje problem z łącznością sieciową lub problem podczas komunikacji ze skonfigurowanym serwerem WSUS. Sprawdź ustawienia programu WSUS i upewnij się, że usługa jest dostępna z klienta.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, Windows Update usługa (wuauserv) jest uruchomiona i nie jest wyłączona.        |
|`0x80070005`| Błąd odmowy dostępu może być spowodowany przez jedną z następujących czynności:<br> Zainfekowany komputer<br> Windows Update nie skonfigurowano poprawnie<br> Błąd uprawnień pliku w folderze %WinDir%\SoftwareDistribution<br> Niewystarczająca ilość miejsca na dysku systemowym (C:).
|Dowolny inny wyjątek ogólny     | Uruchom wyszukiwanie w Internecie, aby uzyskać możliwe rozwiązania, i pracuj z lokalną pomocą techniczną IT.         |

Przejrzenie **pliku %Windir%\Windowsupdate.log** może również pomóc w ustaleniu możliwych przyczyn. Aby uzyskać więcej informacji na temat odczytywania dziennika, zobacz [How to read the Windowsupdate.log file (Jak odczytać plik Windowsupdate.log).](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)

Możesz również pobrać i uruchomić narzędzie do rozwiązywania [Windows Update,](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) aby sprawdzić, czy Windows Update problemy z Windows Update na maszynie.

> [!NOTE]
> Dokumentacja [Windows Update narzędzia do](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) rozwiązywania problemów wskazuje, że jest ona do użycia na klientach z systemem Windows, ale działa również w systemie Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenariusz: przebieg aktualizacji zwraca stan Niepowodzenie (Linux)

### <a name="issue"></a>Problem

Przebieg aktualizacji jest uruchamiany, ale podczas uruchamiania występują błędy.

### <a name="cause"></a>Przyczyna

Możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji.
* Agent aktualizacji (WUA dla systemu Windows, menedżer pakietów specyficzny dla dystrybucji dla systemu Linux) jest błędnie skonfigurowany.
* Określone pakiety zakłócają stosowanie poprawek w chmurze.
* Maszyna jest nieosiągalna.
* Aktualizacje miały zależności, które nie zostały rozwiązane.

### <a name="resolution"></a>Rozwiązanie

Jeśli podczas uruchamiania aktualizacji wystąpią błędy [po](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) pomyślnym uruchomieniu, sprawdź dane wyjściowe zadania z maszyny, której dotyczy problem, w uruchomieniu. Możesz znaleźć określone komunikaty o błędach z maszyn, które możesz zbadać i podjąć działania. Update Management menedżer pakietów musi być w dobrej kondycji w przypadku pomyślnych wdrożeń aktualizacji.

Jeśli określone poprawki, pakiety lub aktualizacje są widoczne bezpośrednio przed [](../update-management/deploy-updates.md#schedule-an-update-deployment) niepowodzeniem zadania, możesz spróbować wykluczyć te elementy z następnego wdrożenia aktualizacji. Aby zebrać informacje dziennika z Windows Update, zobacz [Windows Update plików dziennika](/windows/deployment/update/windows-update-logs).

Jeśli nie można rozwiązać problemu z poprawkami, należy utworzyć kopię pliku **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** i zachować go do celów rozwiązywania problemów przed rozpoczęciem następnego wdrożenia aktualizacji.

## <a name="patches-arent-installed"></a>Poprawki nie są zainstalowane

### <a name="machines-dont-install-updates"></a>Maszyny nie instalują aktualizacji

Spróbuj uruchomić aktualizacje bezpośrednio na maszynie. Jeśli maszyna nie może zastosować aktualizacji, zapoznaj się z listą potencjalnych błędów [w przewodniku rozwiązywania problemów](#hresult).

Jeśli aktualizacje są uruchamiane lokalnie, spróbuj usunąć i ponownie zainstalować agenta na maszynie, korzystając ze wskazówek w temacie Usuwanie maszyny wirtualnej z [Update Management](../update-management/remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Wiem, że aktualizacje są dostępne, ale nie są wyświetlane jako dostępne na moich maszynach

Dzieje się tak często, jeśli maszyny są skonfigurowane do uzyskania aktualizacji z usług WSUS lub Microsoft Endpoint Configuration Manager ale usługi WSUS i Menedżer konfiguracji nie zatwierdziły aktualizacji.

Aby sprawdzić, czy maszyny są skonfigurowane dla usług WSUS i PROGRAMU SCCM, odwołuj się krzyżowo do kluczy rejestru w sekcji Konfigurowanie aktualizacji automatycznych przez edycję rejestru w `UseWUServer` [tym artykule.](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

Jeśli aktualizacje nie są zatwierdzone w programie WSUS, nie są instalowane. Aby sprawdzić niezatwierdzone aktualizacje usługi Log Analytics, należy użyć następującego zapytania.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizacje są wyświetlane jako zainstalowane, ale nie mogę ich znaleźć na swojej maszynie

Aktualizacje są często zastępowane przez inne aktualizacje. Aby uzyskać więcej informacji, zobacz [Update is superseded (Aktualizacja](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) została Windows Update przewodniku rozwiązywania problemów).

### <a name="installing-updates-by-classification-on-linux"></a>Instalowanie aktualizacji według klasyfikacji w systemie Linux

Wdrażanie aktualizacji w systemie Linux według klasyfikacji („Aktualizacje krytyczne i zabezpieczeń”) ma ważne zastrzeżenia, szczególnie w przypadku systemu CentOS. Te ograniczenia są udokumentowane na stronie [Update Management omówienie.](../update-management/overview.md#linux)

### <a name="kb2267602-is-consistently-missing"></a>Stale brakuje aktualizacji KB2267602

KB2267602 to [aktualizacja definicji usługi Windows Defender](https://www.microsoft.com/wdsi/definitions). Jest aktualizowany codziennie.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz swojego problemu lub nie możesz go rozwiązać, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną.

* Uzyskaj odpowiedzi od ekspertów z platformy Azure na [forach platformy Azure.](https://azure.microsoft.com/support/forums/)
* Połącz się z usługą , [@AzureSupport](https://twitter.com/azuresupport) oficjalnym kontem Microsoft Azure w celu poprawy jakości obsługi klienta.
* Zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny [pomocy technicznej platformy Azure i](https://azure.microsoft.com/support/options/) wybierz pozycję Uzyskaj pomoc **techniczną.**
