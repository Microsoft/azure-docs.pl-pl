---
title: Zarządzanie zmiennymi w programie Azure Automation
description: W tym artykule opisano sposób pracy ze zmiennymi w plikach Runbook i konfiguracjach DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f1ace00356583dbb6102317e3d157fb58682710
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832583"
---
# <a name="manage-variables-in-azure-automation"></a>Zarządzanie zmiennymi w programie Azure Automation

Zasoby zmiennych to wartości, które są dostępne dla wszystkich zasobów Runbook i konfiguracji DSC na koncie usługi Automation. Można nimi zarządzać z poziomu Azure Portal, programu PowerShell, w ramach runbook lub w konfiguracji DSC.

Zmienne automatyzacji są przydatne w następujących scenariuszach:

- Udostępnianie wartości wielu elementów Runbook lub konfiguracji DSC.

- Udostępnianie wartości między wieloma zadaniami z tego samego pliku Runbook lub konfiguracji DSC.

- Zarządzanie wartością używaną przez podręczniki Runbook lub konfiguracje DSC z portalu lub z wiersza polecenia programu PowerShell. Przykładem jest zestaw typowych elementów konfiguracji, takich jak lista nazw maszyn wirtualnych, określonej grupy zasobów, nazwa domeny usługi AD i inne.  

Azure Automation zmienne są utrwalane i udostępniane nawet w przypadku niepowodzenia konfiguracji runbook lub DSC. To zachowanie umożliwia jednemu użytkownikowi Runbook lub konfiguracji DSC ustawienie wartości, która będzie następnie używana przez inny, lub przez ten sam obiekt Runbook albo konfigurację DSC przy następnym uruchomieniu.

