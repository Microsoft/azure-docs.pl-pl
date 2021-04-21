---
title: Zarządzanie skryptami wstępnymi i skryptami po wdrożeniu Update Management na platformie Azure
description: W tym artykule opisano sposób konfigurowania skryptów wstępnych i skryptów po wdrożeniu aktualizacji oraz zarządzania nimi.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51067095b7ebb33da61908b1424752b481668f5f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830812"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Zarządzanie skryptami wstępnymi i końcowymi

Skrypty wstępne i skrypty po zakończeniu są elementami Runbook uruchamianymi na koncie Azure Automation przed (przed zadaniem) i po wdrożeniu aktualizacji (po nim). Skrypty wstępne i skrypty po uruchomieniu są uruchamiane w kontekście platformy Azure, a nie lokalnie. Skrypty wstępne są uruchamiane na początku wdrożenia aktualizacji. Skrypty uruchamiane po zakończeniu wdrażania i po każdym skonfigurowanym ponownym uruchomieniu.

## <a name="pre-script-and-post-script-requirements"></a>Wymagania dotyczące skryptów wstępnych i po skrypcie

Aby można było użyć go jako skryptu wstępnego lub po nim, należy zaimportować go na konto usługi Automation i [opublikować.](../manage-runbooks.md#publish-a-runbook)

Obecnie jako skrypty przed i po są obsługiwane tylko środowiska PowerShell i python 2 runbook. Inne typy elementów Runbook, takie jak Python 3, Graficzny, Przepływ pracy programu PowerShell i Graficzny przepływ pracy programu PowerShell, nie są obecnie obsługiwane jako skrypty przed/po.

## <a name="pre-script-and-post-script-parameters"></a>Parametry przed skryptem i po skrypcie

Podczas konfigurowania skryptów wstępnych i skryptów po operacji można przekazać parametry tak samo jak w przypadku planowania runbook. Parametry są definiowane podczas tworzenia wdrożenia aktualizacji. Skrypty wstępne i post-scripts obsługują następujące typy:

* [char]
* [bajt]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Parametry elementów Runbook uruchamianych przed skryptem i po skrypcie nie obsługują typów logicznych, obiektów ani tablic. Te wartości powodują niepowodzenie działania runbook. 

Jeśli potrzebujesz innego typu obiektu, możesz rzutować go na inny typ przy użyciu własnej logiki w runbook.

Oprócz standardowych parametrów runbook podano parametr `SoftwareUpdateConfigurationRunContext` (typ ciąg JSON). Jeśli zdefiniujesz parametr w skrypcie wstępnym lub po nim, zostanie on automatycznie przekazany przez wdrożenie aktualizacji. Parametr zawiera informacje o wdrożeniu aktualizacji, które są podzbiorem informacji zwracanych przez interfejs [API SoftwareUpdateconfigurations.](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) W poniższych sekcjach zdefiniowano skojarzone właściwości.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Właściwości SoftwareUpdateConfigurationRunContext

|Właściwość  |Opis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nazwa konfiguracji aktualizacji oprogramowania.        |
|SoftwareUpdateConfigurationRunId     | Unikatowy identyfikator uruchomienia.        |
|SoftwareUpdateConfigurationSettings     | Kolekcja właściwości związanych z konfiguracją aktualizacji oprogramowania.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Systemy operacyjne, których celem jest wdrożenie aktualizacji.         |
|SoftwareUpdateConfigurationSettings.duration     | Maksymalny czas trwania wdrożenia aktualizacji jest uruchamiany zgodnie ze `PT[n]H[n]M[n]S` standardem ISO8601; nazywany również oknem obsługi.          |
|SoftwareUpdateConfigurationSettings.Windows     | Kolekcja właściwości związanych z komputerami z systemem Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Lista kb/s wykluczonych z wdrożenia aktualizacji.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Klasyfikacje aktualizacji wybrane do wdrożenia aktualizacji.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Ustawienia ponownego uruchamiania wdrożenia aktualizacji.        |
|azureVirtualMachines     | Lista elementów resourceId dla maszyn wirtualnych platformy Azure we wdrożeniu aktualizacji.        |
|nonAzureComputerNames|Lista FQDN komputerów spoza platformy Azure we wdrożeniu aktualizacji.|

Poniższy przykład to ciąg JSON przekazany do parametru **SoftwareUpdateConfigurationRunContext:**

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "PT2H0M",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

Pełny przykład ze wszystkimi właściwościami można znaleźć na stronie: [Pobierz konfigurację aktualizacji oprogramowania według nazwy](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Obiekt `SoftwareUpdateConfigurationRunContext` może zawierać zduplikowane wpisy dla maszyn. Może to spowodować wielokrotne uruchamianie skryptów wstępnych i post na tym samym komputerze. Aby omiń to zachowanie, użyj , `Sort-Object -Unique` aby wybrać tylko unikatowe nazwy maszyn wirtualnych.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Używanie skryptu wstępnego lub skryptu po wdrożeniu

Aby użyć skryptu wstępnego lub skryptu po wdrożeniu aktualizacji, rozpocznij od utworzenia wdrożenia aktualizacji. Wybierz **pozycję Skrypty wstępne + Skrypty po.** Ta akcja spowoduje otwarcie **strony Wybieranie skryptów wstępnych i skryptów po.**

![Wybieranie skryptów](./media/pre-post-scripts/select-scripts.png)

Wybierz skrypt, którego chcesz użyć. W tym przykładzie używamy **runbook UpdateManagement-TurnOnVms.** Po wybraniu tego runbook zostanie otwarta **strona Konfigurowanie** skryptu. Wybierz **pozycję Pre-Script (Przed** skryptem), a następnie wybierz przycisk **OK.**

Powtórz ten proces dla **skryptu UpdateManagement-TurnOffVms.** Jednak po wybraniu typu **skryptu wybierz** pozycję **Post-Script**.

W **sekcji Wybrane** elementy są teraz dostępne oba wybrane skrypty. Jeden jest skryptem wstępnym, a drugi skryptem po:

![Wybrane elementy](./media/pre-post-scripts/selected-items.png)

Zakończ konfigurowanie wdrożenia aktualizacji.

Po zakończeniu wdrażania aktualizacji możesz przejść do strony **Wdrożenia aktualizacji,** aby wyświetlić wyniki. Jak widać, stan jest podany dla skryptów wstępnych i po skrypcie:

![Aktualizowanie wyników](./media/pre-post-scripts/update-results.png)

Po wybraniu uruchomienia wdrożenia aktualizacji wyświetlane są dodatkowe szczegóły skryptów wstępnych i skryptów po. W momencie uruchomienia jest dostarczany link do źródła skryptu.

![Wyniki uruchomienia wdrożenia](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Zatrzymywanie wdrożenia

Jeśli chcesz zatrzymać wdrożenie na podstawie skryptu wstępnego, [musisz](../automation-runbook-execution.md#throw) zgłosić wyjątek. Jeśli tego nie zimkniesz, wdrożenie i skrypt po zakończeniu będą nadal działać. Poniższy fragment kodu pokazuje, jak zgłosić wyjątek przy użyciu programu PowerShell.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

W języku Python 2 obsługa wyjątków jest zarządzana w [bloku try.](https://www.python-course.eu/exception_handling.php)

## <a name="interact-with-machines"></a>Interakcja z maszynami

Skrypty wstępne i skrypty po uruchomieniu są uruchamiane jako runbook na koncie usługi Automation, a nie bezpośrednio na maszynach we wdrożeniu. Zadania wstępne i zadania wykonywane po nich są również uruchamiane w kontekście platformy Azure i nie mają dostępu do maszyn spoza platformy Azure. W poniższych sekcjach przedstawiono sposób bezpośredniej interakcji z maszynami, niezależnie od tego, czy są to maszyny wirtualne platformy Azure, czy maszyny spoza platformy Azure.

### <a name="interact-with-azure-machines"></a>Interakcja z maszynami platformy Azure

Zadania wstępne i zadania wykonywane po zadaniach są uruchamiane jako podręczniki Runbook i nie są natywnie uruchamiane na twoich komputerach wirtualnych platformy Azure we wdrożeniu. Aby korzystać z maszyn wirtualnych platformy Azure, musisz mieć następujące elementy:

* Konto Uruchom jako
* Runbook, który chcesz uruchomić

Aby wchodzić w interakcje z maszynami platformy Azure, należy użyć polecenia cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) do interakcji z maszynami wirtualnych platformy Azure. Aby dowiedzieć się, jak to zrobić, zobacz przykładowy skrypt runbook [Update Management — uruchom skrypt za pomocą polecenia Uruchom](https://github.com/azureautomation/update-management-run-script-with-run-command).

### <a name="interact-with-non-azure-machines"></a>Interakcja z maszynami spoza platformy Azure

Zadania wstępne i zadania wykonywane po nich są uruchamiane w kontekście platformy Azure i nie mają dostępu do maszyn spoza platformy Azure. Aby wchodzić w interakcje z maszynami spoza platformy Azure, musisz mieć następujące elementy:

* Konto Uruchom jako
* Hybrydowy proces roboczy runbook zainstalowany na maszynie
* Runbook, który chcesz uruchomić lokalnie
* Nadrzędny element Runbook

Aby wchodzić w interakcje z maszynami spoza platformy Azure, nadrzędny element Runbook jest uruchamiany w kontekście platformy Azure. Ten podręcznik Runbook wywołuje podrzędny podręcznik Runbook za pomocą polecenia cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Należy określić parametr i podać nazwę hybrydowego procesu roboczego `RunOn` runbook, w celu uruchomienia skryptu. Zobacz przykładowy skrypt Update Management [runbook — uruchom skrypt lokalnie.](https://github.com/azureautomation/update-management-run-script-locally)

## <a name="abort-patch-deployment"></a>Przerwanie wdrażania poprawek

Jeśli skrypt wstępny zwraca błąd, warto przerwać wdrożenie. Aby to zrobić, [musisz](/powershell/module/microsoft.powershell.core/about/about_throw) zgłosić błąd w skrypcie dla dowolnej logiki, która byłaby błędem.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Jeśli w języku Python 2 chcesz zgłosić błąd, gdy wystąpi określony warunek, użyj [instrukcji raise.](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement)

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>Samples

Przykłady skryptów wstępnych i skryptów post można znaleźć w organizacji [Azure Automation GitHub](https://github.com/azureautomation) i Galeria programu PowerShell [lub](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)zaimportować je za pośrednictwem Azure Portal. W tym celu na koncie usługi Automation w obszarze **Automatyzacja procesów** wybierz pozycję **Galeria runbook.** Użyj **Update Management** filtru.

![Lista galerii](./media/pre-post-scripts/runbook-gallery.png)

Możesz też wyszukać je według nazwy skryptu, jak pokazano na poniższej liście:

* Update Management — włączanie maszyn wirtualnych
* Update Management — wyłączanie maszyn wirtualnych
* Update Management — uruchamianie skryptu lokalnie
* Update Management — szablon skryptów wstępnych/post
* Update Management — uruchamianie skryptu za pomocą Uruchamianie polecenia

> [!IMPORTANT]
> Po zaimportowaniu podręczników Runbook należy je opublikować, zanim będzie można ich użyć. W tym celu znajdź ten runbook na koncie usługi Automation, wybierz pozycję **Edytuj,** a następnie wybierz pozycję **Publikuj.**

Wszystkie przykłady są oparte na podstawowym szablonie zdefiniowanym w poniższym przykładzie. Za pomocą tego szablonu można utworzyć własny podręcznik Runbook, który będzie używany ze skryptami wstępnymi i skryptami po. Uwzględniono logikę niezbędną do uwierzytelniania na platformie Azure i obsługi `SoftwareUpdateConfigurationRunContext` parametru.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> W przypadku niegraficznych elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są aliasami polecenia [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Możesz użyć tych polecenia cmdlet lub zaktualizować [moduły](../automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto usługi Automation.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat zarządzania aktualizacjami, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych.](manage-updates-for-vm.md)
