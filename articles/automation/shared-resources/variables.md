---
title: Zarządzanie zmiennymi w Azure Automation
description: Zmienne zasoby to wartości, które są dostępne dla wszystkich elementów Runbook i konfiguracji DSC w Azure Automation.  W tym artykule wyjaśniono szczegółowe informacje o zmiennych i sposobach pracy z nimi w tworzeniu tekstowych i graficznych.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732763"
---
# <a name="manage-variables-in-azure-automation"></a>Zarządzanie zmiennymi w Azure Automation

Zmienne zasoby to wartości, które są dostępne dla wszystkich elementów Runbook i konfiguracji DSC na koncie usługi Automation. Można nimi zarządzać z poziomu Azure Portal, z programu PowerShell, w ramach elementu Runbook lub konfiguracji DSC.

Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnianie wartości wśród wielu elementów Runbook lub konfiguracji DSC.

- Udostępnianie wartości między wieloma zadaniami z tego samego elementu Runbook lub konfiguracji DSC.

- Zarządzanie wartością używaną przez elementy Runbook lub konfiguracje DSC z poziomu portalu lub wiersza polecenia programu PowerShell. Przykładem jest zestaw typowych elementów konfiguracji, takich jak określona lista nazw maszyn wirtualnych, określona grupa zasobów, nazwa domeny usługi AD i wiele innych.  

Azure Automation utrzymuje zmienne i udostępnia je nawet wtedy, gdy konfiguracja elementu Runbook lub konfiguracji DSC zakończy się niepowodzeniem. Takie zachowanie umożliwia jednemu elementowi Runbook lub konfiguracji DSC ustawienie wartości, która jest używana przez inny element Runbook, lub przez ten sam plik Runbook lub konfiguracji DSC przy następnym uruchomieniu.

Azure Automation przechowuje każdą zaszyfrowaną zmienną bezpiecznie. Podczas tworzenia zmiennej można określić jej szyfrowanie i magazyn, Azure Automation jako bezpieczny zasób. Inne zabezpieczane zasoby obejmują poświadczenia, certyfikaty i połączenia. Azure Automation szyfruje te zasoby i zapisuje je przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Klucz jest przechowywany w Key Vault zarządzanym przez system. Przed zapisaniem bezpiecznego elementu zawartości Azure Automation ładuje klucz z Key Vault a następnie używa go do zaszyfrowania elementu zawartości. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Typy zmiennych

Podczas tworzenia zmiennej przy użyciu Azure Portal należy określić typ danych na liście rozwijanej, aby Portal mógł wyświetlić odpowiednią kontrolkę do wprowadzania wartości zmiennej. Następujące typy zmiennych są dostępne w Azure Automation:

* String
* Liczba całkowita
* DateTime
* Boolean
* Zero

Zmienna nie jest ograniczona do wyznaczono typu danych. Należy ustawić zmienną przy użyciu programu Windows PowerShell, jeśli chcesz określić wartość innego typu. Jeśli wskażesz `Not defined`, wartość zmiennej jest ustawiona na wartość null. Należy ustawić wartość przy użyciu polecenia cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) lub `Set-AutomationVariable` działania.

