---
title: Zarządzanie zmiennymi w Azure Automation
description: W tym artykule opisano sposób pracy ze zmiennymi w elementach Runbook i konfiguracjach DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 4749fcb6698ff1716f2cae257cc0efad458bf9a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766189"
---
# <a name="manage-variables-in-azure-automation"></a>Zarządzanie zmiennymi w Azure Automation

Zmienne zasoby to wartości, które są dostępne dla wszystkich elementów Runbook i konfiguracji DSC na koncie usługi Automation. Można nimi zarządzać z poziomu Azure Portal, z programu PowerShell, w ramach elementu Runbook lub konfiguracji DSC.

Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnianie wartości wśród wielu elementów Runbook lub konfiguracji DSC.

- Udostępnianie wartości między wieloma zadaniami z tego samego elementu Runbook lub konfiguracji DSC.

- Zarządzanie wartością używaną przez elementy Runbook lub konfiguracje DSC z poziomu portalu lub wiersza polecenia programu PowerShell. Przykładem jest zestaw typowych elementów konfiguracji, takich jak określona lista nazw maszyn wirtualnych, określona grupa zasobów, nazwa domeny usługi AD i wiele innych.  

Azure Automation utrzymuje zmienne i udostępnia je nawet wtedy, gdy konfiguracja elementu Runbook lub konfiguracji DSC zakończy się niepowodzeniem. Takie zachowanie umożliwia jednemu elementowi Runbook lub konfiguracji DSC ustawienie wartości, która jest używana przez inny element Runbook, lub przez ten sam plik Runbook lub konfiguracji DSC przy następnym uruchomieniu.

