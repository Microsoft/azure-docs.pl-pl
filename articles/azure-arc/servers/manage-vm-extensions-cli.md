---
title: Włączanie rozszerzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z obsługą usługi Azure Arc w środowiskach hybrydowych przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462958"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Włączanie rozszerzeń maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych platformy Azure, które są obsługiwane przez serwery z obsługą usługi Azure ARC, na maszynę hybrydową z systemem Linux lub Windows przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Przed rozpoczęciem korzystania z interfejsu wiersza polecenia platformy Azure do zarządzania rozszerzeniami maszyn wirtualnych na serwerze hybrydowym zarządzanym przez serwery z obsługą łuku należy zainstalować `ConnectedMachine` rozszerzenie interfejsu wiersza polecenia. Uruchom następujące polecenie na serwerze z włączonym Łukem:

```azurecli
az extension add connectedmachine
```

Po zakończeniu instalacji zostanie zwrócony następujący komunikat:

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

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

- Można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi przy użyciu [programu PowerShell](manage-vm-extensions-powershell.md), z poziomu [szablonów](manage-vm-extensions-template.md) [Azure Portal](manage-vm-extensions-portal.md)lub Azure Resource Manager.

- Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.
