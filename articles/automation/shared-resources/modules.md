---
title: Zarządzanie modułami w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania modułami w Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8d22e63be880c0cef0c4072e99ab85bf3250a1c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114278"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie modułami w usłudze Azure Automation

Azure Automation umożliwia zaimportowanie modułów programu PowerShell w celu włączenia poleceń cmdlet w elementach Runbook i zasobach DSC w konfiguracjach DSC. Moduły używane w Azure Automation obejmują:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Moduł `Orchestrator.AssetManagement.Cmdlets` wewnętrzny dla agenta log Analytics dla systemu Windows
* Inne moduły programu PowerShell
* Niestandardowe moduły, które tworzysz 

Podczas tworzenia konta usługi Automation Azure Automation domyślnie importowane są pewne moduły. Zobacz [moduły domyślne](#default-modules).

Gdy Azure Automation wykonuje zadania kompilacji elementów Runbook i DSC, ładuje moduły do piaskownic, w których można uruchomić elementy Runbook, a konfiguracje DSC mogą być kompilowane. Automatyzacja automatycznie umieszcza wszystkie zasoby DSC w modułach na serwerze ściągania DSC. Maszyny mogą ściągnąć zasoby, gdy stosują konfiguracje DSC.

>[!NOTE]
>Pamiętaj, aby zaimportować tylko te moduły, których rzeczywiście potrzebują elementy Runbook i konfiguracje DSC. Nie zalecamy importowania głównego elementu AZ module, ponieważ zawiera on wiele innych modułów, które mogą nie być potrzebne, co może powodować problemy z wydajnością. Zaimportuj poszczególne moduły, takie jak AZ. COMPUTE, zamiast.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Moduły domyślne

W poniższej tabeli wymieniono moduły, które domyślnie Azure Automation Importy podczas tworzenia konta usługi Automation. Automatyzacja może zaimportować nowsze wersje tych modułów. Nie można jednak usunąć oryginalnej wersji z konta usługi Automation, nawet jeśli usuniesz nowszą wersję. Należy pamiętać, że te moduły domyślne obejmują kilka modułów AzureRM. 

Azure Automation nie importuje automatycznie głównego elementu AZ module do żadnych nowych lub istniejących kont usługi Automation. Aby uzyskać więcej informacji na temat pracy z tymi modułami, zobacz [Migrowanie do AZ modules](#migrating-to-az-modules).

> [!NOTE]
> Nie zaleca się modyfikowania modułów i elementów Runbook na kontach usługi Automation, które zawierają [Start/Stop VMS during off-hours rozwiązanie w Azure Automation](../automation-solution-vm-management.md).

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
| GPRegistryPolicyParser | 0.2 |
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

## <a name="az-module-cmdlets"></a>Polecenia AZ module

W przypadku AZ. Automation większość poleceń cmdlet ma takie same nazwy jak dla modułów AzureRM, z tą różnicą, że prefiks AzureRm został zmieniony na AZ. Aby zapoznać się z listą elementów AZ module, które nie są zgodne z tą konwencją nazewnictwa, zobacz [Lista wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Wewnętrzne polecenia cmdlet

W poniższej tabeli zdefiniowano wewnętrzne polecenia cmdlet obsługiwane przez `Orchestrator.AssetManagement.Cmdlets` moduł. Za pomocą tych poleceń cmdlet w elementach Runbook i konfiguracjach DSC można korzystać z zasobów platformy Azure w ramach konta usługi Automation. Polecenia cmdlet są przeznaczone do użycia zamiast poleceń cmdlet Azure PowerShell do pobierania wpisów tajnych z szyfrowanych zmiennych, poświadczeń i szyfrowanych połączeń. 

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
|Oczekiwanie — AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Należy pamiętać, że wewnętrzne polecenia cmdlet różnią się nazwami poleceń cmdlet AZ i AzureRM. Wewnętrzne nazwy poleceń cmdlet nie zawierają słów, takich jak "Azure" lub "AZ" w rzeczowniku, ale używają słowa "Automation". Zalecamy korzystanie z poleceń cmdlet AZ lub AzureRM podczas wykonywania elementu Runbook w piaskownicy platformy Azure lub w hybrydowym procesie roboczym systemu Windows. Wymagają one mniej parametrów i są uruchamiane w kontekście już wykonywanego zadania.

Zalecamy używanie poleceń cmdlet AZ lub AzureRM do manipulowania zasobami Azure Automationymi poza kontekstem elementu Runbook. 

## <a name="module-supporting-get-automationpscredential"></a>Moduł obsługujący Get-AutomationPSCredential

`Get-AutomationPSCredential` Polecenie cmdlet jest częścią modułu `Orchestrator.AssetManagement.Cmdlets`. To polecenie cmdlet zwraca `PSCredential` obiekt, który jest oczekiwany przez większość poleceń cmdlet programu PowerShell, które działają z poświadczeniami. Aby dowiedzieć się więcej o korzystaniu z poświadczeń w Azure Automation, zobacz [zasoby poświadczeń w programie Azure Automation](credentials.md).

## <a name="migrating-to-az-modules"></a>Migrowanie do AZ modules

### <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Ta sekcja zawiera zagadnienia, które należy wziąć pod uwagę podczas migrowania do modułu AZ modules w Azure Automation. Zobacz również [migrowanie Azure PowerShell z AzureRM do AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Używanie modułów AzureRM i AZ modules na tym samym koncie usługi Automation

 Nie zalecamy uruchamiania modułów AzureRM i AZ modules na tym samym koncie usługi Automation. Jeśli na pewno chcesz przeprowadzić migrację z AzureRM do AZ, najlepiej jest w pełni zatwierdzić do kompletnej migracji. Najważniejszym powodem jest to, że Azure Automation często ponownie używa piaskownic w ramach konta usługi Automation, aby zaoszczędzić czas uruchamiania. Jeśli nie przetworzysz pełnej migracji modułu, możesz uruchomić zadanie przy użyciu tylko modułów AzureRM, a następnie uruchomić inne zadanie, używając tylko polecenia AZ modules. Piaskownica zostanie wkrótce przestanie działać i wystąpi błąd krytyczny informujący, że moduły nie są zgodne. W tej sytuacji powstaje losowe awarie dla danego elementu Runbook lub konfiguracji. 

#### <a name="import-of-az-modules-into-the-powershell-session"></a>Importowanie poleceń AZ modułów do sesji programu PowerShell

Importowanie modułu AZ module do konta usługi Automation nie powoduje automatycznego importowania modułu do sesji programu PowerShell używanej przez elementy Runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

* Gdy element Runbook wywołuje polecenie cmdlet z modułu
* Gdy element Runbook importuje moduł jawnie za pomocą polecenia cmdlet [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7)
* Gdy element Runbook importuje inny moduł zależny
    
#### <a name="testing-for-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testowanie elementów Runbook i konfiguracji DSC przed migracją modułu

Pamiętaj, aby dokładnie przetestować wszystkie elementy Runbook i konfiguracje DSC na osobnym koncie usługi Automation przed migracją do modułu AZ modules. 

#### <a name="updates-for-tutorial-runbooks"></a>Aktualizacje elementów Runbook samouczka 

Podczas tworzenia nowego konta usługi Automation, nawet po migracji do programu AZ modules, Azure Automation domyślnie instaluje moduły AzureRM. Można nadal aktualizować elementy Runbook samouczka za pomocą poleceń cmdlet AzureRM. Nie należy jednak uruchamiać tych elementów Runbook.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zatrzymywanie i nieplanowanie wszystkich elementów Runbook, które korzystają z modułów AzureRM

Aby upewnić się, że nie uruchomiono żadnych istniejących elementów Runbook lub konfiguracji DSC, które używają modułów AzureRM, należy zatrzymać i anulować planowanie wszystkich elementów Runbook i konfiguracji, których dotyczy. Najpierw należy sprawdzić, czy każdy element Runbook lub Konfiguracja DSC i jego harmonogramy zostały oddzielnie zaplanowana w celu zapewnienia, że w razie potrzeby można ponownie zaplanować element w przyszłości. 

Gdy wszystko będzie gotowe do usunięcia harmonogramów, możesz użyć Azure Portal lub polecenia cmdlet [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . Zobacz [Usuwanie harmonogramu](schedules.md#removing-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Usuń moduły AzureRM

Przed zaimportowaniem modułów AzureRM można usunąć te moduły. Jednak usunięcie modułów AzureRM może przerwać synchronizację kontroli źródła i spowodować niepowodzenie wszystkich skryptów, które nadal zaplanowano. Jeśli zdecydujesz się usunąć moduły, zobacz [Odinstalowywanie AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Zaimportuj AZ modules

Ta sekcja zawiera informacje na temat importowania modułów AZ w Azure Portal. Pamiętaj, aby zaimportować tylko te moduły AZ, które są potrzebne, a nie cały moduł AZ. Automation. Ponieważ [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla innych modułów AZ module, pamiętaj o zaimportowaniu tego modułu przed innymi.

1. Z poziomu konta usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. 
2. Kliknij pozycję **Przeglądaj Galerię** , aby otworzyć stronę Przeglądaj galerię.  
3. Na pasku wyszukiwania wprowadź nazwę modułu, na przykład `Az.Accounts`. 
4. Na stronie moduł programu PowerShell kliknij przycisk **Importuj** , aby zaimportować moduł do konta usługi Automation.

    ![Importuj moduły do konta usługi Automation](../media/modules/import-module.png)

Ten proces importowania można wykonać za pomocą [Galeria programu PowerShell](https://www.powershellgallery.com) , wyszukując moduł do zaimportowania. Po znalezieniu modułu zaznacz go, wybierz kartę **Azure Automation** , a następnie kliknij pozycję **Wdróż do Azure Automation**.

![Importuj moduły bezpośrednio z galerii](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testowanie elementów Runbook

Po zaimportowaniu modułu AZ modules do konta usługi Automation możesz rozpocząć edytowanie elementów Runbook i konfiguracji DSC, aby użyć nowych modułów. Jednym ze sposobów na przetestowanie modyfikacji elementu Runbook w celu użycia nowych poleceń cmdlet jest użycie `Enable-AzureRmAlias -Scope Process` na początku elementu Runbook. Po dodaniu tego polecenia do elementu Runbook skrypt może być uruchamiany bez zmian. 

## <a name="authoring-modules"></a>Tworzenie modułów

Zalecamy przestrzeganie zagadnień z tej sekcji podczas tworzenia modułu programu PowerShell do użycia w Azure Automation.

### <a name="version-folder"></a>Folder wersji

NIE dołączaj folderu wersji w pakiecie **. zip** modułu.  Ten problem jest mniej istotny dla elementów Runbook, ale powoduje problem z usługą konfiguracji stanu (DSC). Azure Automation automatycznie tworzy folder wersji, gdy moduł jest dystrybuowany do węzłów zarządzanych przez DSC. Jeśli folder wersji istnieje, możesz zakończyć z dwoma wystąpieniami. Oto przykładowa struktura folderów dla modułu DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
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

  Podanie tych informacji powoduje wyświetlenie tekstu pomocy `Get-Help` za pośrednictwem polecenia cmdlet w konsoli programu PowerShell. Ten tekst jest również wyświetlany w Azure Portal.

  ![Pomoc modułu integracji](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ połączenia

Jeśli moduł nawiązuje połączenie z usługą zewnętrzną, zdefiniuj [Typ połączenia](#adding-a-connection-type-to-your-module). Każde polecenie cmdlet w module powinno akceptować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr. Użytkownicy mapują parametry zasobu połączenia do odpowiednich parametrów polecenia cmdlet za każdym razem, gdy wywołuje polecenie cmdlet. Poniższy przykład elementu Runbook, na podstawie przykładu w poprzedniej sekcji, używa zasobu połączenia contoso o nazwie `ContosoConnection` , aby uzyskać dostęp do zasobów firmy Contoso i zwrócić dane z usługi zewnętrznej. W tym przykładzie pola są mapowane na właściwości `UserName` i `Password` `PSCredential` obiektu, a następnie przesyłane do polecenia cmdlet.

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

  Można włączyć podobne zachowanie dla poleceń cmdlet, zezwalając im na akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pól połączeń dla parametrów. Zazwyczaj potrzebny jest zestaw parametrów dla każdego, aby użytkownik nie korzystał Azure Automation może wywoływać polecenia cmdlet bez konstruowania obiektu Hashtable do działania jako obiekt połączenia. Zestaw `UserAccount` parametrów służy do przekazywania właściwości pola połączenia. `ConnectionObject`umożliwia bezpośrednie przekazywanie połączenia.

### <a name="output-type"></a>Typ danych wyjściowych

Zdefiniuj typ danych wyjściowych dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet umożliwia funkcji IntelliSense w czasie projektowania, aby pomóc w ustaleniu właściwości danych wyjściowych polecenia cmdlet podczas tworzenia. Ta metoda jest szczególnie przydatna podczas tworzenia graficznego elementu Runbook, w którym wiedza czasu projektowania jest kluczem do łatwego środowiska użytkownika z Twoim modułem.

Dodaj `[OutputType([<MyOutputType>])]` miejsce `MyOutputType` , gdzie jest prawidłowym typem. Aby dowiedzieć się `OutputType`więcej na temat, zobacz [Informacje o funkcjach OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Poniższy kod jest przykładem dodawania `OutputType` do polecenia cmdlet:

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

  To zachowanie jest podobne do funkcji "Type z wyprzedzeniem" w danych wyjściowych polecenia cmdlet w programie PowerShell ISE bez konieczności uruchamiania go.

  ![Funkcja IntelliSense programu POSH](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stan polecenia cmdlet

Ustaw wszystkie polecenia cmdlet w module jako bezstanowe. Wiele zadań elementów Runbook można jednocześnie uruchamiać w taki `AppDomain` sam sposób, jak w tym samym procesie i w piaskownicy. Jeśli na tych poziomach są udostępnione jakiekolwiek Stany, zadania mogą mieć wpływ na siebie. Takie zachowanie może prowadzić do sporadycznych i trudnych do zdiagnozowania problemów. Oto przykład, co nie zrobić:

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

Upewnij się, że moduł jest w pełni zawarty w pakiecie, który można skopiować za pomocą polecenia xcopy. Moduły Azure Automation są dystrybuowane do piaskownic automatyzacji podczas wykonywania elementów Runbook. Moduły muszą działać niezależnie od hosta, na którym są uruchamiane. 

W przypadku zaimportowania go do środowiska programu PowerShell innego hosta należy mieć możliwość przechowywania i przenoszenia pakietu modułu. W tym celu należy się upewnić, że moduł nie jest zależny od żadnych plików poza folderem modułu, który jest zapakowany podczas importowania modułu do Azure Automation. 

Moduł nie powinien zależeć od żadnych unikatowych ustawień rejestru na hoście. Przykłady to ustawienia wprowadzane podczas instalacji produktu. 

### <a name="module-file-paths"></a>Ścieżki pliku modułu

Upewnij się, że wszystkie pliki w module mają ścieżki zawierające mniej niż 140 znaków. Wszystkie ścieżki o długości od 140 znaków powodują problemy z importowaniem elementów Runbook. Azure Automation nie może zaimportować pliku o rozmiarze ścieżki ponad 140 znaków do sesji programu PowerShell za `Import-Module`pomocą polecenia.

## <a name="importing-modules"></a>Importowanie modułów

W tej sekcji zdefiniowano kilka sposobów importowania modułu do konta usługi Automation. 

### <a name="import-modules-in-azure-portal"></a>Importuj moduły w Azure Portal

Aby zaimportować moduł w Azure Portal:

1. Przejdź do konta usługi Automation.
2. Wybierz **moduły** w obszarze **zasoby udostępnione**.
3. Kliknij przycisk **Dodaj moduł**. 
4. Wybierz plik **zip** , który zawiera moduł.
5. Kliknij przycisk **OK** , aby rozpocząć importowanie procesu.

### <a name="import-modules-using-powershell"></a>Importowanie modułów przy użyciu programu PowerShell

Aby zaimportować moduł do konta usługi Automation, można użyć polecenia cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) . Polecenie cmdlet pobiera adres URL dla modułu module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Można również użyć tego samego polecenia cmdlet do zaimportowania modułu bezpośrednio z Galeria programu PowerShell. Upewnij się `ModuleName` , `ModuleVersion` że [Galeria programu PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importuj moduły z Galeria programu PowerShell

Moduły [Galeria programu PowerShell](https://www.powershellgallery.com) można importować bezpośrednio z galerii lub z konta usługi Automation.

Aby zaimportować moduł bezpośrednio z Galeria programu PowerShell:

1. Przejdź do https://www.powershellgallery.com i Wyszukaj moduł do zaimportowania.
2. Kliknij przycisk **Wdróż, aby Azure Automation** na karcie **Azure Automation** w obszarze **Opcje instalacji**. Ta akcja powoduje otwarcie Azure Portal. 
3. Na stronie Importowanie wybierz konto usługi Automation i kliknij przycisk **OK**.

![Galeria programu PowerShell Moduł importowania](../media/modules/powershell-gallery.png)

Aby zaimportować moduł Galeria programu PowerShell bezpośrednio z konta usługi Automation:

1. Wybierz **moduły** w obszarze **zasoby udostępnione**. 
2. Na stronie moduły kliknij pozycję **Przeglądaj Galerię**, a następnie wyszukaj moduł w galerii. 
3. Wybierz moduł do zaimportowania, a następnie kliknij przycisk **Importuj**. 
4. Na stronie Import kliknij przycisk **OK** , aby rozpocząć proces importowania.

![Galeria programu PowerShell zaimportować z Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Usuwanie modułów

Jeśli masz problemy z modułem lub musisz przywrócić poprzednią wersję modułu, możesz usunąć go z konta usługi Automation. Nie można usunąć oryginalnych wersji [domyślnych modułów](#default-modules) , które są importowane podczas tworzenia konta usługi Automation. Jeśli moduł do usunięcia jest nowszą wersją jednego z [domyślnych modułów](#default-modules), zostanie przywrócony do wersji, która została zainstalowana z kontem usługi Automation. W przeciwnym razie wszystkie moduły, które usuniesz z konta usługi Automation, zostaną usunięte.

### <a name="delete-modules-in-azure-portal"></a>Usuń moduły w Azure Portal

Aby usunąć moduł w Azure Portal:

1. Przejdź do konta usługi Automation i wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. 
2. Wybierz moduł, który chcesz usunąć. 
3. Na stronie **moduł** wybierz pozycję **Usuń**. Jeśli ten moduł jest jednym z [domyślnych modułów](#default-modules), zostanie przywrócony do wersji, która istniała podczas tworzenia konta usługi Automation.

### <a name="delete-modules-using-powershell"></a>Usuwanie modułów przy użyciu programu PowerShell

Aby usunąć moduł za pomocą programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Dodawanie typu połączenia do modułu

Możesz podać niestandardowy [Typ połączenia](../automation-connections.md) do użycia na koncie usługi Automation, dodając opcjonalny plik metadanych do modułu. Ten plik określa Azure Automation typ połączenia, który ma być używany z poleceniami cmdlet modułu na koncie usługi Automation. Aby dowiedzieć się więcej na temat tworzenia modułu programu PowerShell, zobacz [jak napisać moduł skryptu programu PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Użyj połączenia niestandardowego w Azure Portal](../media/modules/connection-create-new.png)

Plik określający właściwości typu połączenia nosi nazwę ** &lt;ModuleName&gt;-Automation. JSON** i znajduje się w folderze modułu skompresowanego pliku **zip** . Ten plik zawiera pola połączenia, które są wymagane do nawiązania połączenia z systemem lub usługą reprezentowaną przez moduł. Konfiguracja umożliwia utworzenie typu połączenia w Azure Automation. Przy użyciu tego pliku można ustawić nazwy pól, typy i określić, czy pola są szyfrowane, czy opcjonalne dla typu połączenia modułu. Poniższy przykład to szablon w formacie pliku **JSON** , który definiuje właściwości nazwy użytkownika i hasła:

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

* Aby uzyskać więcej informacji o korzystaniu z modułów Azure PowerShell, zobacz Wprowadzenie [do usługi Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz [pisanie modułu programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
