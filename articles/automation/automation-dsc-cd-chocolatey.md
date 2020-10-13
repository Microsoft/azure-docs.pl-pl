---
title: Konfigurowanie Azure Automation ciągłego wdrażania przy użyciu czekolady
description: W tym artykule opisano sposób konfigurowania ciągłego wdrażania z konfiguracją stanu i Menedżera pakietów czekolady.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 87504625c298c4fb858ff90430d707081e87cd5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186575"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Konfigurowanie ciągłego wdrażania za pomocą narzędzia Chocolatey

Na świecie DevOps istnieje wiele narzędzi, które pomagają w różnych punktach w potoku ciągłej integracji. [Konfiguracja stanu](automation-dsc-overview.md) Azure Automation jest nowym uzupełnieniem opcji, które mogą być używane przez zespoły DevOps. 

Azure Automation to usługa zarządzana w programie Microsoft Azure, która umożliwia automatyzację różnych zadań przy użyciu elementów Runbook, węzłów i zasobów udostępnionych, takich jak poświadczenia, harmonogramy i zmienne globalne. Azure Automation konfiguracja stanu rozszerza tę możliwość automatyzacji w celu uwzględnienia narzędzi konfiguracji żądanego stanu (DSC) programu PowerShell. Oto doskonały [Przegląd](automation-dsc-overview.md).

W tym artykule pokazano, jak skonfigurować ciągłe wdrażanie (CD) dla komputera z systemem Windows. Możesz łatwo zwiększyć technikę, aby dołączać tyle komputerów z systemem Windows, ile jest to konieczne w roli, na przykład witryny sieci Web, a następnie przejść do innych ról.

