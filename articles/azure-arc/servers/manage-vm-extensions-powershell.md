---
title: Włączanie rozszerzenia maszyny wirtualnej przy użyciu Azure PowerShell
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na Azure Arc działających w środowiskach chmury hybrydowej przy użyciu Azure PowerShell.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 0cb854c9745e8bd7eef35c6f6467c284a6327349
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388588"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Włączanie rozszerzeń maszyn wirtualnych platformy Azure przy użyciu Azure PowerShell

W tym artykule przedstawiono sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych platformy Azure obsługiwanych przez serwery z obsługą Azure Arc na maszynie hybrydowej z systemem Linux lub Windows przy użyciu Azure PowerShell.

> [!NOTE]
> Azure Arc obsługuje wdrażania rozszerzeń maszyn wirtualnych i zarządzania nimi na maszynach wirtualnych platformy Azure. W przypadku maszyn wirtualnych platformy Azure zobacz następujący artykuł [z omówieniem rozszerzenia maszyny wirtualnej.](../../virtual-machines/extensions/overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Komputer z Azure PowerShell. Aby uzyskać instrukcje, [zobacz Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/).

Przed użyciem Azure PowerShell do zarządzania rozszerzeniami maszyn wirtualnych na serwerze hybrydowym zarządzanym przez serwery z usługą Arc należy zainstalować `Az.ConnectedMachine` moduł . Uruchom następujące polecenie na serwerze z usługą Arc:

`Install-Module -Name Az.ConnectedMachine`.

Po zakończeniu instalacji jest zwracany następujący komunikat:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Włączanie rozszerzenia

Aby włączyć rozszerzenie maszyny wirtualnej na serwerze z obsługą usługi Arc, użyj polecenia [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) z parametrami `-Name` , , , , , i `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` `-Settings` .

Poniższy przykład umożliwia włączenie rozszerzenia maszyny wirtualnej usługi Log Analytics na serwerze z systemem Linux z obsługą usługi Arc:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Aby włączyć rozszerzenie maszyny wirtualnej usługi Log Analytics na serwerze z systemem Windows z obsługą usługi Arc, zmień wartość parametru na `-ExtensionType` `"MicrosoftMonitoringAgent"` w poprzednim przykładzie.

Poniższy przykład włącza rozszerzenie niestandardowego skryptu na serwerze z włączoną usługą Arc:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault maszyny wirtualnej (wersja zapoznawcza)

> [!WARNING]
> Klienci programu PowerShell często dodają do polecenia w settings.js, co spowoduje, że akvvm_service `\` `"` się z błędem: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Poniższy przykład umożliwia włączenie Key Vault maszyny wirtualnej (wersja zapoznawcza) na serwerze z obsługą usługi Arc:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Lista zainstalowanych rozszerzeń

Aby uzyskać listę rozszerzeń maszyn wirtualnych na serwerze z usługą Arc, użyj polecenia [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) z `-MachineName` `-ResourceGroupName` parametrami i .

Przykład:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Usuwanie zainstalowanego rozszerzenia

Aby usunąć zainstalowane rozszerzenie maszyny wirtualnej na serwerze z usługą Arc, użyj [polecenia Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) z `-Name` parametrami i `-MachineName` `-ResourceGroupName` .

Aby na przykład usunąć rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux, uruchom następujące polecenie:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Następne kroki

- Rozszerzenia maszyn wirtualnych można wdrażać i usuwać oraz zarządzać nimi przy użyciu interfejsu wiersza polecenia platformy [Azure](manage-vm-extensions-cli.md), z witryny [Azure Portal](manage-vm-extensions-portal.md)lub [Azure Resource Manager szablonów.](manage-vm-extensions-template.md)

- Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych.](troubleshoot-vm-extensions.md)
