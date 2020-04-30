---
title: Rozwiązywanie problemów z zasobami udostępnionymi w Azure Automation
description: Dowiedz się, jak rozwiązywać problemy z zasobami udostępnionymi Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733575"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Rozwiązywanie problemów z zasobami udostępnionymi w Azure Automation

W tym artykule omówiono rozwiązania problemów, które mogą być wykonywane w przypadku korzystania z [udostępnionych zasobów](../automation-intro.md#shared-resources) w programie Azure Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="modules"></a>Moduły

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenariusz: moduł jest zablokowany podczas importowania

#### <a name="issue"></a>Problem

Podczas importowania lub aktualizowania modułów Azure Automation moduł jest zablokowany w stanie importowania.

#### <a name="cause"></a>Przyczyna

Ponieważ importowanie modułów programu PowerShell jest złożonym procesem wieloetapowym, moduł może nie zostać poprawnie zaimportowany i może być zablokowany w stanie przejściowym. Aby dowiedzieć się więcej o procesie importowania, zobacz [Importowanie modułu programu PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć moduł, który jest zablokowany w stanie importowania, za pomocą polecenia cmdlet [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) . Następnie można ponowić próbę importowania modułu.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenariusz: moduły AzureRM są zablokowane podczas importowania po próbie aktualizacji

#### <a name="issue"></a>Problem

Po ponowieniu próby zaktualizowania modułów AzureRM na koncie zostanie wyświetlony baner z następującym komunikatem:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Przyczyna

Istnieje znany problem związany z aktualizowaniem modułów AzureRM na koncie usługi Automation, który znajduje się w grupie zasobów z nazwą liczbową rozpoczynającą się od 0.

#### <a name="resolution"></a>Rozwiązanie

Aby zaktualizować moduły AzureRM na koncie usługi Automation, konto musi znajdować się w grupie zasobów o nazwie alfanumerycznej. Grupy zasobów o nazwach liczbowych zaczynających się od 0 nie mogą teraz zaktualizować modułów AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenariusz: Importowanie modułu nie powiodło się lub polecenia cmdlet nie mogą zostać wykonane po zaimportowaniu

#### <a name="issue"></a>Problem

Importowanie modułu nie powiodło się lub import został pomyślnie zakończony, ale żadne polecenia cmdlet nie są wyodrębniane.

#### <a name="cause"></a>Przyczyna

Niektóre typowe przyczyny, że moduł nie został pomyślnie zaimportowany do Azure Automation są następujące:

* Struktura nie jest zgodna ze strukturą wymaganą przez automatyzację.
* Moduł jest zależny od innego modułu, który nie został wdrożony na koncie usługi Automation.
* W module brakuje jego zależności w folderze.
* Polecenie cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) jest używane do przekazywania modułu i nie podano pełnej ścieżki magazynu lub nie załadowano modułu przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj dowolnego z tych rozwiązań.

* Upewnij się, że moduł jest zgodny z formatem: ModuleName. zip-> ModuleName lub numer wersji — > (ModuleName. PSM1, ModuleName. psd1).
* Otwórz plik **. psd1** i sprawdź, czy moduł ma jakiekolwiek zależności. Jeśli tak, Przekaż te moduły do konta usługi Automation.
* Upewnij się, że wszystkie pliki **dll** , do których istnieją odwołania, znajdują się w folderze modułu.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenariusz: Update-AzureModule. ps1 zawiesza się podczas aktualizowania modułów

#### <a name="issue"></a>Problem

W przypadku aktualizowania modułów platformy Azure przy użyciu elementu Runbook [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) proces aktualizacji modułu jest zawieszony.

#### <a name="cause"></a>Przyczyna

Ustawienie domyślne określające, ile modułów jest aktualizowanych jednocześnie, jest 10 przy użyciu **Update-AzureModule. ps1**. Proces aktualizacji jest podatny na błędy w przypadku jednoczesnego aktualizowania zbyt wielu modułów.

#### <a name="resolution"></a>Rozwiązanie

Nie jest powszechne, że wszystkie moduły AzureRM lub AZ są wymagane na tym samym koncie usługi Automation. Zaleca się Importowanie tylko wymaganych modułów.

> [!NOTE]
> Należy unikać importowania całości `Az.Automation` lub `AzureRM.Automation` modułu, który importuje wszystkie zawarte moduły.

Jeśli proces aktualizacji zawiesza się, należy dodać `SimultaneousModuleImportJobCount` parametr do skryptu **Update-AzureModules. ps1** i podać niższą wartość od wartości domyślnej 10. W przypadku zaimplementowania tej logiki zaleca się rozpoczęcie od wartości 3 lub 5. `SimultaneousModuleImportJobCount`jest parametrem elementu Runbook programu **Update-AutomationAzureModulesForAccount** , który jest używany do aktualizowania modułów platformy Azure. W przypadku wprowadzenia tej korekty proces aktualizacji zostanie uruchomiony dłużej, ale ma lepszą szansę na ukończenie. Poniższy przykład przedstawia parametr i miejsce umieszczenia go w elemencie Runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Konta Uruchom jako

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenariusz: nie można utworzyć lub zaktualizować konta Uruchom jako

#### <a name="issue"></a>Problem

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako zostanie wyświetlony komunikat o błędzie podobny do następującego:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień wymaganych do utworzenia lub zaktualizowania konta Uruchom jako lub zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Aby utworzyć lub zaktualizować konto Uruchom jako, musisz mieć odpowiednie [uprawnienia](../manage-runas-account.md#permissions) do różnych zasobów używanych przez konto Uruchom jako. 

Jeśli przyczyną problemu jest blokada, należy sprawdzić, czy można usunąć blokadę. Następnie przejdź do zasobu, który jest zablokowany w Azure Portal, kliknij prawym przyciskiem myszy blokadę, a następnie kliknij pozycję **Usuń**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenariusz: komunikat o błędzie "nie można odnaleźć punktu wejścia o nazwie" GetPerAdapterInfo "w bibliotece DLL" iplpapi. dll "" podczas wykonywania elementu Runbook

#### <a name="issue"></a>Problem

Podczas wykonywania elementu Runbook jest wyświetlany następujący wyjątek:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest najprawdopodobniej spowodowany niepoprawnie skonfigurowanym [kontem Uruchom jako](../manage-runas-account.md).

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że konto Uruchom jako jest prawidłowo skonfigurowane. Następnie sprawdź, czy masz prawidłowy kod w elemencie Runbook do uwierzytelniania na platformie Azure. Poniższy przykład przedstawia fragment kodu do uwierzytelniania na platformie Azure w elemencie Runbook przy użyciu konta Uruchom jako.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz Twojego problemu powyżej lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż [@AzureSupport](https://twitter.com/azuresupport)połączenie z kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
