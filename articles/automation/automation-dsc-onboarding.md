---
title: Włączanie Azure Automation State Configuration
description: W tym artykule opisano sposób skonfigurowania maszyn do zarządzania za pomocą Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: d338c5f34d49663345582198ff53ba50a2919d7e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829426"
---
# <a name="enable-azure-automation-state-configuration"></a>Włączanie Azure Automation State Configuration

W tym temacie opisano sposób skonfigurowania maszyn do zarządzania przy użyciu Azure Automation State Configuration. Aby uzyskać szczegółowe informacje o tej usłudze, [zobacz Azure Automation State Configuration omówienie usługi](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Włączanie maszyn wirtualnych platformy Azure

Azure Automation State Configuration umożliwia łatwe włączanie maszyn wirtualnych platformy Azure do zarządzania konfiguracją przy użyciu szablonów Azure Portal, szablonów Azure Resource Manager programu PowerShell. Bez konieczności zdalnego połączenia z maszyną wirtualną przez administratora rozszerzenie azure VM Desired State Configuration rejestruje maszynę wirtualną za pomocą Azure Automation State Configuration. Ponieważ rozszerzenie platformy Azure jest uruchamiane asynchronicznie, kroki śledzenia jego postępu znajdują się w tesłudze Check status of VM setup (Sprawdzanie [stanu konfiguracji maszyny wirtualnej).](#check-status-of-vm-setup)

> [!NOTE]
>Wdrażanie dsc w węźle systemu Linux używa **folderu /tmp.** Moduły, takie `nxautomation` jak , są tymczasowo pobierane do weryfikacji przed zainstalowaniem ich w odpowiednich lokalizacjach. Aby moduły zostały poprawnie zainstalowane, agent usługi Log Analytics dla systemu Linux musi mieć uprawnienia do **odczytu/zapisu w folderze /tmp.**<br><br>
>Agent usługi Log Analytics dla systemu Linux działa jako `omsagent` użytkownik. Aby udzielić >zapisu `omsagent` użytkownikowi, uruchom polecenie `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Włączanie maszyny wirtualnej przy użyciu Azure Portal

Aby umożliwić maszynie wirtualnej platformy Azure State Configuration pośrednictwem [Azure Portal:](https://portal.azure.com/)

1. Przejdź do konta Azure Automation, na którym chcesz włączyć maszyny wirtualne. 

2. Na stronie State Configuration wybierz **kartę Węzły,** a następnie kliknij pozycję **Dodaj**.

3. Wybierz maszynę wirtualną do włączenia.

4. Jeśli na maszynie nie jest zainstalowane rozszerzenie żądanego stanu programu PowerShell, a stan zasilania jest uruchomiony, kliknij przycisk **Połącz**.

5. W **obszarze** Rejestracja wprowadź lokalne wartości Menedżer konfiguracji DSC programu [PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig) wymagane dla Twojego przypadku użycia. Opcjonalnie możesz wprowadzić konfigurację węzła do przypisania do maszyny wirtualnej.

![włączanie maszyny wirtualnej](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Włączanie maszyny wirtualnej przy użyciu Azure Resource Manager wirtualnych

Maszynę wirtualną można zainstalować i włączyć na State Configuration przy użyciu Azure Resource Manager szablonów. Przykładowy [szablon, który](https://azure.microsoft.com/resources/templates/101-automation-configuration/) umożliwia istniejącą maszynę wirtualną na State Configuration, można znaleźć w tesłudze Server managed by Desired State Configuration service (Serwer zarządzany przez usługę State Configuration). Jeśli zarządzasz zestawem skalowania maszyn wirtualnych, zobacz przykładowy szablon w temacie Konfiguracja zestawu skalowania maszyn wirtualnych [zarządzana](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)przez program Azure Automation .

### <a name="enable-machines-using-powershell"></a>Włączanie maszyn przy użyciu programu PowerShell

Możesz użyć polecenia cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) w programie PowerShell, aby włączyć maszyny wirtualne dla State Configuration. 

> [!NOTE]
>Polecenie `Register-AzAutomationDscNode` cmdlet jest obecnie implementowane tylko dla maszyn z systemem Windows, ponieważ wyzwala tylko rozszerzenie systemu Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Rejestrowanie maszyn wirtualnych w subskrypcjach platformy Azure

Najlepszym sposobem rejestrowania maszyn wirtualnych z innych subskrypcji platformy Azure jest użycie rozszerzenia DSC w szablonie Azure Resource Manager wdrożenia. Przykłady znajdują się [w Desired State Configuration z Azure Resource Manager szablonów](../virtual-machines/extensions/dsc-template.md).

Aby znaleźć klucz rejestracji i adres URL rejestracji do użycia jako parametry w szablonie, zobacz Bezpieczne włączanie maszyn przy [użyciu rejestracji](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Włączanie maszyn fizycznych/wirtualnych z systemem Windows

Serwery z systemem Windows działające lokalnie lub w innych środowiskach w chmurze (w tym wystąpienia usługi EC2 usług AWS) można włączyć w Azure Automation State Configuration. Serwery muszą mieć dostęp [wychodzący do platformy Azure.](automation-dsc-overview.md#network-planning)

1. Upewnij się, że na maszynach jest zainstalowana najnowsza wersja programu [WMF 5,](https://aka.ms/wmf5latest) aby umożliwić jej State Configuration. Ponadto program WMF 5 musi być zainstalowany na komputerze, który jest używane do włączania maszyn.
1. Postępuj zgodnie z instrukcjami w [te tematu Generate DSC metaconfigurations (Generowanie metakonfiguracji DSC),](#generate-dsc-metaconfigurations) aby utworzyć folder zawierający wymagane metakonfiguracje DSC. 
1. Użyj następującego polecenia cmdlet, aby zdalnie zastosować metakonfiguracje DSC programu PowerShell do maszyn, które mają być włączane. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Jeśli nie można zdalnie zastosować metakonfiguracji DSC programu PowerShell, skopiuj folder **metakonfiguracji** na maszyny, które są w tym celu włączanie. Następnie dodaj kod, aby wywołać [zestaw Set-DscLocalConfigurationManager lokalnie](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) na maszynach.
1. Korzystając z Azure Portal lub polecenia cmdlet, sprawdź, czy maszyny są wyświetlane jako State Configuration zarejestrowane na twoim Azure Automation konta.

## <a name="enable-physicalvirtual-linux-machines"></a>Włączanie maszyn fizycznych/wirtualnych z systemem Linux

Serwery z systemem Linux działające lokalnie lub w innych środowiskach w chmurze można włączyć na State Configuration. Serwery muszą mieć dostęp [wychodzący do platformy Azure.](automation-dsc-overview.md#network-planning)

1. Upewnij się, że najnowsza wersja programu [PowerShell Desired State Configuration dla](https://github.com/Microsoft/PowerShell-DSC-for-Linux) systemu Linux jest zainstalowana na maszynach, aby umożliwić State Configuration.
2. Jeśli lokalne [ustawienia konfiguracji DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) programu PowerShell Menedżer konfiguracji są zgodne z Twoim przypadekem użycia, a chcesz włączyć maszyny tak, aby ściągały i raportowały do State Configuration:

   - Na każdej maszynie z systemem Linux, która ma być włączona, użyj polecenia , aby włączyć maszynę przy użyciu ustawień Menedżer konfiguracji `Register.py` DSC programu PowerShell.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz Bezpieczne włączanie [maszyn przy użyciu rejestracji](#enable-machines-securely-using-registration).

3. Jeśli wartości domyślne lokalnego Menedżer konfiguracji DSC programu PowerShell (LCM) nie są zgodne z Twoim przypadekem użycia lub chcesz włączyć maszyny, które raportować tylko do usługi Azure Automation State Configuration, wykonaj kroki 4–7. W przeciwnym razie przejdź bezpośrednio do kroku 7.

4. Postępuj zgodnie z instrukcjami w sekcji [Generowanie metakonfiguracji DSC,](#generate-dsc-metaconfigurations) aby utworzyć folder zawierający wymagane metakonfiguracje DSC.

5. Upewnij się, że najnowsza wersja programu [WMF 5 jest zainstalowana](https://aka.ms/wmf5latest) na komputerze używanym do włączania maszyn na State Configuration.

6. Dodaj kod w następujący sposób, aby zdalnie zastosować metakonfiguracje DSC programu PowerShell do maszyn do włączenia.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Jeśli nie można zdalnie zastosować metakonfiguracji DSC programu PowerShell, skopiuj metakonfiguracje odpowiadające maszynom zdalnym z folderu opisanego w kroku 4 na maszyny z systemem Linux.

8. Dodaj kod wywołujący `Set-DscLocalConfigurationManager.py` lokalnie na każdej maszynie z systemem Linux, aby umożliwić State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Korzystając z Azure Portal lub polecenia cmdlet, upewnij się, że maszyny do włączenia są teraz wyświetlane jako węzły DSC zarejestrowane na Azure Automation konta.

## <a name="generate-dsc-metaconfigurations"></a>Generowanie metakonfiguracji DSC

Aby włączyć dowolną maszynę State Configuration, można wygenerować [metakonfigurację DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Ta konfiguracja nakazuje agentowi DSC ściąganie danych z usługi i/lub raportów do Azure Automation State Configuration. Metakonfigurację DSC dla usługi Azure Automation State Configuration można wygenerować przy użyciu konfiguracji DSC programu PowerShell lub Azure Automation cmdlet programu PowerShell.

> [!NOTE]
> Metakonfiguracje DSC zawierają wpisy tajne potrzebne do włączenia maszyny na koncie usługi Automation na potrzeby zarządzania. Upewnij się, że wszystkie metakonfiguracje DSC są prawidłowo skonfigurowane, lub usuń je po użyciu.

Obsługa metadanych przez serwer proxy jest kontrolowana przez lokalne Menedżer konfiguracji [,](/powershell/scripting/dsc/managing-nodes/metaconfig)która jest Windows PowerShell DSC. LcM działa na wszystkich węzłach docelowych i jest odpowiedzialny za wywoływanie zasobów konfiguracji, które są zawarte w skrypcie metakonfiguracji DSC. Obsługę serwera proxy można uwzględnić w metakonfiguracji przez dołączenie definicji właściwości i zgodnie z potrzebami w `ProxyURL` `ProxyCredential` `ConfigurationRepositoryWeb` blokach `ResourceRepositoryWeb` , i `ReportServerWeb` . Przykładem ustawienia adresu URL jest `ProxyURL = "http://172.16.3.6:3128";` . Właściwość jest ustawiona na obiekt , zgodnie z opisem `ProxyCredential` `PSCredential` w tece Zarządzanie [poświadczeniami w Azure Automation](shared-resources/credentials.md). 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generowanie metakonfiguracji DSC przy użyciu konfiguracji DSC

1. Otwórz program VSCode (lub ulubiony edytor) jako administrator na maszynie w środowisku lokalnym. Na maszynie musi być zainstalowana najnowsza [wersja programu WMF 5.](https://aka.ms/wmf5latest)
1. Skopiuj poniższy skrypt lokalnie. Ten skrypt zawiera konfigurację DSC programu PowerShell do tworzenia metakonfiguracji oraz polecenie do uruchamiania tworzenia metakonfiguracji.

    > [!NOTE]
    > State Configuration w nazwach konfiguracji węzła jest w Azure Portal. Jeśli przypadek jest niezgodny, węzeł nie będzie pokazywany na karcie **Węzły.**

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

1. Wypełnij klucz rejestracji i adres URL konta usługi Automation, a także nazwy maszyn do włączenia. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz Bezpieczne włączanie [maszyn przy użyciu rejestracji](#enable-machines-securely-using-registration).

1. Jeśli chcesz, aby maszyny zgłaszały informacje o stanie DSC do usługi Azure Automation State Configuration, ale nie mają ściągać konfiguracji ani modułów programu PowerShell, `ReportOnly` ustaw parametr na wartość true.

1. Jeśli `ReportOnly` nie jest ustawiona, maszyny raportuje informacje o stanie DSC, aby Azure Automation State Configuration i konfiguracji ściągania lub modułów programu PowerShell. Ustaw odpowiednie parametry w blokach `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` , i `ReportServerWeb` .

1. Uruchom skrypt. Powinien być teraz dostępny folder katalogu roboczego o nazwie **DscMetaConfigs** zawierający metakonfiguracje DSC programu PowerShell dla maszyn do włączenia (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generowanie metakonfiguracji DSC przy użyciu Azure Automation cmdlet

Jeśli wartości domyślne narzędzia DSC LCM programu PowerShell pasują do Twojego przypadku użycia i chcesz umożliwić maszynom ściąganie z programu i zgłaszanie do usługi Azure Automation State Configuration, możesz wygenerować potrzebne metakonfiguracje DSC po prostu przy użyciu poleceń cmdlet Azure Automation.

1. Otwórz konsolę programu PowerShell lub program VSCode jako administrator na maszynie w środowisku lokalnym.
2. Połącz się z Azure Resource Manager przy [użyciu narzędzia Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount)
3. Pobierz metakonfiguracje DSC programu PowerShell dla maszyn, które chcesz włączyć, z konta usługi Automation, na którym konfigurujesz węzły.

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

1. Powinien być teraz dostępny folder **DscMetaConfigs** zawierający metakonfiguracje DSC programu PowerShell dla maszyn do włączenia (jako administrator).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Bezpieczne włączanie maszyn przy użyciu rejestracji

Maszyny można bezpiecznie włączyć dla konta Azure Automation za pośrednictwem protokołu rejestracji DSC WMF 5. Ten protokół umożliwia węzłem DSC uwierzytelnianie na serwerze ściągania lub serwera raportów DSC programu PowerShell, w tym Azure Automation State Configuration. Węzeł rejestruje się na serwerze pod adresem URL rejestracji i uwierzytelnia przy użyciu klucza rejestracji. Podczas rejestracji węzeł DSC i serwer ściągania/raportów DSC negocjują unikatowy certyfikat, który ma być uwierzytelnianiem na serwerze po rejestracji. Ten proces zapobiega personifikacji węzłów włączonych, na przykład w przypadku naruszenia zabezpieczeń węzła i złośliwego zachowania. Po zarejestrowaniu klucz rejestracji nie jest ponownie używany do uwierzytelniania i jest usuwany z węzła.

Informacje wymagane dla protokołu rejestracji klienta State Configuration klucze  w obszarze **Ustawienia** konta w Azure Portal. 

![Klucze i adres URL usługi Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adres URL rejestracji to pole adresu URL na stronie Klucze.
- Klucz rejestracji to wartość pola Podstawowy **klucz dostępu** lub **Pomocniczy** klucz dostępu na stronie Klucze. Można użyć jednego z tych kluczy.

W celu uzyskania dodatkowych zabezpieczeń można w dowolnym momencie ponownie wygenerować podstawowe i pomocnicze klucze dostępu konta usługi Automation na stronie Klucze. Przyszła rejestracja węzłów uniemożliwia używanie poprzednich kluczy.

## <a name="re-register-a-node"></a>Ponowne rejestrowanie węzła

Po zarejestrowaniu maszyny jako węzła DSC w usłudze Azure Automation State Configuration istnieje kilka powodów, dla których może być konieczne ponowne zarejestrowanie tego węzła w przyszłości.

- **Odnawianie certyfikatu.** W przypadku wersji systemu Windows Server starszych niż Windows Server 2019 każdy węzeł automatycznie negocjuje unikatowy certyfikat uwierzytelniania, który wygasa po roku. Jeśli certyfikat wygaśnie bez odnowienia, węzeł nie może komunikować się z Azure Automation jest oznaczony jako `Unresponsive` . Obecnie protokół rejestracji DSC programu PowerShell nie może automatycznie odnawiać certyfikatów, gdy niedługo wygasną, i należy ponownie zarejestrować węzły po upływie roku. Przed ponowną rejestracją upewnij się, że w każdym węźle jest uruchomiony program WMF 5 RTM. 

    Ponowna rejestracja wykonana 90 dni lub mniej od czasu wygaśnięcia certyfikatu lub w dowolnym momencie po upływie czasu wygaśnięcia certyfikatu powoduje wygenerowanie i ponowne wygenerowanie nowego certyfikatu. Rozwiązanie tego problemu jest zawarte w systemie Windows Server 2019 lub nowszym.

- **Zmiany wartości DSC LCM.** Może być konieczne zmiana wartości [LCM DSC programu PowerShell ustawionych](/powershell/scripting/dsc/managing-nodes/metaConfig4) podczas początkowej rejestracji węzła, na przykład `ConfigurationMode` . Obecnie te wartości agenta DSC można zmienić tylko za pomocą ponownej rejestracji. Jedynym wyjątkiem jest wartość Konfiguracja węzła przypisana do węzła. Można to zmienić bezpośrednio w Azure Automation DSC.

Możesz ponownie zarejestrować węzeł tak samo jak początkowo zarejestrowany węzeł przy użyciu dowolnej metody opisanej w tym dokumencie. Nie trzeba wyrejestrować węzła z Azure Automation State Configuration przed jego ponowną rejestracją.

## <a name="check-status-of-vm-setup"></a>Sprawdzanie stanu konfiguracji maszyny wirtualnej

State Configuration umożliwia łatwe włączanie maszyn wirtualnych platformy Azure z systemem Windows do zarządzania konfiguracją. Za jego pomocą rozszerzenie azure VM Desired State Configuration służy do rejestrowania maszyny wirtualnej w usłudze Azure Automation State Configuration. Ponieważ rozszerzenie azure VM Desired State Configuration działa asynchronicznie, śledzenie postępu i rozwiązywanie problemów z jego wykonywaniem może być ważne.

> [!NOTE]
> Każda metoda włączania maszyn wirtualnych platformy Azure z systemem Windows dla usługi State Configuration, która korzysta z rozszerzenia azure VM Desired State Configuration, może potrwać do godziny, Azure Automation wyświetlić maszyny wirtualne jako zarejestrowane. To opóźnienie jest spowodowane zainstalowaniem programu WMF 5 na maszynie wirtualnej przez rozszerzenie azure VM Desired State Configuration, które jest wymagane do włączenia maszyn wirtualnych dla State Configuration.

Aby wyświetlić stan rozszerzenia maszyny wirtualnej platformy Azure Desired State Configuration:

1. W Azure Portal przejdź do włączonej maszyny wirtualnej.
2. Kliknij **pozycję Rozszerzenia w** obszarze **Ustawienia.** 
3. Teraz wybierz **pozycję DSC** lub **DSCForLinux** w zależności od systemu operacyjnego. 
4. Aby uzyskać więcej informacji, możesz kliknąć pozycję **Wyświetl szczegółowy stan.**

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę, zobacz [Wprowadzenie do Azure Automation State Configuration](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC w celu przypisania ich do węzłów docelowych, zobacz Compile [DSC configurations in Azure Automation State Configuration](automation-dsc-compile.md)(Kompilowanie konfiguracji DSC w systemie Azure Automation State Configuration ).
- Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
- Aby uzyskać informacje o cenach, [zobacz Azure Automation State Configuration cennik.](https://azure.microsoft.com/pricing/details/automation/)
- Aby uzyskać przykład użycia narzędzia Azure Automation State Configuration potoku ciągłego wdrażania, zobacz Set up continuous deployment with Chocolatey (Konfigurowanie ciągłego wdrażania [za pomocą narzędzia Chocolatey).](automation-dsc-cd-chocolatey.md)
- Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz [Rozwiązywanie Azure Automation State Configuration](./troubleshoot/desired-state-configuration.md).
