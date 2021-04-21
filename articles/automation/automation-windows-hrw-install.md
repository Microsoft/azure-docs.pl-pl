---
title: Wdrażanie hybrydowego procesu roboczego runbook systemu Windows w Azure Automation
description: W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego runbook, który umożliwia uruchamianie runbook na maszynach z systemem Windows w lokalnym centrum danych lub środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4bf27ffc888e189f15e1c435309cbeddb1c11fec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830344"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego runbook systemu Windows

Za pomocą funkcji hybrydowego procesu roboczego elementu runbook użytkownika programu Azure Automation można uruchamiać element runbook bezpośrednio na maszynie platformy Azure lub spoza platformy Azure, w tym na serwerach zarejestrowanych Azure Arc serwerach z [obsługą usługi](../azure-arc/servers/overview.md). Na maszynie lub serwerze hostującym rolę można uruchamiać zasoby Runbook bezpośrednio względem tej roli i zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi.

Azure Automation przechowuje i zarządza nimi, a następnie dostarcza je do co najmniej jednej wyznaczonej maszyny. W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego runbook użytkownika na maszynie z systemem Windows, usuwania procesu roboczego oraz usuwania grupy hybrydowych procesów roboczych runbook.

Po pomyślnym wdrożeniu procesu roboczego runbook zapoznaj się z tematem [Runbook on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) (Uruchamianie podręczników runbook w hybrydowym procesów roboczych runbook), aby dowiedzieć się, jak skonfigurować swoje runbook w celu zautomatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy.

### <a name="a-log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics

Rola hybrydowych procesów roboczych runbook zależy od Azure Monitor obszaru roboczego usługi Log Analytics w celu zainstalowania i skonfigurowania roli. Można go utworzyć za pomocą [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), za pomocą [programu PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)lub w Azure Portal [.](../azure-monitor/logs/quick-create-workspace.md)

