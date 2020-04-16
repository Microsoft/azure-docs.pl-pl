---
title: Ciągłe wdrażanie konfiguracji stanu automatyzacji platformy Azure z czekoladowym
description: W tym artykule opisano ciągłe wdrażanie devops przy użyciu konfiguracji stanu usługi Azure Automation z Menedżerem pakietów Chocolatey. Zawiera przykład z pełnym szablonem Menedżera zasobów JSON i źródłem programu PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 0c61a431b985e494148500ed0a7aeb106534ed2c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392116"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Zapewnienie ciągłego wdrażania maszyn wirtualnych przy użyciu konfiguracji stanu automatyzacji i chocolatey

W świecie DevOps istnieje wiele narzędzi, aby pomóc w różnych punktach w potoku ciągłej integracji. [Konfiguracja stanu](automation-dsc-overview.md) automatyzacji platformy Azure to nowy, powitalnym dodatkiem do opcji, które mogą stosować zespoły DevOps. 

Usługa Azure Automation to usługa zarządzana na platformie Microsoft Azure, która umożliwia automatyzację różnych zadań przy użyciu śmiób, węzłów i zasobów udostępnionych, takich jak poświadczenia, harmonogramy i zmienne globalne. Konfiguracja stanu automatyzacji platformy Azure rozszerza tę funkcję automatyzacji o narzędzia do konfiguracji żądanego stanu programu PowerShell (DSC). Oto świetny [przegląd](automation-dsc-overview.md).

W tym artykule pokazano, jak skonfigurować ciągłe wdrażanie (CD) dla komputera z systemem Windows. Technikę można łatwo rozszerzyć, aby uwzględnić dowolną liczbę komputerów z systemem Windows, na przykład w witrynie sieci Web, i przejść stamtąd do dodatkowych ról.

