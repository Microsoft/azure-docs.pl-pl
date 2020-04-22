---
title: Zarządzanie zmiennymi w usłudze Azure Automation
description: Zasoby zmienne są wartościami, które są dostępne dla wszystkich podstawowych podstawowych i cyfrowych konfiguracji w usłudze Azure Automation.  W tym artykule wyjaśniono szczegóły zmiennych i sposób pracy z nimi zarówno w tekście, jak i w grafice.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732763"
---
# <a name="manage-variables-in-azure-automation"></a>Zarządzanie zmiennymi w usłudze Azure Automation

Zasoby zmienne to wartości, które są dostępne dla wszystkich podstawowych podstawowych i podstawowych konfiguracji na koncie automatyzacji. Można zarządzać nimi z witryny Azure portal, z programu PowerShell, w ramach uruchomieniu lub w konfiguracji DSC.

Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnianie wartości między wieloma ustawieniami runbook lub konfiguracjami DSC.

- Udostępnianie wartości między wieloma zadaniami z tej samej konfiguracji uruchomieniu lub DSC.

- Zarządzanie wartością używaną przez elementy runbook lub konfiguracje DSC z portalu lub z wiersza polecenia programu PowerShell. Przykładem jest zestaw typowych elementów konfiguracji, takich jak określona lista nazw maszyn wirtualnych, określona grupa zasobów, nazwa domeny usługi AD i inne.  

Usługa Azure Automation utrwala zmienne i udostępnia je nawet wtedy, gdy konfiguracja systemu runbook lub DSC nie powiedzie się. To zachowanie umożliwia jednej konfiguracji zestawu runbook lub DSC, aby ustawić wartość, która jest następnie używana przez inny system runbook lub przez tę samą konfigurację runbook lub DSC przy następnym uruchomieniu.

Usługa Azure Automation bezpiecznie przechowuje każdą zaszyfrowaną zmienną. Podczas tworzenia zmiennej można określić jej szyfrowanie i magazyn przez usługę Azure Automation jako bezpieczny zasób. Inne bezpieczne zasoby obejmują poświadczenia, certyfikaty i połączenia. Usługa Azure Automation szyfruje te zasoby i przechowuje je przy użyciu unikatowego klucza, który jest generowany dla każdego konta automatyzacji. Klucz jest przechowywany w magazynie kluczy zarządzanym przez system. Przed zapisaniem bezpiecznego zasobu usługa Azure Automation ładuje klucz z usługi Key Vault, a następnie używa go do szyfrowania zasobu. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="variable-types"></a>Typy zmiennych

Podczas tworzenia zmiennej za pomocą witryny Azure portal, należy określić typ danych z listy rozwijanej, tak aby portal może wyświetlać odpowiedni formant do wprowadzania wartości zmiennej. Następujące są typy zmiennych dostępne w usłudze Azure Automation:

* Ciąg
* Liczba całkowita
* DateTime
* Wartość logiczna
* Null

Zmienna nie jest ograniczona do wyznaczonego typu danych. Należy ustawić zmienną przy użyciu programu Windows PowerShell, jeśli chcesz określić wartość innego typu. Jeśli wskażesz, `Not defined`wartość zmiennej jest ustawiona na Null. Należy ustawić wartość za pomocą polecenia cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) lub `Set-AutomationVariable` działania.

Nie można użyć witryny Azure portal do tworzenia lub zmieniania wartości dla złożonego typu zmiennej. Jednak można podać wartość dowolnego typu przy użyciu programu Windows PowerShell. Typy złożone są pobierane jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Można zapisać wiele wartości do jednej zmiennej, tworząc tablicę lub hashtable i zapisywanie go do zmiennej.

