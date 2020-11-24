---
title: Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows w Azure Automation
description: W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego elementu Runbook, którego można użyć do uruchamiania elementów Runbook na maszynach z systemem Windows w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: cb501b954897beb73ae05bfdc7b5ded2221dc114
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95493931"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na platformie Azure lub na maszynach spoza platformy Azure, w tym na serwerach zarejestrowanych przy użyciu [usługi Azure ARC z obsługą serwerów](../azure-arc/servers/overview.md). Na komputerze lub serwerze, na którym znajduje się rola, można uruchamiać elementy Runbook bezpośrednio i z zasobami w środowisku, aby zarządzać tymi zasobami lokalnymi.

Azure Automation przechowuje elementy Runbook i zarządza nimi, a następnie dostarcza je do co najmniej jednej wyznaczeniu maszyn. W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego elementu Runbook użytkownika na komputerze z systemem Windows, sposobu usuwania procesu roboczego oraz usuwania grupy hybrydowych procesów roboczych elementu Runbook.

Po pomyślnym wdrożeniu programu Runbook Worker przejrzyj temat [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy.

### <a name="a-log-analytics-workspace"></a>Obszar roboczy Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook zależy od obszaru roboczego Log Analytics Azure Monitor, aby zainstalować i skonfigurować rolę. Można go utworzyć za pomocą [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), za pomocą [programu PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)lub [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Jeśli nie masz obszaru roboczego Log Analytics Azure Monitor, zapoznaj się ze [wskazówkami dotyczącymi projektu dziennika Azure monitor](../azure-monitor/platform/design-logs-deployment.md) przed utworzeniem obszaru roboczego.

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook wymaga [agenta log Analytics](../azure-monitor/platform/log-analytics-agent.md) dla obsługiwanego systemu operacyjnego Windows. W przypadku serwerów lub maszyn hostowanych poza platformą Azure można zainstalować agenta Log Analytics przy użyciu [serwerów z obsługą usługi Azure Arc](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Obsługiwany system operacyjny Windows

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje następujące systemy operacyjne:

* Windows Server 2019
* Windows Server 2016, wersja 1709 i 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 z dodatkiem SP2 (x64), 2008 R2
* Windows 10 Enterprise (w tym wiele sesji) i Pro
* Windows 8 Enterprise i Pro
* Windows 7 z dodatkiem SP1

### <a name="minimum-requirements"></a>Minimalne wymagania

Minimalne wymagania dotyczące systemu Windows i hybrydowego procesu roboczego elementu Runbook użytkownika:

* Windows PowerShell 5,1 lub nowszy ([pobieranie plików WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 lub nowszy
* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

### <a name="network-configuration"></a>Konfiguracja sieci

Wymagania dotyczące sieci dla hybrydowego procesu roboczego elementu Runbook można znaleźć w temacie [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Dodawanie komputera do grupy hybrydowych procesów roboczych elementu Runbook

Możesz dodać maszynę procesu roboczego do grupy hybrydowych procesów roboczych elementu Runbook na jednym z kont usługi Automation. W przypadku maszyn obsługujących systemowy hybrydowy proces roboczy elementu Runbook zarządzany przez Update Management można je dodać do grupy hybrydowych procesów roboczych elementu Runbook. Należy jednak użyć tego samego konta usługi Automation zarówno dla Update Management, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook.

>[!NOTE]
>Azure Automation [Update Management](update-management/update-mgmt-overview.md) automatycznie instaluje hybrydowy proces roboczy elementu Runbook na platformie Azure lub na maszynie spoza platformy Azure, która jest włączona dla Update Management. Jednak ten proces roboczy nie jest zarejestrowany w żadnej z grup hybrydowych procesów roboczych elementu Runbook na Twoim koncie usługi Automation. Aby uruchomić elementy Runbook na tych komputerach, należy dodać je do grupy hybrydowych procesów roboczych elementu Runbook. Wykonaj krok 6 w sekcji [wdrażanie ręczne](#manual-deployment) , aby dodać je do grupy.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Włącz dla zarządzania z konfiguracją stanu Azure Automation

Informacje o włączaniu maszyn do zarządzania przy użyciu konfiguracji stanu Azure Automation można znaleźć w temacie [enable Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

> [!NOTE]
> Aby zarządzać konfiguracją maszyn, które obsługują rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać maszyny jako węzły DSC.

## <a name="installation-options"></a>Opcje instalacji

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook użytkownika systemu Windows, można użyć jednej z następujących metod.

* Za pomocą dostarczonego skryptu programu PowerShell można całkowicie [zautomatyzować](#automated-deployment) proces konfigurowania co najmniej jednej maszyny z systemem Windows. Jest to zalecana metoda dla maszyn w centrum danych lub w innym środowisku chmury.
* Ręcznie zaimportuj rozwiązanie automatyzacji, Zainstaluj agenta Log Analytics dla systemu Windows i skonfiguruj rolę procesu roboczego na komputerze.

## <a name="automated-deployment"></a>Wdrożenie automatyczne

Metoda zautomatyzowanego wdrażania używa skryptu programu PowerShell **New-OnPremiseHybridWorker.ps1** do automatyzowania i konfigurowania roli hybrydowego procesu roboczego elementu Runbook systemu Windows. Wykonuje następujące czynności:

* Instaluje niezbędne moduły
* Zaloguj się przy użyciu konta platformy Azure
* Weryfikuje istnienie określonej grupy zasobów i konta usługi Automation
* Tworzy odwołania do atrybutów konta usługi Automation
* Tworzy obszar roboczy Log Analytics Azure Monitor, jeśli nie zostanie określony
* Włączanie rozwiązania Azure Automation w obszarze roboczym
* Pobierz i Zainstaluj agenta Log Analytics dla systemu Windows
* Zarejestruj maszynę jako hybrydowy proces roboczy elementu Runbook

Wykonaj następujące kroki, aby zainstalować rolę na komputerze z systemem Windows przy użyciu skryptu.

1. Pobierz skrypt **New-OnPremiseHybridWorker.ps1** z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Po pobraniu skryptu Skopiuj lub uruchom go na maszynie docelowej. Podczas wykonywania skrypt **New-OnPremiseHybridWorker.ps1** używa następujących parametrów.

    | Parametr | Stan | Opis |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obowiązkowy | Nazwa grupy zasobów skojarzonej z kontem usługi Automation. |
    | `AutomationAccountName` | Obowiązkowy | Nazwa konta usługi Automation.
    | `Credential` | Opcjonalne | Poświadczenia, które mają być używane podczas logowania w środowisku platformy Azure. |
    | `HybridGroupName` | Obowiązkowy | Nazwa grupy hybrydowych procesów roboczych elementu Runbook, która została określona jako element docelowy dla elementów Runbook, które obsługują ten scenariusz. |
    | `OMSResourceGroupName` | Opcjonalne | Nazwa grupy zasobów dla obszaru roboczego Log Analytics. Jeśli ta grupa zasobów nie jest określona, `AAResourceGroupName` zostanie użyta wartość. |
    | `SubscriptionID` | Obowiązkowy | Identyfikator subskrypcji platformy Azure skojarzonej z kontem usługi Automation. |
    | `TenantID` | Opcjonalne | Identyfikator organizacji dzierżawy skojarzonej z kontem usługi Automation. |
    | `WorkspaceName` | Opcjonalne | Nazwa obszaru roboczego Log Analytics. Jeśli nie masz obszaru roboczego Log Analytics, skrypt tworzy i konfiguruje go. |

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień 64-bitowy programu PowerShell.

3. W wierszu polecenia programu PowerShell przejdź do folderu zawierającego pobrany skrypt. Zmień wartości parametrów `AutomationAccountName` ,,,, `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` i `WorkspaceName` . Następnie uruchom skrypt.

    Po uruchomieniu skryptu zostanie wyświetlony monit o uwierzytelnienie przy użyciu platformy Azure. Musisz zalogować się przy użyciu konta, które jest członkiem roli **administratorzy subskrypcji** i współadministratorem subskrypcji.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. Zostanie wyświetlony monit o zgodę na zainstalowanie narzędzia NuGet i uwierzytelnienie przy użyciu poświadczeń platformy Azure. Jeśli nie masz najnowszej wersji pakietu NuGet, możesz pobrać ją z [dostępnych wersji dystrybucji NuGet](https://www.nuget.org/downloads).

5. Sprawdź wdrożenie po zakończeniu skryptu. Na stronie **grupy hybrydowych procesów roboczych elementu Runbook** na koncie usługi Automation na karcie **Grupa hybrydowe procesy robocze elementu Runbook użytkownika** zostanie wyświetlona nowa grupa i liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie, wybierając z menu z lewej strony pozycję **hybrydowe procesy robocze** . Na stronie **hybrydowe procesy robocze** można zobaczyć wszystkich członków tej grupy.

## <a name="manual-deployment"></a>Wdrożenie ręczne

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows, wykonaj następujące czynności.

1. Włącz rozwiązanie Azure Automation w obszarze roboczym Log Analytics, uruchamiając następujące polecenie w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień lub w Cloud Shell w [Azure Portal](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Wdróż agenta Log Analytics na maszynie docelowej.

    * W przypadku maszyn wirtualnych platformy Azure Zainstaluj agenta Log Analytics dla systemu Windows przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md). Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym Log Analytics obszarze roboczym. Można użyć szablonu Azure Resource Manager, programu PowerShell lub Azure Policy do przypisywania programu [Deploy log Analytics Agent na potrzeby wbudowanych zasad maszyn wirtualnych z systemem *Linux* lub *Windows*](../governance/policy/samples/built-in-policies.md#monitoring) . Po zainstalowaniu agenta komputer można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation.
    
    * W przypadku maszyn spoza platformy Azure można zainstalować agenta Log Analytics przy użyciu [serwerów z obsługą usługi Azure Arc](../azure-arc/servers/overview.md). Serwery z obsługą Arc obsługują wdrażanie agenta Log Analytics przy użyciu następujących metod:
    
        - Korzystanie z platformy rozszerzeń maszyn wirtualnych.
        
            Ta funkcja na serwerach z obsługą usługi Azure Arc umożliwia wdrożenie rozszerzenia maszyny wirtualnej Log Analytics Agent na serwerze z systemem innym niż Azure. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z obsługą ARC:
        
            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Interfejs wiersza polecenia platformy Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Szablony Menedżer zasobów](../azure-arc/servers/manage-vm-extensions-template.md) platformy Azure
        
        - Przy użyciu Azure Policy.
        
            Korzystając z tego podejścia, należy użyć wbudowanej zasady Azure Policy [Wdróż agenta log Analytics do systemu Linux lub Windows Azure Arc](../governance/policy/samples/built-in-policies.md#monitoring) , aby przeprowadzić inspekcję, jeśli na serwerze z włączonym łukiem jest zainstalowany agent log Analytics. Jeśli Agent nie jest zainstalowany, zostanie automatycznie wdrożony przy użyciu zadania korygowania. Alternatywnie, jeśli planujesz monitorowanie maszyn przy użyciu Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy [Enable Azure monitor dla maszyn wirtualnych](../governance/policy/samples/built-in-initiatives.md#monitoring) , aby zainstalować i skonfigurować agenta log Analytics.

    Zalecamy zainstalowanie agenta Log Analytics dla systemu Windows lub Linux przy użyciu Azure Policy.

3. Sprawdź, czy Agent jest raportowany do obszaru roboczego

    Agent Log Analytics dla systemu Windows łączy komputery z obszarem roboczym Azure Monitor Log Analytics. Po zainstalowaniu agenta na maszynie i nawiązaniu połączenia z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

    Gdy Agent pomyślnie połączył się z obszarem roboczym Log Analytics za kilka minut, możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    W wynikach wyszukiwania powinny być widoczne rekordy pulsu dla maszyny, co oznacza, że jest ona podłączona i zgłaszana do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego im obszaru roboczego. Aby ukończyć instalację i konfigurację agenta, wykonaj następujące czynności.

4. Potwierdź wersję hybrydowego procesu roboczego elementu Runbook na komputerze hostującym agenta Log Analytics, przejdź do `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` podfolderu **wersji** i zanotuj go. Ten folder pojawi się na maszynie kilka minut po włączeniu rozwiązania w obszarze roboczym.

5. Zainstaluj środowisko Runbook i Połącz się z Azure Automation. W przypadku skonfigurowania agenta w celu raportowania do obszaru roboczego Log Analytics i zaimportowania rozwiązania do **automatyzacji** rozwiązanie wypchnięcie `HybridRegistration` modułu programu PowerShell. Ten moduł zawiera `Add-HybridRunbookWorker` polecenie cmdlet. To polecenie cmdlet służy do instalowania środowiska Runbook na komputerze i rejestrowania go w Azure Automation.

    Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Uruchom `Add-HybridRunbookWorker` polecenie cmdlet, określając wartości parametrów `Url` , `Key` i `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Możesz uzyskać informacje wymagane do parametrów `Url` i `Key` ze strony **klucze** na koncie usługi Automation. Wybierz pozycję **klucze** w sekcji **Ustawienia konta** w lewej części strony.

    ![Strona zarządzania kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Dla `Url` parametru skopiuj wartość **adresu URL**.

    * Dla `Key` parametru skopiuj wartość **podstawowy klucz dostępu**.

    * Dla `GroupName` parametru Użyj nazwy grupy hybrydowych procesów roboczych elementu Runbook. Jeśli ta grupa już istnieje na koncie usługi Automation, do niej zostanie dodany bieżący komputer. Jeśli ta grupa nie istnieje, jest dodawana.

    * W razie potrzeby ustaw `Verbose` parametr, aby otrzymać szczegółowe informacje o instalacji.

7. Sprawdź wdrożenie po zakończeniu polecenia. Na stronie **grupy hybrydowych procesów roboczych elementu Runbook** na koncie usługi Automation na karcie **Grupa hybrydowe procesy robocze elementu Runbook użytkownika** zostanie wyświetlona nowa lub istniejąca Grupa oraz liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie, wybierając z menu z lewej strony pozycję **hybrydowe procesy robocze**. Na stronie **hybrydowe procesy robocze** można zobaczyć wszystkich członków tej grupy.

## <a name="install-powershell-modules"></a>Zainstaluj moduły programu PowerShell

Elementy Runbook mogą korzystać z dowolnych działań i poleceń cmdlet zdefiniowanych w modułach zainstalowanych w środowisku Azure Automation. Ponieważ te moduły nie są automatycznie wdrażane na maszynach lokalnych, należy je zainstalować ręcznie. Wyjątek jest modułem platformy Azure. Ten moduł jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług i działań platformy Azure dla Azure Automation.

Ponieważ głównym celem hybrydowego procesu roboczego elementu Runbook jest zarządzanie zasobami lokalnymi, najprawdopodobniej trzeba zainstalować moduły obsługujące te zasoby, w szczególności `PowerShellGet` moduł. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Zainstalowane moduły muszą znajdować się w lokalizacji, do której odwołuje się `PSModulePath` zmienna środowiskowa, dzięki czemu hybrydowy proces roboczy może je automatycznie zaimportować. Aby uzyskać więcej informacji, zobacz [Install modules in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Usuń hybrydowy proces roboczy elementu Runbook

1. W Azure Portal przejdź do konta usługi Automation.

2. W obszarze **Ustawienia konta** wybierz pozycję **klucze** i zanotuj wartości **adresu URL** i **podstawowego klucza dostępu**.

3. Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie przy użyciu adresu URL i podstawowych wartości klucza dostępu. Użyj `Verbose` parametru, aby zapoznać się ze szczegółowym dziennikiem procesu usuwania. Aby usunąć przestarzałe maszyny z grupy hybrydowych procesów roboczych, użyj opcjonalnego `machineName` parametru.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych elementu Runbook, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdej maszyny, która jest członkiem grupy. Następnie wykonaj następujące kroki, aby usunąć grupę:

1. Otwórz konto usługi Automation w Azure Portal.

2. Wybierz **grupy hybrydowych procesów roboczych** w obszarze **Automatyzacja procesów**. Wybierz grupę, którą chcesz usunąć. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stronie właściwości wybranej grupy wybierz pozycję **Usuń**. Zostanie wyświetlony komunikat z prośbą o potwierdzenie tej akcji. Wybierz opcję **tak** , jeśli na pewno chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ukończenie tego procesu może potrwać kilka sekund. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym](troubleshoot/hybrid-runbook-worker.md#general)
