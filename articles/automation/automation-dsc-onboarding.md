---
title: Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu
description: Jak skonfigurować maszyny do zarządzania przy użyciu konfiguracji stanu Azure Automation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457743"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Dlaczego warto zarządzać maszynami z użyciem Azure Automation stanu konfiguracji?

Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Jest on dostępny w Azure Portal przez wybranie pozycji **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**. 

Ta usługa umożliwia szybkie i łatwe skalowanie w tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

Usługa konfiguracji stanu Azure Automation to DSC, co Azure Automation elementów Runbook do obsługi skryptów programu PowerShell. Innymi słowy, w taki sam sposób, jaki Azure Automation ułatwia zarządzanie skryptami programu PowerShell, ułatwia również zarządzanie konfiguracjami DSC. Aby dowiedzieć się więcej na temat korzyści z używania konfiguracji stanu Azure Automation, zobacz [Konfiguracja stanu Azure Automation — Omówienie](automation-dsc-overview.md).

Azure Automation konfiguracja stanu może służyć do zarządzania różnymi maszynami:

- Maszyny wirtualne platformy Azure
- Azure Virtual Machines (klasyczny)
- Fizyczne/wirtualne maszyny z systemem Windows w środowisku lokalnym lub w chmurze innej niż Azure (w tym wystąpienia AWS EC2)
- Fizyczne/wirtualne maszyny z systemem Linux lokalnie, na platformie Azure lub w chmurze innej niż Azure

Jeśli nie jesteś gotowy do zarządzania konfiguracją maszyny z chmury, możesz użyć konfiguracji stanu Azure Automation jako punktu końcowego tylko do raportowania. Ta funkcja umożliwia ustawianie (wypychania) konfiguracji za poorednictwem DSC i wyświetlanie szczegółów raportowania w Azure Automation.