Azure Automation bezpiecznie przechowuje każdą zaszyfrowaną zmienną. Podczas tworzenia zmiennej można określić jej szyfrowanie i magazyn, Azure Automation jako bezpieczny zasób. Po utworzeniu zmiennej nie można zmienić jej stanu szyfrowania bez ponownego utworzenia zmiennej. Jeśli masz zmienne konta usługi Automation przechowujące poufne dane, które nie są jeszcze zaszyfrowane, musisz je usunąć i ponownie utworzyć jako zaszyfrowane zmienne. Zaleca Azure Security Center szyfrowanie wszystkich zmiennych Azure Automation zgodnie z opisem w tece Zmienne konta usługi [Automation powinny być szyfrowane.](../../security-center/recommendations-reference.md#recs-compute) Jeśli masz niezaszyfrowane zmienne, które chcesz wykluczyć [](../../security-center/exempt-resource.md) z tego zalecenia dotyczącego zabezpieczeń, zobacz Temat Wykluczanie zasobu z zaleceń i oceny bezpieczeństwa, aby utworzyć regułę wykluczania.

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza generowanego dla każdego konta usługi Automation. Azure Automation przechowuje klucz w zarządzanym przez system Key Vault. Przed zapisaniem bezpiecznego zasobu automatyzacja ładuje klucz z Key Vault, a następnie używa go do szyfrowania zasobu.

## <a name="variable-types"></a>Typy zmiennych

Podczas tworzenia zmiennej za pomocą Azure Portal należy określić typ danych z listy rozwijanej, aby w portalu można było wyświetlić odpowiednią kontrolkę do wprowadzania wartości zmiennej. Poniżej przedstawiono typy zmiennych dostępne w Azure Automation:

* Ciąg
* Liczba całkowita
* DateTime
* Wartość logiczna
* Zero

Zmienna nie jest ograniczona do określonego typu danych. Należy ustawić zmienną przy użyciu Windows PowerShell, jeśli chcesz określić wartość innego typu. Jeśli `Not defined` wskażemy wartość zmiennej , zostanie ustawiona wartość Null. Należy ustawić wartość za pomocą polecenia cmdlet [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) lub `Set-AutomationVariable` wewnętrznego polecenia cmdlet. W swoich podręcznikach Runbook, które są przeznaczone do uruchamiania w środowisku piaskownicy platformy Azure lub w hybrydowym `Set-AutomationVariable` procesie roboczy runbook systemu Windows, należy użyć programu .

Nie można użyć tej Azure Portal, aby utworzyć lub zmienić wartość dla typu zmiennej złożonej. Można jednak podać wartość dowolnego typu przy użyciu Windows PowerShell. Typy złożone są pobierane jako [Newtonsoft.Jsna. Linq.JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) dla typu obiektu Złożone zamiast typu [PSObject PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Wiele wartości można przechowywać w jednej zmiennej, tworząc tablicę lub tablicę skrótów i zapisując ją w zmiennej.

>[!NOTE]
>Zmienne nazw maszyn wirtualnych mogą mieć maksymalnie 80 znaków. Zmienne grupy zasobów mogą mieć maksymalnie 90 znaków. Zobacz Naming rules and restrictions for Azure resources (Reguły [i ograniczenia nazewnictwa dla zasobów platformy Azure).](../../azure-resource-manager/management/resource-name-rules.md)

## <a name="powershell-cmdlets-to-access-variables"></a>Polecenia cmdlet programu PowerShell do uzyskiwania dostępu do zmiennych

Polecenia cmdlet w poniższej tabeli tworzą zmienne usługi Automation i zarządzają nimi za pomocą programu PowerShell. Są one częścią modułów [Az](modules.md#az-modules).

| Polecenie cmdlet | Opis |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Pobiera wartość istniejącej zmiennej. Jeśli wartość jest typem prostym, pobierany jest ten sam typ. Jeśli jest to typ złożony, `PSCustomObject` pobierany jest typ . <sup>1</sup>|
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Tworzy nową zmienną i ustawia jej wartość.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Usuwa istniejącą zmienną.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Ustawia wartość istniejącej zmiennej. |

<sup>1</sup> Nie można użyć tego polecenia cmdlet do pobrania wartości zaszyfrowanej zmiennej. Jedynym sposobem na to jest użycie wewnętrznego `Get-AutomationVariable` polecenia cmdlet w konfiguracji runbook lub DSC. Aby na przykład wyświetlić wartość zaszyfrowanej zmiennej, możesz utworzyć element Runbook, aby pobrać zmienną, a następnie zapisać ją w strumieniu wyjściowym:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>Wewnętrzne polecenia cmdlet do uzyskiwania dostępu do zmiennych

Wewnętrzne polecenia cmdlet w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w elementy Runbook i konfiguracje DSC. Te polecenia cmdlet są wyposażone w globalny moduł `Orchestrator.AssetManagement.Cmdlets` . Aby uzyskać więcej informacji, zobacz [Wewnętrzne polecenia cmdlet](modules.md#internal-cmdlets).

| Wewnętrzne polecenie cmdlet | Opis |
|:---|:---|
|`Get-AutomationVariable`|Pobiera wartość istniejącej zmiennej.|
|`Set-AutomationVariable`|Ustawia wartość istniejącej zmiennej.|

> [!NOTE]
> Unikaj używania zmiennych w `Name` parametrze `Get-AutomationVariable` polecenia cmdlet w konfiguracji runbook lub DSC. Użycie zmiennej może skomplikować odnajdywanie zależności między podręcznikami Runbook i zmiennymi automatyzacji w czasie projektowania.

## <a name="python-functions-to-access-variables"></a>Funkcje języka Python do uzyskiwania dostępu do zmiennych

Funkcje w poniższej tabeli służą do uzyskiwania dostępu do zmiennych w runbook języka Python 2 i 3. Aplikacje Runbook języka Python 3 są obecnie dostępne w wersji zapoznawczej.

|Funkcje języka Python|Opis|
|:---|:---|
|`automationassets.get_automation_variable`|Pobiera wartość istniejącej zmiennej. |
|`automationassets.set_automation_variable`|Ustawia wartość istniejącej zmiennej. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł w górnej części modułu `automationassets` Runbook języka Python.

## <a name="create-and-get-a-variable"></a>Tworzenie i uzyskiwanie zmiennej

>[!NOTE]
>Jeśli chcesz usunąć szyfrowanie dla zmiennej, musisz usunąć zmienną i utworzyć ją ponownie jako niezaszyfrowany.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Tworzenie i uzyskiwanie zmiennej przy użyciu Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **Zmienne w obszarze** Udostępnione **zasoby.**
2. Na **stronie Zmienne** wybierz pozycję **Dodaj zmienną**.
3. Uzupełnij opcje na **stronie Nowa zmienna,** a następnie wybierz **pozycję Utwórz,** aby zapisać nową zmienną.

> [!NOTE]
> Po zapisaniu zaszyfrowanej zmiennej nie można jej wyświetlić w portalu. Można ją zaktualizować tylko.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Tworzenie i uzyskiwanie zmiennej w Windows PowerShell

Konfiguracja runbook lub DSC używa polecenia cmdlet do utworzenia nowej zmiennej `New-AzAutomationVariable` i ustawienia jej wartości początkowej. Jeśli zmienna jest zaszyfrowana, wywołanie powinno używać `Encrypted` parametru . Skrypt może pobrać wartość zmiennej przy użyciu polecenia `Get-AzAutomationVariable` .

>[!NOTE]
>Skrypt programu PowerShell nie może pobrać zaszyfrowanej wartości. Jedynym sposobem na to jest użycie wewnętrznego `Get-AutomationVariable` polecenia cmdlet.

W poniższym przykładzie pokazano, jak utworzyć zmienną ciągu, a następnie zwrócić jej wartość.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"
$variableValue = "My String"

New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable' `
-Encrypted $false -Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable').Value
```

W poniższym przykładzie pokazano, jak utworzyć zmienną o typie złożonym, a następnie pobrać jej właściwości. W tym przypadku obiekt maszyny wirtualnej z [get-AzVM](/powershell/module/Az.Compute/Get-AzVM) jest używany, określając podzbiór jego właściwości.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" -Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" -AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable" -Encrypted $false -Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable"

$vmName = $vmValue.Value.Name
$vmTags = $vmValue.Value.Tags
```

## <a name="textual-runbook-examples"></a>Tekstowe przykłady runbook

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W poniższym przykładzie pokazano, jak ustawić i pobrać zmienną w tekstowym podręczniku Runbook. W tym przykładzie założono utworzenie zmiennych całkowitych o nazwach **numberOfIterations** i **numberOfRunnings** oraz zmiennej ciągu o **nazwie sampleMessage**.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$numberOfIterations = Get-AutomationVariable -Name "numberOfIterations"
$numberOfRunnings = Get-AutomationVariable -Name "numberOfRunnings"
$sampleMessage = Get-AutomationVariable -Name "sampleMessage"

Write-Output "Runbook has been run $numberOfRunnings times."

for ($i = 1; $i -le $numberOfIterations; $i++) {
    Write-Output "$i`: $sampleMessage"
}
Set-AutomationVariable -Name numberOfRunnings -Value ($numberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

W poniższym przykładzie pokazano, jak uzyskać zmienną, ustawić zmienną i obsłużyć wyjątek dla nieistniejącej zmiennej w elementach Runbook języka Python 2.

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

# <a name="python-3"></a>[Python 3](#tab/python3)

W poniższym przykładzie pokazano, jak uzyskać zmienną, ustawić zmienną i obsłużyć wyjątek dla nieistniejącej zmiennej w elementie Runbook języka Python 3 (wersja zapoznawcza).

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
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Przykłady graficznych elementów Runbook

W graficznym elementie Runbook można dodać działania dla wewnętrznych cmdlet **Get-AutomationVariable** lub **Set-AutomationVariable.** Wystarczy kliknąć prawym przyciskiem myszy każdą zmienną w okienku Biblioteka edytora graficznego i wybrać odpowiednie działanie.

![Dodawanie zmiennej do kanwy](../media/variables/runbook-variable-add-canvas.png)

Na poniższej ilustracji przedstawiono przykładowe działania aktualizowania zmiennej przy użyciu prostej wartości w graficznym elementie Runbook. W tym przykładzie działanie dla programu pobiera pojedynczą maszynę wirtualną platformy Azure i zapisuje nazwę komputera `Get-AzVM` w istniejącej zmiennej ciągu usługi Automation. Nie ma znaczenia, czy link jest [potokiem,](../automation-graphical-authoring-intro.md#use-links-for-workflow) czy sekwencją, ponieważ kod oczekuje tylko jednego obiektu w danych wyjściowych.

![Ustawianie prostej zmiennej](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o poleceniach cmdlet używanych do uzyskiwania dostępu do zmiennych, zobacz [Zarządzanie modułami w programie Azure Automation](modules.md).

- Aby uzyskać ogólne informacje na temat podręczników [Runbook, zobacz Runbook execution in Azure Automation (Wykonywanie Azure Automation Runbook).](../automation-runbook-execution.md)

- Aby uzyskać szczegółowe informacje o konfiguracjach DSC, [zobacz Azure Automation State Configuration omówienie usługi](../automation-dsc-overview.md).
