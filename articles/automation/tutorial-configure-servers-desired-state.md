---
title: Konfigurowanie maszyn w żądanym stanie w Azure Automation
description: W tym artykule opisano sposób konfigurowania maszyn do żądanego stanu przy użyciu konfiguracji stanu Azure Automation.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: f16db3f55ebd0f09e4d7b75750fa319daf03977e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99053571"
---
# <a name="configure-machines-to-a-desired-state"></a>Konfigurowanie żądanego stanu maszyn

Azure Automation konfiguracja stanu pozwala określić konfiguracje dla serwerów i upewnić się, że te serwery są w określonym stanie z upływem czasu.

> [!div class="checklist"]
> - Dołączanie maszyny wirtualnej, która ma być zarządzana przez Azure Automation DSC
> - Przekaż konfigurację do Azure Automation
> - Kompiluj konfigurację do konfiguracji węzła
> - Przypisywanie konfiguracji węzła do węzła zarządzanego
> - Sprawdź stan zgodności zarządzanego węzła

W tym samouczku używana jest prosta [Konfiguracja DSC](/powershell/scripting/dsc/configurations/configurations) , która zapewnia, że usługi IIS są zainstalowane na maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi Azure Automation. Aby dowiedzieć się więcej na temat konta usługi Automation i jego wymagań, zobacz temat [uwierzytelnianie konta usługi Automation — Omówienie](./automation-security-overview.md).
- Maszyna wirtualna w Azure Resource Manager (nieklasyczny) z systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell module w wersji 3,6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znajomość konfiguracji żądanego stanu (DSC). Aby uzyskać informacje na temat DSC, zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Obsługa konfiguracji częściowych

Azure Automation konfiguracja stanu obsługuje korzystanie z [konfiguracji częściowych](/powershell/scripting/dsc/pull-server/partialconfigs). W tym scenariuszu Konfiguracja DSC jest konfigurowana w taki sposób, aby zarządzać wieloma konfiguracjami niezależnie, a każda z nich jest pobierana z Azure Automation. Tylko jedną konfigurację można jednak przypisać do węzła na konto usługi Automation. Oznacza to, że w przypadku korzystania z dwóch konfiguracji dla węzła wymagane są dwa konta usługi Automation.

Aby uzyskać szczegółowe informacje na temat rejestrowania częściowej konfiguracji z usługi ściągania, zapoznaj się z dokumentacją [częściową konfiguracje](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Aby uzyskać więcej informacji o tym, jak zespoły mogą współdziałać, aby wspólnie zarządzać serwerami przy użyciu konfiguracji jako kodu, zobacz [Opis roli DSC w potoku](/powershell/scripting/dsc/overview/authoringadvanced)ciągłej integracji/ciągłego wdrażania.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Utwórz i przekaż konfigurację do Azure Automation

W edytorze tekstów wpisz następujące polecenie i Zapisz je lokalnie jako **TestConfig.ps1**.

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
> W bardziej zaawansowanych scenariuszach, w których wymagane jest zaimportowanie wielu modułów, które udostępniają zasoby DSC, upewnij się, że każdy moduł ma unikatowy `Import-DscResource` wiersz w konfiguracji.

Wywołaj polecenie cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) , aby przekazać konfigurację do konta usługi Automation.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompiluj konfigurację do konfiguracji węzła

Konfiguracja DSC musi zostać skompilowana do konfiguracji węzła, aby można było ją przypisać do węzła. Zobacz [konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

Wywołaj polecenie cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) , aby skompilować `TestConfig` konfigurację do konfiguracji węzła o nazwie `TestConfig.WebServer` w Twoim koncie usługi Automation.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Rejestrowanie maszyny wirtualnej tak, aby była zarządzana przez konfigurację stanu

Konfiguracja stanu Azure Automation służy do zarządzania maszynami wirtualnymi platformy Azure (klasycznymi i Menedżer zasobów), lokalnymi maszynami wirtualnymi, maszynami z systemem Linux, AWS maszynami wirtualnymi i lokalnymi maszynami fizycznymi. W tym temacie omówiono sposób rejestrowania tylko Azure Resource Manager maszyn wirtualnych. Aby uzyskać informacje na temat rejestrowania innych typów maszyn, zobacz sekcję dołączanie [maszyn w celu zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

Wywołaj polecenie cmdlet [register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) , aby zarejestrować maszynę wirtualną z konfiguracją stanu Azure Automation jako węzeł zarządzany. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Określ ustawienia trybu konfiguracji

Użyj polecenia cmdlet [register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) , aby zarejestrować maszynę wirtualną jako węzeł zarządzany i określić właściwości konfiguracji. Na przykład można określić, że stan maszyny ma być stosowany tylko raz, określając `ApplyOnly` jako wartość `ConfigurationMode` właściwości. Konfiguracja stanu nie próbuje zastosować konfiguracji po wstępnej kontroli.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Można również określić częstotliwość sprawdzania stanu konfiguracji przez DSC przy użyciu `ConfigurationModeFrequencyMins` właściwości. Aby uzyskać więcej informacji na temat ustawień konfiguracji DSC, zobacz [Konfigurowanie lokalnego Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Przypisywanie konfiguracji węzła do węzła zarządzanego

Teraz możemy przypisać konfigurację skompilowanego węzła do maszyny wirtualnej, którą chcemy skonfigurować.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Spowoduje to przypisanie konfiguracji węzła o nazwie `TestConfig.WebServer` do zarejestrowanego węzła DSC `DscVm` . Domyślnie węzeł DSC jest sprawdzany pod kątem zgodności z konfiguracją węzła co 30 minut. Informacje o sposobie zmiany interwału sprawdzania zgodności znajdują się w temacie [konfigurowanie Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Sprawdź stan zgodności zarządzanego węzła

Możesz uzyskać raporty dotyczące stanu zgodności zarządzanego węzła przy użyciu polecenia cmdlet [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) .

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Usuń węzły z usługi

Po dodaniu węzła do Azure Automation konfiguracji stanu ustawienia w lokalnych Configuration Manager są skonfigurowane do rejestracji w ramach usługi i konfiguracji ściągania oraz wymaganych modułów w celu skonfigurowania maszyny.
Jeśli zdecydujesz się usunąć węzeł z usługi, możesz to zrobić przy użyciu Azure Portal lub polecenia AZ cmdlets.

> [!NOTE]
> Wyrejestrowanie węzła z usługi ustawia tylko ustawienia Configuration Manager lokalnego, aby węzeł nie był już połączony z usługą.
> Nie wpływa to na konfigurację, która jest aktualnie stosowana do węzła.
> Aby usunąć bieżącą konfigurację, użyj programu [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) lub Usuń lokalny plik konfiguracji (jest to jedyna opcja dla węzłów systemu Linux).

### <a name="azure-portal"></a>Azure Portal

W obszarze Azure Automation kliknij pozycję **Konfiguracja stanu (DSC)** w spisie treści.
Kliknij pozycję **węzły** , aby wyświetlić listę węzłów, które są zarejestrowane w usłudze.
Kliknij nazwę węzła, który chcesz usunąć.
W widoku węzła, który zostanie otwarty, kliknij pozycję **Wyrejestruj**.

### <a name="powershell"></a>PowerShell

Aby wyrejestrować węzeł z usługi konfiguracji stanu Azure Automation przy użyciu programu PowerShell, postępuj zgodnie z dokumentacją polecenia cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz Wprowadzenie do [konfiguracji stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się, jak włączyć węzły, zobacz [Włączanie konfiguracji stanu Azure Automation](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
