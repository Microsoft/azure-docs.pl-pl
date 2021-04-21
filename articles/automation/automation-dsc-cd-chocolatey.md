---
title: Konfigurowanie Azure Automation ciągłego wdrażania za pomocą oprogramowania Chocolatey
description: W tym artykule opisano sposób konfiguracji ciągłego wdrażania za pomocą State Configuration i menedżera pakietów Chocolatey.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 717561614a3e42995bbce6746839fd9b7cbca37e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834862"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Konfigurowanie ciągłego wdrażania za pomocą narzędzia Chocolatey

W świecie DevOps istnieje wiele narzędzi, które pomagają w różnych punktach potoku ciągłej integracji. Azure Automation [State Configuration](automation-dsc-overview.md) to nowy dodatek do opcji, które mogą stosować zespoły DevOps. 

Azure Automation to usługa zarządzana w usłudze Microsoft Azure, która umożliwia automatyzowanie różnych zadań przy użyciu zasobów runbook, węzłów i zasobów udostępnionych, takich jak poświadczenia, harmonogramy i zmienne globalne. Azure Automation State Configuration rozszerza tę możliwość automatyzacji o narzędzia programu PowerShell Desired State Configuration (DSC). Oto doskonałe omówienie [.](automation-dsc-overview.md)

W tym artykule pokazano, jak skonfigurować ciągłe wdrażanie (CD) dla komputera z systemem Windows. Tę technikę można łatwo rozszerzyć w celu dołączyć do roli tyle komputerów z systemem Windows, ile jest to konieczne, na przykład w witrynie internetowej, i przejść z niej do dodatkowych ról.

