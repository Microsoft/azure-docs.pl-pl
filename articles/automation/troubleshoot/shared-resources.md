---
title: Rozwiązywanie problemów z zasobami udostępnionymi w usłudze Azure Automation
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać problemy za pomocą zasobów udostępnionych usługi Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733575"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Rozwiązywanie problemów z zasobami udostępnionymi w usłudze Azure Automation

W tym artykule omówiono rozwiązania problemów, które mogą wystąpić podczas korzystania z [zasobów udostępnionych](../automation-intro.md#shared-resources) w usłudze Azure Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="modules"></a>Moduły

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenariusz: Moduł utknął podczas importowania

#### <a name="issue"></a>Problem

Moduł jest zablokowany w stanie importowania podczas importowania lub aktualizowania modułów usługi Azure Automation.

#### <a name="cause"></a>Przyczyna

Ponieważ importowanie modułów programu PowerShell jest złożonym procesem wieloetapowym, moduł może nie zostać poprawnie zaimportowany i może zostać zablokowany w stanie przejściowym. Aby dowiedzieć się więcej o procesie importowania, zobacz [Importowanie modułu programu PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć moduł, który utknął w stanie importowania przy użyciu polecenia cmdlet [Remove-AzAutomationModule.](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) Następnie można ponowić próbę zaimportowania modułu.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenariusz: Moduły usługi AzureRM utknęły podczas importowania po próbie aktualizacji

#### <a name="issue"></a>Problem

Baner z następującym komunikatem pozostaje na koncie po próbie zaktualizowania modułów usługi AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Przyczyna

Istnieje znany problem z aktualizowaniem modułów AzureRM na koncie automatyzacji, który znajduje się w grupie zasobów o nazwie numerycznej, począwszy od 0.

#### <a name="resolution"></a>Rozwiązanie

Aby zaktualizować moduły usługi AzureRM na koncie automatyzacji, konto musi znajdować się w grupie zasobów o nazwie alfanumerycznej. Grupy zasobów o nazwach liczbowych zaczyna nych od 0 nie mogą obecnie aktualizować modułów usługi AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenariusz: Nie można zaimportować modułu lub nie można wykonać polecenia cmdlet po zaimportowaniu

#### <a name="issue"></a>Problem

Moduł nie można zaimportować lub importuje pomyślnie, ale nie są wyodrębniane polecenia cmdlet.

#### <a name="cause"></a>Przyczyna

Niektóre typowe powody, dla których moduł może nie zostać pomyślnie zaimportować do usługi Azure Automation są:

* Struktura nie pasuje do struktury, która wymaga automatyzacji.
* Moduł zależy od innego modułu, który nie został wdrożony na koncie automatyzacji.
* Moduł brakuje jego zależności w folderze.
* Polecenie cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) jest używane do przekazywania modułu i nie podano pełnej ścieżki magazynu lub nie załadowano modułu przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozwiązanie

Użyj dowolnego z tych rozwiązań, aby rozwiązać ten problem.

* Upewnij się, że moduł jest zgodny z formatem: ModuleName.zip -> ModuleName lub Version Number -> (ModuleName.psm1, ModuleName.psd1).
* Otwórz plik **.psd1** i sprawdź, czy moduł ma jakieś zależności. Jeśli tak, prześlij te moduły do konta automatyzacji.
* Upewnij się, że wszystkie pliki **dll,** do których istnieją odwołania, znajdują się w folderze modułu.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenariusz: Update-AzureModule.ps1 zawiesza się podczas aktualizowania modułów

#### <a name="issue"></a>Problem

Podczas korzystania z [update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook do aktualizacji modułów platformy Azure, proces aktualizacji modułu jest zawieszony.

#### <a name="cause"></a>Przyczyna

Domyślne ustawienie określające, ile modułów jest aktualizowanych jednocześnie, to 10 podczas korzystania z **pliku Update-AzureModule.ps1**. Proces aktualizacji jest podatny na błędy, gdy zbyt wiele modułów są aktualizowane w tym samym czasie.

#### <a name="resolution"></a>Rozwiązanie

Nie jest powszechne, że wszystkie moduły AzureRM lub Az są wymagane na tym samym koncie automatyzacji. Zaleca się importowanie tylko określonych modułów, których potrzebujesz.

> [!NOTE]
> Należy unikać importowania całego `Az.Automation` lub `AzureRM.Automation` modułu, który importuje wszystkie zawarte moduły.

Jeśli proces aktualizacji zostanie wstrzymany, dodaj `SimultaneousModuleImportJobCount` parametr do **skryptu Update-AzureModules.ps1** i podaj niższą wartość niż domyślna wartość 10. Jeśli zaimplementujesz tę logikę, zaleca się rozpoczęcie od wartości 3 lub 5. `SimultaneousModuleImportJobCount`jest parametrem **update-AutomationAzureModulesForAccount** system runbook, który jest używany do aktualizacji modułów platformy Azure. Jeśli dokonasz tej korekty, proces aktualizacji będzie trwalszy, ale ma większe szanse na ukończenie. Poniższy przykład pokazuje parametr i gdzie umieścić go w elementów runbook:

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

Podczas próby utworzenia lub zaktualizowania konta Uruchom jako pojawia się błąd podobny do następującego komunikatu o błędzie:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Przyczyna

Nie masz uprawnień, które są potrzebne do utworzenia lub aktualizacji konta Uruchom jako lub zasób jest zablokowany na poziomie grupy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Aby utworzyć lub zaktualizować konto Uruchom jako, musisz mieć odpowiednie uprawnienia do różnych zasobów [używanych](../manage-runas-account.md#permissions) przez konto Uruchom jako. 

Jeśli problem jest z powodu blokady, sprawdź, czy blokada może zostać usunięta. Następnie przejdź do zasobu zablokowanego w witrynie Azure Portal, kliknij prawym przyciskiem myszy blokadę i kliknij polecenie **Usuń**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenariusz: Podczas wykonywania śg "Nie można znaleźć punktu wejścia o nazwie 'GetPerAdapterInfo' w dll 'iplpapi.dll'"

#### <a name="issue"></a>Problem

Podczas wykonywania elementów runbook otrzymasz następujący wyjątek:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest najprawdopodobniej spowodowany przez niepoprawnie skonfigurowane [uruchom jako konto](../manage-runas-account.md).

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że twoje konto Uruchom jako jest poprawnie skonfigurowane. Następnie sprawdź, czy masz odpowiedni kod w bieście runbook do uwierzytelniania za pomocą platformy Azure. W poniższym przykładzie przedstawiono fragment kodu do uwierzytelnienia na platformie Azure w uruchomieniu za pomocą uruchom jako konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz powyższego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
