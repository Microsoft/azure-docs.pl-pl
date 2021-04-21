---
title: Konfigurowanie żądanego stanu maszyn w Azure Automation
description: W tym artykule opisano sposób konfigurowania żądanego stanu maszyn przy użyciu Azure Automation State Configuration.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70631490e248c801b9465c51548efe8dff704a3e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830146"
---
# <a name="configure-machines-to-a-desired-state"></a>Konfigurowanie żądanego stanu maszyn

Azure Automation State Configuration pozwala określić konfiguracje serwerów i upewnić się, że te serwery są w określonym stanie w czasie.

> [!div class="checklist"]
> - Dołączanie maszyny wirtualnej do zarządzania przez usługę Azure Automation DSC
> - Przekazywanie konfiguracji do Azure Automation
> - Kompilowanie konfiguracji do konfiguracji węzła
> - Przypisywanie konfiguracji węzła do węzła zarządzanego
> - Sprawdzanie stanu zgodności węzła zarządzanego

W tym samouczku użyjemy prostej konfiguracji [DSC,](/powershell/scripting/dsc/configurations/configurations) która zapewnia, że usługi IIS są zainstalowane na maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi Azure Automation. Aby dowiedzieć się więcej o koncie usługi Automation i jego wymaganiach, zobacz [Automation Account authentication overview (Omówienie uwierzytelniania konta usługi Automation).](./automation-security-overview.md)
- Maszyna Azure Resource Manager wirtualna (nie klasyczna) z systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz Tworzenie pierwszej maszyny wirtualnej z systemem [Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell module w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znajomość Desired State Configuration (DSC). Aby uzyskać informacje na temat konfiguracji DSC, [zobacz Windows PowerShell Desired State Configuration Omówienie](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Obsługa konfiguracji częściowych

Azure Automation State Configuration obsługuje korzystanie z konfiguracji [częściowych.](/powershell/scripting/dsc/pull-server/partialconfigs) W tym scenariuszu dsc jest skonfigurowany do zarządzania wieloma konfiguracjami niezależnie, a każda konfiguracja jest pobierana z Azure Automation. Jednak do węzła na konto automatyzacji można przypisać tylko jedną konfigurację. Oznacza to, że jeśli używasz dwóch konfiguracji dla węzła, wymagane będą dwa konta usługi Automation.

Aby uzyskać szczegółowe informacje na temat rejestrowania częściowej konfiguracji z usługi ściągania, zobacz dokumentację dotyczącą konfiguracji [częściowych.](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)

Aby uzyskać więcej informacji na temat współpracy zespołów w celu wspólnego zarządzania serwerami przy użyciu konfiguracji jako kodu, zobacz Understanding DSC's role in a CI/CD Pipeline (Opis roli DSC w [potoku cicha/cd).](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) i postępuj zgodnie z instrukcjami na ekranie.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Tworzenie i przekazywanie konfiguracji do Azure Automation

W edytorze tekstów wpisz następujące informacje i zapisz go lokalnie jako **TestConfig.ps1**.

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
> W bardziej zaawansowanych scenariuszach, w których wymaga się zaimportowania wielu modułów, które zapewniają zasoby DSC, upewnij się, że każdy moduł ma unikatowy `Import-DscResource` wiersz w konfiguracji.

Wywołaj polecenie cmdlet [Import-AzAutomationDscConfiguration,](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) aby przekazać konfigurację na konto usługi Automation.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilowanie konfiguracji do konfiguracji węzła

Aby można było przypisać konfigurację DSC do węzła, należy ją skompilować w konfiguracji węzła. Zobacz [Konfiguracje DSC.](/powershell/scripting/dsc/configurations/configurations)

Wywołaj polecenie cmdlet [Start-AzAutomationDscCompilationJob,](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) aby skompilować konfigurację do konfiguracji węzła o `TestConfig` nazwie na koncie usługi `TestConfig.WebServer` Automation.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Rejestrowanie maszyny wirtualnej, która ma być zarządzana przez State Configuration

Za pomocą usługi Azure Automation State Configuration można zarządzać maszynami wirtualnych platformy Azure (klasycznymi i Resource Manager), lokalnymi maszynami wirtualnych, maszynami z systemem Linux, maszynami wirtualnych platformy AWS i lokalnymi maszynami fizycznymi. W tym temacie opisano sposób rejestrowania tylko Azure Resource Manager wirtualnych. Aby uzyskać informacje na temat rejestrowania innych typów maszyn, zobacz [Dołączanie](automation-dsc-onboarding.md)maszyn do zarządzania przez Azure Automation State Configuration .

Wywołaj polecenie cmdlet [Register-AzAutomationDscNode,](/powershell/module/Az.Automation/Register-AzAutomationDscNode) aby zarejestrować maszynę wirtualną Azure Automation State Configuration jako węzeł zarządzany. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Określanie ustawień trybu konfiguracji

Użyj polecenia cmdlet [Register-AzAutomationDscNode,](/powershell/module/azurerm.automation/register-azurermautomationdscnode) aby zarejestrować maszynę wirtualną jako węzeł zarządzany i określić właściwości konfiguracji. Można na przykład określić, że stan maszyny ma być stosowany tylko raz, określając wartość `ApplyOnly` właściwości `ConfigurationMode` . State Configuration nie próbuje zastosować konfiguracji po początkowym sprawdzaniu.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Można również określić, jak często DSC sprawdza stan konfiguracji przy użyciu `ConfigurationModeFrequencyMins` właściwości . Aby uzyskać więcej informacji na temat ustawień konfiguracji DSC, zobacz Konfigurowanie ustawień [Menedżer konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Przypisywanie konfiguracji węzła do węzła zarządzanego

Teraz możemy przypisać skompilowaną konfigurację węzła do maszyny wirtualnej, którą chcemy skonfigurować.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Przypisuje to konfigurację węzła o nazwie `TestConfig.WebServer` do zarejestrowanego węzła DSC `DscVm` . Domyślnie węzeł DSC jest sprawdzany pod kątem zgodności z konfiguracją węzła co 30 minut. Aby uzyskać informacje na temat zmieniania interwału sprawdzania zgodności, zobacz Konfigurowanie lokalnego [Menedżer konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Sprawdzanie stanu zgodności węzła zarządzanego

Raporty dotyczące stanu zgodności węzła zarządzanego można uzyskać za pomocą polecenia cmdlet [Get-AzAutomationDscNodeReport.](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Usuwanie węzłów z usługi

Po dodaniu węzła do Azure Automation State Configuration ustawienia w ustawieniach lokalnych Menedżer konfiguracji są ustawiane do rejestrowania w usłudze i konfiguracji ściągania oraz wymaganych modułów w celu skonfigurowania maszyny.
Jeśli zdecydujesz się usunąć węzeł z usługi, możesz to zrobić za pomocą polecenia cmdlet Azure Portal Az.

> [!NOTE]
> Wyrejestrowanie węzła z usługi ustawia tylko ustawienia lokalnego Menedżer konfiguracji, aby węzeł nie łączył się już z usługą.
> Nie ma to wpływu na konfigurację, która jest obecnie stosowana do węzła.
> Aby usunąć bieżącą konfigurację, użyj programu [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) lub usuń lokalny plik konfiguracji (jest to jedyna opcja dla węzłów systemu Linux).

### <a name="azure-portal"></a>Azure Portal

Z Azure Automation kliknij pozycję **Konfiguracja stanu (DSC)** w spisie treści.
Następnie kliknij **pozycję Węzły,** aby wyświetlić listę węzłów zarejestrowanych w usłudze.
Kliknij nazwę węzła, który chcesz usunąć.
W widoku Węzeł, który zostanie otwarty, kliknij pozycję **Wyrejestruj**.

### <a name="powershell"></a>PowerShell

Aby wyrejestrować węzeł z Azure Automation State Configuration przy użyciu programu PowerShell, postępuj zgodnie z dokumentacją polecenia cmdlet [Unregister-AzAutomationDscNode.](/powershell/module/az.automation/unregister-azautomationdscnode)

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę, zobacz [Wprowadzenie do Azure Automation State Configuration](automation-dsc-getting-started.md).
- Aby dowiedzieć się, jak włączyć węzły, zobacz [Włączanie Azure Automation State Configuration](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC w celu przypisania ich do węzłów docelowych, zobacz Compile [DSC configurations in Azure Automation State Configuration](automation-dsc-compile.md)(Kompilowanie konfiguracji DSC w systemie Azure Automation State Configuration ).
- Aby zobaczyć przykład użycia narzędzia Azure Automation State Configuration w potoku ciągłego wdrażania, zobacz Konfigurowanie ciągłego wdrażania [za pomocą narzędzia Chocolatey.](automation-dsc-cd-chocolatey.md)
- Aby uzyskać informacje o cenach, [zobacz Azure Automation State Configuration cennik.](https://azure.microsoft.com/pricing/details/automation/)
- Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
