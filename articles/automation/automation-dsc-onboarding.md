---
title: Maszyny wbudowane do zarządzania przez konfigurację stanu automatyzacji platformy Azure
description: W tym artykule opisano sposób konfigurowania maszyn do zarządzania za pomocą konfiguracji stanu automatyzacji platformy Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406199"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Maszyny wbudowane do zarządzania przez konfigurację stanu automatyzacji platformy Azure

Konfiguracja stanu automatyzacji platformy Azure to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w chmurach lub lokalnych centrach danych. Dostęp do usługi można uzyskać w witrynie Azure portal, wybierając **konfigurację stanu (DSC)** w obszarze **Zarządzanie konfiguracją**. 

Dzięki konfiguracji stanu automatyzacji platformy Azure można szybko i łatwo osiągnąć skalowalność na tysiącach komputerów z centralnej, bezpiecznej lokalizacji. Można łatwo na komputerach wbudowanych, przypisać im deklaratywnych konfiguracji i wyświetlić raporty, które pokazują zgodność każdej maszyny ze stanem, który został określony.

Usługa konfiguracji stanu automatyzacji platformy Azure jest dsc, jakie są elementy runbook usługi Azure Automation do skryptów programu PowerShell. Innymi słowy w taki sam sposób, w jaki usługa Azure Automation ułatwia zarządzanie skryptami programu PowerShell, pomaga również zarządzać konfiguracjami DSC. Aby dowiedzieć się więcej o korzyściach płynących z korzystania z konfiguracji stanu automatyzacji platformy Azure, zobacz [omówienie konfiguracji stanu automatyzacji platformy Azure.](automation-dsc-overview.md)

Za pomocą konfiguracji stanu automatyzacji platformy Azure można zarządzać różnymi komputerami:

- Maszyny wirtualne platformy Azure
- Maszyny wirtualne platformy Azure (klasyczne)
- Fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure (w tym wystąpienia elastycznej chmury obliczeniowej [EC2] usług Amazon Web Services [AWS] Elastic Compute Cloud [EC2])
- Lokalne maszyny z systemem Linux fizyczne/wirtualne, na platformie Azure lub w chmurze innej niż Azure

Jeśli nie jesteś gotowy do zarządzania konfiguracją komputera z chmury, możesz użyć konfiguracji stanu automatyzacji platformy Azure jako punktu końcowego tylko do raportu. Ta funkcja umożliwia ustawienie lub wypychanie konfiguracji za pośrednictwem dsc i wyświetlanie szczegółów raportowania w usłudze Azure Automation.

