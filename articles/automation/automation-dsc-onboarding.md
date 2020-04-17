---
title: Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure
description: Jak skonfigurować maszyny do zarządzania za pomocą konfiguracji stanu automatyzacji usługi Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457743"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Dlaczego warto zarządzać komputerami za pomocą konfiguracji stanu automatyzacji platformy Azure?

Konfiguracja stanu automatyzacji platformy Azure to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnej chmurze lub lokalnym centrum danych. Dostęp do niej jest dostępny w witrynie Azure portal, wybierając **pozycję Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**. 

Usługa ta umożliwia skalowalność na tysiącach maszyn szybko i łatwo z centralnej, bezpiecznej lokalizacji. Można łatwo przywkłędować maszyny, przypisać im deklaratywnych konfiguracji i wyświetlić raporty pokazujące zgodność każdej maszyny z żądanym stanem, który określisz.

Usługa konfiguracji stanu automatyzacji platformy Azure jest dsc, jakie są elementy runbook usługi Azure Automation do skryptów programu PowerShell. Innymi słowy w taki sam sposób, w jaki usługa Azure Automation ułatwia zarządzanie skryptami programu PowerShell, pomaga również zarządzać konfiguracjami DSC. Aby dowiedzieć się więcej o korzyściach płynących z korzystania z konfiguracji stanu automatyzacji platformy Azure, zobacz [omówienie konfiguracji stanu automatyzacji platformy Azure.](automation-dsc-overview.md)

Konfiguracja stanu automatyzacji platformy Azure może służyć do zarządzania różnymi komputerami:

- Maszyny wirtualne platformy Azure
- Maszyny wirtualne platformy Azure (klasyczne)
- Fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure (w tym wystąpienia AWS EC2)
- Lokalne maszyny z systemem Linux fizyczne/wirtualne, na platformie Azure lub w chmurze innej niż Azure

Jeśli nie jesteś gotowy do zarządzania konfiguracją komputera z chmury, można użyć konfiguracji stanu automatyzacji platformy Azure jako punktu końcowego tylko do raportu. Ta funkcja umożliwia ustawienie (wypychanie) konfiguracji za pośrednictwem dsc i wyświetlanie szczegółów raportowania w usłudze Azure Automation.

