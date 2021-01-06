---
title: Włączanie rozszerzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z obsługą usługi Azure Arc w środowiskach hybrydowych przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6edb7d55e542f963c75693d535fa3b50dc5b827b
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916205"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Włączanie rozszerzeń maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych obsługiwanych przez serwery z obsługą usługi Azure Arc na maszynie hybrydowej z systemem Linux lub Windows przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Polecenia ConnectedMachine nie są dostarczane jako część interfejsu wiersza polecenia platformy Azure. Przed rozpoczęciem korzystania z interfejsu wiersza polecenia platformy Azure do zarządzania rozszerzeniami maszyn wirtualnych na serwerze hybrydowym zarządzanym przez serwery z obsługą łuku należy załadować rozszerzenie ConnectedMachine. Uruchom następujące polecenie, aby je pobrać:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Włącz rozszerzenie

Aby włączyć rozszerzenie maszyny wirtualnej na serwerze z włączonym łukem, użyj [AZ connectedmachine Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) z `--machine-name` `--extension-name` `--location` parametrami,,,, `--type` `settings` i `--publisher` .

Poniższy przykład włącza rozszerzenie maszyny wirtualnej Log Analytics na serwerze z włączonym Łukem:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

Poniższy przykład włącza rozszerzenie niestandardowego skryptu na serwerze z włączonym Łukem:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Poniższy przykład włącza rozszerzenie maszyny wirtualnej Key Vault (wersja zapoznawcza) na serwerze z włączonym łukiem:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Zainstalowano rozszerzenia list

Aby uzyskać listę rozszerzeń maszyn wirtualnych na serwerze z włączonym Łukem, użyj [AZ connectedmachine Extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) z `--machine-name` `--resource-group` parametrami i.

Przykład:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Domyślnie dane wyjściowe poleceń interfejsu wiersza polecenia platformy Azure są w formacie JSON (JavaScript Object Notation). Aby zmienić domyślne dane wyjściowe na listę lub tabelę, na przykład użyj polecenia [AZ Configure--Output](/cli/azure/reference-index). Możesz również dodać `--output` do dowolnego polecenia dla jednorazowej zmiany w formacie danych wyjściowych.

Poniższy przykład pokazuje częściowe dane wyjściowe JSON z `az connectedmachine extension -list` polecenia:

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

## <a name="remove-an-installed-extension"></a>Usuń zainstalowane rozszerzenie

Aby usunąć zainstalowane rozszerzenie maszyny wirtualnej na serwerze z włączonym Łukem, użyj [AZ connectedmachine Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) z `--extension-name` `--machine-name` `--resource-group` parametrami i.

Aby na przykład usunąć rozszerzenie maszyny wirtualnej Log Analytics dla systemu Linux, uruchom następujące polecenie:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

- Można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi przy użyciu [Azure PowerShell](manage-vm-extensions-powershell.md)z [Azure Portal](manage-vm-extensions-portal.md)lub [szablonów Azure Resource Manager](manage-vm-extensions-template.md).

- Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.

- Zapoznaj się z artykułem [Omówienie](/cli/azure/ext/connectedmachine/connectedmachine/extension) rozszerzenia maszyny wirtualnej interfejsu wiersza polecenia platformy Azure, aby uzyskać więcej informacji na temat poleceń.