![Ciągłe wdrażanie maszyn wirtualnych IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="at-a-high-level"></a>Na wysokim poziomie

Dzieje się tu sporo, ale na szczęście można je podzielić na dwa główne procesy:

- Pisanie kodu i testowanie go, a następnie tworzenie i publikowanie pakietów instalacyjnych dla głównych i pomocniczych wersji systemu.
- Tworzenie maszyn wirtualnych, które instalują i wykonują kod w pakietach i go zarządzają.  

Po obu tych podstawowych procesów są w miejscu, jest łatwo automatycznie zaktualizować pakiet na maszynach wirtualnych, jak nowe wersje są tworzone i wdrażane.

## <a name="component-overview"></a>Omówienie komponentów

Menedżery pakietów, takie jak [apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) są dobrze znane w świecie Linuksa, ale nie tak bardzo w świecie systemu Windows.
[Chocolatey](https://chocolatey.org/) jest takie coś, a scott hanselman [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) na ten temat jest doskonałym wprowadzeniem. W skrócie, Chocolatey pozwala na użycie wiersza polecenia do instalowania pakietów z centralnego repozytorium na systemie operacyjnym Windows. Możesz tworzyć własne repozytorium i zarządzać nim, a Chocolatey może instalować pakiety z dowolnej liczby wyznaczonych repozytoriów.

[Program PowerShell DSC](/powershell/scripting/dsc/overview/overview) to narzędzie programu PowerShell, które umożliwia zadeklarowanie żądanej konfiguracji dla komputera. Na przykład, jeśli chcesz chocolatey zainstalowany, IIS zainstalowany, port 80 otwarty i wersja 1.0.0 witryny zainstalowanej, DSC Local Configuration Manager (LCM) implementuje tę konfigurację. Serwer ściągania DSC przechowuje repozytorium konfiguracji dla komputerów. LCM na każdym komputerze sprawdza się okresowo, aby sprawdzić, czy jego konfiguracja jest zgodna z zapisaną konfiguracją. Można zgłosić stan lub spróbować doprowadzić komputer z powrotem do wyrównania z przechowywanej konfiguracji. Można edytować zapisaną konfigurację na serwerze ściągania, aby spowodować wyrównanie komputera lub zestawu maszyn ze zmienioną konfiguracją.

Zasób DSC to moduł kodu, który ma określone możliwości, takie jak zarządzanie siecią, usługą Active Directory lub programem SQL Server. Chocolatey DSC Resource wie, jak uzyskać dostęp do serwera NuGet (między innymi), pobrać pakiety, zainstalować pakiety i tak dalej. W [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)znajduje się wiele innych zasobów DSC. Te moduły można zainstalować na serwerze ściągania konfiguracji usługi Azure Automation State do użycia przez konfiguracje.

Szablony Menedżera zasobów zapewniają deklaratywny sposób generowania infrastruktury, na przykład sieci, podsieci, zabezpieczeń sieci i routingu, modułów równoważenia obciążenia, kart sieciowych, maszyn wirtualnych itd. Oto [artykuł,](../azure-resource-manager/management/deployment-models.md) który porównuje model wdrażania Menedżera zasobów (deklaratywny) z modelem wdrażania usługi Azure Service Management (ASM lub klasycznym) (imperatywem). Ten artykuł zawiera omówienie dostawców podstawowych zasobów: obliczeń, magazynu i sieci.

Jedną z kluczowych cech szablonu Menedżera zasobów jest jego możliwość zainstalowania rozszerzenia maszyny Wirtualnej na maszynie Wirtualnej w miarę jego obsługi. Rozszerzenie maszyny Wirtualnej ma określone możliwości, takie jak uruchamianie skryptu niestandardowego, instalowanie oprogramowania antywirusowego i uruchamianie skryptu konfiguracji DSC. Istnieje wiele innych typów rozszerzeń maszyn wirtualnych.

## <a name="quick-trip-around-the-diagram"></a>Szybka podróż po diagramie

Począwszy od góry, można napisać kod, skompilować go, przetestować go, a następnie utworzyć pakiet instalacyjny. Chocolatey może obsługiwać różnego rodzaju pakiety instalacyjne, takie jak MSI, MSU, ZIP. I masz pełną moc PowerShell do rzeczywistej instalacji, jeśli natywne możliwości Chocolatey nie są do niego. Umieść paczkę w jakimś miejscu osiągalnym – repozytorium pakietów. W tym przykładzie użycia użyto folderu publicznego na koncie magazynu obiektów blob platformy Azure, ale może on znajdować się w dowolnym miejscu. Chocolatey działa natywnie z serwerami NuGet i kilka innych do zarządzania metadanych pakietu. [W tym artykule](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) opisano opcje. W przykładzie użycia użytek NuGet. Nuspec to metadane dotyczące pakietów. Informacje Nuspec są kompilowane do NuPkg i przechowywane na serwerze NuGet. Gdy konfiguracja żąda pakietu według nazwy i odwołuje się do serwera NuGet, chocolatey zasobów DSC na maszynie wirtualnej pobiera pakiet i instaluje go. Można również zażądać określonej wersji pakietu.

W lewym dolnym rogu obrazu znajduje się szablon usługi Azure Resource Manager. W tym przykładzie użycia rozszerzenie maszyny Wirtualnej rejestruje maszynę wirtualną z serwerem ściągania konfiguracji usługi Azure Automation State jako węzeł. Konfiguracja jest przechowywana w serwerze ściągania dwa razy: raz jako zwykły tekst i raz skompilowany jako plik MOF. W witrynie Azure portal MOF reprezentuje konfigurację węzła, w przeciwieństwie do prostej konfiguracji. Jest to artefakt, który jest skojarzony z węzłem, więc węzeł będzie znał jego konfiguracji. Szczegóły poniżej pokazują, jak przypisać konfigurację węzła do węzła.

Tworzenie Nuspec, kompilowanie go i przechowywanie go na serwerze NuGet jest małą rzeczą. A ty już zarządzasz maszynami wirtualnymi. 

Następnym krokiem do ciągłego wdrażania wymaga skonfigurowania serwera ściągania jeden raz, rejestrowania węzłów z nim jeden raz i tworzenia i przechowywania początkowej konfiguracji na serwerze. Ponieważ pakiety są uaktualniane i wdrażane do repozytorium, wystarczy odświeżyć konfigurację konfiguracji i węzła na serwerze ściągania w razie potrzeby.

Jeśli nie zaczynasz od szablonu Menedżera zasobów, to dobrze. Istnieją polecenia programu PowerShell ułatwiające rejestrowanie maszyn wirtualnych na serwerze ściągania. Aby uzyskać więcej informacji, zobacz [Maszyny dołączania do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Informacje o przykładzie użycia

Przykład użycia w tym artykule rozpoczyna się od maszyny Wirtualnej z ogólnego obrazu systemu Windows Server 2012 R2 z galerii platformy Azure. Możesz zacząć od dowolnego zapisanego obrazu, a następnie dostosować stamtąd z konfiguracją DSC.
Jednak zmiana konfiguracji, która jest pieczona w obrazie jest znacznie trudniejsze niż dynamicznie aktualizowanie konfiguracji przy użyciu DSC.

Nie trzeba używać szablonu Menedżera zasobów i rozszerzenia maszyny Wirtualnej, aby użyć tej techniki z maszynami wirtualnymi. A maszyny wirtualne nie muszą być na platformie Azure, aby być pod zarządzaniem dyskami CD. Wszystko, co jest konieczne jest to, że Chocolatey być zainstalowane i LCM skonfigurowany na maszynie Wirtualnej, więc wie, gdzie jest serwer ściągania.

Podczas aktualizowania pakietu na maszynie Wirtualnej, która jest w wersji produkcyjnej, należy wziąć tej maszyny Wirtualnej z rotacji, gdy aktualizacja jest zainstalowana. Sposób, w jaki to robisz, jest bardzo zróżnicowany. Na przykład za maszynę wirtualną za moduł równoważenia obciążenia platformy Azure, można dodać sondy niestandardowej. Podczas aktualizowania maszyny Wirtualnej, mają punkt końcowy sondy zwraca 400. Tweak niezbędne do spowodowania tej zmiany może być wewnątrz konfiguracji, jak można dostosować, aby przełączyć go z powrotem do powrotu 200 po zakończeniu aktualizacji.

Pełne źródło dla tego przykładu użycia znajduje się w [tym projekcie programu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) w usłudze GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Krok 1: Konfigurowanie serwera ściągania i konta automatyzacji

Przy uwierzytelnionym`Connect-AzAccount`( ) wiersz polecenia programu PowerShell: (może potrwać kilka minut, podczas gdy serwer ściągania jest skonfigurowany)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Konto automatyzacji można umieścić w dowolnym z następujących regionów (nazywanych również lokalizacjami): East US 2, South Central US, US Gov Virginia, West Europe, Southeast Asia, Japan East, Central India and Australia Southeast, Canada Central, North Europe.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: Wprowadzanie poprawek rozszerzenia maszyny Wirtualnej do szablonu Menedżera zasobów

Szczegółowe informacje dotyczące rejestracji maszyn wirtualnych (przy użyciu rozszerzenia maszyny Wirtualnej DSC programu PowerShell) podane w tym [szablonie szybkiego startu platformy Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)
Ten krok rejestruje nową maszynę wirtualną z serwerem ściągania na liście węzłów konfiguracji stanu. Częścią tej rejestracji jest określenie konfiguracji węzła, który ma zostać zastosowany do węzła. Ta konfiguracja węzła nie musi jeszcze istnieć na serwerze ściągania, więc dobrze, że krok 4 jest, gdy odbywa się to po raz pierwszy. Ale tutaj w kroku 2 musisz zdecydować nazwę węzła i nazwę konfiguracji. W tym przykładzie użycia węzeł jest "isvbox", a konfiguracja jest "ISVBoxConfig". Tak więc nazwa konfiguracji węzła (do określenia w DeploymentTemplate.json) to 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Krok 3: Dodawanie wymaganych zasobów DSC do serwera ściągania

Galeria programu PowerShell jest instrumentowana do instalowania zasobów DSC na koncie usługi Azure Automation.
Przejdź do odpowiedniego zasobu i kliknij przycisk "Wdrażanie w usłudze Azure Automation".

![Przykład galerii programu PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Inna technika niedawno dodana do witryny Azure portal umożliwia ściąganie nowych modułów lub aktualizowanie istniejących modułów. Kliknij zasób konta automatyzacji, kafelek Zasoby i na koniec kafelek Moduły. Ikona Galeria przeglądania umożliwia wyświetlenie listy modułów w galerii, przechodzenie do szczegółów i ostateczne importowanie ich do konta automatyzacji. Jest to świetny sposób, aby od czasu do czasu aktualizować moduły. Funkcja importu sprawdza zależności z innymi modułami, aby upewnić się, że nic nie zostanie zsynchronizowane.

Istnieje również ręczne podejście, używane tylko raz na zasób, chyba że chcesz uaktualnić go później. Aby uzyskać więcej informacji na temat tworzenia modułów integracji programu PowerShell, zobacz [Tworzenie modułów integracji dla usługi Azure Automation.](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

>[!NOTE]
>Struktura folderów modułu integracji programu PowerShell dla komputera z systemem Windows różni się nieco od struktury folderów oczekiwanej przez usługę Azure Automation. 

1. Zainstaluj [program Windows Management Framework w wersji 5](https://aka.ms/wmf5latest) (nie jest potrzebny dla systemu Windows 10).

2. Zainstaluj moduł integracyjny.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Skopiuj folder modułu z **folderu c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** do folderu tymczasowego.

4. Usuń próbki i dokumentację z folderu głównego.

5. Siąz folder główny, nazywając plik ZIP nazwą folderu.

6. Umieść plik ZIP w lokalizacji dostępnej HTTP, takiej jak magazyn obiektów blob na koncie usługi Azure Storage.

7. Uruchom następujące polecenie.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

W uwzględniony przykład implementuje te kroki dla cChoco i xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Krok 4: Dodaj konfigurację węzła do serwera ściągania

Nie ma nic specjalnego w pierwszym zaimportowaniu konfiguracji do serwera ściągania i kompilacji. Wszystkie późniejsze importy lub kompilacje tej samej konfiguracji wyglądają dokładnie tak samo. Za każdym razem, gdy aktualizujesz pakiet i musisz wypchnąć go do produkcji, wykonaj ten krok po upewnieniu się, że plik konfiguracyjny jest poprawny — w tym nowa wersja pakietu. Oto plik konfiguracyjny **ISVBoxConfig.ps1:**

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

Oto skrypt **New-ConfigurationScript.ps1** (zmodyfikowany w celu użycia modułu Az):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Te kroki powodują, że nowa konfiguracja węzła o nazwie **ISVBoxConfig.isvbox** jest umieszczana na serwerze ściągania. Nazwa konfiguracji węzła `configurationName.nodeName`jest zbudowana jako .

## <a name="step-5-create-and-maintain-package-metadata"></a>Krok 5: Tworzenie i obsługa metadanych pakietu

Dla każdego pakietu, który można umieścić w repozytorium pakietów, należy Nuspec, który opisuje go. Musi być skompilowany i przechowywany na serwerze NuGet. Proces ten opisano [tutaj](https://docs.nuget.org/create/creating-and-publishing-a-package). 

MyGet.org można **używać** jako serwera NuGet. Możesz kupić tę usługę, ale to jest bezpłatna jednostka SKU startowa. W [NuGet](https://www.nuget.org/)znajdziesz instrukcje dotyczące instalowania własnego serwera NuGet dla pakietów prywatnych.

## <a name="step-6-tie-it-all-together"></a>Krok 6: Związać to wszystko razem

Za każdym razem, gdy wersja przechodzi qa i jest zatwierdzony do wdrożenia, pakiet jest tworzony, a nuspec i nupkg są aktualizowane i wdrażane na serwerze NuGet. Konfiguracja (krok 4) musi również zostać zaktualizowana, aby uzgodnić nowy numer wersji. Następnie musi zostać wysłany do serwera ściągania i skompilowany.

Od tego momentu to do maszyn wirtualnych, które zależą od tej konfiguracji, aby wyciągnąć aktualizację i zainstalować go. Każda z tych aktualizacji jest prosta — tylko wiersz lub dwie programu PowerShell. W przypadku usługi Azure DevOps niektóre z nich są hermetyzowane w zadaniach kompilacji, które można połączyć w połączeniu w kompilacji. Ten [artykuł](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) zawiera więcej szczegółów. To [repozytorium GitHub](https://github.com/Microsoft/vso-agent-tasks) zawiera szczegółowe informacje o dostępnych zadaniach kompilacji.

## <a name="related-articles"></a>Pokrewne artykuły:
* [Omówienie usługi Azure Automation DSC](automation-dsc-overview.md)
* [Maszyny dołączające do zarządzania przez usługę Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Konfiguracja stanu automatyzacji platformy Azure](automation-dsc-overview.md).
- Aby uzyskać informacje o tym, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Aby zobaczyć przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md).