Jeśli nie masz obszaru roboczego usługi Log Analytics Azure Monitor, przed utworzeniem obszaru roboczego zapoznaj się ze wskazówkami Azure Monitor [projektu](../azure-monitor/logs/design-logs-deployment.md) dziennika usługi Log.

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Rola hybrydowych procesów roboczych runbook wymaga agenta [usługi Log Analytics](../azure-monitor/agents/log-analytics-agent.md) dla obsługiwanego systemu operacyjnego Windows. W przypadku serwerów lub maszyn hostowanych poza platformą Azure można zainstalować agenta usługi Log Analytics przy użyciu Azure Arc serwerów z [obsługą usługi](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Obsługiwany system operacyjny Windows

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje następujące systemy operacyjne:

* Windows Server 2019 (w tym Server Core)
* Windows Server 2016, wersje 1709 i 1803 (z wyjątkiem Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (w tym wiele sesji) i Pro
* Windows 8 Enterprise i Pro
* Windows 7 z dodatkiem SP1

### <a name="minimum-requirements"></a>Minimalne wymagania

Minimalne wymagania dotyczące hybrydowego procesu roboczego runbook systemu Windows i użytkownika to:

* Windows PowerShell 5.1[(pobierz program WMF 5.1).](https://www.microsoft.com/download/details.aspx?id=54616) PowerShell Core nie jest obsługiwana.
* .NET Framework 4.6.2 lub nowszy
* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

### <a name="network-configuration"></a>Konfiguracja sieci

Aby uzyskać informacje o wymaganiach dotyczących sieci dla hybrydowego procesu roboczego runbook, [zobacz Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Dodawanie maszyny do grupy hybrydowych procesów roboczych runbook

Maszynę procesu roboczego można dodać do grupy hybrydowych procesów roboczych runbook na jednym z kont usługi Automation. W przypadku maszyn hostujących systemowy hybrydowy proces roboczy runbook zarządzany przez Update Management można dodać je do grupy hybrydowych procesów roboczych runbook. Należy jednak użyć tego samego konta usługi Automation zarówno dla Update Management, jak i członkostwa w grupie hybrydowych procesów roboczych runbook.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatycznie instaluje hybrydowy proces roboczy runbook systemu na maszynie platformy Azure lub spoza platformy Azure, która jest włączona dla Update Management. Jednak ten proces roboczy nie jest zarejestrowany w żadnych grupach hybrydowych procesów roboczych runbook na twoim koncie usługi Automation. Aby uruchamiać swoje podręczniki Runbook na tych maszynach, należy dodać je do grupy hybrydowych procesów roboczych runbook. Wykonaj krok 6 w sekcji [Wdrożenie ręczne,](#manual-deployment) aby dodać je do grupy.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Włączanie zarządzania przy użyciu Azure Automation State Configuration

Aby uzyskać informacje o włączaniu maszyn do zarządzania za pomocą Azure Automation State Configuration, zobacz [Włączanie zarządzania maszynami przez](automation-dsc-onboarding.md)Azure Automation State Configuration .

> [!NOTE]
> Aby zarządzać konfiguracją maszyn, które obsługują rolę hybrydowych procesów roboczych runbook za pomocą Desired State Configuration (DSC), należy dodać maszyny jako węzły DSC.

## <a name="installation-options"></a>Opcje instalacji

Aby zainstalować i skonfigurować hybrydowy proces roboczy runbook użytkownika systemu Windows, można użyć jednej z następujących metod.

* Użyj dostarczonego skryptu programu [](#automated-deployment) PowerShell, aby całkowicie zautomatyzować proces konfigurowania co najmniej jednej maszyny z systemem Windows. Jest to zalecana metoda dla maszyn w centrum danych lub w innym środowisku chmury.
* Ręcznie zaimportuj rozwiązanie usługi Automation, zainstaluj agenta usługi Log Analytics dla systemu Windows i skonfiguruj rolę procesu roboczego na maszynie.

## <a name="automated-deployment"></a>Wdrożenie automatyczne

Istnieją dwie metody automatycznego wdrażania hybrydowego procesu roboczego runbook. Możesz zaimportować i uruchomić go z galerii runbook w galerii Azure Portal lub ręcznie pobrać skrypt z Galeria programu PowerShell.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importowanie runbook z galerii runbook

Procedura importowania jest szczegółowo opisana w tesłudze [Import runbook from GitHub with the Azure Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). Nazwa importowanego runbook to Create Automation Windows HybridWorker (Utwórz **automatyzację w systemie Windows HybridWorker).**

Elementy Runbook wykorzystują następujące parametry.

| Parametr | Stan | Opis |
| ------- | ----- | ----------- |
| `Location` | Obowiązkowy | Lokalizacja obszaru roboczego usługi Log Analytics. |
| `ResourceGroupName` | Obowiązkowy | Grupa zasobów dla konta usługi Automation. |
| `AccountName` | Obowiązkowy | Nazwa konta usługi Automation, w którym zostanie zarejestrowany hybrydowy proces roboczy uruchamiania. |
| `CreateLA` | Obowiązkowy | W przypadku wartości true program używa wartości `WorkspaceName` w celu utworzenia obszaru roboczego usługi Log Analytics. W przypadku wartości false wartość musi `WorkspaceName` odwoływać się do istniejącego obszaru roboczego. |
| `LAlocation` | Opcjonalne | Lokalizacja, w której zostanie utworzony obszar roboczy usługi Log Analytics lub gdzie już istnieje. |
| `WorkspaceName` | Opcjonalne | Nazwa obszaru roboczego usługi Log Analytics do użycia. |
| `CreateVM` | Obowiązkowy | W przypadku wartości true użyj wartości `VMName` jako nazwy nowej maszyny wirtualnej. W przypadku false użyj funkcji , `VMName` aby znaleźć i zarejestrować istniejącą maszynę wirtualną. |
| `VMName` | Opcjonalne | Nazwa tworzonej lub zarejestrowanej maszyny wirtualnej w zależności od wartości `CreateVM` . |
| `VMImage` | Opcjonalne | Nazwa obrazu maszyny wirtualnej do utworzenia. |
| `VMlocation` | Opcjonalne | Lokalizacja utworzonej lub zarejestrowanej maszyny wirtualnej. Jeśli ta lokalizacja nie jest określona, używana jest `LAlocation` wartość . |
| `RegisterHW` | Obowiązkowy | W przypadku wartości true zarejestruj maszynę wirtualną jako hybrydowy proces roboczy. |
| `WorkerGroupName` | Obowiązkowy | Nazwa grupy hybrydowych procesów roboczych. |

### <a name="download-a-script-from-the-powershell-gallery"></a>Pobierz skrypt z Galeria programu PowerShell

Ta metoda zautomatyzowanego wdrażania używa skryptu programu PowerShell **New-OnPremiseHybridWorker.ps1** do automatyzowania i konfigurowania roli hybrydowego procesu roboczego runbook systemu Windows. Wykonuje następujące czynności:

* Instaluje niezbędne moduły
* Signs in with your Azure account (Dane do konta platformy Azure)
* Weryfikuje istnienie określonej grupy zasobów i konta usługi Automation
* Tworzy odwołania do atrybutów konta usługi Automation
* Tworzy obszar roboczy Azure Monitor usługi Log Analytics, jeśli nie zostanie określony
* Włączanie rozwiązania Azure Automation w obszarze roboczym
* Pobieranie i instalowanie agenta usługi Log Analytics dla systemu Windows
* Rejestrowanie maszyny jako hybrydowego procesu roboczego runbook

Wykonaj poniższe kroki, aby zainstalować rolę na maszynie z systemem Windows przy użyciu skryptu .

1. Pobierz skrypt **New-OnPremiseHybridWorker.ps1** ze strony [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Po pobraniu skryptu skopiuj lub uruchom go na maszynie docelowej. Skrypt używa następujących parametrów.

    | Parametr | Stan | Opis |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obowiązkowy | Nazwa grupy zasobów skojarzonej z kontem usługi Automation. |
    | `AutomationAccountName` | Obowiązkowy | Nazwa konta usługi Automation.
    | `Credential` | Opcjonalne | Poświadczenia do użycia podczas logowania się do środowiska platformy Azure. |
    | `HybridGroupName` | Obowiązkowy | Nazwa grupy hybrydowych procesów roboczych runbook, która jest określana jako element docelowy dla elementów Runbook, które obsługują ten scenariusz. |
    | `OMSResourceGroupName` | Opcjonalne | Nazwa grupy zasobów dla obszaru roboczego usługi Log Analytics. Jeśli ta grupa zasobów nie zostanie określona, zostanie `AAResourceGroupName` użyta wartość . |
    | `SubscriptionID` | Obowiązkowy | Identyfikator subskrypcji platformy Azure skojarzonej z kontem usługi Automation. |
    | `TenantID` | Opcjonalne | Identyfikator organizacji dzierżawy skojarzonej z Twoim kontem usługi Automation. |
    | `WorkspaceName` | Opcjonalne | Nazwa obszaru roboczego usługi Log Analytics. Jeśli nie masz obszaru roboczego usługi Log Analytics, skrypt go utworzy i skonfiguruje. |

1. Otwórz 64-bitowy wiersz polecenia programu PowerShell z podwyższonym poziomem uprawnień.

1. W wierszu polecenia programu PowerShell przejdź do folderu zawierającego pobrany skrypt. Zmień wartości parametrów `AutomationAccountName` , `AAResourceGroupName` , , , `OMSResourceGroupName` i `HybridGroupName` `SubscriptionID` `WorkspaceName` . Następnie uruchom skrypt.

    Po uruchomieniu skryptu zostanie wyświetlony monit o uwierzytelnienie na platformie Azure. Musisz zalogować się przy użyciu konta, które jest członkiem roli **Administratorzy** subskrypcji i współzałożycieldą subskrypcji.

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

1. Zostanie wyświetlony monit o wyrażenie zgody na zainstalowanie pakietu NuGet i uwierzytelnienie się przy użyciu poświadczeń platformy Azure. Jeśli nie masz najnowszej wersji pakietu NuGet, możesz pobrać ją ze strony [Dostępne wersje dystrybucji NuGet.](https://www.nuget.org/downloads)

1. Po zakończeniu wykonywania skryptu zweryfikuj wdrożenie. Na stronie **Grupy hybrydowych procesów** roboczych elementów Runbook na koncie usługi Automation na karcie Grupa hybrydowych procesów roboczych elementów **Runbook** użytkownika jest przedstawiana nowa grupa i liczba członków. Jeśli jest to istniejąca grupa, liczba elementów członkowskich jest zwiększana. Możesz wybrać grupę z listy na stronie. W menu po lewej stronie wybierz pozycję **Hybrydowe pracownicy.** Na stronie **Hybrydowe pracownicy** można zobaczyć każdego członka grupy na liście.

## <a name="manual-deployment"></a>Wdrożenie ręczne

Aby zainstalować i skonfigurować hybrydowy proces roboczy runbook systemu Windows, wykonaj następujące kroki.

1. Włącz rozwiązanie Azure Automation w obszarze roboczym usługi Log Analytics, uruchamiając następujące polecenie w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień lub Cloud Shell w Azure Portal [.](https://portal.azure.com)

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Wd wdrażać agenta usługi Log Analytics na maszynie docelowej.

    * W przypadku maszyn wirtualnych platformy Azure zainstaluj agenta usługi Log Analytics dla systemu Windows przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Windows.](../virtual-machines/extensions/oms-windows.md) Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym usługi Log Analytics. Możesz użyć szablonu usługi Azure Resource Manager, programu PowerShell lub Azure Policy, aby przypisać wbudowane zasady Wdrażanie agenta usługi Log Analytics dla maszyn wirtualnych z systemem Linux lub [ *Windows.*](../governance/policy/samples/built-in-policies.md#monitoring) Po zainstalowaniu agenta maszynę można dodać do grupy hybrydowych procesów roboczych runbook na koncie usługi Automation.
    
    * W przypadku maszyn spoza platformy Azure można zainstalować agenta usługi Log Analytics przy użyciu Azure Arc serwerów z [obsługą usługi](../azure-arc/servers/overview.md). Serwery z usługą Arc obsługują wdrażanie agenta usługi Log Analytics przy użyciu następujących metod:
    
        - Korzystanie z platformy rozszerzeń maszyn wirtualnych.
        
            Ta funkcja na Azure Arc serwerach z włączoną obsługą usługi umożliwia wdrożenie rozszerzenia maszyny wirtualnej agenta usługi Log Analytics na serwerze z systemem Windows i/lub Linux spoza platformy Azure. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z usługą Arc:
        
            - W [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Interfejs [wiersza polecenia platformy Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Szablony [Resource Manager Azure](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Przy użyciu Azure Policy.
        
            Korzystając z tej metody Azure Policy, należy użyć wbudowanych zasad wdrażania agenta usługi Log Analytics na maszynach z systemem Linux lub [Windows Azure Arc](../governance/policy/samples/built-in-policies.md#monitoring) w celu inspekcji, czy na serwerze z usługą Arc jest zainstalowany agent usługi Log Analytics. Jeśli agent nie jest zainstalowany, automatycznie wdraża go przy użyciu zadania korygowania. Alternatywnie, jeśli planujesz monitorowanie maszyn za pomocą usługi Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy Włącz usługę [Azure Monitor dla maszyn wirtualnych,](../governance/policy/samples/built-in-initiatives.md#monitoring) aby zainstalować i skonfigurować agenta usługi Log Analytics.

    Zalecamy zainstalowanie agenta usługi Log Analytics dla systemu Windows lub Linux przy użyciu Azure Policy.

1. Sprawdzanie, czy agent raportuje do obszaru roboczego

    Agent usługi Log Analytics dla systemu Windows łączy maszyny z Azure Monitor usługi Log Analytics. Zainstalowanie agenta na maszynie i połączenie go z obszarem roboczym spowoduje automatyczne pobranie składników wymaganych przez hybrydowy proces roboczy runbook.

    Jeśli po kilku minutach agent pomyślnie nałączono połączenie z obszarem roboczym usługi Log Analytics, możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    W wynikach wyszukiwania powinny być widać rekordy pulsu dla maszyny, wskazujące, że maszyna jest połączona, i raportujące do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego mu obszaru roboczego. Wykonaj poniższe kroki, aby ukończyć instalację i konfigurację agenta.

1. Potwierdź wersję hybrydowego procesu roboczego runbook na komputerze hostującym agenta usługi Log Analytics, przejdź do podfolderu wersji i `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` zanotuj go.  Ten folder pojawi się na maszynie kilka minut po włączeniu rozwiązania w obszarze roboczym.

1. Zainstaluj środowisko runbook i połącz się z Azure Automation. Po skonfigurowaniu agenta do zgłaszania do obszaru roboczego usługi Log Analytics i importowania rozwiązania **usługi Automation** rozwiązanie wypycha moduł `HybridRegistration` programu PowerShell. Ten moduł zawiera `Add-HybridRunbookWorker` polecenie cmdlet . To polecenie cmdlet pozwala zainstalować środowisko runbook na maszynie i zarejestrować je w Azure Automation.

    Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. Uruchom polecenie `Add-HybridRunbookWorker` cmdlet , określając wartości parametrów `Url` , i `Key` `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Informacje wymagane dla parametrów oraz ze strony Klucze na `Url` `Key` koncie usługi Automation.  Wybierz **pozycję Klucze** w sekcji Ustawienia **konta** w lewej części strony.

    ![Strona Zarządzanie kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Dla `Url` parametru skopiuj wartość adresu **URL**.

    * Dla `Key` parametru skopiuj wartość **PRIMARY ACCESS KEY (PODSTAWOWY KLUCZ DOSTĘPU).**

    * Dla `GroupName` parametru użyj nazwy grupy hybrydowych procesów roboczych runbook. Jeśli ta grupa już istnieje na koncie usługi Automation, zostanie do niego dodana bieżąca maszyna. Jeśli ta grupa nie istnieje, zostanie dodana.

    * W razie potrzeby ustaw `Verbose` parametr , aby otrzymywać szczegółowe informacje o instalacji.

1. Sprawdź wdrożenie po zakończeniu polecenia. Na stronie **Grupy hybrydowych procesów** roboczych elementów Runbook na koncie usługi Automation na karcie Grupy hybrydowych procesów roboczych elementów **Runbook** użytkownika jest przedstawiana nowa lub istniejąca grupa oraz liczba członków. Jeśli jest to istniejąca grupa, liczba elementów członkowskich jest zwiększana. Możesz wybrać grupę z listy na stronie. W menu po lewej stronie wybierz pozycję **Hybrydowe pracownicy.** Na stronie **Hybrydowe pracownicy** można zobaczyć każdego członka grupy na liście.

## <a name="install-powershell-modules"></a>Instalowanie modułów programu PowerShell

Przy użyciu dowolnych działań i polecenia cmdlet zdefiniowanych w modułach zainstalowanych w środowisku Azure Automation Runbook. Ponieważ te moduły nie są automatycznie wdrażane na maszynach lokalnych, należy zainstalować je ręcznie. Wyjątkiem jest moduł platformy Azure. Ten moduł jest instalowany domyślnie i zapewnia dostęp do polecenia cmdlet dla wszystkich usług i działań platformy Azure dla Azure Automation.

Ponieważ głównym celem hybrydowego procesu roboczego runbook jest zarządzanie zasobami lokalnymi, najprawdopodobniej musisz zainstalować moduły, które obsługują te zasoby, szczególnie `PowerShellGet` moduł . Aby uzyskać informacje na temat instalowania Windows PowerShell modułów, [zobacz Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Moduły, które są zainstalowane, muszą znajdować się w lokalizacji, do których odwołuje się zmienna środowiskowa , aby hybrydowy proces roboczy `PSModulePath` może je automatycznie zaimportować. Aby uzyskać więcej informacji, zobacz [Install Modules in PSModulePath (Instalowanie modułów w programie PSModulePath).](/powershell/scripting/developer/module/installing-a-powershell-module)

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Usuwanie hybrydowego procesu roboczego runbook

1. W Azure Portal przejdź do swojego konta usługi Automation.

1. W **obszarze Account Settings (Ustawienia** konta) wybierz pozycję **Keys** (Klucze) i zanotuj wartości pól **URL (Adres URL)** **i Primary Access Key (Podstawowy klucz dostępu).**

1. Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie z wartościami adresu URL i podstawowego klucza dostępu. Użyj `Verbose` parametru , aby uzyskać szczegółowy dziennik procesu usuwania. Aby usunąć nieaktywne maszyny z grupy hybrydowych procesów roboczych, użyj opcjonalnego `machineName` parametru .

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych runbook, należy najpierw usunąć hybrydowy proces roboczy runbook z każdej maszyny, która jest członkiem grupy. Następnie użyj następujących kroków, aby usunąć grupę:

1. Otwórz konto usługi Automation w Azure Portal.

1. Wybierz **pozycję Grupy hybrydowych procesów roboczych** w obszarze **Automatyzacja procesów.** Wybierz grupę, którą chcesz usunąć. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na stronie właściwości wybranej grupy wybierz pozycję **Usuń**. Zostanie wyświetlony komunikat z prośbą o potwierdzenie tej akcji. Wybierz **pozycję Tak,** jeśli masz pewność, że chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ten proces może potrwać kilka sekund. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować swoje podręczniki Runbook w celu zautomatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz Runbook on a Hybrid Runbook Worker (Uruchamianie podręczników runbook w hybrydowym [procesach roboczych runbook).](automation-hrw-run-runbooks.md)

* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowymi procesami roboczymi runbook, zobacz Rozwiązywanie problemów z hybrydowymi [procesami roboczymi runbook.](troubleshoot/hybrid-runbook-worker.md#general)
