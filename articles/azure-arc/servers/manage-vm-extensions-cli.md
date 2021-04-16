---
title: Włączanie rozszerzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na Azure Arc działających w środowiskach chmury hybrydowej przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25e75ede30139201789cd86e6ebddda09a664eb4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388741"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Włączanie rozszerzeń maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule przedstawiono sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych obsługiwanych przez serwery z obsługą Azure Arc na maszynie hybrydowej z systemem Linux lub Windows przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!NOTE]
> Azure Arc obsługuje wdrażania rozszerzeń maszyn wirtualnych i zarządzania nimi na maszynach wirtualnych platformy Azure. W przypadku maszyn wirtualnych platformy Azure zobacz następujący artykuł [z omówieniem rozszerzenia maszyny wirtualnej.](../../virtual-machines/extensions/overview.md)

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Polecenia ConnectedMachine nie są dostarczane jako część interfejsu wiersza polecenia platformy Azure. Przed użyciem interfejsu wiersza polecenia platformy Azure do zarządzania rozszerzeniami maszyn wirtualnych na serwerze hybrydowym zarządzanym przez serwery z usługą Arc należy załadować rozszerzenie ConnectedMachine. Uruchom następujące polecenie, aby go pobrać:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Włączanie rozszerzenia

Aby włączyć rozszerzenie maszyny wirtualnej na serwerze z obsługą usługi Arc, użyj polecenia [az connectedmachine extension create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) z parametrami , , , , i `--machine-name` `--extension-name` `--location` `--type` `settings` `--publisher` .

Poniższy przykład umożliwia włączenie rozszerzenia maszyny wirtualnej usługi Log Analytics na serwerze z obsługą usługi Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

Poniższy przykład włącza rozszerzenie niestandardowego skryptu na serwerze z włączoną usługą Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Poniższy przykład umożliwia włączenie Key Vault maszyny wirtualnej (wersja zapoznawcza) na serwerze z obsługą usługi Arc:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Lista zainstalowanych rozszerzeń

Aby uzyskać listę rozszerzeń maszyn wirtualnych na serwerze z obsługą usługi Arc, użyj [polecenia az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) z `--machine-name` `--resource-group` parametrami i .

Przykład:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Domyślnie dane wyjściowe poleceń interfejsu wiersza polecenia platformy Azure są w danych JSON (JavaScript Object Notation). Aby zmienić domyślne dane wyjściowe na listę lub tabelę, na przykład użyj [az configure --output](/cli/azure/reference-index). Można również dodać do `--output` dowolnego polecenia, aby zmienić format danych wyjściowych raz.

W poniższym przykładzie pokazano częściowe dane wyjściowe JSON z `az connectedmachine extension -list` polecenia :

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Usuwanie zainstalowanego rozszerzenia

Aby usunąć zainstalowane rozszerzenie maszyny wirtualnej na serwerze z usługą Arc, użyj [polecenia az connectedmachine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) z `--extension-name` parametrami i `--machine-name` `--resource-group` .

Aby na przykład usunąć rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux, uruchom następujące polecenie:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

- Rozszerzenia maszyn wirtualnych można wdrażać i usuwać oraz zarządzać nimi przy użyciu narzędzia [Azure PowerShell](manage-vm-extensions-powershell.md), z Azure Portal [lub](manage-vm-extensions-portal.md)Azure Resource Manager [szablonów.](manage-vm-extensions-template.md)

- Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych.](troubleshoot-vm-extensions.md)

- Aby uzyskać więcej informacji na temat poleceń, zapoznaj się z artykułem [Omówienie](/cli/azure/ext/connectedmachine/connectedmachine/extension) rozszerzenia maszyny wirtualnej interfejsu wiersza polecenia platformy Azure.