>[!NOTE]
>Zmienne nazwy maszyny Wirtualnej mogą mieć maksymalnie 80 znaków. Zmienne grupy zasobów mogą mieć maksymalnie 90 znaków. Zobacz [Reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Polecenia cmdlet programu PowerShell, które tworzą zasoby zmienne i zarządzają nimi

W przypadku modułu Az polecenia cmdlet w poniższej tabeli są używane do tworzenia zasobów zmiennych automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu Az.Automation](/powershell/azure/overview), który jest dostępny do użytku w systemach runbook automation i konfiguracjach DSC.

| Polecenie cmdlet | Opis |
|:---|:---|
|[Get-AzAutomationWariowalne](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Pobiera wartość istniejącej zmiennej. Nie można użyć tego polecenia cmdlet do pobrania wartości zaszyfrowanej zmiennej. Jedynym sposobem, aby to zrobić, jest użycie `Get-AutomationVariable` działania w konfiguracji uruchomieniu lub DSC. |
|[Nowe-AzAutomationWarzywalny](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Tworzy nową zmienną i ustawia jej wartość.|
|[Usuń-AzAutomationWariowalne](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Usuwa istniejącą zmienną.|
|[Set-AzAutomationWariowalne](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Ustawia wartość istniejącej zmiennej. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Działania mające na celu dostęp do zmiennych w systemach runbook i konfiguracjach DSC

Działania w poniższej tabeli są używane do uzyskiwania dostępu do zmiennych w konfiguracjach elementów runbook i DSC. Polecenia cmdlet dla tych działań są `Orchestrator.AssetManagement.Cmdlets`wyposażone w moduł globalny.

| Działanie | Opis |
|:---|:---|
|`Get-AutomationVariable`|Pobiera wartość istniejącej zmiennej.|
|`Set-AutomationVariable`|Ustawia wartość istniejącej zmiennej.|

> [!NOTE]
> Należy unikać używania `Name` zmiennych w parametrze `Get-AutomationVariable` w konfiguracji uruchomieniu lub DSC. Użycie tego parametru może skomplikować odnajdowanie zależności między elementami runbook lub konfiguracjami DSC i zmiennymi automatyzacji w czasie projektowania.

Należy `Get-AutomationVariable` zauważyć, że nie działa w programie PowerShell, ale tylko w konfiguracji roboczej lub DSC. Na przykład, aby wyświetlić wartość zaszyfrowanej zmiennej, można utworzyć element runbook, aby uzyskać zmienną, a następnie zapisać ją w strumieniu wyjściowym:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funkcje dostępu do zmiennych w podręcznikach elementów runbook języka Python 2

Funkcje w poniższej tabeli są używane do uzyskiwania dostępu do zmiennych w uruchomieniu w języku Python 2.

|Funkcje Języka Python 2|Opis|
|:---|:---|
|`automationassets.get_automation_variable`|Pobiera wartość istniejącej zmiennej. |
|`automationassets.set_automation_variable`|Ustawia wartość istniejącej zmiennej. |

> [!NOTE]
> Aby uzyskać `automationassets` dostęp do funkcji zasobu, należy zaimportować moduł u góry podręcznika python.

## <a name="working-with-automation-variables"></a>Praca ze zmiennymi automatyzacji

>[!NOTE]
>Aby usunąć szyfrowanie zmiennej, należy usunąć ją i ponownie utworzyć jako niezaszyfrowane.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Tworzenie nowej zmiennej przy użyciu portalu Azure

1. Na koncie automatyzacji kliknij kafelek **Zasoby,** a następnie w bloku **Zasoby** i wybierz pozycję **Zmienne**.
2. Na **kafelku Zmienne** wybierz pozycję **Dodaj zmienną**.
3. Uzupełnij opcje na bloku **Nowa zmienna,** a następnie kliknij przycisk **Utwórz,** aby zapisać nową zmienną.

> [!NOTE]
> Po zapisaniu zaszyfrowanej zmiennej nie można jej wyświetlić w portalu. Można go tylko zaktualizować.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Tworzenie i używanie zmiennej w programie Windows PowerShell

Skrypt programu PowerShell `New-AzAutomationVariable` używa polecenia cmdlet lub jego odpowiednika modułu AzureRM, aby utworzyć nową zmienną i ustawić jej wartość początkową. Jeśli zmienna jest szyfrowana, `Encrypted` wywołanie należy użyć parametru.

Skrypt pobiera wartość zmiennej przy użyciu [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Jeśli wartość jest typem prostym, polecenie cmdlet pobiera ten sam typ. Jeśli jest to typ złożony, `PSCustomObject` typ jest pobierany.

>[!NOTE]
>Skrypt programu PowerShell nie może pobrać zaszyfrowanej wartości. Jedynym sposobem, aby to zrobić, jest użycie `Get-AutomationVariable` działania w konfiguracji uruchomieniu lub DSC.

W poniższym przykładzie pokazano, jak utworzyć zmienną typu String, a następnie zwrócić jej wartość.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

W poniższym przykładzie pokazano, jak utworzyć zmienną o typie złożonym, a następnie pobrać jej właściwości. W takim przypadku używany jest obiekt maszyny wirtualnej z [pliku Get-AzVM.](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Tworzenie i używanie zmiennej w konfiguracji śmi co żyli lub dsc

Jedynym sposobem utworzenia nowej zmiennej z poziomu konfiguracji systemu runbook lub DSC jest użycie `New-AzAutomationVariable` polecenia cmdlet lub jego odpowiednika modułu AzureRM. Skrypt używa tego polecenia cmdlet do ustawiania wartości początkowej zmiennej. Skrypt może następnie pobrać wartość `Get-AzAutomationVariable`za pomocą programu . Jeśli wartość jest typem prostym, ten sam typ jest pobierany. Jeśli jest to typ złożony, `PSCustomObject` a następnie typ jest pobierany.

>[!NOTE]
>Jedynym sposobem pobrania zaszyfrowanej wartości jest użycie `Get-AutomationVariable` działania w konfiguracji runbook lub DSC. 

### <a name="textual-runbook-samples"></a>Przykłady tekstowego podstawowego podstawowego podstawowego podstawowego

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Ustawianie i pobieranie prostej wartości ze zmiennej

Poniższe przykładowe polecenia pokazują, jak ustawić i pobrać zmienną w tekście runbook. W tym przykładzie przyjęto założenie, `NumberOfIterations` że `NumberOfRunnings` powstanie zmiennych `SampleMessage`całkowitych o nazwie i oraz zmiennej ciągowej o nazwie .

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Ustawianie i pobieranie zmiennej w uruchomieniu dla języka Python 2

W poniższym przykładzie pokazano, jak używać zmiennej, ustawić zmienną i obsługiwać wyjątek dla nieistniejącej zmiennej w wiązce elementów runbook języka Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Przykłady graficznego ekscesu

W graficznym uruchomieniu można dodać `Get-AutomationVariable` `Set-AutomationVariable` lub działanie. Wystarczy kliknąć zmienną w okienku Biblioteka edytora graficznego i wybrać odpowiednie działanie.

![Dodawanie zmiennej do kanwy](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Ustawianie wartości w zmiennej

Na poniższej ilustracji przedstawiono przykładowe działania mające na celu zaktualizowanie zmiennej o prostej wartości w graficznym uruchomieniu biecie. W tym `Get-AzVM` przykładzie pobiera pojedynczą maszynę wirtualną platformy Azure i zapisuje nazwę komputera do istniejącej zmiennej ciągu automatyzacji. Nie ma znaczenia, czy [łącze jest potoku lub sekwencji,](../automation-graphical-authoring-intro.md#links-and-workflow) ponieważ kod oczekuje tylko jeden obiekt w danych wyjściowych.

![Ustawianie zmiennej prostej](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o łączeniu działań w tworzenia graficznym, zobacz [Łącza w tworzenia graficznym](../automation-graphical-authoring-intro.md#links-and-workflow).
- Aby rozpocząć korzystanie z graficznych śmięty, zobacz [Mój pierwszy graficzny element runbook](../automation-first-runbook-graphical.md).
