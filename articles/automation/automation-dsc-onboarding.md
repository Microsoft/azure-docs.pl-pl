---
title: Włącz konfigurację stanu Azure Automation
description: W tym artykule opisano sposób konfigurowania maszyn do zarządzania przy użyciu konfiguracji stanu Azure Automation.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c0dc68bd7dacf0cd7f4be9732d45831e2dbb712c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897007"
---
# <a name="enable-azure-automation-state-configuration"></a>Włącz konfigurację stanu Azure Automation

W tym temacie opisano, jak można skonfigurować maszyny do zarządzania przy użyciu konfiguracji stanu Azure Automation. Aby uzyskać szczegółowe informacje o tej usłudze, zobacz [Konfiguracja stanu Azure Automation — Omówienie](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Włączanie maszyn wirtualnych platformy Azure

Azure Automation konfiguracja stanu umożliwia łatwe włączenie maszyn wirtualnych platformy Azure w celu zarządzania konfiguracją przy użyciu Azure Portal, szablonów Azure Resource Manager lub programu PowerShell. Na wyciągnięcieu i bez administratora, który ma zdalny dostęp do maszyny wirtualnej, rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure rejestruje maszynę wirtualną z konfiguracją stanu Azure Automation. Ponieważ rozszerzenie platformy Azure działa asynchronicznie, kroki śledzenia postępu są dostępne w obszarze [Sprawdź stan instalacji maszyny wirtualnej](#check-status-of-vm-setup).

> [!NOTE]
>Wdrożenie DSC w węźle systemu Linux używa folderu **/tmp** . Moduły takie jak `nxautomation` są tymczasowo pobierane do weryfikacji przed ich zainstalowaniem w odpowiednich lokalizacjach. Aby zapewnić poprawne zainstalowanie modułów, Agent Log Analytics dla systemu Linux musi mieć uprawnienia do odczytu/zapisu w folderze **/tmp** .<br><br>
>Agent Log Analytics dla systemu Linux działa jako `omsagent` użytkownik. Aby udzielić użytkownikowi uprawnień do >zapisu `omsagent` , uruchom polecenie `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Włącz maszynę wirtualną przy użyciu Azure Portal

Aby umożliwić maszynie wirtualnej platformy Azure konfigurację stanu za pomocą [Azure Portal](https://portal.azure.com/):

1. Przejdź do konta Azure Automation, w którym chcesz włączyć maszyny wirtualne. 

2. Na stronie Konfiguracja stanu wybierz kartę **węzły** , a następnie kliknij przycisk **Dodaj**.

3. Wybierz maszynę wirtualną do włączenia.

4. Jeśli na komputerze nie zainstalowano rozszerzenia żądanego stanu programu PowerShell, a stan jest uruchomiony, kliknij przycisk **Połącz**.

5. W obszarze **rejestracja** wprowadź [wartości lokalnych Configuration Manager programu PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig) , które są wymagane w przypadku użycia. Opcjonalnie możesz wprowadzić konfigurację węzła, która ma zostać przypisana do maszyny wirtualnej.

![Włączanie maszyny wirtualnej](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Włączanie maszyny wirtualnej przy użyciu szablonów Azure Resource Manager

Za pomocą szablonów Azure Resource Manager można zainstalować i włączyć maszynę wirtualną w celu skonfigurowania stanu. Aby zapoznać się z przykładowym szablonem umożliwiającym korzystanie z istniejącej maszyny wirtualnej do konfiguracji stanu, zobacz temat [serwer zarządzany przez usługę konfiguracji żądanego stanu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) . Jeśli zarządzasz zestawem skalowania maszyn wirtualnych, zobacz przykładowy szablon w [konfiguracji zestawu skalowania maszyn wirtualnych zarządzanych przez Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Włączanie maszyn przy użyciu programu PowerShell

Aby włączyć maszyny wirtualne do konfiguracji stanu, można użyć polecenia cmdlet [register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) w programie PowerShell. 

> [!NOTE]
>`Register-AzAutomationDscNode`Polecenie cmdlet jest obecnie zaimplementowane tylko dla maszyn z systemem Windows, ponieważ wyzwala tylko rozszerzenie systemu Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Rejestrowanie maszyn wirtualnych w ramach subskrypcji platformy Azure

Najlepszym sposobem na zarejestrowanie maszyn wirtualnych z innych subskrypcji platformy Azure jest użycie rozszerzenia DSC w szablonie wdrażania Azure Resource Manager. Przykłady są dostępne w [rozszerzeniu konfiguracji żądanego stanu z szablonami Azure Resource Manager](../virtual-machines/extensions/dsc-template.md).

Aby znaleźć klucz rejestracji i adres URL rejestracji do użycia jako parametry w szablonie, zobacz [bezpieczne Włączanie maszyn przy użyciu rejestracji](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Włącz fizyczne/wirtualne maszyny z systemem Windows

Serwery z systemem Windows działające lokalnie lub w innych środowiskach w chmurze (w tym wystąpieniach AWS EC2) można włączyć, aby Azure Automation konfigurację stanu. Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że na komputerach jest zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest) umożliwiająca włączenie konfiguracji stanu. Ponadto program WMF 5 musi być zainstalowany na komputerze używanym do włączania maszyn.
1. Postępuj zgodnie z instrukcjami w temacie [generowanie konfiguracji DSC](#generate-dsc-metaconfigurations) w celu utworzenia folderu zawierającego wymagane konfiguracje konfiguracji DSC. 
1. Użyj poniższego polecenia cmdlet, aby zdalnie zastosować konfiguracje konfiguracji DSC programu PowerShell do maszyn, które mają zostać włączone. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Jeśli nie można zastosować zdalnego konfigurowania konfiguracji DSC programu PowerShell, skopiuj folder **konfiguracje** do maszyn, które są włączane. Następnie Dodaj kod do wywołania [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) lokalnie na maszynach.
1. Za pomocą Azure Portal lub poleceń cmdlet Sprawdź, czy maszyny są wyświetlane jako węzły konfiguracji stanu zarejestrowane na koncie usługi Azure Automation.

## <a name="enable-physicalvirtual-linux-machines"></a>Włącz fizyczne/wirtualne maszyny z systemem Linux

Do konfigurowania stanu można włączyć serwery z systemem Linux działające lokalnie lub w innych środowiskach w chmurze. Serwery muszą mieć [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning).

1. Upewnij się, że na komputerach jest zainstalowana najnowsza wersja [konfiguracji żądanego stanu programu PowerShell dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) , aby włączyć konfigurację stanu.
2. Jeśli [lokalne Configuration Manager konfiguracji DSC programu PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) są zgodne z przypadkiem użycia i chcesz włączyć maszyny, aby oba te elementy były ściągane z programu i do konfiguracji stanu:

   - Na każdym komputerze z systemem Linux, aby włączyć `Register.py` maszynę z ustawieniami domyślnymi lokalnych Configuration Manager DSC programu PowerShell.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz [bezpieczne Włączanie maszyn przy użyciu rejestracji](#enable-machines-securely-using-registration).

3. Jeśli ustawienia domyślne Configuration Manager lokalnego (LCM) konfiguracji DSC programu PowerShell nie pasują do Twojego przypadku użycia lub chcesz włączyć maszyny, które tylko raportują do Azure Automation konfiguracji stanu, wykonaj kroki 4-7. W przeciwnym razie przejdź bezpośrednio do kroku 7.

4. Postępuj zgodnie z instrukcjami w sekcji [Generuj konfiguracje konfiguracji DSC](#generate-dsc-metaconfigurations) , aby utworzyć folder zawierający wymagane konfiguracje konfiguracji DSC.

5. Upewnij się, że na komputerze używanym do konfigurowania stanu maszyny jest zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest) .

6. Dodaj kod w następujący sposób, aby zdalnie zastosować konfiguracje konfiguracji DSC programu PowerShell do maszyn, które mają zostać włączone.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Jeśli nie można zastosować zdalnego konfigurowania konfiguracji DSC programu PowerShell, skopiuj konfiguracje konfiguracji odpowiadające maszynom zdalnym z folderu opisanego w kroku 4 do maszyn z systemem Linux.

8. Dodaj kod do wywołania `Set-DscLocalConfigurationManager.py` lokalnego na każdym komputerze z systemem Linux, aby włączyć konfigurację stanu.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Za pomocą Azure Portal lub poleceń cmdlet upewnij się, że maszyny, które mają być włączone, są teraz wyświetlane jako węzły DSC zarejestrowane na koncie usługi Azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Generuj konfiguracje konfiguracji DSC

Aby włączyć konfigurację stanu dowolnego komputera, można wygenerować [konfigurację z konfiguracją DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Ta konfiguracja nakazuje agentowi DSC ściąganie z i/lub raportów w celu Azure Automation konfiguracji stanu. Konfigurację konfiguracji DSC dla konfiguracji stanu Azure Automation można wygenerować przy użyciu konfiguracji DSC programu PowerShell lub poleceń cmdlet programu PowerShell Azure Automation.

> [!NOTE]
> Konfiguracje konfiguracji DSC zawierają wpisy tajne, które są konieczne do włączenia komputera na koncie usługi Automation na potrzeby zarządzania. Upewnij się, że wszystkie utworzone konfiguracje konfiguracji DSC są prawidłowo chronione, lub usuń je po użyciu.

Obsługa serwera proxy dla konfiguracji z systemem jest kontrolowana przez [Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaconfig), który jest aparatem DSC środowiska Windows PowerShell. LCM działa na wszystkich węzłach docelowych i jest odpowiedzialny za wywoływanie zasobów konfiguracyjnych, które znajdują się w skrypcie konfiguracji DSC. Obsługę proxy można uwzględnić w konfiguracji, przez uwzględnienie definicji `ProxyURL` i `ProxyCredential` właściwości zgodnie z wymaganiami w `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` `ReportServerWeb` blokach, i. Przykładem ustawienia adresu URL jest `ProxyURL = "http://172.16.3.6:3128";` . `ProxyCredential`Właściwość jest ustawiona na `PSCredential` obiekt, zgodnie z opisem w temacie [Zarządzanie poświadczeniami w Azure Automation](shared-resources/credentials.md). 

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

1. Wprowadź klucz rejestracji i adres URL dla konta usługi Automation, a także nazwy maszyn, które mają zostać włączone. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz [bezpieczne Włączanie maszyn przy użyciu rejestracji](#enable-machines-securely-using-registration).

1. Jeśli chcesz, aby maszyny raportują informacje o stanie DSC do Azure Automation konfiguracji stanu, ale nie pobieraj konfiguracji lub modułów programu PowerShell, ustaw `ReportOnly` dla parametru wartość true.

1. Jeśli `ReportOnly` nie jest ustawiona, komputery raportują informacje o stanie DSC w celu Azure Automation konfiguracji stanu i konfiguracji ściągania lub modułów programu PowerShell. Ustaw odpowiednio parametry w `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` blokach,, i `ReportServerWeb` .

1. Uruchom skrypt. Powinien teraz istnieć folder katalogu roboczego o nazwie **DscMetaConfigs**, zawierający konfiguracje konfiguracji DSC programu PowerShell dla maszyn, które mają zostać włączone (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generuj konfiguracje konfiguracji DSC przy użyciu Azure Automation poleceń cmdlet

Jeśli domyślne ustawienia LCM platformy PowerShell są zgodne z przypadkiem użycia i chcesz umożliwić komputerom zarówno ściąganie, jak i raporty, aby Azure Automation konfigurację stanu, można wygenerować wymagane konfiguracje konfiguracji DSC za pomocą Azure Automation poleceń cmdlet.

1. Otwórz konsolę programu PowerShell lub programu vscode jako administrator na komputerze w środowisku lokalnym.
2. Połącz się z Azure Resource Manager przy użyciu polecenia [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount).
3. Pobierz konfiguracje konfiguracji DSC programu PowerShell dla maszyn, które chcesz włączyć z konta usługi Automation, w którym konfigurujesz węzły.

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

1. Teraz należy mieć folder **DscMetaConfigs** zawierający konfiguracje konfiguracji DSC usługi PowerShell dla maszyn, które mają zostać włączone (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Bezpieczne Włączanie maszyn przy użyciu rejestracji

Można bezpiecznie włączać maszyny dla konta Azure Automation za pomocą protokołu rejestracji maszyn wirtualnych WMF 5. Ten protokół umożliwia uwierzytelnianie węzła DSC na serwerze ściągania lub raportów DSC programu PowerShell, w tym konfiguracji stanu Azure Automation. Węzeł rejestruje się na serwerze w adresie URL rejestracji i uwierzytelnia przy użyciu klucza rejestracji. Podczas rejestracji, w węźle DSC i serwerze ściągania/raportów DSC jest negocjowany unikatowy certyfikat dla węzła, który ma być używany do uwierzytelniania na serwerze po rejestracji. Ten proces zapobiega personifikacji między włączonymi węzłami, na przykład w przypadku naruszenia bezpieczeństwa i niebezpiecznego węzła. Po rejestracji klucz rejestracji nie jest używany do uwierzytelniania ponownie i jest usuwany z węzła.

Informacje wymagane do protokołu rejestracji konfiguracji stanu można uzyskać z **kluczy** w obszarze **ustawienia konta** w Azure Portal. 

![Klucze i adres URL usługi Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adres URL rejestracji to pole adresu URL na stronie klucze.
- Klucz rejestracji to wartość pola **podstawowego klucza dostępu** lub pola **pomocniczy klucz dostępu** na stronie klucze. Można użyć dowolnego klucza.

Aby zwiększyć bezpieczeństwo, można w dowolnym momencie wygenerować ponownie podstawowy i pomocniczy klucz dostępu konta usługi Automation na stronie klucze. Ponowne generowanie kluczy uniemożliwia rejestracje węzłów w przyszłości przed użyciem poprzednich kluczy.

## <a name="re-register-a-node"></a>Zarejestruj ponownie węzeł

Po zarejestrowaniu maszyny jako węzła DSC w konfiguracji stanu Azure Automation istnieje kilka powodów, dla których może być konieczne ponowne zarejestrowanie tego węzła w przyszłości.

- **Odnowienie certyfikatu.** W przypadku wersji systemu Windows Server starszych niż Windows Server 2019 każdy węzeł automatycznie negocjuje unikatowy certyfikat na potrzeby uwierzytelniania, który wygaśnie po upływie jednego roku. Jeśli certyfikat wygaśnie bez odnowienia, węzeł nie może komunikować się z Azure Automation i jest oznaczony `Unresponsive` . Obecnie protokół rejestracji DSC programu PowerShell nie może automatycznie odnawiać certyfikatów, gdy zbliżają się do wygaśnięcia, i należy ponownie zarejestrować węzły po roku. Przed ponownym zarejestrowaniem upewnij się, że w każdym węźle jest uruchomiony program WMF 5 RTM. 

    Ponowne zarejestrowanie wykonane przez 90 dni lub mniej od czasu wygaśnięcia certyfikatu lub w dowolnym momencie po upływie czasu wygaśnięcia certyfikatu powoduje wygenerowanie i użycie nowego certyfikatu. Rozwiązanie tego problemu jest zawarte w systemie Windows Server 2019 lub nowszym.

- **Zmiany w wartościach LCM DSC.** Może być konieczne zmiana [wartości LCM usługi POWERSHELL DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) ustawionych podczas początkowej rejestracji węzła, na przykład `ConfigurationMode` . Obecnie te wartości agenta DSC można zmienić tylko przez ponowną rejestrację. Jedynym wyjątkiem jest wartość konfiguracji węzła przypisana do węzła. Można to zmienić w Azure Automation DSC bezpośrednio.

Węzeł można ponownie zarejestrować tak jak początkowo zarejestrowano węzeł przy użyciu dowolnej metody opisanej w tym dokumencie. Nie musisz wyrejestrować węzła z konfiguracji stanu Azure Automation przed jego ponownym zarejestrowaniem.

## <a name="check-status-of-vm-setup"></a>Sprawdź stan konfiguracji maszyny wirtualnej

Konfiguracja stanu umożliwia łatwe włączenie maszyn wirtualnych systemu Windows Azure w celu zarządzania konfiguracją. Pod wyciągnięciem, rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure służy do rejestrowania maszyny wirtualnej z konfiguracją stanu Azure Automation. Ponieważ rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure jest uruchamiane asynchronicznie, śledzenie jego postępu i rozwiązywanie problemów może być ważne.

> [!NOTE]
> Dowolna Metoda włączania maszyn wirtualnych systemu Windows Azure dla konfiguracji stanu, która używa rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure, może potrwać do godziny w celu Azure Automation wyświetlenia maszyn wirtualnych jako zarejestrowanych. To opóźnienie jest spowodowane instalacją programu WMF 5 na maszynie wirtualnej przez rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure, które jest wymagane do włączenia maszyn wirtualnych do konfiguracji stanu.

Aby wyświetlić stan rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure:

1. W Azure Portal przejdź do maszyny wirtualnej, która jest włączona.
2. W obszarze **Ustawienia** kliknij pozycję **rozszerzenia** . 
3. Teraz wybierz opcję **DSC** lub **DSCForLinux**, w zależności od używanego systemu operacyjnego. 
4. Aby uzyskać więcej informacji, kliknij pozycję **Wyświetl szczegółowy stan**.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz Wprowadzenie do [konfiguracji stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Przykład użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania można znaleźć w temacie [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
- Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz [Rozwiązywanie problemów z konfiguracją stanu Azure Automation](./troubleshoot/desired-state-configuration.md).