> [!NOTE]
> Zarządzanie maszynami wirtualnymi platformy Azure za pomocą konfiguracji stanu automatyzacji platformy Azure jest uwzględniane bez dodatkowych opłat, jeśli zainstalowana wersja rozszerzenia konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure jest większa niż 2,70. Aby uzyskać więcej informacji, zobacz [**strona cennika automatyzacji**](https://azure.microsoft.com/pricing/details/automation/).

W poniższych sekcjach tego artykułu opisano, jak można dołączać maszyny wymienione powyżej do konfiguracji stanu automatyzacji platformy Azure.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="onboarding-azure-vms"></a>Dołączanie maszyn wirtualnych platformy Azure

Konfiguracja stanu automatyzacji platformy Azure umożliwia łatwe dołączanie maszyn wirtualnych platformy Azure do zarządzania konfiguracją przy użyciu witryny Azure portal, szablonów usługi Azure Resource Manager lub programu PowerShell. Pod maską i bez konieczności zdalnego dostępu administratora do maszyny Wirtualnej rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej rejestruje maszynę wirtualną z konfiguracją stanu automatyzacji platformy Azure. Ponieważ rozszerzenie platformy Azure działa asynchronicznie, kroki, aby śledzić jego postęp lub rozwiązywać problemy z nim znajdują się w sekcji [rozwiązywanie problemów z maszyną wirtualną platformy Azure dołączania](#troubleshooting-azure-virtual-machine-onboarding) w tym artykule.

> [!NOTE]
>Wdrażanie dsc do węzła systemu Linux używa **/tmp** folderu. Moduły, `nxautomation` takie jak są tymczasowo pobierane do weryfikacji przed zainstalowaniem ich w odpowiednich lokalizacjach. Aby upewnić się, że moduły są poprawnie instalowane, agent usługi Log Analytics dla systemu Linux potrzebuje uprawnień do odczytu/zapisu w folderze **/tmp.**<br><br>
>Agent analizy dzienników dla `omsagent` systemu Linux działa jako użytkownik. Aby udzielić użytkownikowi uprawnień `omsagent` do zapisu `setfacl -m u:omsagent:rwx /tmp`>, uruchom polecenie .

### <a name="onboard-a-vm-using-azure-portal"></a>Wbudowana maszyna wirtualna przy użyciu witryny Azure portal

Aby dołączać konfigurację usługi Azure VM do usługi Azure Automation State za pośrednictwem [witryny Azure portal:](https://portal.azure.com/)

1. Przejdź do konta usługi Azure Automation, na którym mają być dołączane maszyny wirtualne. 

2. Na stronie Konfiguracja stanu wybierz kartę **Węzły,** a następnie kliknij pozycję **Dodaj**.

3. Wybierz maszynę wirtualną do wbudowanej.

4. Jeśli na komputerze nie zainstalowano rozszerzenia żądanego stanu programu PowerShell i stan zasilania jest uruchomiony, kliknij przycisk **Połącz**.

5. W obszarze **Rejestracja**wprowadź [wartości lokalnego programu PowerShell DSC programu Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) wymagane dla przypadku użycia. Opcjonalnie można wprowadzić konfigurację węzła, aby przypisać do maszyny Wirtualnej.

![Dołączania](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Dołączanie maszyny Wirtualnej przy użyciu szablonów usługi Azure Resource Manager

Maszynę Wirtualną można wdrożyć i wdrożyć w konfiguracji stanu automatyzacji platformy Azure przy użyciu szablonów usługi Azure Resource Manager. Zobacz [Serwer zarządzany przez usługę konfiguracji żądanego stanu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) dla przykładowego szablonu, który dołącza istniejącą maszynę wirtualną do konfiguracji stanu automatyzacji platformy Azure. Jeśli zarządzasz zestawem skalowania maszyny wirtualnej, zobacz przykładowy szablon w [konfiguracji zestawu skalowania maszyny wirtualnej zarządzanej przez usługę Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Urządzenia pokładowe korzystające z programu PowerShell

Polecenia cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) w programie PowerShell można użyć do wbudowanych maszyn wirtualnych do konfiguracji stanu automatyzacji platformy Azure. 

> [!NOTE]
>Polecenie `Register-AzAutomationDscNode` cmdlet jest obecnie implementowane tylko dla komputerów z systemem Windows, ponieważ wyzwala tylko rozszerzenie systemu Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Rejestrowanie maszyn wirtualnych w ramach subskrypcji platformy Azure

Najlepszym sposobem rejestrowania maszyn wirtualnych z innych subskrypcji platformy Azure jest użycie rozszerzenia DSC w szablonie wdrażania usługi Azure Resource Manager. Przykłady znajdują się w [rozszerzeniu konfiguracji żądanego stanu z szablonami usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Aby znaleźć klucz rejestracji i adres URL rejestracji do użycia jako parametry w [szablonie,](#onboarding-securely-using-registration) zobacz Dołączanie bezpiecznie przy użyciu rejestracji sekcji w tym artykule.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Dołączanie fizycznych/wirtualnych maszyn z systemem Windows

Serwery systemu Windows z systemem Windows z systemem lokalnym lub w innych środowiskach w chmurze (w tym wystąpienia AWS EC2) można skonfigurować w usłudze Azure Automation State. Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że najnowsza wersja [WMF 5](https://aka.ms/wmf5latest) jest zainstalowana na komputerach do wbudowanej konfiguracji stanu automatyzacji platformy Azure. Ponadto w 5 WMF musi być zainstalowany na komputerze, który jest używany do operacji dołączania.
1. Postępuj zgodnie ze wskazówkami w sekcji [Generowanie metakonfiguracji DSC,](#generating-dsc-metaconfigurations) aby utworzyć folder zawierający wymagane metakonfiguracje DSC. 
1. Użyj następującego polecenia cmdlet, aby zdalnie zastosować metakonfiguracje dsc programu PowerShell do komputerów, które mają być wbudowane. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Jeśli nie można zdalnie zastosować metakonfiguracji dsc programu PowerShell, skopiuj folder **metakonfiguracji** do komputerów, na których się włączysz. Następnie dodaj kod, aby wywołać [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lokalnie na komputerach.
1. Korzystając z witryny Azure portal lub poleceń cmdlet, sprawdź, czy maszyny do wbudowanego są wyświetlane jako węzły konfiguracji stanu zarejestrowane na koncie usługi Azure Automation.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Dołączanie fizycznych/wirtualnych maszyn z systemem Linux

Można dołączać serwery systemu Linux działające lokalnie lub w innych środowiskach chmury do konfiguracji stanu automatyzacji platformy Azure. Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że najnowsza wersja [konfiguracji żądanego stanu programu PowerShell dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowana na komputerach, aby była zainstalowana na pokładzie konfiguracji stanu automatyzacji platformy Azure.
2. Jeśli [program PowerShell DSC Local Configuration Manager domyślnie](/powershell/scripting/dsc/managing-nodes/metaConfig4) pasuje do przypadku użycia i chcesz na komputerach wbudowanych, tak aby zarówno pobierać z i raport do konfiguracji stanu automatyzacji platformy Azure:

   - Na każdym komputerze z systemem Linux do `Register.py` wbudowanej konfiguracji stanu automatyzacji platformy Azure należy użyć do wbudowanego przy użyciu domyślnych ustawień domyślnych lokalnego programu PowerShell DSC configuration manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta automatyzacji, zobacz [dołączanie bezpiecznie przy użyciu rejestracji](#onboarding-securely-using-registration) sekcji tego artykułu.

3. Jeśli domyślne ustawienia domyślne lokalnego programu PowerShell DSC (Local Configuration Manager) (LCM) nie są zgodne z przypadkiem użycia lub chcesz na komputerach wbudowanych, które zgłaszają tylko do konfiguracji stanu automatyzacji platformy Azure, wykonaj kroki 4-7. W przeciwnym razie przejdź bezpośrednio do kroku 7.

4. Postępuj zgodnie ze wskazówkami w sekcji [Generowanie metakonfiguracji DSC,](#generating-dsc-metaconfigurations) aby utworzyć folder zawierający wymagane metakonfiguracje DSC.

5. Upewnij się, że najnowsza wersja [WMF 5](https://aka.ms/wmf5latest) jest zainstalowana na komputerze używanym do dołączania.

6. Dodaj kod w następujący sposób, aby zdalnie zastosować metakonfiguracje dsc programu PowerShell do komputerów, które mają być wbudowane.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Jeśli nie można zdalnie zastosować metakonfiguracji dsc programu PowerShell, skopiuj metakonfiguracje odpowiadające maszynom zdalnym z folderu opisanego w kroku 4 na komputery z systemem Linux.

8. Dodaj kod `Set-DscLocalConfigurationManager.py` do wywoływania lokalnie na każdym komputerze z systemem Linux do wbudowanej konfiguracji stanu automatyzacji platformy Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Korzystając z witryny Azure portal lub poleceń cmdlet, upewnij się, że maszyny do wbudowanego teraz są wyświetlane jako węzły DSC zarejestrowane na koncie usługi Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generowanie metakonfiguracji DSC

Aby przywkłęka dowolnego komputera do konfiguracji stanu automatyzacji platformy Azure, można wygenerować [metakonfigurację DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Ta konfiguracja nakazuje agentowi DSC, aby pobierał z i/lub raportować konfigurację stanu automatyzacji platformy Azure. Metakonfigurację DSC dla konfiguracji stanu automatyzacji usługi Azure automation można wygenerować przy użyciu konfiguracji dsc programu PowerShell lub poleceń cmdlet programu Azure Automation PowerShell.

> [!NOTE]
> Metakonfiguracje DSC zawierają wpisy tajne potrzebne do wbudowanego komputera do konta automatyzacji do zarządzania. Upewnij się, że należy prawidłowo chronić wszystkie utworzone metakonfiguracje DSC lub usunąć je po użyciu.

Obsługa serwera proxy dla metakonfiguracji jest kontrolowana przez LCM, który jest aparatem DSC programu Windows PowerShell. LCM działa na wszystkich węzłach docelowych i jest odpowiedzialny za wywoływanie zasobów konfiguracji, które są zawarte w skrypcie metakonfiguracji DSC. Obsługę serwera proxy można uwzględnić w metakonfiguracji, dołączając definicje adresu `ConfigurationRepositoryWeb`URL `ResourceRepositoryWeb`serwera `ReportServerWeb` proxy i poświadczeń serwera proxy zgodnie z potrzebami w programach , i blokach. Zobacz [Konfigurowanie lokalnego menedżera konfiguracji](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generowanie metakonfiguracji DSC przy użyciu konfiguracji DSC

1. Otwórz vscode (lub ulubiony edytor) jako administrator na komputerze w środowisku lokalnym. Urządzenie musi mieć zainstalowaną najnowszą wersję [WMF 5.](https://aka.ms/wmf5latest)
1. Skopiuj lokalny skrypt. Ten skrypt zawiera konfigurację dsc programu PowerShell do tworzenia konfiguracji metakonfiguracji i polecenie rozpoczęcia tworzenia metakonfiguracji.

    > [!NOTE]
    > W nazwach konfiguracji węzła konfiguracji stanu rozróżniana jest wielkość liter w witrynie Azure portal. Jeśli sprawa jest niezgodna, węzeł nie pojawi się na karcie **Węzły.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations
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

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Wypełnij klucz rejestracji i adres URL konta Automatyzacji, a także nazwy maszyn, które mają być wbudowane. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta automatyzacji, zobacz [sekcję Dołączanie bezpiecznie przy użyciu rejestracji.](#onboarding-securely-using-registration)

1. Jeśli chcesz, aby maszyny zgłaszały informacje o stanie DSC do konfiguracji stanu usługi Azure `ReportOnly` Automation, ale nie ściągały konfiguracji lub modułów programu PowerShell, ustaw parametr na true.

1. Jeśli `ReportOnly` nie jest ustawiona, maszyny raportują informacje o stanie DSC do konfiguracji stanu usługi Azure Automation i ściągają konfigurację lub moduły programu PowerShell. Odpowiednio ustawić parametry `ConfigurationRepositoryWeb`w `ResourceRepositoryWeb`, `ReportServerWeb` i bloki.

1. Uruchom skrypt. Teraz powinien mieć folder katalogu roboczego o nazwie **DscMetaConfigs**, zawierający metakonfiguracje DSC programu PowerShell dla komputerów do wbudowanego (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generowanie metakonfiguracji DSC przy użyciu poleceń cmdlet usługi Azure Automation

Jeśli ustawienia domyślne programu PowerShell DSC LCM są zgodne z przypadkiem użycia i chcesz na komputerach wbudowanych zarówno pobierać z i raportować do konfiguracji stanu automatyzacji platformy Azure, można wygenerować potrzebne metakonfiguracje DSC po prostu przy użyciu poleceń cmdlet usługi Azure Automation.

1. Otwórz konsolę programu PowerShell lub vscode jako administrator na komputerze w środowisku lokalnym.
2. Połącz się z usługą Azure Resource Manager przy użyciu [connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Pobierz metakonfiguracje DSC programu PowerShell dla maszyn, które chcesz mieć wbudowane, z konta automatyzacji, na którym konfigurujesz węzły.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Teraz powinien mieć folder **DscMetaConfigs zawierający** metakonfiguracje DSC programu PowerShell dla komputerów do wbudowanego (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Bezpieczne dołączanie przy użyciu rejestracji

Maszyny mogą bezpiecznie dołączać do konta usługi Azure Automation za pośrednictwem protokołu rejestracji WMF 5 DSC. Ten protokół umożliwia węzłowi DSC uwierzytelnienie na serwerze ściągania lub raportu dsc programu PowerShell DSC, w tym konfiguracji stanu automatyzacji platformy Azure. Węzeł rejestruje się z serwerem pod adresem URL rejestracji i uwierzytelnia się przy użyciu klucza rejestracji. Podczas rejestracji węzeł DSC i serwer ściągania/raportu DSC negocjują unikatowy certyfikat dla węzła, który ma być używany do uwierzytelniania w rejestracji post-rejestracji serwera. Ten proces uniemożliwia węzłom wbudowanym personifikowanie się nawzajem, na przykład, jeśli węzeł jest zagrożony i zachowuje się złośliwie. Po rejestracji klucz rejestracji nie jest ponownie używany do uwierzytelniania i jest usuwany z węzła.

Informacje wymagane dla protokołu rejestracji konfiguracji stanu można uzyskać z **kluczy** w obszarze **Ustawienia konta** w witrynie Azure portal. 

![Klucze automatyzacji platformy Azure i adres URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adres URL rejestracji to pole ADRESU URL na stronie Klucze.
- Klucz rejestracji to wartość pola **Klucz dostępu podstawowego** lub **klucza dostępu pomocniczego** na stronie Klucze. Można użyć jednego z tych kluczy.

Aby zwiększyć bezpieczeństwo, można ponownie wygenerować podstawowe i pomocnicze klucze dostępu konta automatyzacji w dowolnym momencie na keys strony. Regeneracja kluczy uniemożliwia przyszłe rejestracje węzłów przy użyciu poprzednich kluczy.

## <a name="re-registering-a-node"></a>Ponowna rejestracja węzła

Po zarejestrowaniu komputera jako węzła DSC w konfiguracji stanu automatyzacji usługi Azure, istnieje kilka powodów, dla których może być konieczne ponowne zarejestrowanie tego węzła w przyszłości.

- **Odnowienie certyfikatu.** W przypadku wersji systemu Windows Server przed systemem Windows Server 2019 każdy węzeł automatycznie negocjuje unikatowy certyfikat uwierzytelniania, który wygasa po upływie jednego roku. Jeśli certyfikat wygaśnie bez odnowienia, węzeł nie może `Unresponsive`komunikować się z usługą Azure Automation i jest oznaczony . Obecnie protokół rejestracji dsc programu PowerShell nie może automatycznie odnawiać certyfikatów, gdy zbliżają się do wygaśnięcia i należy ponownie zarejestrować węzły po roku. Przed ponowną rejestracją upewnij się, że każdy węzeł jest uruchomiony WMF 5 RTM. 

    Ponowna rejestracja wykonana 90 dni lub mniej od czasu wygaśnięcia certyfikatu lub w dowolnym momencie po czasie wygaśnięcia certyfikatu powoduje wygenerowanie i użycie nowego certyfikatu. Rozwiązanie tego problemu znajduje się w systemie Windows Server 2019 i nowszych.

- **Zmiany wartości DSC LCM.** Może być konieczna zmiana [wartości LCM dsc programu PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) ustawionych podczas początkowej rejestracji węzła, `ConfigurationMode`na przykład . Obecnie można zmienić te wartości agenta DSC tylko poprzez ponowną rejestrację. Jedynym wyjątkiem jest wartość konfiguracji węzła przypisana do węzła. Można to zmienić bezpośrednio w usłudze Azure Automation DSC.

Węzeł można ponownie zarejestrować, tak jak początkowo zarejestrowano węzeł, korzystając z dowolnej metody dołączania opisanej w tym dokumencie. Nie trzeba wyrejestrować węzła z konfiguracji stanu usługi Azure Automation przed ponownym zarejestrowaniem go.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Rozwiązywanie problemów z dołączaniem maszyny wirtualnej platformy Azure

Konfiguracja stanu automatyzacji platformy Azure umożliwia łatwe dołączanie maszyn wirtualnych systemu Windows platformy Azure do zarządzania konfiguracją. Pod maską rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure służy do rejestrowania maszyny Wirtualnej w konfiguracji stanu automatyzacji platformy Azure. Ponieważ rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure działa asynchronicznie, śledzenie jego postępów i rozwiązywanie problemów z jego wykonaniem może być ważne.

> [!NOTE]
> Dowolna metoda dołączania maszyny Wirtualnej systemu Windows platformy Azure do konfiguracji stanu automatyzacji platformy Azure, która używa rozszerzenia konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure, może potrwać do godziny, aby usługa Azure Automation wyświetliła ją jako zarejestrowaną. To opóźnienie jest spowodowane instalacją WMF 5 na maszynie wirtualnej przez rozszerzenie konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure, które jest wymagane do dołączania maszyny Wirtualnej do konfiguracji stanu automatyzacji usługi Azure.

Aby rozwiązać problem lub wyświetlić stan rozszerzenia konfiguracji żądanego stanu maszyny Wirtualnej platformy Azure:

1. W witrynie Azure portal przejdź do maszyny Wirtualnej, która jest dołączana.
2. Kliknij **pozycję Rozszerzenia** w obszarze **Ustawienia**. 
3. Teraz wybierz **DSC** lub **DSCForLinux**, w zależności od systemu operacyjnego. 
4. Aby uzyskać więcej informacji, możesz kliknąć pozycję **Wyświetl szczegółowy stan**.

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z konfiguracją żądanego stanu usługi Azure Automation (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o tym, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Na przykład przy użyciu konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Przykład użycia: Ciągłe wdrażanie na maszynach wirtualnych przy użyciu konfiguracji stanu automatyzacji platformy Azure i chocolatey](automation-dsc-cd-chocolatey.md).