![Ciągłe wdrażanie maszyn wirtualnych IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na wysokim poziomie

W tym przypadku dzieje się sporo, ale na szczęście można je podzielić na dwa główne procesy:

- Pisanie kodu i testowanie go, a następnie tworzenie i publikowanie pakietów instalacyjnych dla wersji głównych i pomocniczych systemu.
- Tworzenie maszyn wirtualnych, które instalują i wykonują kod w pakietach, i zarządzanie nimi.  

Po utworzeniu obu tych podstawowych procesów można łatwo automatycznie zaktualizować pakiet na swoich maszyn wirtualnych w przypadku tworzenia i wdrażania nowych wersji.

## <a name="component-overview"></a>Omówienie składników

Menedżery pakietów, takie [jak apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) są dobrze znane w świecie systemu Linux, ale nie tak wiele w świecie systemu Windows.
[Chocolatey](https://chocolatey.org/) to coś takiego, a [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) Scotta Hanselmana na ten temat jest doskonałym wprowadzeniem. W skrócie Chocolatey umożliwia instalowanie pakietów z centralnego repozytorium w systemie operacyjnym Windows przy użyciu wiersza polecenia. Możesz utworzyć własne repozytorium i zarządzać nimi, a chocolatey może instalować pakiety z dowolnej liczby wyznaczanych repozytoriów.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) to narzędzie programu PowerShell, które umożliwia zadeklarowanie konfiguracji maszyny. Jeśli na przykład chcesz zainstalować oprogramowanie Chocolatey, zainstalowane usługi IIS, otwarty port 80 i zainstalowaną wersję 1.0.0 witryny internetowej, lokalna Menedżer konfiguracji DSC (LCM) implementuje konfigurację. Serwer ściągania DSC przechowuje repozytorium konfiguracji dla maszyn. Program LCM na każdej maszynie okresowo sprawdza, czy jego konfiguracja jest zgodności z przechowywaną konfiguracją. Może zgłaszać stan lub próbować przywrócić komputer do wyrównania z przechowywaną konfiguracją. Możesz edytować przechowywaną konfigurację na serwerze ściągania, aby spowodować dopasowanie maszyny lub zestawu maszyn do zmienionej konfiguracji.

Zasób DSC to moduł kodu, który ma określone możliwości, takie jak zarządzanie siecią, usługą Active Directory lub SQL Server. Zasób Chocolatey DSC wie, jak uzyskać dostęp do serwera NuGet (między innymi), pobierać pakiety, instalować pakiety i tak dalej. Istnieje wiele innych zasobów DSC w Galeria programu PowerShell [.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) Te moduły należy zainstalować na serwerze ściągania Azure Automation State Configuration do użytku przez konfiguracje.

Resource Manager zapewniają deklaratywną sposób generowania infrastruktury, na przykład sieci, podsieci, zabezpieczeń i routingu sieci, usług równoważenia obciążenia, kart sieciowych, maszyn wirtualnych i tak dalej. Oto artykuł, [w](../azure-resource-manager/management/deployment-models.md) którym porównano model wdrażania Resource Manager (deklaratywny) z modelem wdrażania usługi Azure Service Management (ASM lub klasycznym) (imperatywnym). Ten artykuł zawiera omówienie podstawowych dostawców zasobów: zasobów obliczeniowych, magazynu i sieci.

Jedną z kluczowych funkcji szablonu Resource Manager jest możliwość instalowania rozszerzenia maszyny wirtualnej na maszynie wirtualnej podczas jej aprowizowania. Rozszerzenie maszyny wirtualnej ma określone możliwości, takie jak uruchamianie niestandardowego skryptu, instalowanie oprogramowania antywirusowego i uruchamianie skryptu konfiguracji DSC. Istnieje wiele innych typów rozszerzeń maszyn wirtualnych.

## <a name="quick-trip-around-the-diagram"></a>Szybka podróż po diagramie

Zaczynając od góry, piszesz kod, tworzysz go, testujesz, a następnie tworzysz pakiet instalacyjny. Chocolatey może obsługiwać różne typy pakietów instalacyjnych, takich jak MSI, MSU, ZIP. Masz również pełną moc programu PowerShell, aby wykonać rzeczywistą instalację, jeśli natywne możliwości Chocolatey nie są do niego w pełni dostępne. Umieść pakiet w miejscu dostępnym — repozytorium pakietów. W tym przykładzie użycia użyto folderu publicznego na koncie usługi Azure Blob Storage, ale może to być dowolne miejsce. Chocolatey działa natywnie z serwerami NuGet i kilkoma innymi w celu zarządzania metadanymi pakietów. [W tym artykule](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) opisano opcje. W przykładzie użycia jest używany program NuGet. Nuspec to metadane dotyczące pakietów. Informacje nuspec są kompilowane do zestawu NuPkg i przechowywane na serwerze NuGet. Gdy konfiguracja żąda pakietu według nazwy i odwołuje się do serwera NuGet, zasób Chocolatey DSC na maszynie wirtualnej pobiera pakiet i instaluje go. Możesz również zażądać określonej wersji pakietu.

W lewym dolnym rogu obrazu znajduje się Azure Resource Manager szablonu. W tym przykładzie użycia rozszerzenie maszyny wirtualnej rejestruje maszynę wirtualną przy użyciu Azure Automation State Configuration ściągania jako węzeł. Konfiguracja jest przechowywana na serwerze ściągania dwa razy: raz jako zwykły tekst i raz skompilowana jako plik MOF. W Azure Portal pliku MOF reprezentuje konfigurację węzła, a nie prostą konfigurację. Jest to artefakt skojarzony z węzłem, aby węzeł znał jego konfigurację. Szczegóły poniżej pokazują, jak przypisać konfigurację węzła do węzła.

Tworzenie nuspec, kompilowanie go i przechowywanie go na serwerze NuGet to mała rzecz. I już zarządzasz maszynami wirtualnych. 

Następny krok do ciągłego wdrażania wymaga skonfigurowania serwera ściągania jeden raz, zarejestrowania węzłów na nim jeden raz oraz utworzenia i zapisania konfiguracji początkowej na serwerze. Ponieważ pakiety są uaktualniane i wdrażane w repozytorium, wystarczy odświeżyć konfigurację i konfigurację węzła na serwerze ściągania zgodnie z potrzebami.

Jeśli nie zaczynasz od szablonu Resource Manager, to w porządku. Istnieją polecenia programu PowerShell, które ułatwiają rejestrowanie maszyn wirtualnych na serwerze ściągania. Aby uzyskać więcej informacji, zobacz [Dołączanie maszyn do zarządzania przez Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Informacje o przykładzie użycia

Przykład użycia w tym artykule rozpoczyna się od maszyny wirtualnej z ogólnego obrazu systemu Windows Server 2012 R2 z galerii platformy Azure. Możesz rozpocząć od dowolnego przechowywanego obrazu, a następnie dostosować go w tym miejscu za pomocą konfiguracji DSC.
Jednak zmiana konfiguracji tworzonej w obrazie jest znacznie trudniejsza niż dynamiczne aktualizowanie konfiguracji przy użyciu konfiguracji DSC.

Nie musisz używać szablonu maszyny Resource Manager rozszerzenia maszyny wirtualnej, aby użyć tej techniki z maszynami wirtualnym. Maszyny wirtualne nie muszą być na platformie Azure, aby podlegać zarządzaniu cd. Wystarczy zainstalować oprogramowanie Chocolatey i skonfigurować na maszynie wirtualnej lcm, aby wiedziała, gdzie znajduje się serwer ściągania.

Podczas aktualizowania pakietu na maszynie wirtualnej, która jest w środowisku produkcyjnym, należy wywróć tę maszynę wirtualną poza rotację podczas instalowania aktualizacji. Sposób, w jaki to robisz, różni się znacznie. Na przykład w przypadku maszyny wirtualnej za Azure Load Balancer można dodać sondę niestandardową. Podczas aktualizowania maszyny wirtualnej punkt końcowy sondy zwraca 400. Dostosowanie niezbędne do przyczyny tej zmiany może być wewnątrz konfiguracji, podobnie jak dostosowanie w celu przełączenia go z powrotem na zwracanie błędu 200 po zakończeniu aktualizacji.

Pełne źródło tego przykładu użycia znajduje się w [tym Visual Studio w witrynie](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Krok 1. Konfigurowanie serwera ściągania i konta usługi Automation

W uwierzytelnionym ( ) wierszu polecenia programu PowerShell: (może potrwać kilka minut, gdy `Connect-AzAccount` serwer ściągania jest ustawiony)

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

Konto usługi Automation można umieścić w dowolnym z następujących regionów (nazywanych również lokalizacjami): Wschodnie stany USA 2, Południowo-środkowe stany USA, US Gov Wirginia, Europa Zachodnia, Azja Południowo-Wschodnia, Japonia Wschodnia, Indie Środkowe i Australia Południowo-Wschodnia, Kanada Środkowa, Europa Północna.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2. Dostosowanie rozszerzenia maszyny wirtualnej do Resource Manager szablonu

Szczegółowe informacje dotyczące rejestracji maszyny wirtualnej (przy użyciu rozszerzenia dsc maszyny wirtualnej programu PowerShell) podane w tym szablonie [Szybkiego startu platformy Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)
Ten krok rejestruje nową maszynę wirtualną na serwerze ściągania na liście State Configuration węzłów. Częścią tej rejestracji jest określenie konfiguracji węzła, która ma zostać zastosowana do węzła. Ta konfiguracja węzła nie musi jeszcze istnieć na serwerze ściągania, dlatego krok 4 jest w stanie wykonać to po raz pierwszy. Jednak w kroku 2 należy zdecydować nazwę węzła i nazwę konfiguracji. W tym przykładzie użycia węzłem jest "isvbox", a konfiguracja to "ISVBoxConfig". Dlatego nazwa konfiguracji węzła (określona w DeploymentTemplate.js) to "ISVBoxConfig.isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Krok 3. Dodawanie wymaganych zasobów DSC do serwera ściągania

Narzędzie Galeria programu PowerShell instrumentowane w celu zainstalowania zasobów DSC na koncie Azure Automation danych.
Przejdź do zasobu, którego potrzebujesz, a następnie kliknij przycisk "Wd wdrażaj Azure Automation".

![Galeria programu PowerShell przykład](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Inna technika niedawno dodana do Azure Portal umożliwia ściąganie nowych modułów lub aktualizowanie istniejących modułów. Kliknij zasób konta usługi Automation, kafelek Zasoby, a na koniec kafelek Moduły. Ikona Przeglądaj galerię umożliwia wyświetlanie listy modułów w galerii, przechodzenie do szczegółów i ostatecznie importowanie do konta usługi Automation. Jest to doskonały sposób na utrzymanie aktualnych modułów od czasu do czasu. Funkcja importowania sprawdza zależności z innymi modułami, aby upewnić się, że nic nie zostanie zsynchronizowane.

Istnieje również metoda ręczna, używana tylko raz dla każdego zasobu, chyba że chcesz ją uaktualnić później. Aby uzyskać więcej informacji na temat tworzenia modułów integracji programu PowerShell, zobacz [Authoring Integration Modules for Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Struktura folderów modułu integracji programu PowerShell dla komputera z systemem Windows różni się nieco od struktury folderów oczekiwanej przez Azure Automation. 

1. Zainstaluj [Windows Management Framework wersji 5](https://aka.ms/wmf5latest) (nie jest to wymagane w przypadku Windows 10).

2. Zainstaluj moduł integracji.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Skopiuj folder module z **folderu c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** do folderu tymczasowego.

4. Usuń przykłady i dokumentację z folderu głównego.

5. Spakuj folder główny, nadając plikowi ZIP nazwę folderu.

6. Umieść plik ZIP w dostępnej lokalizacji HTTP, takiej jak magazyn obiektów blob na koncie usługi Azure Storage.

7. Uruchom następujące polecenie.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Uwzględniony przykład implementuje te kroki dla cChoco i xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Krok 4. Dodawanie konfiguracji węzła do serwera ściągania

Nie ma nic specjalnego w przypadku pierwszego importowania konfiguracji na serwer ściągania i kompilowania. Wszystkie późniejsze importy lub kompilacje tej samej konfiguracji wyglądają dokładnie tak samo. Za każdym razem, gdy aktualizujesz pakiet i musisz wypchnąć go do produkcji, ten krok należy wykonać po upewnieniu się, że plik konfiguracji jest poprawny — łącznie z nową wersją pakietu. Oto plik **konfiguracji,** ISVBoxConfig.ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Oto skrypt **New-ConfigurationScript.ps1** (zmodyfikowany w celu używania modułu Az):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published -Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

W wyniku tych kroków na serwerze ściągania zostanie umieszczona nowa konfiguracja węzła o nazwie **ISVBoxConfig.isvbox.** Nazwa konfiguracji węzła ma budowaną nazwę `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Krok 5. Tworzenie i obsługa metadanych pakietu

Dla każdego pakietu wprowadzonego do repozytorium pakietów potrzebny jest pakiet Nuspec, który go opisuje. Musi być skompilowany i przechowywany na serwerze NuGet. Ten proces opisano [tutaj.](https://docs.nuget.org/create/creating-and-publishing-a-package) 

Możesz użyć **MyGet.org** jako serwera NuGet. Możesz kupić tę usługę, ale jest to bezpłatna, startowa SKU. Na [stronie NuGet](https://www.nuget.org/)znajdziesz instrukcje dotyczące instalowania własnego serwera NuGet dla pakietów prywatnych.

## <a name="step-6-tie-it-all-together"></a>Krok 6. Powiąż wszystko ze sobą

Za każdym razem, gdy wersja przechodzi przez usługę pytań i odpowiedzi i jest zatwierdzana do wdrożenia, pakiet jest tworzony, a pakiety nuspec i nupkg są aktualizowane i wdrażane na serwerze NuGet. Konfigurację (krok 4) należy również zaktualizować, aby uzgodnić nowy numer wersji. Następnie należy wysłać go do serwera ściągania i skompilować.

Od tego momentu do maszyn wirtualnych, które zależą od tej konfiguracji, należy ściągnięcie i zainstalowanie aktualizacji. Każda z tych aktualizacji jest prosta — wystarczy wiersz lub dwa z programu PowerShell. Na Azure DevOps niektóre z nich są hermetyzowane w zadaniach kompilacji, które mogą być połączone w kompilacji. Ten [artykuł](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) zawiera więcej szczegółów. To [repozytorium GitHub](https://github.com/Microsoft/vso-agent-tasks) szczegółowo o szczegółach dostępnych zadań kompilacji.

## <a name="related-articles"></a>Pokrewne artykuły:
* [Azure Automation DSC — omówienie](automation-dsc-overview.md)
* [Dołączanie maszyn do zarządzania przez Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Azure Automation State Configuration omówienie.](automation-dsc-overview.md)
- Aby rozpocząć korzystanie z tej funkcji, zobacz [Rozpoczynanie pracy z Azure Automation State Configuration](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC w celu przypisania ich do węzłów docelowych, zobacz Compile [DSC configurations in Azure Automation State Configuration](automation-dsc-compile.md).
- Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
- Aby uzyskać informacje o cenach, [zobacz Azure Automation State Configuration cennik.](https://azure.microsoft.com/pricing/details/automation/)