![Ciągłe wdrażanie dla maszyn wirtualnych IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na wysokim poziomie

W tym miejscu nie ma już miejsca, ale na szczęście można podzielić na dwa główne procesy:

- Pisanie kodu i testowanie go, a następnie Tworzenie i publikowanie pakietów instalacyjnych dla wersji głównych i pomocniczych systemu.
- Tworzenie maszyn wirtualnych, które instalują i wykonują kod w pakietach, oraz zarządzanie nimi.  

Po utworzeniu obu tych podstawowych procesów można łatwo automatycznie aktualizować pakiet na swoich maszynach wirtualnych, gdy są tworzone i wdrażane nowe wersje.

## <a name="component-overview"></a>Przegląd składnika

Menedżerów pakietów, takich jak [apt — get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) , są dobrze znani w świecie Linux, ale nie w świecie systemu Windows.
[Czekolada](https://chocolatey.org/) to taka rzecz, a [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) Scott Hanselman w temacie jest doskonałym wprowadzeniem. W Nutshell, czekolada umożliwia korzystanie z wiersza polecenia w celu zainstalowania pakietów z centralnego repozytorium w systemie operacyjnym Windows. Możesz utworzyć własne repozytorium i zarządzać nim, a czekolada może instalować pakiety z dowolnej liczby wyznaczonych repozytoriów.

[POWERSHELL DSC](/powershell/scripting/dsc/overview/overview) to narzędzie programu PowerShell, które pozwala na zadeklarować konfigurację dla maszyny. Jeśli na przykład chcesz zainstalować instalację czekoladową, zainstalowane usługi IIS, port 80 otwarty i wersja 1.0.0 witryny sieci Web, funkcja Local Configuration Manager (LCM) implementuje tę konfigurację. Serwer ściągający DSC przechowuje repozytorium konfiguracji dla maszyn. LCM sprawdza okresowo, aby sprawdzić, czy jego konfiguracja jest zgodna z przechowywaną konfiguracją. Może zgłosić stan lub podjąć próbę przełączenia komputera w celu dostosowania go do przechowywanej konfiguracji. Można edytować przechowywaną konfigurację na serwerze ściągania, aby spowodować, że maszyna lub zestaw maszyn mają być wyrównane z zmienioną konfiguracją.

Zasób DSC to moduł kodu, który ma określone możliwości, takie jak zarządzanie sieciami, Active Directory lub SQL Server. Zasób czekolady DSC wie, jak uzyskać dostęp do serwera NuGet (między innymi), pobrać pakiety, zainstalować pakiety i tak dalej. W [Galeria programu PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)istnieje wiele innych zasobów DSC. Te moduły są instalowane na serwerze ściągania konfiguracji stanu Azure Automation do użycia przez konfiguracje.

Szablony Menedżer zasobów zapewniają deklaratywny sposób generowania infrastruktury, na przykład sieci, podsieci, zabezpieczeń sieciowych i routingu, modułów równoważenia obciążenia, kart sieciowych, maszyn wirtualnych i tak dalej. Oto [artykuł](../azure-resource-manager/management/deployment-models.md) porównujący model wdrażania Menedżer zasobów (deklaracyjne) z modelem wdrażania usługi Azure Service Management (ASM lub klasycznym). Ten artykuł zawiera omówienie podstawowych dostawców zasobów: obliczenia, magazyn i sieć.

Jedną z kluczowych funkcji szablonu Menedżer zasobów jest możliwość instalacji rozszerzenia maszyny wirtualnej na maszynie wirtualnej w ramach aprowizacji. Rozszerzenie maszyny wirtualnej ma określone możliwości, takie jak uruchamianie skryptu niestandardowego, instalowanie oprogramowania antywirusowego i uruchamianie skryptu konfiguracji DSC. Istnieje wiele innych typów rozszerzeń maszyn wirtualnych.

## <a name="quick-trip-around-the-diagram"></a>Szybka podróż na diagramie

Zaczynając od góry, napiszesz swój kod, kompilujesz go, testujesz, a następnie utworzysz pakiet instalacyjny. Czekolada może obsługiwać różne typy pakietów instalacyjnych, takich jak MSI, MSU, ZIP. I masz pełną moc programu PowerShell, aby przeprowadzić rzeczywistą instalację, jeśli natywne możliwości nie są do niego dostępne. Umieść pakiet w miejscu dostępnym — repozytorium pakietu. Ten przykład użycia używa folderu publicznego na koncie usługi Azure Blob Storage, ale może być dowolnym z dowolnego miejsca. Czekolada działa natywnie z serwerami NuGet i kilkoma innymi na potrzeby zarządzania metadanymi pakietu. W [tym artykule](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) opisano opcje. Przykład użycia korzysta z NuGet. Nuspec to metadane dotyczące pakietów. Informacje nuspec są kompilowane w NuPkg i przechowywane na serwerze NuGet. Gdy konfiguracja żąda pakietu przy użyciu nazwy i odwołuje się do serwera NuGet, zasób z czekolady DSC na maszynie wirtualnej poniesie pakiet i zainstaluje go. Możesz również zażądać określonej wersji pakietu.

W lewym dolnym rogu obrazu istnieje szablon Azure Resource Manager. W tym przykładzie użycia rozszerzenie maszyny wirtualnej rejestruje maszynę wirtualną z serwerem ściągania konfiguracji stanu Azure Automation jako węzeł. Konfiguracja jest przechowywana na serwerze ściągania dwa razy: raz jako zwykły tekst i po skompilowaniu jako plik MOF. W Azure Portal plik MOF reprezentuje konfigurację węzła, a nie prostą konfigurację. Jest to artefakt skojarzony z węzłem, dzięki czemu węzeł będzie znać jego konfigurację. Szczegóły poniżej przedstawiają sposób przypisywania konfiguracji węzła do węzła.

Utworzenie nuspec, skompilowanie go i przechowywanie go na serwerze NuGet jest małym zadaniem. I już zarządzasz maszynami wirtualnymi. 

Następnym krokiem do ciągłego wdrażania jest wymaganie skonfigurowania serwera ściągania jednorazowo, zarejestrowania węzłów za pomocą tej operacji oraz utworzenia i zapisania konfiguracji początkowej na serwerze. Gdy pakiety są uaktualniane i wdrażane w repozytorium, w razie potrzeby należy odświeżyć konfigurację i konfigurację węzła na serwerze ściągania.

Jeśli nie korzystasz z szablonu Menedżer zasobów, jest to dokładne. Istnieją polecenia programu PowerShell ułatwiające rejestrowanie maszyn wirtualnych na serwerze ściągania. Aby uzyskać więcej informacji, zobacz sekcję dołączanie [maszyn w celu zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Informacje o przykładzie użycia

Przykład użycia w tym artykule zaczyna się od maszyny wirtualnej z ogólnego obrazu systemu Windows Server 2012 R2 z galerii platformy Azure. Możesz rozpocząć od dowolnego przechowywanego obrazu, a następnie dostosować je za pomocą konfiguracji DSC.
Jednak zmiana konfiguracji rozszerzania na obraz jest znacznie trudniejsza niż dynamiczne aktualizowanie konfiguracji przy użyciu DSC.

Nie musisz używać szablonu Menedżer zasobów i rozszerzenia maszyny wirtualnej, aby użyć tej techniki z maszynami wirtualnymi. A Twoje maszyny wirtualne nie muszą znajdować się na platformie Azure, aby można było zarządzać nimi. Jest to konieczne, aby zainstalować czekoladę i LCM skonfigurowany na maszynie wirtualnej, aby wiedział, gdzie jest serwer ściągania.

W przypadku aktualizowania pakietu na maszynie wirtualnej, która jest w środowisku produkcyjnym, należy przetworzyć maszynę wirtualną poza rotacją, podczas gdy aktualizacja jest zainstalowana. Jak to zrobić. Na przykład w przypadku maszyny wirtualnej za Azure Load Balancer można dodać sondę niestandardową. Podczas aktualizowania maszyny wirtualnej należy zwrócić punkt końcowy sondy o 400. Dostosowanie niezbędne do tego, że ta zmiana może znajdować się w konfiguracji, ponieważ może to spowodować przełączenie go w celu powrotu do 200 po zakończeniu aktualizacji.

Pełne źródło tego przykładu użycia znajduje się w [tym projekcie programu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) w witrynie GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Krok 1. Konfigurowanie serwera ściągania i konta usługi Automation

W `Connect-AzAccount` wierszu polecenia programu PowerShell uwierzytelnionego (): (może upłynąć kilka minut, gdy serwer ściągania jest skonfigurowany)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Konto usługi Automation można umieścić w dowolnym z następujących regionów (nazywanych również lokalizacjami): Wschodnie stany USA 2, Południowo-środkowe stany USA, US Gov Wirginia, Europa Zachodnia, Azja Południowo-Wschodnia, Japonia Wschodnia, Indie Środkowe i Australia Południowo-Wschodnia, Kanada Środkowa i Europa Północna.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2. naczynienie rozszerzenia maszyny wirtualnej do szablonu Menedżer zasobów

Szczegóły dotyczące rejestracji maszyny wirtualnej (przy użyciu rozszerzenia maszyny wirtualnej DSC programu PowerShell) dostępnego w tym [szablonie szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Ten krok rejestruje nową maszynę wirtualną na serwerze ściągania z listy węzłów konfiguracji stanu. Częścią tej rejestracji jest określenie konfiguracji węzła, która ma zostać zastosowana do węzła. Ta konfiguracja węzła nie musi jeszcze istnieć na serwerze ściągania, więc warto wykonać krok 4, aby to zrobić po raz pierwszy. Ale w kroku 2 należy podjąć decyzję o nazwie węzła i nazwie konfiguracji. W tym przykładzie użycia węzeł ma wartość "isvbox", a konfiguracja to "ISVBoxConfig". W związku z tym nazwa konfiguracji węzła (do określenia w DeploymentTemplate.json) ma wartość "ISVBoxConfig. isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Krok 3. Dodawanie wymaganych zasobów DSC do serwera ściągania

Galeria programu PowerShell jest Instrumentacją do instalowania zasobów DSC na koncie Azure Automation.
Przejdź do żądanego zasobu, a następnie kliknij przycisk "wdróż do Azure Automation".

![Przykład Galeria programu PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Inna technika ostatnio dodana do Azure Portal umożliwia ściąganie nowych modułów lub aktualizowanie istniejących modułów. Kliknij kafelek zasób konta usługi Automation, elementy zawartości i na końcu kafelka moduły. Ikona Przeglądaj Galerię umożliwia wyświetlenie listy modułów w galerii, przechodzenie do szczegółów i ostateczne Importowanie do konta usługi Automation. Jest to świetny sposób, aby zapewnić aktualność swoich modułów od czasu do czasu. Ponadto funkcja import sprawdza zależności z innymi modułami, aby upewnić się, że nic nie jest zsynchronizowane.

Istnieje również ręczne podejście, które jest używane tylko raz dla każdego zasobu, chyba że chcesz uaktualnić go później. Aby uzyskać więcej informacji na temat tworzenia modułów integracji programu PowerShell, zobacz [Tworzenie modułów integracji dla Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Struktura folderów modułu integracji programu PowerShell dla komputera z systemem Windows jest nieco inna niż struktura folderów oczekiwana przez Azure Automation. 

1. Zainstaluj program [Windows Management Framework V5](https://aka.ms/wmf5latest) (niewymagany w przypadku systemu Windows 10).

2. Zainstaluj moduł integracji.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Skopiuj folder module z **lokalizacji C:\Program Files\WindowsPowerShell\Modules\MODULE-Name** do folderu tymczasowego.

4. Usuń przykłady i dokumentację z folderu głównego.

5. Nazwij folder główny, nadając mu nazwę folderu.

6. Umieść plik ZIP w dostępnej lokalizacji HTTP, takiej jak BLOB Storage na koncie usługi Azure Storage.

7. Uruchom następujące polecenie.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

W dołączonym przykładzie są implementowane następujące kroki dla cChoco i xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Krok 4. Dodawanie konfiguracji węzła do serwera ściągania

Nie ma żadnych specjalnych informacji o tym, jak po raz pierwszy zaimportować konfigurację do serwera ściągania i skompilować. Wszystkie późniejsze Importy lub kompilacje tej samej konfiguracji wyglądają dokładnie tak samo. Za każdym razem, gdy aktualizujesz pakiet i konieczne jest wypchnięcie go do środowiska produkcyjnego, wykonaj ten krok po upewnieniu się, że plik konfiguracji jest prawidłowy — w tym nowej wersji pakietu. Oto **ISVBoxConfig.ps1**pliku konfiguracji:

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

Oto skrypt **New-ConfigurationScript.ps1** (zmodyfikowany do korzystania z AZ module):

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

Te kroki powodują powstanie nowej konfiguracji węzła o nazwie **ISVBoxConfig. isvbox** umieszczanej na serwerze ściągania. Nazwa konfiguracji węzła jest skompilowana jako `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Krok 5. Tworzenie i konserwowanie metadanych pakietu

Dla każdego pakietu, który został umieszczony w repozytorium pakietu, potrzebne jest nuspec. Musi być skompilowana i przechowywana na serwerze NuGet. Ten proces jest opisany [tutaj](https://docs.nuget.org/create/creating-and-publishing-a-package). 

Możesz użyć **MyGet.org** jako serwera NuGet. Możesz kupić tę usługę, ale Thee jest bezpłatną starterem jednostki SKU. W pakiecie [NuGet](https://www.nuget.org/)znajdziesz instrukcje dotyczące instalowania własnego serwera NuGet dla pakietów prywatnych.

## <a name="step-6-tie-it-all-together"></a>Krok 6. powiązanie całości

Za każdym razem, gdy wersja przekazuje funkcję pytania i jest zatwierdzona do wdrożenia, tworzony jest pakiet, a nuspec i NUPKG są aktualizowane i wdrażane na serwerze NuGet. Aby można było wyrazić zgodę na nowy numer wersji, należy również zaktualizować konfigurację (krok 4). Należy je następnie wysłać do serwera ściągania i skompilować.

Od tego momentu do maszyn wirtualnych, które są zależne od tej konfiguracji, można ściągnąć aktualizację i zainstalować ją. Każda z tych aktualizacji jest prosta — tylko wiersz lub dwa z programu PowerShell. W przypadku usługi Azure DevOps niektóre z nich są hermetyzowane w zadaniach kompilacji, które można połączyć ze sobą w kompilacji. Ten [artykuł](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) zawiera więcej szczegółów. To [repozytorium GitHub](https://github.com/Microsoft/vso-agent-tasks) zawiera szczegółowe informacje o dostępnych zadaniach kompilacji.

## <a name="related-articles"></a>Pokrewne artykuły:
* [Azure Automation DSC — Omówienie](automation-dsc-overview.md)
* [Dołączanie maszyn w celu zarządzania przez Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [Omówienie konfiguracji stanu Azure Automation](automation-dsc-overview.md).
- Aby rozpocząć korzystanie z tej funkcji, zobacz Rozpoczynanie [pracy z konfiguracją stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
