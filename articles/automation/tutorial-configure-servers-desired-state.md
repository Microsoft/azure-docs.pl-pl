---
title: Konfigurowanie wymaganego stanu serwerów i zarządzanie odejściem od tego stanu za pomocą usługi Azure Automation
description: Samouczek — zarządzanie konfiguracjami serwerów za pomocą konfiguracji stanu automatyzacji platformy Azure
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678709"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurowanie serwerów do żądanego stanu i zarządzanie dryftem

Konfiguracja stanu automatyzacji usługi Azure umożliwia określenie konfiguracji dla serwerów i zapewnienie, że te serwery są w określonym stanie w czasie.

> [!div class="checklist"]
> - Wbudowana maszyna wirtualna, która ma być zarządzana przez usługę Azure Automation DSC
> - Przekazywanie konfiguracji do usługi Azure Automation
> - Kompilowanie konfiguracji do konfiguracji węzła
> - Przypisywanie konfiguracji węzła do węzła zarządzanego
> - Sprawdzanie stanu zgodności węzła zarządzanego

W tym samouczku używamy prostej [konfiguracji DSC,](/powershell/scripting/dsc/configurations/configurations) która zapewnia, że usługi IIS są zainstalowane na maszynie Wirtualnej.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
- Maszyna wirtualna usługi Azure Resource Manager (nie klasyczna) z systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej systemu Windows w witrynie Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Moduł programu Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znajomość konfiguracji żądanego stanu (DSC). Aby uzyskać informacje o dsc, zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Obsługa konfiguracji częściowych

Konfiguracja stanu automatyzacji platformy Azure obsługuje korzystanie z [częściowych konfiguracji](/powershell/scripting/dsc/pull-server/partialconfigs). W tym scenariuszu DSC jest skonfigurowany do zarządzania wieloma konfiguracjami niezależnie, a każda konfiguracja jest pobierana z usługi Azure Automation. Jednak tylko jedna konfiguracja może być przypisana do węzła na konto automatyzacji. Oznacza to, że jeśli używasz dwóch konfiguracji dla węzła, będziesz potrzebować dwóch kont automatyzacji.

