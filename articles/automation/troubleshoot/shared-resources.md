---
title: Rozwiązywanie problemów z zasobami udostępnionymi Azure Automation
description: W tym artykule opisano sposób rozwiązywania problemów z zasobami udostępnionymi Azure Automation.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 1a822166ae4c2bf793e0fa50e93018f499fcc27a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99053624"
---
# <a name="troubleshoot-shared-resource-issues"></a>Rozwiązywanie problemów z zasobami udostępnionymi

W tym artykule omówiono problemy, które mogą wystąpić w przypadku korzystania z [udostępnionych zasobów](../automation-intro.md#shared-resources) w programie Azure Automation.

## <a name="modules"></a>Moduły

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenariusz: moduł jest zablokowany podczas importowania

#### <a name="issue"></a>Problem

Podczas importowania lub aktualizowania modułów Azure Automation moduł jest zablokowany w stanie *importowania* .

#### <a name="cause"></a>Przyczyna

Ponieważ importowanie modułów programu PowerShell to złożony, wieloetapowy proces, moduł może nie zostać prawidłowo zaimportowany i może być zablokowany w stanie przejściowym. Aby dowiedzieć się więcej o procesie importowania, zobacz [Importowanie modułu programu PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć moduł, który jest zablokowany za pomocą polecenia cmdlet [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) . Następnie można ponowić próbę importowania modułu.

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

Istnieje znany problem związany z aktualizowaniem modułów AzureRM na koncie usługi Automation. W przypadku wystąpienia problemu występuje, jeśli moduły znajdują się w grupie zasobów z nazwą liczbową rozpoczynającą się od 0.

#### <a name="resolution"></a>Rozwiązanie

Aby zaktualizować moduły AzureRM na koncie usługi Automation, konto musi znajdować się w grupie zasobów o nazwie alfanumerycznej. Grupy zasobów o nazwach liczbowych zaczynających się od 0 nie mogą teraz zaktualizować modułów AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenariusz: Importowanie modułu nie powiodło się lub polecenia cmdlet nie mogą zostać wykonane po zaimportowaniu

#### <a name="issue"></a>Problem

Nie można zaimportować modułu lub zaimportuje go pomyślnie, ale żadne polecenia cmdlet nie są wyodrębniane.

#### <a name="cause"></a>Przyczyna

Niektóre typowe przyczyny, że moduł nie został pomyślnie zaimportowany do Azure Automation są następujące:

* Struktura nie jest zgodna ze strukturą wymaganą przez automatyzację.
* Moduł jest zależny od innego modułu, który nie został wdrożony na koncie usługi Automation.
* W module brakuje jego zależności w folderze.
* Polecenie cmdlet [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) jest używane do przekazywania modułu i nie podano pełnej ścieżki magazynu lub nie załadowano modułu przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj dowolnego z tych rozwiązań:

* Upewnij się, że moduł jest zgodny z formatem: ModuleName.zip-> ModuleName lub Version Number-> (ModuleName. PSM1, ModuleName.psd1).
* Otwórz plik **. psd1** i sprawdź, czy moduł ma jakiekolwiek zależności. Jeśli tak, Przekaż te moduły do konta usługi Automation.
* Upewnij się, że wszystkie pliki **dll** , do których istnieją odwołania, znajdują się w folderze modułu.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenariusz: Update-AzureModule.ps1 zawiesza się podczas aktualizowania modułów

#### <a name="issue"></a>Problem

Gdy korzystasz z elementu Runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) do aktualizowania modułów platformy Azure, proces aktualizacji modułu zostanie zawieszony.

#### <a name="cause"></a>Przyczyna

W przypadku tego elementu Runbook domyślne ustawienie określające, ile modułów jest aktualizowanych jednocześnie, wynosi 10. Proces aktualizacji jest podatny na błędy w przypadku jednoczesnego aktualizowania zbyt wielu modułów.

#### <a name="resolution"></a>Rozwiązanie

Nie jest powszechne, że wszystkie moduły AzureRM lub AZ są wymagane na tym samym koncie usługi Automation. Należy zaimportować tylko wymagane moduły.

> [!NOTE]
> Należy unikać importowania całości `Az.Automation` lub `AzureRM.Automation` modułu, który importuje wszystkie zawarte moduły.

Jeśli proces aktualizacji zawiesza się, Dodaj `SimultaneousModuleImportJobCount` parametr do skryptu **Update-AzureModules.ps1** i podaj niższą wartość od wartości domyślnej 10. W przypadku zaimplementowania tej logiki spróbuj rozpocząć od wartości 3 lub 5. `SimultaneousModuleImportJobCount` jest parametrem elementu Runbook programu **Update-AutomationAzureModulesForAccount** , który jest używany do aktualizowania modułów platformy Azure. W przypadku wprowadzenia tej korekty proces aktualizacji zostanie uruchomiony dłużej, ale ma lepszą szansę na ukończenie. Poniższy przykład przedstawia parametr i miejsce umieszczenia go w elemencie Runbook:

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

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako zostanie wyświetlony komunikat o błędzie podobny do poniższego:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień wymaganych do utworzenia lub zaktualizowania konta Uruchom jako lub zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Aby utworzyć lub zaktualizować konto Uruchom jako, musisz mieć odpowiednie [uprawnienia](../automation-security-overview.md#permissions) do różnych zasobów używanych przez konto Uruchom jako.

Jeśli problem wynika z blokady, sprawdź, czy można usunąć blokadę. Następnie przejdź do zasobu, który jest zablokowany w Azure Portal, kliknij prawym przyciskiem myszy blokadę, a następnie wybierz pozycję **Usuń**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenariusz: Wystąpił błąd "nie można odnaleźć punktu wejścia o nazwie" GetPerAdapterInfo "w bibliotece DLL" iplpapi.dll "" podczas wykonywania elementu Runbook

#### <a name="issue"></a>Problem

Podczas wykonywania elementu Runbook jest wyświetlany następujący wyjątek:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest najprawdopodobniej spowodowany niepoprawnie skonfigurowanym [kontem Uruchom jako](../automation-security-overview.md).

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że konto Uruchom jako jest prawidłowo skonfigurowane. Następnie sprawdź, czy masz prawidłowy kod w elemencie Runbook do uwierzytelniania na platformie Azure. Poniższy przykład przedstawia fragment kodu do uwierzytelniania na platformie Azure w elemencie Runbook przy użyciu konta Uruchom jako.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Następne kroki

Jeśli ten artykuł nie rozwiąże problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) . To oficjalne konto Microsoft Azure do łączenia społeczności platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.