> [!NOTE]
> Zarządzanie maszynami wirtualnymi platformy Azure za pomocą konfiguracji stanu automatyzacji platformy Azure jest bezpłatne, jeśli zainstalowane rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure jest w wersji 2.70 lub nowszej. Aby uzyskać więcej informacji, zobacz [Ustalanie cen automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

W poniższych sekcjach tego artykułu opisano, jak można dołączać wcześniej wymienione maszyny do konfiguracji stanu automatyzacji platformy Azure.

## <a name="onboard-azure-vms"></a>Wbudowane maszyny wirtualne platformy Azure

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az dla procesu roboczego hybrydowego systemu runbook, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [aktualizacji modułów programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

Ponieważ rozszerzenie platformy Azure działa asynchronicznie, firma Microsoft zapewnia kroki, aby śledzić jego postęp lub rozwiązywać problemy z nim w sekcji [Rozwiązywanie problemów z dołączaniem maszyny wirtualnej platformy Azure](#troubleshoot-azure-virtual-machine-onboarding) w tym artykule.

> [!NOTE]
> Wdrażanie dsc do węzła systemu Linux używa */tmp* folderu. Moduły, `nxautomation` takie jak są tymczasowo pobierane do weryfikacji przed ich zainstalowaniem. Aby upewnić się, że moduły są poprawnie zainstalowane, agent usługi Log Analytics dla systemu Linux wymaga uprawnień do odczytu/zapisu w folderze */tmp.*<br><br>
> Agent analizy dzienników dla systemu Linux działa jako użytkownik *omsagent.* Aby udzielić uprawnienia do zapisu użytkownikowi *omsagent,* uruchom polecenie `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-vms-by-using-the-azure-portal"></a>Wbudowane maszyny wirtualne przy użyciu witryny Azure portal

Aby dołączać maszyny wirtualne platformy Azure do konfiguracji stanu automatyzacji platformy Azure za pośrednictwem [witryny Azure portal:](https://portal.azure.com/)

1. Przejdź do konta usługi Azure Automation, na którym chcesz dołączać maszyny wirtualne. 

1. Na stronie **Konfiguracja stanu** wybierz kartę **Węzły,** a następnie wybierz pozycję **Dodaj**.

1. Wybierz maszynę wirtualną do wbudowanej.

1. Jeśli na komputerze nie zainstalowano rozszerzenia Żądanego Stanu programu PowerShell i jest uruchomiony stan zasilania, wybierz opcję **Połącz**.

1. W obszarze **Rejestracja**wprowadź [wartości lokalnego programu PowerShell DSC (LCM)](/powershell/scripting/dsc/managing-nodes/metaConfig) wymagane dla przypadku użycia. Opcjonalnie można wprowadzić konfigurację węzła, aby przypisać do maszyny Wirtualnej.

![Okienko rejestracji maszyny Wirtualnej](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Wbudowane maszyny wirtualne przy użyciu szablonów usługi Azure Resource Manager

Maszynę Wirtualną można wdrożyć i wdrożyć w konfiguracji stanu automatyzacji platformy Azure przy użyciu szablonów usługi Azure Resource Manager. Przykładowy szablon, który dołącza istniejącą konfigurację stanu automatyzacji platformy Azure Automation, zobacz [Serwer zarządzany przez usługę konfiguracji żądanego stanu](https://azure.microsoft.com/resources/templates/101-automation-configuration/). Jeśli zarządzasz zestawem skalowania maszyny wirtualnej, zobacz przykładowy szablon w [konfiguracji zestawu skalowania maszyny wirtualnej zarządzanej przez usługę Azure Automation.](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)

### <a name="onboard-vms-by-using-powershell"></a>Wbudowane maszyny wirtualne przy użyciu programu PowerShell

Polecenia cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) w programie PowerShell można użyć do wbudowanych maszyn wirtualnych do konfiguracji stanu automatyzacji platformy Azure. 

> [!NOTE]
>Polecenie `Register-AzAutomationDscNode` cmdlet jest obecnie implementowane tylko dla komputerów z systemem Windows, ponieważ polecenie cmdlet wyzwala tylko rozszerzenie systemu Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Rejestrowanie maszyn wirtualnych w ramach subskrypcji platformy Azure

Najlepszym sposobem rejestrowania maszyn wirtualnych z innych subskrypcji platformy Azure jest użycie rozszerzenia DSC w szablonie wdrażania usługi Azure Resource Manager. Przykłady znajdują się w [rozszerzeniu konfiguracji żądanego stanu z szablonami usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Aby znaleźć klucz rejestracji i adres URL rejestracji do użycia jako parametry w [szablonie,](#onboard-securely-by-using-registration) zobacz wbudowany bezpiecznie przy użyciu rejestracji sekcji w tym artykule.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Wbudowane fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure

Można dołączać serwery systemu Windows, które są uruchomione lokalnie lub w innych środowiskach chmury, w tym wystąpienia AWS EC2, do konfiguracji stanu automatyzacji platformy Azure. Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że najnowsza wersja [programu Windows Management Framework 5](https://aka.ms/wmf5latest) jest zainstalowana na komputerach, które mają być wbudowane w konfiguracji stanu automatyzacji platformy Azure. Ponadto program Windows Management Framework 5 musi być zainstalowany na komputerze używanym do operacji dołączania.
1. Aby utworzyć folder zawierający wymagane metakonfiguracje DSC, postępuj zgodnie ze wskazówkami w sekcji [Generowanie metakonfiguracji DSC.](#generate-dsc-metaconfigurations) 
1. Aby zdalnie zastosować metakonfiguracje dsc programu PowerShell do komputerów, które mają być wbudowane, należy użyć następującego polecenia cmdlet: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Jeśli nie można zdalnie zastosować metakonfiguracji dsc programu PowerShell, skopiuj folder **metakonfiguracji** do komputerów, na których się włączysz. Następnie dodaj kod, aby wywołać [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lokalnie na komputerach.

1. W witrynie Azure portal lub przy użyciu poleceń cmdlet sprawdź, czy maszyny do wbudowanego są wyświetlane jako węzły konfiguracji stanu zarejestrowane na koncie usługi Azure Automation.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Wbudowane fizyczne/wirtualne maszyny z systemem Linux lokalnie lub w chmurze innej niż Azure

Można dołączać serwery systemu Linux działające lokalnie lub w innych środowiskach chmury do konfiguracji stanu automatyzacji platformy Azure. Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że najnowsza wersja [konfiguracji żądanego stanu programu PowerShell dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowana na komputerach, które mają być wbudowane w konfigurację stanu automatyzacji platformy Azure.
1. Jeśli [ustawienia domyślne LCM programu PowerShell DSC są](/powershell/scripting/dsc/managing-nodes/metaConfig4) zgodne z przypadkiem użycia i chcesz na komputerach wbudowanych, tak aby zarówno ściągać, jak i raportować do konfiguracji stanu automatyzacji platformy Azure, wykonaj następujące czynności:

   a. Na każdym komputerze z systemem Linux do `Register.py` wbudowanej konfiguracji stanu automatyzacji platformy Azure należy użyć do ich dołączania przy użyciu domyślnych ustawień domyślnych lokalnego programu PowerShell DSC configuration manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta automatyzacji, zobacz [wbudowane bezpiecznie przy użyciu sekcji rejestracji](#onboard-securely-by-using-registration) w tym artykule.  
   d. Przejdź do kroku 4.
   
1. Jeśli ustawienia domyślne LCM programu PowerShell DSC nie pasują do przypadku użycia lub jeśli chcesz na komputerach wbudowanych, które zgłaszają się tylko do konfiguracji stanu automatyzacji platformy Azure, wykonaj kroki a-d. W przeciwnym razie przejdź bezpośrednio do kroku d.

    a. Aby utworzyć folder zawierający wymagane metakonfiguracje DSC, postępuj zgodnie ze wskazówkami w sekcji [Generowanie metakonfiguracji DSC.](#generate-dsc-metaconfigurations)

    b. Upewnij się, że najnowsza wersja [programu Windows Management Framework 5](https://aka.ms/wmf5latest) jest zainstalowana na komputerze, który jest używany do dołączania.

    d. Aby zdalnie zastosować metakonfiguracje dsc programu PowerShell do komputerów, które mają być wbudowane, dodaj następujący kod:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Jeśli nie można zdalnie zastosować metakonfiguracji dsc programu PowerShell, skopiuj metakonfiguracje odpowiadające maszynom zdalnym z folderu opisanego w kroku 3a na komputery z systemem Linux.

1. Dodaj kod do wywołania *Set-DscLocalConfigurationManager.py* lokalnie na każdym komputerze z systemem Linux, który ma być dołączany do konfiguracji stanu automatyzacji platformy Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Za pomocą witryny Azure portal lub polecenia cmdlet, upewnij się, że maszyny do wbudowanego teraz są wyświetlane jako węzły DSC zarejestrowane na koncie usługi Azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Generowanie metakonfiguracji DSC

Aby przywkłęka dowolnego komputera do konfiguracji stanu automatyzacji platformy Azure, można wygenerować [metakonfigurację DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Ta konfiguracja nakazuje agentowi DSC, aby pobierał z witryny konfiguracji stanu automatyzacji usługi Azure Automation lub raport. Metakonfigurację dsc dla konfiguracji stanu automatyzacji usługi Azure automation można wygenerować przy użyciu konfiguracji dsc programu PowerShell lub poleceń cmdlet programu Azure Automation PowerShell.

> [!NOTE]
> Konfiguracje metakonfiguracji DSC zawierają wpisy tajne potrzebne do dołączania komputera do konta automatyzacji do zarządzania. Pamiętaj, aby prawidłowo chronić wszystkie utworzone metakonfiguracje DSC lub usuwać je po użyciu.

Obsługa serwera proxy dla metakonfiguracji jest kontrolowana przez LCM, który jest aparatem DSC programu Windows PowerShell. LCM działa na wszystkich węzłach docelowych i jest odpowiedzialny za wywoływanie zasobów konfiguracji, które są zawarte w skrypcie metakonfiguracji DSC. 

Obsługę serwera proxy można uwzględnić w metakonfiguracji, dołączając definicje adresu `ConfigurationRepositoryWeb`URL `ResourceRepositoryWeb`serwera `ReportServerWeb` proxy i poświadczeń serwera proxy zgodnie z potrzebami w programach , i blokach. Zobacz [Konfigurowanie lokalnego programu Configuration Manager](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>Generowanie metakonfiguracji DSC przy użyciu konfiguracji DSC

1. Otwórz program Visual Studio Code (lub ulubiony edytor) jako administrator na komputerze w środowisku lokalnym. Urządzenie musi mieć zainstalowaną najnowszą wersję [programu Windows Management Framework 5.](https://aka.ms/wmf5latest)
1. Skopiuj lokalny skrypt. Ten skrypt zawiera konfigurację dsc programu PowerShell do tworzenia konfiguracji metakonfiguracji i polecenie rozpoczęcia tworzenia metakonfiguracji.

    > [!NOTE]
    > W nazwach konfiguracji węzła konfiguracji stanu rozróżniana jest wielkość liter w witrynie Azure portal. Jeśli sprawa jest niezgodna, węzeł nie pojawi się na karcie **Węzły.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Wypełnij klucz rejestracji i adres URL konta Automatyzacji, a także nazwy maszyn, które mają być wbudowane. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta automatyzacji, zobacz [wbudowane bezpiecznie przy użyciu](#onboard-securely-by-using-registration) sekcji rejestracji.

1. Jeśli chcesz, aby maszyny zgłaszały informacje o stanie DSC do konfiguracji stanu usługi Azure `ReportOnly` Automation, ale nie ściągały konfiguracji lub modułów programu PowerShell, ustaw parametr na *true*.

1. Jeśli `ReportOnly` nie jest ustawiona, maszyny raportują informacje o stanie DSC do konfiguracji stanu usługi Azure Automation i ściągają konfigurację lub moduły programu PowerShell. Odpowiednio ustawić parametry `ConfigurationRepositoryWeb`w `ResourceRepositoryWeb`, `ReportServerWeb` i bloki.

1. Uruchom skrypt. Teraz powinien mieć folder katalogu roboczego o nazwie *DscMetaConfigs*, który zawiera metakonfiguracje DSC programu PowerShell dla komputerów do wbudowanego (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Generowanie metakonfiguracji DSC przy użyciu poleceń cmdlet usługi Azure Automation

Jeśli ustawienia domyślne programu PowerShell DSC LC LCM są zgodne z przypadkiem użycia i chcesz na komputerach wbudowanych zarówno pobierać z i raportować do konfiguracji stanu automatyzacji platformy Azure, można wygenerować potrzebne metakonfiguracje DSC po prostu przy użyciu poleceń cmdlet usługi Azure Automation.

1. Otwórz konsolę programu PowerShell lub kod programu Visual Studio jako administrator na komputerze w środowisku lokalnym.
1. Połącz się z usługą Azure Resource Manager przy użyciu [connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. Z konta automatyzacji, na którym konfigurujesz węzły, pobierz metakonfiguracje dsc programu PowerShell dla komputerów, które chcesz mieć wbudowane.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Teraz powinien mieć folder *DscMetaConfigs,* który zawiera metakonfiguracje DSC programu PowerShell dla maszyn, które mają być wbudowane (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>Bezpieczne korzystanie z rejestracji

Maszyny mogą być bezpiecznie dołączane do konta usługi Azure Automation za pośrednictwem protokołu rejestracji dsc programu Windows Management Framework 5 DSC. Ten protokół umożliwia węzłowi DSC uwierzytelnienie na serwerze ściągania lub raportu dsc programu PowerShell DSC, w tym konfiguracji stanu automatyzacji platformy Azure. Węzeł rejestruje się z serwerem pod adresem URL rejestracji i uwierzytelnia się przy użyciu klucza rejestracji. 

Podczas rejestracji węzeł DSC i serwer ściągania/raportów DSC negocjują unikatowy certyfikat dla węzła używanego do uwierzytelniania w rejestracji post-rejestracji serwera. Ten proces uniemożliwia węzłom wbudowanym personifikowanie się nawzajem (na przykład, jeśli węzeł zostanie naruszony i zachowuje się złośliwie). 

Po rejestracji klucz rejestracji nie jest ponownie do uwierzytelniania i jest usuwany z węzła.

Aby uzyskać informacje wymagane dla protokołu rejestracji konfiguracji stanu, przejdź do witryny Azure portal i w obszarze **Ustawienia konta**wybierz pozycję **Klucze**. 

![Klucze automatyzacji platformy Azure i adres URL w okienku Klucze](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **Adres URL rejestracji:** wartość w polu **ADRESU URL.**
- **Klucz rejestracji:** Wartość w polu **Klucz dostępu podstawowego** lub w polu **Klucz dostępu pomocniczego.** Można użyć jednego z klawiszy.

Aby zwiększyć bezpieczeństwo, można ponownie wygenerować podstawowe i pomocnicze klucze dostępu konta automatyzacji w dowolnym momencie w okienku **Klucze.** Regeneracja kluczy uniemożliwia przyszłe rejestracje węzłów przy użyciu poprzednich kluczy.

## <a name="re-register-a-node"></a>Ponowne rejestrowanie węzła

Po zarejestrowaniu komputera jako węzła DSC w konfiguracji stanu automatyzacji platformy Azure może być konieczne ponowne zarejestrowanie tego węzła w przyszłości z następujących powodów:

- **Odnawianie certyfikatów:** W przypadku wersji systemu Windows Server wcześniejszych niż Windows Server 2019 każdy węzeł automatycznie negocjuje unikatowy certyfikat uwierzytelniania, który wygasa po upływie jednego roku. Jeśli certyfikat wygaśnie bez odnowienia, węzeł nie może komunikować się z usługą Azure Automation i jest oznaczony jako *Brak odpowiedzi.* 

  Obecnie protokół rejestracji dsc programu PowerShell nie może automatycznie odnawiać certyfikatów, gdy zbliżają się do wygaśnięcia i należy ponownie zarejestrować węzły po roku. Przed ponowną rejestracją upewnij się, że w każdym węźle jest uruchomiony program Windows Management Framework 5 RTM. 

    Ponowna rejestracja wykonana 90 lub mniej dni od czasu wygaśnięcia certyfikatu lub w dowolnym momencie po czasie wygaśnięcia certyfikatu powoduje wygenerowanie i użycie nowego certyfikatu. Rozwiązanie tego problemu znajduje się w systemie Windows Server 2019 i nowszych.

- **Zmiany wartości DSC LCM:** Może być konieczna zmiana [wartości LCM programu PowerShell DSC,](/powershell/scripting/dsc/managing-nodes/metaConfig4) które zostały ustawione podczas początkowej rejestracji węzła (na przykład `ConfigurationMode`). Obecnie można zmienić te wartości agenta DSC tylko poprzez ponowną rejestrację. Jedynym wyjątkiem jest wartość konfiguracji węzła przypisana do węzła. Tę wartość można zmienić bezpośrednio w usłudze Azure Automation DSC.

Węzeł można ponownie zarejestrować tak, jak początkowo został zarejestrowany, przy użyciu dowolnej metody dołączania, które są opisane w tym dokumencie. Nie trzeba wyrejestrować węzła z konfiguracji stanu usługi Azure Automation przed ponownym zarejestrowaniem go.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Rozwiązywanie problemów z dołączaniem maszyny wirtualnej platformy Azure

Konfiguracja stanu automatyzacji platformy Azure umożliwia łatwe dołączanie maszyn wirtualnych systemu Windows platformy Azure do zarządzania konfiguracją. Pod maską rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure służy do rejestrowania maszyny Wirtualnej w konfiguracji stanu automatyzacji platformy Azure. Ponieważ rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure działa asynchronicznie, śledzenie jego postępów i rozwiązywanie problemów z jego wykonaniem może być ważne.

> [!NOTE]
> Dowolna metoda dołączania maszyny Wirtualnej systemu Windows platformy Azure do konfiguracji stanu automatyzacji platformy Azure, która używa rozszerzenia konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure, może potrwać do godziny, aby usługa Azure Automation wyświetliła ją jako zarejestrowaną. To opóźnienie wynika z instalacji programu Windows Management Framework 5 na maszynie wirtualnej przez rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure, które jest wymagane do dołączania maszyny Wirtualnej do konfiguracji stanu automatyzacji platformy Azure.

Aby rozwiązać problem lub wyświetlić stan rozszerzenia konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure:

1. W witrynie Azure portal przejdź do maszyny Wirtualnej, która jest dołączana.
2. W obszarze **Ustawienia** wybierz pozycję **Rozszerzenia**. 
3. Wybierz **DSC** lub **DSCForLinux**, w zależności od systemu operacyjnego. 
4. Aby uzyskać więcej informacji o rozszerzeniu, wybierz **pozycję Wyświetl stan szczegółowy**.

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z konfiguracją żądanego stanu usługi Azure Automation (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Na przykład przy użyciu konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [ciągłe wdrażanie na maszynach wirtualnych przy użyciu konfiguracji stanu automatyzacji platformy Azure i Chocolatey](automation-dsc-cd-chocolatey.md).
