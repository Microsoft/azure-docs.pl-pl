---
title: Zarządzanie modułami w usłudze Azure Automation
description: W tym artykule opisano sposób używania modułów programu PowerShell do włączania poleceń cmdlet w zasobach Runbook i DSC w konfiguracjach DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaff96907b48ddc0fc92296a015ceb063149e6ec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832756"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie modułami w usłudze Azure Automation

Azure Automation korzysta z wielu modułów programu PowerShell, aby włączyć polecenia cmdlet w zasobach Runbook i DSC w konfiguracjach DSC. Obsługiwane moduły obejmują:

* [Azure PowerShell Az.Automation.](/powershell/azure/new-azureps-module-az)
* [Azure PowerShell AzureRM.Automation.](/powershell/module/azurerm.automation/)
* Inne moduły programu PowerShell.
* Moduł `Orchestrator.AssetManagement.Cmdlets` wewnętrzny.
* Moduły języka Python 2.
* Moduły niestandardowe, które tworzysz.

Podczas tworzenia konta usługi Automation program Azure Automation niektóre moduły. Zobacz [Moduły domyślne.](#default-modules)

## <a name="sandboxes"></a>Piaskownice

Gdy automatyzacja wykonuje zadania kompilacji runbook i DSC, ładuje moduły do piaskownic, w których można uruchamiać te podręczniki i kompilować konfiguracje DSC. Automatyzacja automatycznie umieszcza również wszystkie zasoby DSC w modułach na serwerze ściągania DSC. Maszyny mogą ściągać zasoby po zastosowaniu konfiguracji DSC.

>[!NOTE]
>Pamiętaj, aby zaimportować tylko moduły, których wymagają twoje moduły Runbook i konfiguracje DSC. Nie zalecamy importowania głównego modułu Az. Zawiera on wiele innych modułów, które mogą nie być potrzebne, co może powodować problemy z wydajnością. Zamiast tego zaimportuj poszczególne moduły, takie jak Az.Compute.

Piaskownica w chmurze obsługuje maksymalnie 48 wywołań systemowych i ogranicza wszystkie pozostałe wywołania ze względów bezpieczeństwa. Inne funkcje, takie jak zarządzanie poświadczeniami i niektóre sieci, nie są obsługiwane w piaskownicy chmury.

Ze względu na liczbę dołączonych modułów i polecenia cmdlet trudno jest wcześniej wiedzieć, które polecenia cmdlet będą owały nieobsługiwane wywołania. Ogólnie rzecz biorąc, występuje problem z poleceniami cmdlet, które wymagają podwyższonego poziomu dostępu, wymagają poświadczeń jako parametru lub polecenia cmdlet związane z siecią. Wszystkie polecenia cmdlet, które wykonują operacje sieciowe pełnego stosu, nie są obsługiwane w piaskownicy, w tym [Connect-AipService](/powershell/module/aipservice/connect-aipservice) z modułu programu PowerShell usługi AIPService i [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) z modułu DNSClient.

Są to znane ograniczenia dotyczące piaskownicy. Zalecanym obejściem jest wdrożenie hybrydowego procesu [roboczego runbook lub](../automation-hybrid-runbook-worker.md) użycie [Azure Functions](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Moduły domyślne

W poniższej tabeli wymieniono moduły, Azure Automation które są importowany domyślnie podczas tworzenia konta usługi Automation. Automatyzacja może importować nowsze wersje tych modułów. Nie można jednak usunąć oryginalnej wersji z konta usługi Automation, nawet jeśli usuniesz nowszą wersję. Należy pamiętać, że te moduły domyślne obejmują kilka modułów AzureRM.

Moduły domyślne są również nazywane modułami globalnymi. W Azure Portal modułu **globalnego** będzie miała wartość **true** podczas wyświetlania modułu, który został zaimportowany podczas tworzenia konta.

![Zrzut ekranu przedstawiający globalną właściwość modułu w witrynie Azure Portal](../media/modules/automation-global-modules.png)

Automatyzacja nie importuje automatycznie głównego modułu Az do żadnych nowych ani istniejących kont usługi Automation. Aby uzyskać więcej informacji na temat pracy z tymi modułami, zobacz [Migrowanie do modułów Az](#migrate-to-az-modules).

> [!NOTE]
> Nie zalecamy modyfikowania modułów i elementu Runbook na kontach usługi Automation używanych do wdrażania [Start/Stop VMs during off-hours](../automation-solution-vm-management.md) usługi.

|Nazwa modułu|Wersja|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Moduły Az

W przypadku narzędzia Az.Automation większość z tych cmdlet ma takie same nazwy jak używane w modułach AzureRM, z tą różnicą, że prefiks został `AzureRM` zmieniony na `Az` . Aby uzyskać listę modułów Az, które nie są zgodne z tą konwencją nazewnictwa, zobacz [listę wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Wewnętrzne polecenia cmdlet

Azure Automation obsługuje wewnętrzny moduł agenta usługi `Orchestrator.AssetManagement.Cmdlets` Log Analytics dla systemu Windows, instalowany domyślnie. W poniższej tabeli zdefiniowano wewnętrzne polecenia cmdlet. Te polecenia cmdlet są przeznaczone do korzystania z nich zamiast Azure PowerShell do interakcji z zasobami udostępnionym. Mogą pobierać wpisy tajne z zaszyfrowanych zmiennych, poświadczeń i szyfrowanych połączeń.

>[!NOTE]
>Wewnętrzne polecenia cmdlet są dostępne tylko w przypadku wykonywania w środowisku piaskownicy platformy Azure lub hybrydowego procesu roboczego runbook systemu Windows. 

|Nazwa|Opis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Należy pamiętać, że wewnętrzne polecenia cmdlet różnią się od nazw cmdlet Az i AzureRM. Wewnętrzne nazwy polecenia cmdlet nie zawierają słów takich jak `Azure` lub w rzeczowniku, `Az` ale używają słowa `Automation` . Zalecamy ich użycie w przypadku używania polecenia cmdlet Az lub AzureRM podczas wykonywania element runbook w piaskownicy platformy Azure lub w hybrydowym procesie roboczym runbook systemu Windows. Wymagają one mniejszej liczby parametrów i są uruchamiane w kontekście zadania, które jest już uruchomione.

Użyj polecenia cmdlet Az lub AzureRM do manipulowania zasobami usługi Automation poza kontekstem runbook. 

## <a name="python-modules"></a>Moduły języka Python

W programie można tworzyć podręczniki runbook języka Python 2 Azure Automation. Aby uzyskać informacje o module języka Python, zobacz [Zarządzanie pakietami języka Python 2 w Azure Automation](../python-packages.md).

## <a name="custom-modules"></a>Moduły niestandardowe

Azure Automation obsługuje niestandardowe moduły programu PowerShell, które tworzysz do użycia z twoimi modułami Runbook i konfiguracjami DSC. Jednym z typów modułu niestandardowego jest moduł integracji, który opcjonalnie zawiera plik metadanych w celu zdefiniowania funkcji niestandardowych dla polecenia cmdlet modułu. Przykład użycia modułu integracji znajduje się w tece [Dodawanie typu połączenia](../automation-connections.md#add-a-connection-type).

Azure Automation zaimportować moduł niestandardowy, aby udostępnić jego polecenia cmdlet. Moduł jest przechowywane w tle i używany w piaskownicach platformy Azure, podobnie jak inne moduły.

## <a name="migrate-to-az-modules"></a>Migrowanie do modułów Az

W tej sekcji opisano sposób migracji do modułów Az usługi Automation. Aby uzyskać więcej informacji, zobacz [Migrate Azure PowerShell from AzureRM to Az (Migrowanie Azure PowerShell z usługi AzureRM do az).](/powershell/azure/migrate-from-azurerm-to-az)

Nie zalecamy uruchamiania modułów AzureRM i Az na tym samym koncie usługi Automation. Jeśli masz pewność, że chcesz przeprowadzić migrację z usługi AzureRM do az, najlepiej jest w pełni zatwierdzić pełną migrację. Automatyzacja często używa piaskownic w ramach konta usługi Automation, aby zaoszczędzić czas uruchamiania. Jeśli nie dokonasz pełnej migracji modułu, możesz uruchomić zadanie, które używa tylko modułów AzureRM, a następnie uruchomić inne zadanie, które używa tylko modułów Az. Piaskownica wkrótce ulega awarii i pojawia się komunikat o błędzie informujący, że moduły nie są zgodne. Ta sytuacja powoduje losowe awarie dla dowolnego określonego runbook lub konfiguracji.

>[!NOTE]
>Podczas tworzenia nowego konta usługi Automation, nawet po migracji do modułów Az, usługa Automation domyślnie instaluje moduły AzureRM. Nadal można aktualizować tutorial runbook za pomocą polecenia cmdlet azureRM. Nie należy jednak uruchamiać tych runbook.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testowanie podręczników Runbook i konfiguracji DSC przed migracją modułów

Przed migracją do modułów Az należy dokładnie przetestować wszystkie podręczniki Runbook i konfiguracje DSC na osobnym koncie usługi Automation. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zatrzymywanie i coschedule wszystkich runbook, które używają modułów AzureRM

Aby upewnić się, że nie są uruchamiane żadne istniejące podręczniki Runbook ani konfiguracje DSC, które korzystają z modułów AzureRM, należy zatrzymać i odpiąć wszystkie objęte nim konfiguracje. Najpierw upewnij się, że poszczególne konfiguracje elementów Runbook lub DSC i ich harmonogramy są przeglądane osobno, aby mieć pewność, że w razie potrzeby będzie można ponownie zaplanować element w przyszłości.

Gdy wszystko będzie gotowe do usunięcia harmonogramów, możesz użyć polecenia cmdlet [Azure Portal lub Remove-AzureRmAutomationSchedule.](/powershell/module/azurerm.automation/remove-azurermautomationschedule) Zobacz [Usuwanie harmonogramu](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Usuwanie modułów AzureRM

Moduły AzureRM można usunąć przed zaimportowaniem modułów Az. Jeśli jednak to zrobisz, możesz przerwać synchronizację kontroli źródła i spowodować niepowodzenie wszystkich skryptów, które nadal są zaplanowane. Jeśli zdecydujesz się usunąć moduły, zobacz [Odinstalowywanie usługi AzureRM.](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)

### <a name="import-az-modules"></a>Importowanie modułów Az

Zaimportowanie modułu Az na konto usługi Automation nie spowoduje automatycznego zaimportowania modułu do sesji programu PowerShell, z których korzystają element Runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

* Gdy runbook wywołuje polecenie cmdlet z modułu.
* Gdy runbook importuje moduł jawnie za pomocą polecenia cmdlet [Import-Module.](/powershell/module/microsoft.powershell.core/import-module)
* Gdy runbook importuje moduł jawnie za pomocą [instrukcji using modułu.](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) Instrukcja using jest obsługiwana od Windows PowerShell 5.0 i obsługuje klasy oraz importowanie typów wyli tylko.
* Gdy runbook importuje inny moduł zależny.

Moduły Az można zaimportować do konta usługi Automation z Azure Portal. Pamiętaj, aby zaimportować tylko potrzebne moduły Az, a nie każdy dostępny moduł Az. Ponieważ [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla innych modułów Az, należy zaimportować ten moduł przed innymi.

1. Na koncie usługi Automation w obszarze **Zasoby udostępnione** wybierz pozycję **Moduły**.
2. Wybierz **pozycję Przeglądaj galerię.**  
3. Na pasku wyszukiwania wprowadź nazwę modułu (na przykład `Az.Accounts` ).
4. Na stronie Moduł programu PowerShell wybierz pozycję **Importuj,** aby zaimportować moduł na konto usługi Automation.

    ![Zrzut ekranu przedstawiający importowanie modułów na konto usługi Automation](../media/modules/import-module.png)

Ten import można również wykonać za pośrednictwem [Galeria programu PowerShell](https://www.powershellgallery.com), wyszukując moduł do zaimportowania. Po odnalezieniu modułu wybierz go, a następnie wybierz **kartę Azure Automation** modułu. Wybierz **pozycję Wd wdrażaj Azure Automation**.

![Zrzut ekranu przedstawiający importowanie modułów bezpośrednio z Galeria programu PowerShell](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testowanie swoich runbook

Po zaimportowaniu modułów Az do konta usługi Automation możesz rozpocząć edytowanie swoich modułów Runbook i konfiguracji DSC, aby korzystać z nowych modułów. Jednym ze sposobów testowania modyfikacji runbook w celu używania nowych poleceń cmdlet jest użycie polecenia na początku `Enable-AzureRmAlias -Scope Process` tego podręcznika. Dodając to polecenie do swojego runbook, skrypt może działać bez zmian.

## <a name="author-modules"></a>Tworzenie modułów

Zalecamy, aby podczas tworzenia niestandardowego modułu programu PowerShell do użycia w programie Azure Automation. Aby przygotować moduł do importu, musisz utworzyć co najmniej plik psd1, psm1 lub plik **dll** modułu programu PowerShell o takiej samej nazwie jak folder modułu. Następnie spakujesz folder modułu, aby Azure Automation zaimportować go jako pojedynczy plik. Pakiet **zip** powinien mieć taką samą nazwę jak folder zawartego modułu.

Aby dowiedzieć się więcej na temat tworzenia modułu programu PowerShell, zobacz How to Write a PowerShell Script Module (Jak [napisać moduł skryptu programu PowerShell).](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)

### <a name="version-folder"></a>Folder wersji

Wersja modułu programu PowerShell obok siebie umożliwia korzystanie z więcej niż jednej wersji modułu w programie PowerShell. Może to być przydatne, jeśli masz starsze skrypty, które zostały przetestowane i działają tylko w określonej wersji modułu programu PowerShell, ale inne skrypty wymagają nowszej wersji tego samego modułu programu PowerShell.

Aby utworzyć moduły programu PowerShell tak, aby zawierały wiele wersji, utwórz folder modułu, a następnie utwórz folder w tym folderze modułu dla każdej wersji modułu, którego chcesz używać. W poniższym przykładzie moduł o nazwie *TestModule* udostępnia dwie wersje: 1.0.0 i 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

W każdym folderze wersji skopiuj pliki dll modułu programu PowerShell psm1, psd1 lub powershell, które są modułem, do odpowiedniego folderu wersji.  Spakuj folder modułu, aby Azure Automation zaimportować go jako pojedynczy plik zip. Chociaż w usłudze Automation jest tylko najwyższa wersja zaimportowanych modułów, to jeśli pakiet modułu zawiera wersje modułu obok siebie, wszystkie są dostępne do użycia w twoich konfiguracjach runbook lub DSC.  

Chociaż automatyzacja obsługuje moduły zawierające wersje obok siebie w tym samym pakiecie, nie obsługuje używania wielu wersji modułu podczas importu pakietów modułów. Na przykład zaimportujesz **moduł A** zawierający wersje 1 i 2 do konta usługi Automation. Później zaktualizujemy **moduł A** tak, aby uwzględniał wersje 3 i 4 podczas importowania na konto usługi Automation, tylko wersje 3 i 4 mogą być używane w ramach dowolnych konfiguracji DSC lub runbook. Jeśli wymagane są wszystkie wersje — 1, 2, 3 i 4 — muszą być dostępne, importowany plik zip powinien zawierać wersje 1, 2, 3 i 4.

Jeśli zamierzasz używać różnych wersji tego samego modułu między modułami Runbook, zawsze deklaruj wersję, której chcesz używać w swoim module Runbook, za pomocą polecenia cmdlet i uwzględnij `Import-Module` parametr `-RequiredVersion <version>` . Nawet jeśli wersja, której chcesz użyć, to najnowsza wersja. Wynika to z tego, że zadania runbook mogą być uruchamiane w tej samej piaskownicy. Jeśli piaskownica już jawnie załadowała moduł o określonym numerze wersji, ponieważ poprzednie zadanie w tej piaskownicy już to robiło, przyszłe zadania w tej piaskownicy nie będą automatycznie ładować najnowszej wersji tego modułu. Wynika to z tego, że niektóre jego wersje są już załadowane do piaskownicy.

W przypadku zasobu DSC użyj następującego polecenia, aby określić określoną wersję:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Informacje pomocy

Dołącz synopsis, opis i adres URI pomocy dla każdego polecenia cmdlet w module. W programie PowerShell można zdefiniować informacje pomocy dla poleceń cmdlet za pomocą `Get-Help` polecenia cmdlet . W poniższym przykładzie pokazano, jak zdefiniować synopsis i ułatwić URI w **pliku modułu psm1.**

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
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Podanie tych informacji umożliwia podanie tekstu pomocy za pomocą `Get-Help` polecenia cmdlet w konsoli programu PowerShell. Ten tekst jest również wyświetlany w Azure Portal.

  ![Zrzut ekranu przedstawiający pomoc modułu integracji](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ połączenia

Jeśli moduł łączy się z usługą zewnętrzną, zdefiniuj typ połączenia przy użyciu [niestandardowego modułu integracji](#custom-modules). Każde polecenie cmdlet w module powinno akceptować wystąpienie tego typu połączenia (obiekt połączenia) jako parametr. Użytkownicy mapują parametry zasobu połączenia na odpowiednie parametry polecenia cmdlet za każdym razem, gdy wywołują polecenie cmdlet.

![Używanie połączenia niestandardowego w Azure Portal](../media/modules/connection-create-new.png)

W poniższym przykładzie runbook użyto zasobu połączenia firmy Contoso o nazwie w celu uzyskania dostępu do zasobów firmy Contoso i `ContosoConnection` zwrócenia danych z usługi zewnętrznej. W tym przykładzie pola są mapowane na właściwości i obiektu, a następnie `UserName` przekazywane do polecenia `Password` `PSCredential` cmdlet .

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Łatwiejszym i lepszym sposobem podejścia do tego zachowania jest bezpośrednie przekazanie obiektu połączenia do polecenia cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Podobne zachowanie można włączyć dla swoich cmdlet, zezwalając im na akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pól połączenia dla parametrów. Zazwyczaj należy ustawić parametr dla każdego z nich, aby użytkownik, który nie korzysta z usługi Automation, może wywołać polecenia cmdlet bez tworzenia tablicy skrótów, która będzie działać jako obiekt połączenia. Zestaw parametrów `UserAccount` służy do przekazania właściwości pola połączenia. `ConnectionObject` Umożliwia przejście połączenia bezpośrednio.

### <a name="output-type"></a>Typ danych wyjściowych

Zdefiniuj typ danych wyjściowych dla wszystkich cmdlet w module. Zdefiniowanie typu danych wyjściowych dla polecenia cmdlet umożliwia funkcji IntelliSense w czasie projektowania określenie właściwości wyjściowych polecenia cmdlet podczas tworzenia. Ta praktyka jest szczególnie przydatna podczas tworzenia graficznych elementów Runbook, dla których wiedza w czasie projektowania jest kluczem do łatwego obsługi użytkownika modułu.

Dodaj `[OutputType([<MyOutputType>])]` , gdzie jest `MyOutputType` prawidłowym typem. Aby dowiedzieć się więcej na temat `OutputType` funkcji , zobacz About Functions OutputTypeAttribute (Informacje o [typie OutputTypeAttribute funkcji).](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) Poniższy kod jest przykładem dodawania do `OutputType` polecenia cmdlet:

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

  ![Zrzut ekranu przedstawiający typ danych wyjściowych graficznego runbook](../media/modules/runbook-graphical-module-output-type.png)

  To zachowanie jest podobne do funkcji "typ z wyprzedzeniem" danych wyjściowych polecenia cmdlet w środowisku usługi integracji programu PowerShell bez konieczności jego uruchamiania.

  ![Zrzut ekranu funkcji IntelliSense posh](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stan polecenia cmdlet

Zamieć wszystkie polecenia cmdlet w module jako bez stanowe. Wiele zadań elementów Runbook może jednocześnie działać w tym samym procesie `AppDomain` i piaskownicy. Jeśli na tych poziomach istnieje jakikolwiek stan udostępniony, zadania mogą mieć na siebie wpływ. Takie zachowanie może prowadzić do sporadycznych i trudnych do zdiagnozowania problemów. Oto przykład tego, czego nie należy robić:

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

Upewnij się, że moduł jest w pełni zawarty w pakiecie, który można skopiować za pomocą narzędzia xcopy. Moduły automatyzacji są dystrybuowane do piaskownic usługi Automation podczas wykonywania podręczników Runbook. Moduły muszą działać niezależnie od hosta, który je uruchamia.

Po zaimportowaniu do środowiska programu PowerShell innego hosta pakiet modułu powinien być w stanie spakować i przenieść go. Powinien on działać normalnie. Aby to zrobić, upewnij się, że moduł nie zależy od żadnych plików spoza folderu modułu, który jest zamapowany podczas importowania modułu do usługi Automation.

Moduł nie powinien zależeć od żadnych unikatowych ustawień rejestru na hoście. Przykłady to ustawienia, które są dokonywane po zainstalowaniu produktu.

### <a name="module-file-paths"></a>Ścieżki plików modułu

Upewnij się, że wszystkie pliki w module mają ścieżki o mniej niż 140 znakach. Wszystkie ścieżki o długości ponad 140 znaków powodują problemy z importowaniem plików Runbook. Automatyzacja nie może zaimportować pliku o rozmiarze ścieżki ponad 140 znaków do sesji programu PowerShell za pomocą polecenia `Import-Module` .

## <a name="import-modules"></a>Importowanie modułów

W tej sekcji zdefiniowano kilka sposobów importowania modułu do konta usługi Automation.

### <a name="import-modules-in-the-azure-portal"></a>Importowanie modułów w Azure Portal

Aby zaimportować moduł w Azure Portal:

1. Przejdź do konta usługi Automation.
2. W **obszarze Udostępnione zasoby** wybierz pozycję **Moduły**.
3. Wybierz **pozycję Dodaj moduł.**
4. Wybierz plik **zip** zawierający moduł.
5. Wybierz **przycisk OK,** aby rozpocząć importowanie procesu.

### <a name="import-modules-by-using-powershell"></a>Importowanie modułów przy użyciu programu PowerShell

Aby zaimportować moduł na konto usługi Automation, możesz użyć polecenia cmdlet [New-AzAutomationModule.](/powershell/module/az.automation/new-azautomationmodule) Polecenie cmdlet pobiera adres URL dla pakietu zip modułu.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Możesz również użyć tego samego polecenia cmdlet, aby zaimportować moduł bezpośrednio z Galeria programu PowerShell polecenia. Pamiętaj, aby pobrać `ModuleName` i `ModuleVersion` z [Galeria programu PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importowanie modułów z Galeria programu PowerShell

Moduły administracyjne [można Galeria programu PowerShell](https://www.powershellgallery.com) bezpośrednio z galerii lub z konta usługi Automation.

Aby zaimportować moduł bezpośrednio z Galeria programu PowerShell:

1. Przejdź do https://www.powershellgallery.com i wyszukaj moduł do zaimportowania.
2. W **obszarze Opcje instalacji** na karcie **Azure Automation** wybierz pozycję **Wd wdrażaj do Azure Automation**. Ta akcja powoduje otwarcie Azure Portal. 
3. Na stronie Importowanie wybierz swoje konto usługi Automation, a następnie wybierz przycisk **OK.**

![Zrzut ekranu przedstawiający moduł Galeria programu PowerShell importowania](../media/modules/powershell-gallery.png)

Aby zaimportować Galeria programu PowerShell bezpośrednio z konta usługi Automation:

1. W **obszarze Udostępnione zasoby** wybierz pozycję **Moduły**. 
2. Wybierz **pozycję Przeglądaj galerię,** a następnie wyszukaj moduł w galerii. 
3. Wybierz moduł do zaimportowania, a następnie wybierz pozycję **Importuj**. 
4. Wybierz **przycisk OK,** aby rozpocząć proces importowania.

![Zrzut ekranu przedstawiający importowanie Galeria programu PowerShell modułu z Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Usuwanie modułów

Jeśli masz problemy z modułem lub musisz wrócić do poprzedniej wersji modułu, możesz usunąć go z konta usługi Automation. Nie można usunąć oryginalnych wersji [](#default-modules) modułów domyślnych, które są importowane podczas tworzenia konta usługi Automation. Jeśli moduł do usunięcia jest nowszą [](#default-modules)wersją jednego z modułów domyślnych, zostanie wycofany do wersji zainstalowanej przy użyciu konta usługi Automation. W przeciwnym razie każdy moduł usunięty z konta usługi Automation zostanie usunięty.

### <a name="delete-modules-in-the-azure-portal"></a>Usuwanie modułów w Azure Portal

Aby usunąć moduł z Azure Portal:

1. Przejdź do swojego konta usługi Automation. W **obszarze Udostępnione zasoby** wybierz pozycję **Moduły**.
2. Wybierz moduł, który chcesz usunąć.
3. Na stronie Moduł wybierz pozycję **Usuń.** Jeśli ten moduł jest jednym z domyślnych [modułów](#default-modules), zostanie wycofany do wersji, która istniała podczas tworzenia konta usługi Automation.

### <a name="delete-modules-by-using-powershell"></a>Usuwanie modułów przy użyciu programu PowerShell

Aby usunąć moduł za pomocą programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat używania Azure PowerShell modułów, zobacz [Rozpoczynanie pracy z Azure PowerShell](/powershell/azure/get-started-azureps).

* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz Writing a Windows PowerShell module (Pisanie modułu [Windows PowerShell PowerShell).](/powershell/scripting/developer/module/writing-a-windows-powershell-module)
