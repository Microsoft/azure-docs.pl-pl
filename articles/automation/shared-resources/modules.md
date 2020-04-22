---
title: Zarządzanie modułami w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania modułami w usłudze Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770943"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie modułami w usłudze Azure Automation

Usługa Azure Automation umożliwia importowanie modułów programu PowerShell w celu włączenia poleceń cmdlet w uruchomieniu i zasobach DSC w konfiguracjach DSC. Moduły używane w usłudze Azure Automation obejmują:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Usługa AzureShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Moduł `Orchestrator.AssetManagement.Cmdlets` wewnętrzny dla agenta usługi Log Analytics dla systemu Windows
* [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Inne moduły programu PowerShell
* Utworzone moduły niestandardowe 

Podczas tworzenia konta automatyzacji usługa Azure Automation domyślnie importuje niektóre moduły. Zobacz [Moduły domyślne](#default-modules).

Gdy usługa Azure Automation wykonuje zadania kompilacji runbook i DSC, ładuje moduły do izolowania, gdzie można uruchomić elementy runbook i konfiguracje DSC można skompilować. Automatyzacja automatycznie umieszcza również wszystkie zasoby DSC w modułach na serwerze ściągania DSC. Maszyny mogą pobierać zasoby po zastosowaniu konfiguracji DSC.

>[!NOTE]
>Pamiętaj, aby zaimportować tylko moduły, które faktycznie potrzebują śmięty i konfiguracji DSC. Nie importuj moduł zestawienia, na przykład Az.Automation, w każdym przypadku.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="default-modules"></a>Moduły domyślne

W poniższej tabeli wymieniono moduły, które usługa Azure Automation importuje domyślnie podczas tworzenia konta automatyzacji. Automatyzacja może importować nowsze wersje tych modułów. Jednak nie można usunąć oryginalnej wersji z konta automatyzacji, nawet jeśli usuniesz nowszą wersję. Należy zauważyć, że te moduły domyślne zawierają kilka modułów AzureRM. 

> [!NOTE]
> Nie zaleca się zmieniania modułów i śmigieł na kontach automatyzacji, które zawierają rozwiązania. 

|Nazwa modułu|Wersja|
|---|---|
| AudytPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolcyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Diagnostyka programu Microsoft.PowerShell.Diagnostics |  |
| Zarządzanie programem Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Zarządzanie programem Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| Źródła PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc (Polityka bezpieczeństwa) | 2.1.0.0 |
| StateConfigCompositeŹródła | 1 |
| xDSCDołączenie do | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |


Moduły Az.Automation są preferowane w konfiguracjach czął i DSC. Jednak usługa Azure Automation nie importuje modułu Az pakietów zbiorczych automatycznie do żadnych nowych lub istniejących kont automatyzacji. Aby uzyskać więcej informacji na temat pracy z tymi modułami, zobacz [Migrowanie do modułów Az](#migrating-to-az-modules).

## <a name="internal-cmdlets"></a>Wewnętrzne polecenia cmdlet

W poniższej tabeli zdefiniowano wewnętrzne polecenia `Orchestrator.AssetManagement.Cmdlets` cmdlet obsługiwane przez moduł. Użyj ich w konfiguracjach żyła i konfiguracji DSC do interakcji z zasobami automatyzacji na koncie automatyzacji. Polecenia cmdlet są przeznaczone do pobierania wpisów tajnych z zaszyfrowanych zmiennych, poświadczeń i połączeń szyfrowanych. 

> [!NOTE]
> Polecenia cmdlet programu Azure PowerShell nie można uzyskać wpisów tajnych zasobów, które mogą pobierać wewnętrzne polecenia cmdlet. 

|Nazwa|Opis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationOb|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Te wewnętrzne polecenia cmdlet są dostępne w usłudze Windows Hybrid Runbook Worker, ale nie w usłudze Linux Hybrid Runbook Worker. Są one wykonywane z piaskownicy programu Orchestrator, która jest używana przez proces hybrydowy.  Ich użycie nie wymaga niejawnego połączenia z platformą Azure, jak podczas korzystania z konta Uruchom jako do uwierzytelniania.

Zamiast używać wewnętrznych poleceń cmdlet, należy użyć poleceń cmdlet Az lub AzureRM dla umownicze i konfiguracje uruchomione bezpośrednio na komputerze lub względem zasobów w środowisku. W takich przypadkach należy niejawnie połączyć się z platformą Azure podczas korzystania z poleceń cmdlet, jak podczas korzystania z konta Uruchom jako do uwierzytelniania na platformie Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Moduły obsługujące system Get-AutomationPSCredential

W przypadku rozwoju lokalnego przy użyciu `Get-AutomationPSCredential` zestawu narzędzi azure automation authoring toollet polecenie cmdlet jest częścią zestawu [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). W przypadku platformy Azure pracującej z kontekstem automatyzacji polecenie cmdlet znajduje się w `Orchestrator.AssetManagement.Cmdlets`pliku . Aby dowiedzieć się więcej na temat używania poświadczeń w usłudze Azure Automation, zobacz [Zasoby poświadczeń w usłudze Azure Automation.](credentials.md)

## <a name="migrating-to-az-modules"></a>Migracja do modułów Az

Istnieje kilka rzeczy, które należy wziąć pod uwagę podczas korzystania z modułów Az w usłudze Azure Automation:

* Rozwiązania wyższego poziomu na koncie automatyzacji mogą używać życzek i modułów. W związku z tym edytowanie książek runbook lub uaktualnianie modułów może potencjalnie powodować problemy z rozwiązaniami. Przed zaimportowanie modułów Az należy dokładnie przetestować wszystkie elementy runbook i rozwiązania na osobnym koncie automatyzacji. 

* Wszelkie modyfikacje modułów mogą negatywnie wpłynąć na [maszyny wirtualne Start/Stop podczas rozwiązania poza godzinami pracy.](../automation-solution-vm-management.md) 

* Importowanie modułu Az do konta automatyzacji nie powoduje automatycznego importowania modułu w sesji programu PowerShell używanej przez elementy runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

    * Gdy projekt runbook wywołuje polecenie cmdlet z modułu
    * Gdy projekt runbook zaimportuje `Import-Module` moduł jawnie z poleceniem cmdlet
    * Gdy projekt runbook importuje inny moduł zależny

Po zakończeniu migracji modułów nie próbuj uruchamiać uruchomieniu ćnu przy użyciu modułów AzureRM na koncie automatyzacji. Zaleca się również, aby nie importować ani nie aktualizować modułów usługi AzureRM na koncie. Należy wziąć pod uwagę konto zmigrowane do Az.Automation i działać tylko z modułami Az. 

>[!IMPORTANT]
>Podczas tworzenia nowego konta automatyzacji usługa Azure Automation domyślnie instaluje moduły AzureRM. Nadal można zaktualizować podręczniki runbook za pomocą poleceń cmdlet azurerm. Jednak nie należy uruchamiać tych śmiętków runbook.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zatrzymywać i odcinać wszystkie programy runbook korzystające z modułów AzureRM

Aby upewnić się, że nie uruchamiasz żadnych istniejących wiązek runbook, które używają modułów AzureRM, zatrzymaj i odeślij wszystkie, których dotyczy problem, żylile. Możesz zobaczyć, jakie harmonogramy istnieją i które harmonogramy usunąć, uruchamiając kod podobny do tego przykładu:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Ważne jest, aby przejrzeć każdy harmonogram oddzielnie, aby upewnić się, że można go zremisować w przyszłości dla śmiętków runbook, jeśli to konieczne.

### <a name="import-the-az-modules"></a>Importowanie modułów Az

W tej sekcji opisano, jak zaimportować moduły Az w witrynie Azure portal. Pamiętaj, aby zaimportować tylko moduły Az, których potrzebujesz, a nie cały moduł Az.Automation. Ponieważ [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla innych modułów Az, należy zaimportować ten moduł przed innymi.

1. Na koncie automatyzacji wybierz pozycję **Moduły** w obszarze **Zasoby udostępnione**. 
2. Kliknij **pozycję Przeglądaj galerię,** aby otworzyć stronę Przeglądaj galerię.  
3. Na pasku wyszukiwania wprowadź nazwę modułu, `Az.Accounts`na przykład . 
4. Na stronie Moduł programu PowerShell kliknij przycisk **Importuj,** aby zaimportować moduł do konta automatyzacji.

    ![Importowanie modułów do konta automatyzacji](../media/modules/import-module.png)

Ten proces importowania można również wykonać za pośrednictwem [galerii programu PowerShell,](https://www.powershellgallery.com) wyszukując moduł do zaimportowania. Po znalezieniu modułu wybierz go, wybierz kartę **Usługi Azure Automation** i kliknij pozycję **Wdrażanie w usłudze Azure Automation**.

![Importowanie modułów bezpośrednio z galerii](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testowanie eks-ów

Po zaimportowaniu modułów Az do konta automatyzacji można rozpocząć edycję śmiób, aby korzystać z nowych modułów. Większość poleceń cmdlet ma takie same nazwy jak dla modułów AzureRM, z tą różnicą, że prefiks AzureRM (lub AzureRm) został zmieniony na Az. Aby uzyskać listę modułów, które nie są zgodne z tą konwencją nazewnictwa, zobacz [listę wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jednym ze sposobów przetestowania modyfikacji śmiętu w celu `Enable-AzureRmAlias -Scope Process` użycia nowych poleceń cmdlet jest użycie na początku zestawu runbook. Dodając to polecenie do egobiegu, skrypt może działać bez zmian.

## <a name="authoring-modules"></a>Moduły tworzenia

Zaleca się, aby postępować zgodnie z zagadnieniami w tej sekcji podczas tworzenia modułu programu PowerShell do użycia w usłudze Azure Automation.

### <a name="references-to-azurerm-and-az"></a>Odwołania do azurerm i az

Jeśli odwołuje się do modułów Az w module, upewnij się, że nie są również odwoływanie się do modułów AzureRM. Nie można używać obu zestawów modułów w tym samym czasie. 

### <a name="version-folder"></a>Folder Wersja

NIE dołączaj folderu wersji do pakietu **.zip** dla swojego modułu.  Ten problem jest mniej problemem dla śmięty, ale powoduje problem z usługą konfiguracji stanu (DSC). Usługa Azure Automation automatycznie tworzy folder wersji, gdy moduł jest dystrybuowany do węzłów zarządzanych przez DSC. Jeśli istnieje folder wersji, kończy się z dwóch wystąpień. Oto przykładowa struktura folderów dla modułu DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informacje o pomocy

Dołącz streszczenie, opis i identyfikator URI pomocy dla każdego polecenia cmdlet w module. W programie PowerShell można zdefiniować informacje pomocy `Get-Help` dla poleceń cmdlet przy użyciu polecenia cmdlet. W poniższym przykładzie pokazano, jak zdefiniować streszczenie i pomóc identyfikatora URI w pliku modułu **.psm1.**

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Podanie tych informacji pokazuje `Get-Help` tekst pomocy za pośrednictwem polecenia cmdlet w konsoli programu PowerShell. Ten tekst jest również wyświetlany w witrynie Azure portal.

  ![Pomoc modułu integracji](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ połączenia

Jeśli moduł łączy się z usługą zewnętrzną, należy zdefiniować [typ połączenia](#adding-a-connection-type-to-your-module). Każde polecenie cmdlet w module powinno akceptować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr. Użytkownicy mapują parametry zasobu połączenia na odpowiednie parametry polecenia cmdlet za każdym razem, gdy wywołują polecenie cmdlet. Poniższy przykład elementów runbook, na podstawie przykładu w poprzedniej `ContosoConnection` sekcji, używa zasobu połączenia Contoso wywoływanego do uzyskiwania dostępu do zasobów Contoso i zwracania danych z usługi zewnętrznej. W tym przykładzie pola są `UserName` mapowane na `Password` `PSCredential` i właściwości obiektu, a następnie przekazywane do polecenia cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Łatwiejszym i lepszym sposobem podejścia do tego zachowania jest bezpośrednie przekazanie obiektu połączenia do polecenia cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Podobne zachowanie dla poleceń cmdlet można włączyć, umożliwiając im bezpośrednie akceptowanie obiektu połączenia jako parametru, a nie tylko pola połączenia dla parametrów. Zazwyczaj chcesz zestaw parametrów dla każdego, tak aby użytkownik nie korzystający z usługi Azure Automation można wywołać polecenia cmdlet bez konstruowania mieszania do działania jako obiekt połączenia. Zestaw parametrów `UserAccount` służy do przekazywania właściwości pola połączenia. `ConnectionObject`umożliwia przejście połączenia bezpośrednio przez.

### <a name="output-type"></a>Typ wyjścia

Zdefiniuj typ wyjściowy dla wszystkich poleceń cmdlet w module. Definiowanie typu danych wyjściowych dla polecenia cmdlet umożliwia intellisense w czasie projektowania, aby ułatwić określenie właściwości wyjściowych polecenia cmdlet podczas tworzenia. Praktyka ta jest szczególnie przydatna podczas tworzenia graficznego podręcznika runbook, dla którego wiedza w czasie projektowania jest kluczem do łatwego doświadczenia użytkownika z modułem.

`[OutputType([<MyOutputType>])]` Dodaj, `MyOutputType` gdzie jest prawidłowy typ. Aby dowiedzieć `OutputType`się więcej o , zobacz [Informacje o funkcjach OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Poniższy kod jest przykładem dodawania `OutputType` do polecenia cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Typ danych wyjściowych graficznego elementu Runbook](../media/modules/runbook-graphical-module-output-type.png)

  To zachowanie jest podobne do funkcji "typ z wyprzedzeniem" danych wyjściowych polecenia cmdlet w programie PowerShell ISE bez konieczności uruchamiania go.

  ![Funkcja IntelliSense programu POSH](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stan polecenia cmdlet

Wszystkie polecenia cmdlet w module są bezstanowe. Wiele zadań uruchomieniu można jednocześnie `AppDomain` uruchamiać w tym samym i tym samym procesie i piaskownicy. Jeśli istnieje dowolny stan współużytkowane na tych poziomach, zadania mogą mieć wpływ na siebie nawzajem. To zachowanie może prowadzić do sporadyczne i trudne do zdiagnozowania problemy. Oto przykład czego nie robić:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Zależność modułu

Upewnij się, że moduł jest w pełni zawarty w pakiecie, który można skopiować za pomocą xcopy. Moduły usługi Azure Automation są dystrybuowane do skrzynek izolowanych automatyzacji podczas wykonywania uruchomieniu. systemu. Moduły muszą działać niezależnie od hosta, który je uruchamia. 

Powinieneś być w stanie spakować i przenieść pakiet modułu i mieć go działać normalnie po zaimportowaniu do środowiska programu PowerShell innego hosta. Aby tak się stało, upewnij się, że moduł nie zależy od plików spoza folderu modułu, który jest spakowany, gdy moduł jest importowany do usługi Azure Automation. 

Moduł nie powinien zależeć od żadnych unikatowych ustawień rejestru na hoście. Przykładami są ustawienia, które są dokonywane po zainstalowaniu produktu. 

### <a name="module-file-paths"></a>Ścieżki plików modułu

Upewnij się, że wszystkie pliki w module mają ścieżki z mniej niż 140 znaków. Wszystkie ścieżki o długości powyżej 140 znaków powodują problemy z importowaniem śmięty. Usługa Azure Automation nie może zaimportować pliku o rozmiarze ścieżki `Import-Module`powyżej 140 znaków do sesji programu PowerShell z programem .

## <a name="importing-modules"></a>Importowanie modułów

W tej sekcji zdefiniowano kilka sposobów, które można zaimportować moduł do konta automatyzacji. 

### <a name="import-modules-in-azure-portal"></a>Importowanie modułów w witrynie Azure portal

Aby zaimportować moduł w witrynie Azure portal:

1. Przejdź do swojego konta automatyzacji.
2. Wybierz **moduły** w obszarze **Zasoby udostępnione**.
3. Kliknij **pozycję Dodaj moduł**. 
4. Wybierz plik **zip** zawierający moduł.
5. Kliknij **przycisk OK,** aby rozpocząć importowanie procesu.

### <a name="import-modules-using-powershell"></a>Importowanie modułów przy użyciu programu PowerShell

Polecenia cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) można użyć do zaimportowania modułu do konta automatyzacji. Polecenie cmdlet przyjmuje adres URL pakietu .zip modułu.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Można również użyć tego samego polecenia cmdlet, aby bezpośrednio zaimportować moduł z galerii programu PowerShell. Upewnij się, `ModuleName` `ModuleVersion` że można pobrać i z [galerii programu PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importowanie modułów z galerii programu PowerShell

Moduły [galerii programu PowerShell](https://www.powershellgallery.com) można importować bezpośrednio z galerii lub z konta automatyzacji.

Aby zaimportować moduł bezpośrednio z galerii programu PowerShell:

1. Przejdź https://www.powershellgallery.com do i wyszukaj moduł do zaimportowania.
2. Kliknij **pozycję Wdrażanie w usłudze Azure Automation** na karcie **Automatyzacja platformy Azure** w obszarze Opcje **instalacji**. Ta akcja otwiera witrynę Azure portal. 
3. Na stronie Importowanie wybierz konto automatyzacji i kliknij przycisk **OK**.

![Moduł importu galerii programu PowerShell](../media/modules/powershell-gallery.png)

Aby zaimportować moduł Galerii programu PowerShell bezpośrednio z konta automatyzacji:

1. Wybierz **moduły** w obszarze **Zasoby udostępnione**. 
2. Na stronie Moduły kliknij pozycję **Przeglądaj galerię**, a następnie wyszukaj w galerii moduł. 
3. Wybierz moduł do zaimportowania i kliknij przycisk **Importuj**. 
4. Na stronie Importuj kliknij przycisk **OK,** aby rozpocząć proces importowania.

![Importowanie galerii programu PowerShell z witryny Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Usuwanie modułów

Jeśli masz problemy z modułem lub musisz przywrócić poprzednią wersję modułu, możesz usunąć go z konta automatyzacji. Nie można usunąć oryginalnych wersji [modułów domyślnych,](#default-modules) które są importowane podczas tworzenia konta automatyzacji. Jeśli moduł do usunięcia jest nowszą wersją jednego z [modułów domyślnych,](#default-modules)jest wycofywany do wersji zainstalowanej na koncie automatyzacji. W przeciwnym razie każdy moduł usunięty z konta automatyzacji zostanie usunięty.

### <a name="delete-modules-in-azure-portal"></a>Usuwanie modułów w witrynie Azure portal

Aby usunąć moduł w witrynie Azure portal:

1. Przejdź do konta automatyzacji i wybierz **moduły** w obszarze **Zasoby udostępnione**. 
2. Wybierz moduł, który chcesz usunąć. 
3. Na stronie **Moduł** wybierz pozycję **Usuń**. Jeśli ten moduł jest jednym z [modułów domyślnych,](#default-modules)jest wycofywany do wersji, która istniała podczas tworzenia konta automatyzacji.

### <a name="delete-modules-using-powershell"></a>Usuwanie modułów przy użyciu programu PowerShell

Aby usunąć moduł za pośrednictwem programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Dodawanie typu połączenia do modułu

Można podać [niestandardowy typ połączenia](../automation-connections.md) do użycia na koncie automatyzacji, dodając opcjonalny plik metadanych do modułu. Ten plik określa typ połączenia usługi Azure Automation, który ma być używany z poleceniami cmdlet modułu na koncie automatyzacji. Aby dowiedzieć się więcej na temat tworzenia modułu programu PowerShell, zobacz [Jak napisać moduł skryptu programu PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Używanie połączenia niestandardowego w witrynie Azure portal](../media/modules/connection-create-new.png)

Plik określający właściwości typu połączenia nosi nazwę ** &lt;ModuleName&gt;-Automation.json** i znajduje się w folderze modułu skompresowanego pliku **zip.** Ten plik zawiera pola połączenia, które są wymagane do połączenia z systemem lub usługą, którą reprezentuje moduł. Konfiguracja umożliwia utworzenie typu połączenia w usłudze Azure Automation. Za pomocą tego pliku można ustawić nazwy pól, typy i czy pola są zaszyfrowane lub opcjonalne dla typu połączenia modułu. Poniższy przykład jest szablonem w formacie pliku **json,** który definiuje nazwę użytkownika i właściwości hasła:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat korzystania z modułów programu Azure PowerShell, zobacz [Wprowadzenie do programu Azure PowerShell.](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)
* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz [Pisanie modułu programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
