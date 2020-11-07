---
title: Włącz rozszerzenie maszyny wirtualnej przy użyciu Azure PowerShell
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z obsługą usługi Azure ARC z włączonymi środowiskami chmury hybrydowej przy użyciu Azure PowerShell.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 5ed9db23cd19814ff05c2f142f51cea869f2c2d4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359072"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Włącz rozszerzenia maszyny wirtualnej platformy Azure przy użyciu Azure PowerShell

W tym artykule opisano sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych platformy Azure, które są obsługiwane przez serwery z obsługą usługi Azure ARC, na maszynę hybrydową z systemem Linux lub Windows przy użyciu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- Komputer z Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/).

Przed użyciem Azure PowerShell do zarządzania rozszerzeniami maszyn wirtualnych na serwerze hybrydowym zarządzanym przez serwery z obsługą łuku należy zainstalować `Az.ConnectedMachine` moduł. Uruchom następujące polecenie na serwerze z włączonym Łukem:

`Install-Module -Name Az.ConnectedMachine`.

Po zakończeniu instalacji zostanie zwrócony następujący komunikat:

`The installed extension `AZ. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Włącz rozszerzenie

Aby włączyć rozszerzenie maszyny wirtualnej na serwerze z włączonym łukem, użyj polecenie [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) z `-Name` `-ResourceGroupName` `-MachineName` parametrami,,,, `-Location` `-Publisher` `ExtensionType` i `-Settings` .

Poniższy przykład włącza rozszerzenie maszyny wirtualnej Log Analytics na serwerze z systemem Linux z włączonym łukiem:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

Poniższy przykład włącza rozszerzenie niestandardowego skryptu na serwerze z włączonym Łukem:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Rozszerzenie maszyny wirtualnej Key Vault (wersja zapoznawcza)

> [!WARNING]
> Klienci programu PowerShell często dodają `\` do `"` programu w settings.js, na którym spowoduje to niepowodzenie akvvm_service z powodu błędu: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Poniższy przykład włącza rozszerzenie maszyny wirtualnej Key Vault (wersja zapoznawcza) na serwerze z włączonym łukiem:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @{
        "observedCert1"
       }
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationLocationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Zainstalowano rozszerzenia list

Aby uzyskać listę rozszerzeń maszyn wirtualnych na serwerze z włączonym łukiem, użyj parametru [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) z `-MachineName` `-ResourceGroupName` parametrami i.

Przykład:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Usuń zainstalowane rozszerzenie

Aby usunąć zainstalowane rozszerzenie maszyny wirtualnej na serwerze z włączonym Łukem, użyj polecenie [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) z `-Name` `-MachineName` `-ResourceGroupName` parametrami i.

Aby na przykład usunąć rozszerzenie maszyny wirtualnej Log Analytics dla systemu Linux, uruchom następujące polecenie:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Następne kroki

- Można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi przy użyciu [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md), z poziomu szablonów [Azure Portal](manage-vm-extensions-portal.md)lub [Azure Resource Manager](manage-vm-extensions-template.md).

- Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.
