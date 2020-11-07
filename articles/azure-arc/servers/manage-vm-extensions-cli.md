---
title: Włączanie rozszerzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z obsługą usługi Azure Arc w środowiskach hybrydowych przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 59c984f4adaec2261d1b08748aa5a91c8246418d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359119"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Włączanie rozszerzeń maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych platformy Azure, które są obsługiwane przez serwery z obsługą usługi Azure ARC, na maszynę hybrydową z systemem Linux lub Windows przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="enable-extension"></a>Włącz rozszerzenie

Aby włączyć rozszerzenie maszyny wirtualnej na serwerze z włączonym łukem, użyj [AZ connectedmachine Machine-Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) z `--machine-name` `--extension-name` `--location` parametrami,,,, `--type` `settings` i `--publisher` .

Poniższy przykład włącza rozszerzenie maszyny wirtualnej Log Analytics na serwerze z systemem Linux z włączonym łukiem:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Poniższy przykład włącza rozszerzenie niestandardowego skryptu na serwerze z włączonym Łukem:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Poniższy przykład włącza rozszerzenie maszyny wirtualnej Key Vault (wersja zapoznawcza) na serwerze z włączonym łukiem:

```azurecli
az connectedmachine machine-extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Zainstalowano rozszerzenia list

Aby uzyskać listę rozszerzeń maszyn wirtualnych na serwerze z włączonym Łukem, użyj [AZ connectedmachine Machine-Extension list](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) z `--machine-name` `--resource-group` parametrami i.

Przykład:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Domyślnie dane wyjściowe poleceń interfejsu wiersza polecenia platformy Azure są w formacie JSON (JavaScript Object Notation). Aby zmienić domyślne dane wyjściowe na listę lub tabelę, na przykład użyj polecenia [AZ Configure--Output](/cli/azure/reference-index). Możesz również dodać `--output` do dowolnego polecenia dla jednorazowej zmiany w formacie danych wyjściowych.

Poniższy przykład pokazuje częściowe dane wyjściowe JSON z `az connectedmachine machine-extension -list` polecenia:

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

Aby usunąć zainstalowane rozszerzenie maszyny wirtualnej na serwerze z włączonym Łukem, użyj [AZ connectedmachine Machine-Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) z `--extension-name` `--machine-name` `--resource-group` parametrami i.

Aby na przykład usunąć rozszerzenie maszyny wirtualnej Log Analytics dla systemu Linux, uruchom następujące polecenie:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

- Można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi przy użyciu [Azure PowerShell](manage-vm-extensions-powershell.md)z [Azure Portal](manage-vm-extensions-portal.md)lub [szablonów Azure Resource Manager](manage-vm-extensions-template.md).

- Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.