Aby uzyskać szczegółowe informacje na temat rejestrowania częściowej konfiguracji z usługi ściągania, zobacz dokumentację [konfiguracji częściowej](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Aby uzyskać więcej informacji na temat współpracy zespołów w celu wspólnego zarządzania serwerami przy użyciu konfiguracji jako kodu, zobacz [Opis roli DSC w potoku ciągłej integracji/ciągłego wdrażania.](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Tworzenie i przekazywanie konfiguracji do usługi Azure Automation


W edytorze tekstu wpisz następujące polecenie i zapisz go lokalnie jako **TestConfig.ps1**.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> W bardziej zaawansowanych scenariuszach, w których wymagane jest importowanie wielu modułów, `Import-DscResource` które zapewniają zasoby DSC, upewnij się, że każdy moduł ma unikatową linię w konfiguracji.

Wywołanie polecenia cmdlet [import-AzAutomationDscConfiguration,](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) aby przekazać konfigurację do konta automatyzacji.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilowanie konfiguracji do konfiguracji węzła

Konfiguracja DSC musi zostać skompilowana do konfiguracji węzła, zanim będzie można ją przypisać do węzła. Zobacz [konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

Wywołanie polecenia cmdlet [Start-AzAutomationDscCompilationJob,](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) aby skompilować `TestConfig` konfigurację do konfiguracji węzła o nazwie `TestConfig.WebServer` na koncie automatyzacji.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Rejestrowanie maszyny wirtualnej, która ma być zarządzana przez konfigurację stanu

Za pomocą konfiguracji stanu automatyzacji platformy Azure można zarządzać maszynami wirtualnymi platformy Azure (zarówno klasycznymi, jak i Menedżerem zasobów), lokalnymi maszynami wirtualnymi, maszynami z systemem Linux, maszynami wirtualnymi AWS i lokalnymi komputerami fizycznymi. W tym temacie opisano sposób rejestrowania tylko maszyn wirtualnych usługi Azure Resource Manager. Aby uzyskać informacje dotyczące rejestrowania innych typów maszyn, zobacz [Dołączanie maszyn do zarządzania przez konfigurację stanu automatyzacji platformy Azure.](automation-dsc-onboarding.md)

Wywołanie polecenia cmdlet [Register-AzAutomationDscNode,](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) aby zarejestrować maszynę wirtualną w konfiguracji stanu automatyzacji platformy Azure jako węzeł zarządzany. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Określanie ustawień trybu konfiguracji

Polecenie cmdlet [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) służy do rejestrowania maszyny Wirtualnej jako węzła zarządzanego i określania właściwości konfiguracji. Na przykład można określić, że stan komputera ma być stosowany `ApplyOnly` tylko raz, `ConfigurationMode` określając jako wartość właściwości. Konfiguracja stanu nie próbuje zastosować konfiguracji po wstępnym sprawdzeniu.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Można również określić, jak często DSC `ConfigurationModeFrequencyMins` sprawdza stan konfiguracji przy użyciu właściwości. Aby uzyskać więcej informacji na temat ustawień konfiguracji DSC, zobacz [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Przypisywanie konfiguracji węzła do węzła zarządzanego

Teraz możemy przypisać skompilowaną konfigurację węzła do maszyny Wirtualnej, którą chcemy skonfigurować.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Spowoduje to przypisanie `TestConfig.WebServer` konfiguracji węzła o `DscVm`nazwie do zarejestrowanego węzła DSC . Domyślnie węzeł DSC jest sprawdzany pod kątem zgodności z konfiguracją węzła co 30 minut. Aby uzyskać informacje dotyczące zmieniania interwału sprawdzania zgodności, zobacz [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Sprawdzanie stanu zgodności węzła zarządzanego

Raporty dotyczące stanu zgodności węzła zarządzanego można uzyskać za pomocą polecenia cmdlet [Get-AzAutomationDscNodeReport.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Usuwanie węzłów z serwisu

Po dodaniu węzła do konfiguracji stanu automatyzacji usługi Azure, ustawienia w programie Local Configuration Manager są ustawione do rejestrowania się w usłudze i ściągania konfiguracji i wymaganych modułów do skonfigurowania komputera.
Jeśli zdecydujesz się usunąć węzeł z usługi, można to zrobić za pomocą witryny Azure portal lub polecenia cmdlet Az.

> [!NOTE]
> Wyrejestrowanie węzła z usługi ustawia tylko ustawienia lokalnego programu Menedżer konfiguracji, dzięki czemu węzeł nie łączy się już z usługą.
> Nie ma to wpływu na konfigurację, która jest aktualnie stosowana do węzła.
> Aby usunąć bieżącą konfigurację, użyj programu [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) lub usuń lokalny plik konfiguracyjny (jest to jedyna opcja dla węzłów systemu Linux).

### <a name="azure-portal"></a>Azure Portal

W usłudze Azure Automation kliknij **na konfigurację stanu (DSC)** w spisie treści.
Następnie kliknij **węzły,** aby wyświetlić listę węzłów, które są zarejestrowane w usłudze.
Kliknij nazwę węzła, który chcesz usunąć.
W widoku węzła, który zostanie otwarty, kliknij pozycję **Wyrejestruj**.

### <a name="powershell"></a>PowerShell

Aby wyrejestrować węzeł z usługi Konfiguracji stanu automatyzacji usługi Azure Automation przy użyciu programu PowerShell, postępuj zgodnie z dokumentacją polecenia cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o tym, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się, jak dołączać węzły, zobacz [Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [polecenia cmdlet konfiguracji stanu automatyzacji platformy Azure](/powershell/module/azurerm.automation/#automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md)