> [!NOTE]
> Zarządzanie maszynami wirtualnymi platformy Azure za pomocą konfiguracji stanu Azure Automation jest bezpłatne, jeśli zainstalowana wersja rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure jest większa niż 2,70. Aby uzyskać więcej informacji, zobacz [**stronę z cennikiem usługi Automation**](https://azure.microsoft.com/pricing/details/automation/).

W poniższych sekcjach tego artykułu opisano, jak można dołączyć maszyny wymienione powyżej do konfiguracji stanu Azure Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Dołączanie maszyn wirtualnych platformy Azure

Azure Automation konfiguracja stanu umożliwia łatwe dołączanie maszyn wirtualnych platformy Azure w celu zarządzania konfiguracją przy użyciu Azure Portal, szablonów Azure Resource Manager lub programu PowerShell. Na wyciągnięcieu i bez administratora, który ma zdalny dostęp do maszyny wirtualnej, rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure rejestruje maszynę wirtualną z konfiguracją stanu Azure Automation. Ponieważ rozszerzenie platformy Azure jest uruchomione asynchronicznie, należy wykonać kroki umożliwiające śledzenie jego postępu lub Rozwiązywanie problemów z sekcją Rozwiązywanie problemów z dołączaniem do [maszyny wirtualnej platformy Azure](#troubleshooting-azure-virtual-machine-onboarding) w tym artykule.

> [!NOTE]
>Wdrożenie DSC w węźle systemu Linux używa folderu **/tmp** . Moduły takie jak `nxautomation` są tymczasowo pobierane do weryfikacji przed ich zainstalowaniem w odpowiednich lokalizacjach. Aby zapewnić poprawne zainstalowanie modułów, Agent Log Analytics dla systemu Linux musi mieć uprawnienia do odczytu/zapisu w folderze **/tmp** .<br><br>
>Agent Log Analytics dla systemu Linux działa jako `omsagent` użytkownik. Aby udzielić `omsagent` użytkownikowi uprawnień do >zapisu, uruchom polecenie `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-a-vm-using-azure-portal"></a>Dołączanie maszyny wirtualnej przy użyciu Azure Portal

Aby dołączyć maszynę wirtualną platformy Azure do Azure Automation konfiguracji stanu za pomocą [Azure Portal](https://portal.azure.com/):

1. Przejdź do konta Azure Automation, do którego chcesz dołączyć maszyny wirtualne. 

2. Na stronie Konfiguracja stanu wybierz kartę **węzły** , a następnie kliknij przycisk **Dodaj**.

3. Wybierz maszynę wirtualną do dołączenia.

4. Jeśli na komputerze nie zainstalowano rozszerzenia żądanego stanu programu PowerShell, a stan jest uruchomiony, kliknij przycisk **Połącz**.

5. W obszarze **rejestracja**wprowadź [wartości lokalnych Configuration Manager programu PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig) , które są wymagane w przypadku użycia. Opcjonalnie możesz wprowadzić konfigurację węzła, która ma zostać przypisana do maszyny wirtualnej.

![dołączania](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Dołączanie maszyny wirtualnej przy użyciu szablonów Azure Resource Manager

Aby Azure Automation konfigurację stanu przy użyciu szablonów Azure Resource Manager, można wdrożyć i dołączyć maszynę wirtualną. Zobacz temat [serwer zarządzany przez usługę konfiguracji żądanego stanu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) , aby zapoznać się z przykładowym szablonem służącym do dołączania istniejącej maszyny wirtualnej w celu Azure Automation konfiguracji stanu. Jeśli zarządzasz zestawem skalowania maszyn wirtualnych, zobacz przykładowy szablon w [konfiguracji zestawu skalowania maszyn wirtualnych zarządzanych przez Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Dodawanie maszyn przy użyciu programu PowerShell

Aby dołączać maszyny wirtualne do Azure Automation konfiguracji stanu, można użyć polecenia cmdlet [register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) w programie PowerShell. 

> [!NOTE]
>`Register-AzAutomationDscNode` Polecenie cmdlet jest obecnie zaimplementowane tylko dla maszyn z systemem Windows, ponieważ wyzwala tylko rozszerzenie systemu Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Rejestrowanie maszyn wirtualnych w ramach subskrypcji platformy Azure

Najlepszym sposobem na zarejestrowanie maszyn wirtualnych z innych subskrypcji platformy Azure jest użycie rozszerzenia DSC w szablonie wdrażania Azure Resource Manager. Przykłady są dostępne w [rozszerzeniu konfiguracji żądanego stanu z szablonami Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Aby znaleźć klucz rejestracji i adres URL rejestracji do użycia jako parametry szablonu, zapoznaj się z sekcją dołączanie [za pomocą rejestracji](#onboarding-securely-using-registration) w tym artykule.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Dołączanie fizycznych/wirtualnych maszyn z systemem Windows

Aby Azure Automation konfigurację stanu, można dołączyć serwery Windows działające lokalnie lub w innych środowiskach chmur (w tym wystąpieniach usługi AWS EC2). Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że na komputerach jest zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest) , aby dołączać konfigurację stanu Azure Automation. Ponadto program WMF 5 musi być zainstalowany na komputerze używanym do operacji dołączania.
1. Postępuj zgodnie z instrukcjami w sekcji [generowanie konfiguracyjnych konfiguracji DSC](#generating-dsc-metaconfigurations) , aby utworzyć folder zawierający wymagane konfiguracje konfiguracji DSC. 
1. Użyj poniższego polecenia cmdlet, aby zdalnie zastosować konfiguracje konfiguracji DSC programu PowerShell do maszyn, które chcesz dołączyć. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Jeśli nie można zastosować zdalnego konfigurowania konfiguracji DSC programu PowerShell, skopiuj folder **konfiguracje** do maszyn, które są dołączane. Następnie Dodaj kod do wywołania [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) lokalnie na maszynach.
1. Za pomocą Azure Portal lub poleceń cmdlet Sprawdź, czy maszyny do dołączenia są wyświetlane jako węzły konfiguracji stanu zarejestrowane na koncie usługi Azure Automation.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Dołączanie fizycznych/wirtualnych maszyn z systemem Linux

Aby Azure Automation konfigurację stanu, można dołączyć serwery z systemem Linux działające lokalnie lub w innych środowiskach w chmurze. Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że Najnowsza wersja [konfiguracji żądanego stanu programu PowerShell dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowana na komputerach w celu dołączenia do konfiguracji stanu Azure Automation.
2. Jeśli [lokalne Configuration Manager konfiguracji DSC programu PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) są zgodne z przypadkiem użycia, i chcesz dołączyć maszyny, aby były zarówno ściągane, jak i raportowane Azure Automation konfiguracja stanu:

   - Na każdym komputerze z systemem Linux, aby dołączać Azure Automation konfigurację `Register.py` stanu, użyj narzędzia do dołączania za pomocą ustawień domyślnych Configuration Manager lokalnych dla programu PowerShell.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zapoznaj się z sekcją "Dołączanie [bezpieczne przy użyciu rejestracji](#onboarding-securely-using-registration) " w tym artykule.

3. Jeśli domyślne Configuration Manager (LCM) lokalnego programu PowerShell nie są zgodne z przypadkiem użycia lub chcesz dołączyć maszyny, które tylko raportują do Azure Automation konfiguracji stanu, wykonaj kroki 4-7. W przeciwnym razie przejdź bezpośrednio do kroku 7.

4. Postępuj zgodnie z instrukcjami w sekcji [generowanie konfiguracyjnych konfiguracji DSC](#generating-dsc-metaconfigurations) , aby utworzyć folder zawierający wymagane konfiguracje konfiguracji DSC.

5. Upewnij się, że na maszynie używanej do dołączania jest zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest) .

6. Dodaj kod w następujący sposób, aby zdalnie zastosować konfiguracje konfiguracji DSC programu PowerShell do maszyn, które chcesz dołączyć.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Jeśli nie można zastosować zdalnego konfigurowania konfiguracji DSC programu PowerShell, skopiuj konfiguracje konfiguracji odpowiadające maszynom zdalnym z folderu opisanego w kroku 4 do maszyn z systemem Linux.

8. Dodaj kod do wywołania `Set-DscLocalConfigurationManager.py` lokalnego na każdym komputerze z systemem Linux w celu dołączenia do konfiguracji stanu Azure Automation.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Za pomocą Azure Portal lub poleceń cmdlet upewnij się, że maszyny do dołączenia są teraz wyświetlane jako węzły DSC zarejestrowane na koncie usługi Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generowanie konfiguracji DSC

Aby dodać maszynę do Azure Automation konfigurację stanu, można wygenerować [konfigurację konfiguracji DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Ta konfiguracja nakazuje agentowi DSC ściąganie z i/lub raportów w celu Azure Automation konfiguracji stanu. Konfigurację konfiguracji DSC dla konfiguracji stanu Azure Automation można wygenerować przy użyciu konfiguracji DSC programu PowerShell lub poleceń cmdlet programu PowerShell Azure Automation.

> [!NOTE]
> Konfiguracje konfiguracji DSC zawierają wpisy tajne, które są konieczne do dołączenia komputera do konta usługi Automation na potrzeby zarządzania. Upewnij się, że wszystkie utworzone konfiguracje konfiguracji DSC są prawidłowo chronione, lub usuń je po użyciu.

Obsługa serwera proxy dla konfiguracji z systemem jest kontrolowana przez LCM, który jest aparatem DSC środowiska Windows PowerShell. LCM działa na wszystkich węzłach docelowych i jest odpowiedzialny za wywoływanie zasobów konfiguracyjnych, które znajdują się w skrypcie konfiguracji DSC. Obsługę proxy można uwzględnić w konfiguracji `ConfigurationRepositoryWeb`, dołączając definicje adresu URL serwera proxy i poświadczenia serwera proxy zgodnie z wymaganiami w blokach, `ResourceRepositoryWeb`i. `ReportServerWeb` Zobacz [konfigurowanie Configuration Manager lokalnego](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generuj konfiguracje konfiguracji DSC przy użyciu konfiguracji DSC

1. Otwórz programu vscode (lub ulubiony Edytor) jako administrator na komputerze w środowisku lokalnym. Na komputerze musi być zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest) .
1. Skopiuj następujący skrypt lokalnie. Ten skrypt zawiera konfigurację DSC programu PowerShell służącą do tworzenia konfiguracji, a także polecenie do uruchamiania tworzenia konfiguracji.

    > [!NOTE]
    > W Azure Portal nazwy konfiguracji węzła konfiguracji stanu są rozróżniane wielkości liter. Jeśli wielkość liter nie jest zgodna, węzeł nie będzie wyświetlany na karcie **węzły** .

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

1. Wprowadź klucz rejestracji i adres URL dla konta usługi Automation, a także nazwy maszyn do dołączenia. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz sekcję [rejestracja](#onboarding-securely-using-registration) , która jest bezpieczna.

1. Jeśli chcesz, aby maszyny raportują informacje o stanie DSC do Azure Automation konfiguracji stanu, ale nie pobieraj konfiguracji lub modułów programu PowerShell `ReportOnly` , ustaw dla parametru wartość true.

1. Jeśli `ReportOnly` nie jest ustawiona, komputery raportują informacje o stanie DSC w celu Azure Automation konfiguracji stanu i konfiguracji ściągania lub modułów programu PowerShell. Ustaw odpowiednio parametry w `ConfigurationRepositoryWeb`blokach `ResourceRepositoryWeb`,, `ReportServerWeb` i.

1. Uruchom skrypt. Teraz powinien istnieć folder katalogu roboczego o nazwie **DscMetaConfigs**, zawierający konfiguracje konfiguracji DSC programu PowerShell dla maszyn, które mają zostać dołączone (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generuj konfiguracje konfiguracji DSC przy użyciu Azure Automation poleceń cmdlet

Jeśli domyślne ustawienia LCM programu PowerShell są zgodne z przypadkiem użycia i chcesz dołączyć maszyny do obu ściągających i raportów, aby Azure Automation konfigurację stanu, można wygenerować wymagane konfiguracje konfiguracji DSC, używając Azure Automation poleceń cmdlet.

1. Otwórz konsolę programu PowerShell lub programu vscode jako administrator na komputerze w środowisku lokalnym.
2. Połącz się z Azure Resource Manager przy użyciu polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Pobierz konfiguracje konfiguracji DSC programu PowerShell dla maszyn, które chcesz dołączyć do konta usługi Automation, w którym konfigurujesz węzły.

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

1. Teraz należy mieć folder **DscMetaConfigs** zawierający konfiguracje konfiguracji DSC programu PowerShell dla maszyn do dołączenia (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Bezpieczne dołączanie przy użyciu rejestracji

Maszyny mogą bezpiecznie dołączać do konta Azure Automation przy użyciu protokołu rejestracji DSC programu WMF 5. Ten protokół umożliwia uwierzytelnianie węzła DSC na serwerze ściągania lub raportów DSC programu PowerShell, w tym konfiguracji stanu Azure Automation. Węzeł rejestruje się na serwerze w adresie URL rejestracji i uwierzytelnia przy użyciu klucza rejestracji. Podczas rejestracji, w węźle DSC i serwerze ściągania/raportów DSC jest negocjowany unikatowy certyfikat dla węzła, który ma być używany do uwierzytelniania na serwerze po rejestracji. Ten proces uniemożliwia dołączenie węzłów od siebie nawzajem, na przykład w przypadku naruszenia bezpieczeństwa i niebezpiecznego węzła. Po rejestracji klucz rejestracji nie jest używany do uwierzytelniania ponownie i jest usuwany z węzła.

Informacje wymagane do protokołu rejestracji konfiguracji stanu można uzyskać z **kluczy** w obszarze **ustawienia konta** w Azure Portal. 

![Klucze i adres URL usługi Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adres URL rejestracji to pole adresu URL na stronie klucze.
- Klucz rejestracji to wartość pola **podstawowego klucza dostępu** lub pola **pomocniczy klucz dostępu** na stronie klucze. Można użyć dowolnego klucza.

Aby zwiększyć bezpieczeństwo, można w dowolnym momencie wygenerować ponownie podstawowy i pomocniczy klucz dostępu konta usługi Automation na stronie klucze. Ponowne generowanie kluczy uniemożliwia rejestracje węzłów w przyszłości przed użyciem poprzednich kluczy.

## <a name="re-registering-a-node"></a>Ponowne rejestrowanie węzła

Po zarejestrowaniu maszyny jako węzła DSC w konfiguracji stanu Azure Automation istnieje kilka powodów, dla których może być konieczne ponowne zarejestrowanie tego węzła w przyszłości.

- **Odnowienie certyfikatu.** W przypadku wersji systemu Windows Server starszych niż Windows Server 2019 każdy węzeł automatycznie negocjuje unikatowy certyfikat na potrzeby uwierzytelniania, który wygaśnie po upływie jednego roku. Jeśli certyfikat wygaśnie bez odnowienia, węzeł nie może komunikować się z Azure Automation i jest oznaczony `Unresponsive`. Obecnie protokół rejestracji DSC programu PowerShell nie może automatycznie odnawiać certyfikatów, gdy zbliżają się do wygaśnięcia, i należy ponownie zarejestrować węzły po roku. Przed ponownym zarejestrowaniem upewnij się, że w każdym węźle jest uruchomiony program WMF 5 RTM. 

    Ponowne zarejestrowanie wykonane przez 90 dni lub mniej od czasu wygaśnięcia certyfikatu lub w dowolnym momencie po upływie czasu wygaśnięcia certyfikatu powoduje wygenerowanie i użycie nowego certyfikatu. Rozwiązanie tego problemu jest zawarte w systemie Windows Server 2019 lub nowszym.

- **Zmiany w wartościach LCM DSC.** Może być konieczne zmiana [wartości LCM usługi POWERSHELL DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) ustawionych podczas początkowej rejestracji węzła, na przykład `ConfigurationMode`. Obecnie te wartości agenta DSC można zmienić tylko przez ponowną rejestrację. Jedynym wyjątkiem jest wartość konfiguracji węzła przypisana do węzła. Można to zmienić w Azure Automation DSC bezpośrednio.

Węzeł można ponownie zarejestrować tak jak początkowo zarejestrowano węzeł przy użyciu dowolnej metody dołączania opisanej w tym dokumencie. Nie musisz wyrejestrować węzła z konfiguracji stanu Azure Automation przed jego ponownym zarejestrowaniem.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Rozwiązywanie problemów z dołączaniem do maszyny wirtualnej platformy Azure

Azure Automation konfiguracja stanu umożliwia łatwe dołączanie maszyn wirtualnych z systemem Windows Azure na potrzeby zarządzania konfiguracją. Pod wyciągnięciem, rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure służy do rejestrowania maszyny wirtualnej z konfiguracją stanu Azure Automation. Ponieważ rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure jest uruchamiane asynchronicznie, śledzenie jego postępu i rozwiązywanie problemów może być ważne.

> [!NOTE]
> Dowolna metoda dołączania maszyny wirtualnej z systemem Windows na platformie Azure w celu Azure Automation konfiguracji stanu korzystającej z rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure może potrwać do godziny, aby Azure Automation pokazać ją jako zarejestrowaną. To opóźnienie jest spowodowane instalacją programu WMF 5 na maszynie wirtualnej przez rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure, które jest wymagane do dołączenia maszyny wirtualnej do konfiguracji stanu Azure Automation.

Aby rozwiązać problem lub wyświetlić stan rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure:

1. W Azure Portal przejdź do maszyny wirtualnej, która jest dołączana.
2. W obszarze **Ustawienia**kliknij pozycję **rozszerzenia** . 
3. Teraz wybierz opcję **DSC** lub **DSCForLinux**, w zależności od używanego systemu operacyjnego. 
4. Aby uzyskać więcej informacji, kliknij pozycję **Wyświetl szczegółowy stan**.

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów przy użyciu konfiguracji żądanego stanu Azure Automation (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [wprowadzenie do konfiguracji stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [przykład użycia: ciągłe wdrażanie na maszynach wirtualnych przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md).