Azure Automation przechowuje każdą zaszyfrowaną zmienną bezpiecznie. Podczas tworzenia zmiennej można określić jej szyfrowanie i magazyn, Azure Automation jako bezpieczny zasób. Po utworzeniu zmiennej nie można zmienić jej stanu szyfrowania bez ponownego tworzenia zmiennej. Zalecenie Azure Security Center polega na zaszyfrowaniu wszystkich zmiennych Azure Automation zgodnie z opisem w temacie [zmienne konta usługi Automation powinny być szyfrowane](../../security-center/recommendations-reference.md#recs-computeapp).

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Azure Automation przechowuje klucz w Key Vault zarządzanych przez system. Przed zapisaniem bezpiecznego elementu zawartości Usługa Automation ładuje klucz z Key Vault a następnie używa go do zaszyfrowania elementu zawartości. 

## <a name="variable-types"></a>Typy zmiennych

Podczas tworzenia zmiennej przy użyciu Azure Portal należy określić typ danych na liście rozwijanej, aby Portal mógł wyświetlić odpowiednią kontrolkę do wprowadzania wartości zmiennej. Następujące typy zmiennych są dostępne w Azure Automation:

* Ciąg
* Liczba całkowita
* DateTime
* Boolean (wartość logiczna)
* Zero

Zmienna nie jest ograniczona do określonego typu danych. Należy ustawić zmienną przy użyciu programu Windows PowerShell, jeśli chcesz określić wartość innego typu. Jeśli wskażesz `Not defined` , wartość zmiennej jest ustawiona na wartość null. Należy ustawić wartość przy użyciu polecenia cmdlet [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) lub wewnętrznego `Set-AutomationVariable` polecenia cmdlet.

Nie można użyć Azure Portal do utworzenia lub zmiany wartości typu złożonej zmiennej. Można jednak podać wartość dowolnego typu przy użyciu programu Windows PowerShell. Typy złożone są pobierane jako [Newtonsoft.Jsw. LINQ. JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) dla złożonego typu obiektu zamiast PSObject typu [parametr PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Można przechowywać wiele wartości w pojedynczej zmiennej przez utworzenie tablicy lub Hashtable i zapisanie jej do zmiennej.

>[!NOTE]
>Zmienne nazw maszyn wirtualnych mogą zawierać maksymalnie 80 znaków. Zmienne grupy zasobów mogą zawierać maksymalnie 90 znaków. Zobacz [reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>Polecenia cmdlet programu PowerShell umożliwiające dostęp do zmiennych

Polecenia cmdlet w poniższej tabeli tworzą zmienne automatyzacji i zarządzają nimi przy użyciu programu PowerShell. Są one dostarczane jako część [AZ modules](modules.md#az-modules).

| Polecenie cmdlet | Opis |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Pobiera wartość istniejącej zmiennej. Jeśli wartość jest typu prostego, pobierany jest ten sam typ. Jeśli jest to typ złożony, `PSCustomObject` pobierany jest typ. <br>**Uwaga:** Nie można użyć tego polecenia cmdlet do pobrania wartości zaszyfrowanej zmiennej. Jedynym sposobem, aby to zrobić, jest użycie wewnętrznego `Get-AutomationVariable` polecenia cmdlet w elemencie Runbook lub konfiguracji DSC. Zobacz [wewnętrzne polecenia cmdlet, aby uzyskać dostęp do zmiennych](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Tworzy nową zmienną i ustawia jej wartość.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Usuwa istniejącą zmienną.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Ustawia wartość istniejącej zmiennej. |

## <a name="internal-cmdlets-to-access-variables"></a>Wewnętrzne polecenia cmdlet do uzyskiwania dostępu do zmiennych

Wewnętrzne polecenia cmdlet w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elementach Runbook i konfiguracjach DSC. Te polecenia cmdlet są dołączone do modułu globalnego `Orchestrator.AssetManagement.Cmdlets` . Aby uzyskać więcej informacji, zobacz [wewnętrzne polecenia cmdlet](modules.md#internal-cmdlets).

| Wewnętrzne polecenie cmdlet | Opis |
|:---|:---|
|`Get-AutomationVariable`|Pobiera wartość istniejącej zmiennej.|
|`Set-AutomationVariable`|Ustawia wartość istniejącej zmiennej.|

> [!NOTE]
> Należy unikać używania zmiennych w `Name` parametrze `Get-AutomationVariable` w elemencie Runbook lub konfiguracji DSC. Użycie zmiennych może komplikuje odnajdywanie zależności między elementami Runbook i zmiennymi automatyzacji w czasie projektowania.

`Get-AutomationVariable` Program nie działa w programie PowerShell, ale tylko w konfiguracji elementu Runbook lub DSC. Na przykład aby wyświetlić wartość zaszyfrowanej zmiennej, można utworzyć element Runbook, aby uzyskać zmienną, a następnie zapisać ją w strumieniu danych wyjściowych:

```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Funkcja Python 2 do uzyskiwania dostępu do zmiennych

Funkcje w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elemencie Runbook języka Python 2.

|Funkcje języka Python 2|Opis|
|:---|:---|
|`automationassets.get_automation_variable`|Pobiera wartość istniejącej zmiennej. |
|`automationassets.set_automation_variable`|Ustawia wartość istniejącej zmiennej. |

> [!NOTE]
> `automationassets`Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł w górnej części elementu Runbook języka Python.

## <a name="create-and-get-a-variable"></a>Utwórz i Pobierz zmienną

>[!NOTE]
>Jeśli chcesz usunąć szyfrowanie dla zmiennej, należy usunąć zmienną i utworzyć ją ponownie jako niezaszyfrowaną.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Tworzenie i pobieranie zmiennej przy użyciu Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz **zmienne** w obszarze **zasoby udostępnione**.
2. Na stronie **zmienne** wybierz pozycję **Dodaj zmienną**.
3. Wypełnij opcje na stronie **Nowa zmienna** , a następnie wybierz pozycję **Utwórz** , aby zapisać nową zmienną.

> [!NOTE]
> Po zapisaniu zaszyfrowanej zmiennej nie można jej wyświetlić w portalu. Można ją zaktualizować.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Tworzenie i pobieranie zmiennej w programie Windows PowerShell

Element Runbook lub Konfiguracja DSC używa `New-AzAutomationVariable` polecenia cmdlet do utworzenia nowej zmiennej i ustawienia jej początkowej wartości. Jeśli zmienna jest zaszyfrowana, wywołanie powinno użyć `Encrypted` parametru. Skrypt może pobrać wartość zmiennej przy użyciu `Get-AzAutomationVariable` .

>[!NOTE]
>Skrypt programu PowerShell nie może pobrać zaszyfrowanej wartości. Jedynym sposobem, aby to zrobić, jest użycie wewnętrznego `Get-AutomationVariable` polecenia cmdlet.

Poniższy przykład pokazuje, jak utworzyć zmienną ciągu, a następnie zwrócić jej wartość.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Poniższy przykład pokazuje, jak utworzyć zmienną typu złożonego, a następnie pobrać jej właściwości. W takim przypadku obiekt maszyny wirtualnej z [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) jest używany do określenia podzbioru właściwości.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable"

$vmName = $vmValue.Name
$vmExtensions = $vmValue.Extensions
```
## <a name="textual-runbook-examples"></a>Przykłady przykładowych elementów Runbook

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Pobieranie i Ustawianie prostej wartości ze zmiennej

Poniższy przykład pokazuje, jak ustawić i pobrać zmienną w tekstowym elemencie Runbook. W tym przykładzie przyjęto założenie, że tworzone są zmienne całkowite o nazwach `NumberOfIterations` i `NumberOfRunnings` i zmienną ciągu o nazwie `SampleMessage` .

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Pobieranie i Ustawianie zmiennej w elemencie Runbook języka Python 2

Poniższy przykład pokazuje, jak uzyskać zmienną, ustawić zmienną i obsłużyć wyjątek dla nieistniejącej zmiennej w elemencie Runbook języka Python 2.

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

## <a name="graphical-runbook-examples"></a>Przykłady graficznych elementów Runbook

W graficznym elemencie Runbook można dodać działania dla wewnętrznych poleceń cmdlet `Get-AutomationVariable` lub `Set-AutomationVariable` . Po prostu kliknij prawym przyciskiem myszy każdą zmienną w okienku Biblioteka w edytorze graficznym i wybierz odpowiednie działanie.

![Dodaj zmienną do kanwy](../media/variables/runbook-variable-add-canvas.png)

Na poniższej ilustracji przedstawiono przykładowe czynności służące do zaktualizowania zmiennej o prostej wartości w graficznym elemencie Runbook. W tym przykładzie działanie programu `Get-AzVM`  Pobiera pojedynczą maszynę wirtualną platformy Azure i zapisuje nazwę komputera do istniejącej zmiennej ciągu automatyzacji. Nie ma znaczenia, czy [łącze jest potokiem, czy sekwencją](../automation-graphical-authoring-intro.md#use-links-for-workflow) , ponieważ kod oczekuje tylko pojedynczego obiektu w danych wyjściowych.

![Ustaw prostą zmienną](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat poleceń cmdlet służących do uzyskiwania dostępu do zmiennych, zobacz [Zarządzanie modułami w Azure Automation](modules.md).
* Aby uzyskać ogólne informacje o elementach Runbook, zobacz [wykonywanie elementów Runbook w Azure Automation](../automation-runbook-execution.md).
* Aby uzyskać szczegółowe informacje na temat konfiguracji DSC, zobacz [Konfiguracja stanu Azure Automation przegląd](../automation-dsc-overview.md).