Nie można użyć Azure Portal do utworzenia lub zmiany wartości typu złożonej zmiennej. Można jednak podać wartość dowolnego typu przy użyciu programu Windows PowerShell. Typy złożone są pobierane jako [parametr PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Można przechowywać wiele wartości w pojedynczej zmiennej przez utworzenie tablicy lub Hashtable i zapisanie jej do zmiennej.

>[!NOTE]
>Zmienne nazw maszyn wirtualnych mogą zawierać maksymalnie 80 znaków. Zmienne grupy zasobów mogą zawierać maksymalnie 90 znaków. Zobacz [reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Polecenia cmdlet programu PowerShell, które tworzą i zarządzają zmiennymi zasobów

Polecenia AZ module w poniższej tabeli służą do tworzenia zasobów zmiennych automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu AZ. Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

| Polecenie cmdlet | Opis |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Pobiera wartość istniejącej zmiennej. Nie można użyć tego polecenia cmdlet do pobrania wartości zaszyfrowanej zmiennej. Jedynym sposobem, aby to zrobić, jest użycie `Get-AutomationVariable` działania w elemencie Runbook lub konfiguracji DSC. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Tworzy nową zmienną i ustawia jej wartość.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Usuwa istniejącą zmienną.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Ustawia wartość istniejącej zmiennej. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Działania umożliwiające dostęp do zmiennych w elementach Runbook i konfiguracjach DSC

Działania w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elementach Runbook i konfiguracjach DSC. Polecenia cmdlet dla tych działań pochodzą z modułu `Orchestrator.AssetManagement.Cmdlets`globalnego.

| Działanie | Opis |
|:---|:---|
|`Get-AutomationVariable`|Pobiera wartość istniejącej zmiennej.|
|`Set-AutomationVariable`|Ustawia wartość istniejącej zmiennej.|

> [!NOTE]
> Należy unikać używania zmiennych w `Name` parametrze `Get-AutomationVariable` w elemencie Runbook lub konfiguracji DSC. Użycie tego parametru może komplikuje odnajdywanie zależności między elementami Runbook lub konfiguracjami DSC a zmiennymi automatyzacji w czasie projektowania.

Należy pamiętać `Get-AutomationVariable` , że program nie działa w programie PowerShell, ale tylko w konfiguracji elementu RUNBOOK lub DSC. Na przykład aby wyświetlić wartość zaszyfrowanej zmiennej, można utworzyć element Runbook, aby uzyskać zmienną, a następnie zapisać ją w strumieniu danych wyjściowych:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funkcje do uzyskiwania dostępu do zmiennych w elementach Runbook języka Python 2

Funkcje w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elemencie Runbook języka Python 2.

|Funkcje języka Python 2|Opis|
|:---|:---|
|`automationassets.get_automation_variable`|Pobiera wartość istniejącej zmiennej. |
|`automationassets.set_automation_variable`|Ustawia wartość istniejącej zmiennej. |

> [!NOTE]
> Aby uzyskać dostęp do `automationassets` funkcji zasobów, należy zaimportować moduł w górnej części elementu Runbook języka Python.

## <a name="working-with-automation-variables"></a>Praca ze zmiennymi automatyzacji

>[!NOTE]
>Jeśli chcesz usunąć szyfrowanie dla zmiennej, należy usunąć zmienną i utworzyć ją ponownie jako niezaszyfrowaną.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Tworzenie nowej zmiennej przy użyciu Azure Portal

1. Na koncie usługi Automation kliknij kafelek **elementy zawartości** , następnie blok **zasoby** , a następnie wybierz pozycję **zmienne**.
2. Na kafelku **zmienne** wybierz pozycję **Dodaj zmienną**.
3. Uzupełnij opcje w bloku **Nowa zmienna** , a następnie kliknij przycisk **Utwórz** , aby zapisać nową zmienną.

> [!NOTE]
> Po zapisaniu zaszyfrowanej zmiennej nie można jej wyświetlić w portalu. Można ją zaktualizować.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Tworzenie i używanie zmiennej w programie Windows PowerShell

Skrypt programu PowerShell używa `New-AzAutomationVariable` polecenia cmdlet lub jego odpowiednika modułu AzureRM, aby utworzyć nową zmienną i ustawić jej wartość początkową. Jeśli zmienna jest zaszyfrowana, wywołanie powinno użyć `Encrypted` parametru.

Skrypt Pobiera wartość zmiennej przy użyciu polecenia [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Jeśli wartość jest typu prostego, polecenie cmdlet pobiera ten sam typ. Jeśli jest to typ złożony, pobierany `PSCustomObject` jest typ.

>[!NOTE]
>Skrypt programu PowerShell nie może pobrać zaszyfrowanej wartości. Jedynym sposobem, aby to zrobić, jest użycie `Get-AutomationVariable` działania w elemencie Runbook lub konfiguracji DSC.

Poniższy przykład pokazuje, jak utworzyć zmienną typu String, a następnie zwrócić jej wartość.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Poniższy przykład pokazuje, jak utworzyć zmienną typu złożonego, a następnie pobrać jej właściwości. W tym przypadku jest używany obiekt maszyny wirtualnej z elementu [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Tworzenie i używanie zmiennej w elemencie Runbook lub konfiguracji DSC

Jedynym sposobem utworzenia nowej zmiennej z poziomu elementu Runbook lub konfiguracji DSC jest użycie `New-AzAutomationVariable` polecenia cmdlet lub jego AzureRM. Skrypt używa tego polecenia cmdlet, aby ustawić początkową wartość zmiennej. Skrypt może następnie pobrać wartość przy użyciu `Get-AzAutomationVariable`. Jeśli wartość jest typu prostego, pobierany jest ten sam typ. Jeśli jest to typ złożony, pobierany jest `PSCustomObject` typ.

>[!NOTE]
>Jedynym sposobem na pobranie zaszyfrowanej wartości jest użycie `Get-AutomationVariable` działania w elemencie Runbook lub konfiguracji DSC. 

### <a name="textual-runbook-samples"></a>Przykładowe elementy Runbook

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Ustawianie i pobieranie prostej wartości ze zmiennej

Następujące przykładowe polecenia pokazują, jak ustawić i pobrać zmienną w tekstowym elemencie Runbook. W tym przykładzie przyjęto założenie, że `NumberOfIterations` tworzone `NumberOfRunnings` są zmienne całkowite o nazwach i i zmienną ciągu o nazwie `SampleMessage`.

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Ustawianie i pobieranie zmiennej w elemencie Runbook języka Python 2

Poniższy przykład pokazuje, jak używać zmiennej, ustawiać zmienną i obsłużyć wyjątek dla nieistniejącej zmiennej w elemencie Runbook języka Python 2.

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

### <a name="graphical-runbook-samples"></a>Graficzne przykłady elementów Runbook

W graficznym elemencie Runbook można dodać działanie `Get-AutomationVariable` lub. `Set-AutomationVariable` Po prostu kliknij prawym przyciskiem myszy zmienną w okienku Biblioteka w edytorze graficznym i wybierz odpowiednie działanie.

![Dodaj zmienną do kanwy](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Ustawianie wartości w zmiennej

Na poniższej ilustracji przedstawiono przykładowe działania służące do zaktualizowania zmiennej o prostej wartości w graficznym elemencie Runbook. W tym przykładzie `Get-AzVM` pobiera pojedynczą maszynę wirtualną platformy Azure i zapisuje nazwę komputera do istniejącej zmiennej ciągu automatyzacji. Nie ma znaczenia, czy [łącze jest potokiem, czy sekwencją](../automation-graphical-authoring-intro.md#links-and-workflow) , ponieważ kod oczekuje tylko pojedynczego obiektu w danych wyjściowych.

![Ustaw prostą zmienną](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat łączenia działań w tworzeniu grafiki, zobacz [linki w temacie Tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow).
- Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [mój pierwszy graficzny element Runbook](../automation-first-runbook-graphical.md).
