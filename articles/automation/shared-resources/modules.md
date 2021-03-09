---
title: Zarządzanie modułami w usłudze Azure Automation
description: W tym artykule opisano sposób korzystania z modułów programu PowerShell w celu włączenia poleceń cmdlet w elementach Runbook i zasobach DSC w konfiguracjach DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: ae3329401a138bc0566ea93a8fbf2071fd44f02c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503423"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie modułami w usłudze Azure Automation

Azure Automation używa wielu modułów programu PowerShell do włączania poleceń cmdlet w elementach Runbook i zasobach DSC w konfiguracjach DSC. Obsługiwane są następujące moduły:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az).
* [Azure PowerShell AzureRM. Automation](/powershell/module/azurerm.automation/).
* Inne moduły programu PowerShell.
* `Orchestrator.AssetManagement.Cmdlets`Moduł wewnętrzny.
* Moduły Python 2.
* Niestandardowe moduły, które tworzysz.

Podczas tworzenia konta usługi Automation Azure Automation domyślnie importowane są pewne moduły. Zobacz [moduły domyślne](#default-modules).

## <a name="sandboxes"></a>Piaskownice

Gdy Automatyzacja wykonuje zadania kompilacji elementów Runbook i DSC, ładuje moduły do piaskownic, w których można uruchomić elementy Runbook, a konfiguracje DSC mogą być kompilowane. Automatyzacja automatycznie umieszcza wszystkie zasoby DSC w modułach na serwerze ściągania DSC. Maszyny mogą ściągnąć zasoby, gdy stosują konfiguracje DSC.

>[!NOTE]
>Pamiętaj, aby zaimportować tylko te moduły, których wymagają elementy Runbook i konfiguracje DSC. Nie zalecamy importowania głównego elementu AZ module. Obejmuje to wiele innych modułów, które mogą nie być potrzebne, co może powodować problemy z wydajnością. Zaimportuj poszczególne moduły, takie jak AZ. COMPUTE, zamiast.

Piaskownica w chmurze obsługuje maksymalnie 48 wywołań systemowych i ogranicza wszystkie inne wywołania ze względów bezpieczeństwa. Inne funkcje, takie jak zarządzanie poświadczeniami i niektóre sieci, nie są obsługiwane w piaskownicy chmury.

Ze względu na liczbę modułów i poleceń cmdlet, trudno poznać wcześniej, które polecenia cmdlet spowodują nieobsługiwane wywołania. Ogólnie rzecz biorąc, mamy problemy z poleceniami cmdlet, które wymagają podwyższonego poziomu uprawnień, wymagają poświadczeń jako parametru lub poleceń cmdlet związanych z siecią. Wszystkie polecenia cmdlet, które wykonują operacje na pełnym stosie, nie są obsługiwane w piaskownicy, w tym [Connect-AipService](/powershell/module/aipservice/connect-aipservice) z modułu AipService PowerShell i [rozwiązywania dnsname](/powershell/module/dnsclient/resolve-dnsname) z modułu DNSClient.

Są to znane ograniczenia dotyczące piaskownicy. Zalecanym obejściem jest wdrożenie [hybrydowego procesu roboczego elementu Runbook](../automation-hybrid-runbook-worker.md) lub użycie [Azure Functions](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Moduły domyślne

W poniższej tabeli wymieniono moduły, które domyślnie Azure Automation Importy podczas tworzenia konta usługi Automation. Automatyzacja może zaimportować nowsze wersje tych modułów. Nie można jednak usunąć oryginalnej wersji z konta usługi Automation, nawet jeśli usuniesz nowszą wersję. Należy pamiętać, że te moduły domyślne obejmują kilka modułów AzureRM.

Moduły domyślne są również znane jako moduły globalne. W Azure Portal Właściwość **modułu globalnego** będzie miała **wartość true** podczas wyświetlania modułu, który został zaimportowany podczas tworzenia konta.

![Zrzut ekranu właściwości modułu globalnego w witrynie Azure Portal](../media/modules/automation-global-modules.png)

Automatyzacja nie powoduje automatycznego importowania głównego elementu AZ module do żadnych nowych lub istniejących kont usługi Automation. Aby uzyskać więcej informacji na temat pracy z tymi modułami, zobacz [Migrowanie do AZ modules](#migrate-to-az-modules).

> [!NOTE]
> Nie zaleca się modyfikowania modułów i elementów Runbook na kontach usługi Automation używanych do wdrażania funkcji [Start/Stop VMS during off-hours](../automation-solution-vm-management.md) .

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
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. polecenia cmdlet | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>AZ modules

W przypadku AZ. Automation większość poleceń cmdlet ma takie same nazwy jak te, które są używane dla modułów AzureRM, z tą różnicą, że `AzureRM` prefiks został zmieniony na `Az` . Aby zapoznać się z listą takich modułów, które nie przestrzegają tej konwencji nazewnictwa, zobacz [listę wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Wewnętrzne polecenia cmdlet

Azure Automation obsługuje moduł wewnętrzny `Orchestrator.AssetManagement.Cmdlets` dla agenta log Analytics dla systemu Windows, który jest instalowany domyślnie. W poniższej tabeli przedstawiono definicje wewnętrznych poleceń cmdlet. Te polecenia cmdlet są przeznaczone do użycia zamiast Azure PowerShell poleceń cmdlet do współpracy z zasobami udostępnionymi. Mogą pobrać wpisy tajne z szyfrowanych zmiennych, poświadczeń i szyfrowanych połączeń.

>[!NOTE]
>Wewnętrzne polecenia cmdlet są dostępne tylko w przypadku wykonywania elementów Runbook w środowisku piaskownicy platformy Azure lub w hybrydowym procesie roboczym elementu Runbook systemu Windows. 

|Nazwa|Opis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Należy pamiętać, że wewnętrzne polecenia cmdlet różnią się nazwami poleceń cmdlet AZ i AzureRM. Wewnętrzne nazwy poleceń cmdlet nie zawierają słów takich jak `Azure` lub `Az` w rzeczownik, ale używają wyrazu `Automation` . Zalecamy korzystanie z poleceń cmdlet AZ lub AzureRM podczas wykonywania elementu Runbook w piaskownicy platformy Azure lub w hybrydowym procesie roboczym elementu Runbook systemu Windows. Wymagają one mniej parametrów i są uruchamiane w kontekście zadania, które jest już uruchomione.

Użyj poleceń cmdlet AZ lub AzureRM do manipulowania zasobami usługi Automation poza kontekstem elementu Runbook. 

## <a name="python-modules"></a>Moduły języka Python

W Azure Automation można tworzyć elementy Runbook języka Python 2. Aby uzyskać informacje o module języka Python, zobacz [Zarządzanie pakietami Python 2 w Azure Automation](../python-packages.md).

## <a name="custom-modules"></a>Moduły niestandardowe

Azure Automation obsługuje niestandardowe moduły programu PowerShell, które są tworzone do użycia z elementami Runbook i konfiguracjami DSC. Jeden z typów modułu niestandardowego to moduł integracji, który opcjonalnie zawiera plik metadanych służący do definiowania niestandardowej funkcji poleceń cmdlet modułu. Przykład użycia modułu integracji znajduje się w obszarze [Dodaj typ połączenia](../automation-connections.md#add-a-connection-type).

Azure Automation można zaimportować niestandardowego modułu w celu udostępnienia jego poleceń cmdlet. W tle przechowuje moduł i używa go w piaskownicach platformy Azure, podobnie jak w przypadku innych modułów.

## <a name="migrate-to-az-modules"></a>Migrowanie do AZ modules

Ta sekcja zawiera informacje na temat migracji do modułu AZ modules w usłudze Automation. Aby uzyskać więcej informacji, zobacz [migrowanie Azure PowerShell z AzureRM do AZ](/powershell/azure/migrate-from-azurerm-to-az).

Nie zalecamy uruchamiania modułów AzureRM i AZ modules na tym samym koncie usługi Automation. Jeśli na pewno chcesz przeprowadzić migrację z AzureRM do AZ, najlepiej jest w pełni zatwierdzić do kompletnej migracji. Automatyzacja często ponownie używa piaskownic w ramach konta usługi Automation, aby zaoszczędzić czas uruchamiania. Jeśli nie przetworzysz pełnej migracji modułu, możesz uruchomić zadanie, które używa tylko modułów AzureRM, a następnie uruchomić inne zadanie, które używa tylko AZ module. Piaskownica zostanie wkrótce przestanie działać i zostanie wyświetlony komunikat o błędzie informujący, że moduły nie są zgodne. Ta sytuacja powoduje losowe awarie dla każdego określonego elementu Runbook lub konfiguracji.

>[!NOTE]
>Podczas tworzenia nowego konta usługi Automation, nawet po migracji do programu AZ modules, Automatyzacja domyślnie instaluje moduły AzureRM. Można nadal aktualizować elementy Runbook samouczka za pomocą poleceń cmdlet AzureRM. Nie należy jednak uruchamiać tych elementów Runbook.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Przetestuj elementy Runbook i konfiguracje DSC przed migracją modułu

Należy uważnie przetestować wszystkie elementy Runbook i konfiguracje DSC w osobnym koncie usługi Automation przed migracją do modułu AZ modules. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zatrzymywanie i nieplanowanie wszystkich elementów Runbook, które korzystają z modułów AzureRM

Aby upewnić się, że nie uruchomiono żadnych istniejących elementów Runbook lub konfiguracji DSC, które używają modułów AzureRM, należy zatrzymać i anulować planowanie wszystkich elementów Runbook i konfiguracji, których dotyczy. Najpierw upewnij się, że każdy element Runbook lub Konfiguracja DSC i jego harmonogramy zostały oddzielnie, aby upewnić się, że w razie potrzeby można ponownie zaplanować element w przyszłości.

Gdy wszystko będzie gotowe do usunięcia harmonogramów, możesz użyć Azure Portal lub polecenia cmdlet [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) . Zobacz [Usuwanie harmonogramu](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Usuń moduły AzureRM

Przed zaimportowaniem modułów AzureRM można je usunąć. Jeśli jednak to zrobisz, możesz przerwać synchronizację kontroli źródła i spowodować, że wszystkie skrypty, które są nadal zaplanowane, zakończą się niepowodzeniem. Jeśli zdecydujesz się usunąć moduły, zobacz [Odinstalowywanie AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Importuj AZ modules

Importowanie modułu AZ module do konta usługi Automation nie powoduje automatycznego importowania modułu do sesji programu PowerShell używanej przez elementy Runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

* Gdy element Runbook wywołuje polecenie cmdlet z modułu.
* Gdy element Runbook importuje moduł jawnie za pomocą polecenia cmdlet [Import-Module](/powershell/module/microsoft.powershell.core/import-module) .
* Gdy element Runbook importuje moduł jawnie za pomocą instrukcji [using module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) . Instrukcja using jest obsługiwana począwszy od programu Windows PowerShell 5,0 i obsługuje klasy i typ wyliczenia.
* Gdy element Runbook importuje inny moduł zależny.

Można zaimportować moduły AZ w Azure Portal. Pamiętaj, aby zaimportować tylko te moduły AZ, które są potrzebne, a nie cały moduł AZ. Automation. Ponieważ [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla innych modułów AZ module, pamiętaj o zaimportowaniu tego modułu przed innymi.

1. Na koncie usługi Automation w obszarze **udostępnione zasoby** wybierz pozycję **moduły**.
2. Wybierz pozycję **Przeglądaj Galerię**.  
3. Na pasku wyszukiwania wprowadź nazwę modułu (na przykład `Az.Accounts` ).
4. Na stronie moduł programu PowerShell wybierz pozycję **Importuj** , aby zaimportować moduł do konta usługi Automation.

    ![Zrzut ekranu przedstawiający importowanie modułów do konta usługi Automation](../media/modules/import-module.png)

Ten import można również wykonać za pomocą [Galeria programu PowerShell](https://www.powershellgallery.com), wyszukując moduł do zaimportowania. Po znalezieniu modułu zaznacz go, a następnie wybierz kartę **Azure Automation** . Wybierz pozycję **Wdróż do Azure Automation**.

![Zrzut ekranu przedstawiający importowanie modułów bezpośrednio z Galeria programu PowerShell](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testowanie elementów Runbook

Po zaimportowaniu modułu AZ modules do konta usługi Automation możesz rozpocząć edytowanie elementów Runbook i konfiguracji DSC, aby użyć nowych modułów. Jednym ze sposobów na przetestowanie modyfikacji elementu Runbook w celu użycia nowych poleceń cmdlet jest użycie `Enable-AzureRmAlias -Scope Process` polecenia na początku elementu Runbook. Po dodaniu tego polecenia do elementu Runbook skrypt może być uruchamiany bez zmian.

## <a name="author-modules"></a>Tworzenie modułów

Zalecamy przestrzeganie zagadnień z tej sekcji podczas tworzenia niestandardowego modułu programu PowerShell do użycia w programie Azure Automation. Aby przygotować moduł do importowania, należy utworzyć co najmniej plik. psd1,. PSM1 lub PowerShell module **. dll** o takiej samej nazwie jak folder modułu. Następnie można zakodować folder modułu, aby Azure Automation mógł go zaimportować jako pojedynczy plik. Pakiet **zip** powinien mieć taką samą nazwę jak folder zawartego modułu.

Aby dowiedzieć się więcej na temat tworzenia modułu programu PowerShell, zobacz [jak napisać moduł skryptu programu PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Folder wersji

Obsługa wersji modułów równoległych programu PowerShell umożliwia korzystanie z więcej niż jednej wersji modułu w programie PowerShell. Może to być przydatne, jeśli masz starsze skrypty, które zostały przetestowane i pracują tylko z określoną wersją modułu programu PowerShell, ale inne skrypty wymagają nowszej wersji tego samego modułu programu PowerShell.

Aby skonstruować moduły programu PowerShell w taki sposób, aby zawierały wiele wersji, należy utworzyć folder modułu, a następnie utworzyć folder w tym folderze modułu dla każdej wersji modułu, który ma być używany. W poniższym przykładzie moduł o nazwie *TestModule* udostępnia dwie wersje, 1.0.0 i 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

W każdym z folderów wersji Skopiuj pliki. **dll** programu PowerShell. PSM1,. Psd1 lub PowerShell, które składają się na moduł do odpowiedniej wersji folderu. Zakoduj w górę folderu modułu, aby Azure Automation mógł zaimportować go jako pojedynczy plik zip. Podczas gdy Automatyzacja pokazuje tylko najwyższą zaimportowaną wersję modułu, jeśli pakiet modułu zawiera równoległe wersje modułu, są one dostępne do użycia w elementach Runbook lub konfiguracjach DSC.  

Chociaż Automatyzacja obsługuje moduły zawierające wersje równoległe tego samego pakietu, nie obsługuje on korzystania z wielu wersji modułu w ramach importów pakietów modułu. Na przykład zaimportowano **moduł A**, który zawiera wersje 1 i 2 do konta usługi Automation. Później podczas importowania **modułu A** w celu uwzględnienia wersji 3 i 4 w przypadku zaimportowania do konta usługi Automation w ramach wszystkich elementów Runbook lub konfiguracji DSC można używać tylko wersji 3 i 4. Jeśli wszystkie wersje — 1, 2, 3 i 4 będą dostępne, plik zip, którego import powinien zawierać wersje 1, 2, 3 i 4.

Jeśli zamierzasz używać różnych wersji tego samego modułu między elementami Runbook, należy zawsze zadeklarować wersję, która ma być używana w elemencie Runbook za pomocą `Import-Module` polecenia cmdlet, i dołączyć parametr `-RequiredVersion <version>` . Nawet wtedy, gdy wersja, której chcesz użyć, to Najnowsza wersja. Dzieje się tak, ponieważ zadania elementu Runbook mogą działać w tej samej piaskownicy. Jeśli piaskownica jawnie załadowała moduł o określonym numerze wersji, ponieważ w tym przypadku poprzednie zadanie w tym obszarze piaskownicy poinformowało o tym, przyszłe zadania w tym obszarze piaskownicy nie będą automatycznie ładować najnowszej wersji tego modułu. Wynika to z faktu, że pewna wersja jest już załadowana w piaskownicy.

W przypadku zasobu DSC Użyj następującego polecenia, aby określić określoną wersję:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Informacje pomocy

Uwzględnij streszczenie, opis i identyfikator URI pomocy dla każdego polecenia cmdlet w module. W programie PowerShell można zdefiniować informacje pomocy dla poleceń cmdlet przy użyciu `Get-Help` polecenia cmdlet. Poniższy przykład pokazuje, jak zdefiniować streszczenie i identyfikator URI pomocy w pliku modułu **. PSM1** .

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

  Podanie tych informacji powoduje wyświetlenie tekstu pomocy za pośrednictwem `Get-Help` polecenia cmdlet w konsoli programu PowerShell. Ten tekst jest również wyświetlany w Azure Portal.

  ![Zrzut ekranu przedstawiający pomoc modułu integracji](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ połączenia

Jeśli moduł nawiązuje połączenie z usługą zewnętrzną, zdefiniuj typ połączenia przy użyciu [niestandardowego modułu integracji](#custom-modules). Każde polecenie cmdlet w module powinno akceptować wystąpienie tego typu połączenia (obiektu połączenia) jako parametr. Użytkownicy mapują parametry zasobu połączenia do odpowiednich parametrów polecenia cmdlet za każdym razem, gdy wywołuje polecenie cmdlet.

![Użyj połączenia niestandardowego w Azure Portal](../media/modules/connection-create-new.png)

Poniższy przykład elementu Runbook używa zasobu połączenia contoso o nazwie, `ContosoConnection` Aby uzyskać dostęp do zasobów firmy Contoso i zwracać dane z usługi zewnętrznej. W tym przykładzie pola są mapowane na `UserName` `Password` właściwości i `PSCredential` obiektu, a następnie przesyłane do polecenia cmdlet.

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

Można włączyć podobne zachowanie dla poleceń cmdlet, zezwalając im na akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pól połączeń dla parametrów. Zazwyczaj potrzebny jest zestaw parametrów dla każdego, aby użytkownik, który nie korzysta z automatyzacji, mógł wywołać polecenia cmdlet bez konstruowania obiektu Hashtable do działania jako obiekt połączenia. Zestaw parametrów `UserAccount` służy do przekazywania właściwości pola połączenia. `ConnectionObject` umożliwia bezpośrednie przekazywanie połączenia.

### <a name="output-type"></a>Typ danych wyjściowych

Zdefiniuj typ danych wyjściowych dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet umożliwia funkcji IntelliSense w czasie projektowania, aby pomóc w ustaleniu właściwości danych wyjściowych polecenia cmdlet podczas tworzenia. Ta metoda jest szczególnie przydatna podczas tworzenia graficznego elementu Runbook, w którym wiedza czasu projektowania jest kluczem do łatwego środowiska użytkownika z Twoim modułem.

Dodaj `[OutputType([<MyOutputType>])]` , gdzie `MyOutputType` jest prawidłowym typem. Aby dowiedzieć się więcej na temat `OutputType` , zobacz [Informacje o funkcjach OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Poniższy kod jest przykładem dodawania `OutputType` do polecenia cmdlet:

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

  ![Zrzut ekranu przedstawiający graficzny typ danych wyjściowych elementu Runbook](../media/modules/runbook-graphical-module-output-type.png)

  To zachowanie jest podobne do funkcji "Type z wyprzedzeniem" w danych wyjściowych polecenia cmdlet w środowisku usługi integracji programu PowerShell, bez konieczności jego uruchamiania.

  ![Zrzut ekranu funkcji IntelliSense POSH](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stan polecenia cmdlet

Ustaw wszystkie polecenia cmdlet w module jako bezstanowe. Wiele zadań elementów Runbook można jednocześnie uruchamiać w taki sam sposób, jak w tym `AppDomain` samym procesie i w piaskownicy. Jeśli na tych poziomach są udostępnione jakiekolwiek Stany, zadania mogą mieć wpływ na siebie. Takie zachowanie może prowadzić do sporadycznych i trudnych do zdiagnozowania problemów. Oto przykład, co nie zrobić:

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

Upewnij się, że moduł jest w pełni zawarty w pakiecie, który można skopiować za pomocą polecenia xcopy. Moduły automatyzacji są dystrybuowane do piaskownic automatyzacji podczas wykonywania elementów Runbook. Moduły muszą działać niezależnie od hosta, na którym są uruchamiane.

W przypadku zaimportowania go do środowiska programu PowerShell innego hosta należy mieć możliwość przechowywania i przenoszenia pakietu modułu. W tym celu należy się upewnić, że moduł nie jest zależny od żadnych plików poza folderem modułu, który jest zapakowany podczas importowania modułu do usługi Automation.

Moduł nie powinien zależeć od żadnych unikatowych ustawień rejestru na hoście. Przykłady to ustawienia wprowadzane podczas instalacji produktu.

### <a name="module-file-paths"></a>Ścieżki pliku modułu

Upewnij się, że wszystkie pliki w module mają ścieżki zawierające mniej niż 140 znaków. Wszystkie ścieżki o długości od 140 znaków powodują problemy z importowaniem elementów Runbook. Automatyzacja nie może zaimportować pliku o rozmiarze ścieżki ponad 140 znaków do sesji programu PowerShell za pomocą polecenia `Import-Module` .

## <a name="import-modules"></a>Importowanie modułów

W tej sekcji zdefiniowano kilka sposobów importowania modułu do konta usługi Automation.

### <a name="import-modules-in-the-azure-portal"></a>Importuj moduły w Azure Portal

Aby zaimportować moduł w Azure Portal:

1. Przejdź do konta usługi Automation.
2. W obszarze **zasoby udostępnione** wybierz pozycję **moduły**.
3. Wybierz pozycję **Dodaj moduł**.
4. Wybierz plik **zip** , który zawiera moduł.
5. Wybierz **przycisk OK** , aby rozpocząć importowanie procesu.

### <a name="import-modules-by-using-powershell"></a>Importowanie modułów przy użyciu programu PowerShell

Aby zaimportować moduł do konta usługi Automation, można użyć polecenia cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) . Polecenie cmdlet pobiera adres URL dla modułu module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Można również użyć tego samego polecenia cmdlet do zaimportowania modułu bezpośrednio z Galeria programu PowerShell. Upewnij się, że `ModuleName` `ModuleVersion` [Galeria programu PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importuj moduły z Galeria programu PowerShell

Moduły [Galeria programu PowerShell](https://www.powershellgallery.com) można importować bezpośrednio z galerii lub z konta usługi Automation.

Aby zaimportować moduł bezpośrednio z Galeria programu PowerShell:

1. Przejdź do https://www.powershellgallery.com i Wyszukaj moduł do zaimportowania.
2. W obszarze **Opcje instalacji** na karcie **Azure Automation** wybierz pozycję **Wdróż do Azure Automation**. Ta akcja powoduje otwarcie Azure Portal. 
3. Na stronie Importowanie wybierz konto usługi Automation, a następnie wybierz **przycisk OK**.

![Zrzut ekranu modułu Galeria programu PowerShell import](../media/modules/powershell-gallery.png)

Aby zaimportować moduł Galeria programu PowerShell bezpośrednio z konta usługi Automation:

1. W obszarze **zasoby udostępnione** wybierz pozycję **moduły**. 
2. Wybierz pozycję **Przeglądaj Galerię**, a następnie wyszukaj moduł w galerii. 
3. Wybierz moduł do zaimportowania, a następnie wybierz pozycję **Importuj**. 
4. Wybierz **przycisk OK** , aby rozpocząć proces importowania.

![Zrzut ekranu przedstawiający Importowanie modułu Galeria programu PowerShell z Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Usuń moduły

Jeśli masz problemy z modułem lub musisz przywrócić poprzednią wersję modułu, możesz usunąć go z konta usługi Automation. Nie można usunąć oryginalnych wersji [domyślnych modułów](#default-modules) , które są importowane podczas tworzenia konta usługi Automation. Jeśli moduł do usunięcia jest nowszą wersją jednego z [domyślnych modułów](#default-modules), zostanie przywrócony do wersji, która została zainstalowana z kontem usługi Automation. W przeciwnym razie wszystkie moduły, które usuniesz z konta usługi Automation, zostaną usunięte.

### <a name="delete-modules-in-the-azure-portal"></a>Usuń moduły w Azure Portal

Aby usunąć moduł w Azure Portal:

1. Przejdź do konta usługi Automation. W obszarze **zasoby udostępnione** wybierz pozycję **moduły**.
2. Wybierz moduł, który chcesz usunąć.
3. Na stronie moduł wybierz pozycję **Usuń**. Jeśli ten moduł jest jednym z [domyślnych modułów](#default-modules), zostanie przywrócony do wersji, która istniała podczas tworzenia konta usługi Automation.

### <a name="delete-modules-by-using-powershell"></a>Usuwanie modułów przy użyciu programu PowerShell

Aby usunąć moduł za pomocą programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o korzystaniu z modułów Azure PowerShell, zobacz Wprowadzenie [do usługi Azure PowerShell](/powershell/azure/get-started-azureps).

* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz [pisanie modułu programu Windows PowerShell](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
