---
title: Rozwiązywanie Azure Automation zasobów udostępnionych
description: W tym artykule opisano sposób rozwiązywania problemów z Azure Automation współdzielonych zasobów.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b497b0a8f34b4310e3f11beed982c4453fc79159
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830830"
---
# <a name="troubleshoot-shared-resource-issues"></a>Rozwiązywanie problemów z zasobami udostępnionymi

W tym artykule omówiono problemy, które mogą wystąpić podczas korzystania z zasobów [udostępnionych w](../automation-intro.md#shared-resources) Azure Automation.

## <a name="modules"></a>Moduły

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenariusz: moduł jest zablokowany podczas importowania

#### <a name="issue"></a>Problem

Moduł jest zablokowany w stanie *Importowanie* podczas importowania lub aktualizowania Azure Automation modułów.

#### <a name="cause"></a>Przyczyna

Ponieważ importowanie modułów programu PowerShell jest złożonym, wieloetapowym procesem, moduł może nie zostać poprawnie zaimportowany i może zostać zablokowany w stanie przejściowym. Aby dowiedzieć się więcej na temat procesu importowania, zobacz [Importowanie modułu programu PowerShell.](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, musisz usunąć moduł zablokowany za pomocą polecenia cmdlet [Remove-AzAutomationModule.](/powershell/module/Az.Automation/Remove-AzAutomationModule) Następnie możesz ponowić próbę zaimportowania modułu.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenariusz: Moduły AzureRM są zablokowane podczas importowania po próbie aktualizacji

#### <a name="issue"></a>Problem

Transparent z następującym komunikatem pozostaje na Twoim koncie po próbie zaktualizowania modułów AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Przyczyna

Istnieje znany problem z aktualizowaniem modułów AzureRM na koncie usługi Automation. W szczególności problem występuje, gdy moduły znajdują się w grupie zasobów o nazwie liczbowej rozpoczynającej się od 0.

#### <a name="resolution"></a>Rozwiązanie

Aby zaktualizować moduły AzureRM na koncie usługi Automation, konto musi znajdować się w grupie zasobów o nazwie alfanumerycznej. Grupy zasobów o nazwach liczbowych rozpoczynających się od 0 nie mogą obecnie aktualizować modułów AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenariusz: Importowanie modułu kończy się niepowodzeniem lub nie można wykonać polecenia cmdlet po zaimportowaniu

#### <a name="issue"></a>Problem

Nie można zaimportować modułu lub pomyślnie zaimportować, ale żadne polecenia cmdlet nie są wyodrębnione.

#### <a name="cause"></a>Przyczyna

Niektóre typowe przyczyny, dla których moduł może nie zostać pomyślnie zaimportowany do Azure Automation to:

* Struktura nie jest dopasowana do struktury, która jest potrzebna przez usługę Automation.
* Moduł zależy od innego modułu, który nie został wdrożony na twoim koncie usługi Automation.
* W tym module brakuje zależności w folderze .
* Polecenie cmdlet [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) jest używane do przekazywania modułu i nie podano pełnej ścieżki magazynu lub moduł nie został załadowany przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj dowolnego z tych rozwiązań:

* Upewnij się, że moduł ma następujący format: ModuleName.zip -> ModuleName lub Version Number -> (ModuleName.psm1, ModuleName.psd1).
* Otwórz plik **psd1** i sprawdź, czy moduł ma jakiekolwiek zależności. Jeśli tak, przekaż te moduły na konto usługi Automation.
* Upewnij się, że wszystkie przywołyne pliki **dll** znajdują się w folderze modułu.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenariusz: Update-AzureModule.ps1 zawiesza się podczas aktualizowania modułów

#### <a name="issue"></a>Problem

Jeśli używasz modułu runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) do aktualizowania modułów platformy Azure, proces aktualizacji modułu jest zawieszony.

#### <a name="cause"></a>Przyczyna

W przypadku tego podręcznika Runbook domyślnym ustawieniem określającym, ile modułów jest aktualizowanych jednocześnie, jest 10. Proces aktualizacji jest podatny na błędy, gdy jednocześnie jest aktualizowanych zbyt wiele modułów.

#### <a name="resolution"></a>Rozwiązanie

Nie jest typowe, że wszystkie moduły AzureRM lub Az są wymagane na tym samym koncie usługi Automation. Należy importować tylko potrzebne moduły.

> [!NOTE]
> Unikaj importowania całego `Az.Automation` modułu lub , który `AzureRM.Automation` importuje wszystkie zawarte moduły.

Jeśli proces aktualizacji zostanie wstrzymany, dodaj parametr doUpdate-AzureModules.ps1skryptu i podaj niższą wartość niż domyślna `SimultaneousModuleImportJobCount` 10.  Jeśli zaim implementujesz tę logikę, spróbuj uruchomić od wartości 3 lub 5. `SimultaneousModuleImportJobCount` jest parametrem systemowego **runbook Update-AutomationAzureModulesForAccount,** który jest używany do aktualizowania modułów platformy Azure. Jeśli to zrównowagniesz, proces aktualizacji będzie dłuższy, ale będzie mieć większe szanse na ukończenie. W poniższym przykładzie pokazano parametr i miejsce, w którym ma on być umieszczany w elementy runbook:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenariusz: nie można utworzyć ani zaktualizować konta Uruchom jako

#### <a name="issue"></a>Problem

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako zostanie wyświetlony błąd podobny do następującego:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień wymaganych do utworzenia lub zaktualizowania konta Uruchom jako albo zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Aby utworzyć lub zaktualizować konto Uruchom jako, musisz mieć [odpowiednie](../automation-security-overview.md#permissions) uprawnienia do różnych zasobów używanych przez konto Uruchom jako.

Jeśli problem jest związany z blokadą, sprawdź, czy blokadę można usunąć. Następnie przejdź do zasobu, który jest zablokowany w Azure Portal, kliknij prawym przyciskiem myszy blokadę i wybierz polecenie **Usuń**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenariusz: podczas wykonywania element Runbook występuje błąd "Nie można odnaleźć punktu wejścia o nazwie "GetPerAdapterInfo" w bibliotece DLL "iplpapi.dll""

#### <a name="issue"></a>Problem

Podczas wykonywania runbook otrzymujesz następujący wyjątek:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest najprawdopodobniej spowodowany niepoprawnie skonfigurowanym kontem [Uruchom jako.](../automation-security-overview.md)

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że konto Uruchom jako jest prawidłowo skonfigurowane. Następnie sprawdź, czy w swoim runbooku jest odpowiedni kod do uwierzytelniania na platformie Azure. W poniższym przykładzie pokazano fragment kodu do uwierzytelniania na platformie Azure w ramach runbook przy użyciu konta Uruchom jako.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Następne kroki

Jeśli ten artykuł nie rozwiąże problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów z platformy Azure za [pośrednictwem forów platformy Azure.](https://azure.microsoft.com/support/forums/)
* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z . Jest to oficjalne konto Microsoft Azure łączenia społeczności platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Zdaj zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny pomocy [technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną.**
