---
title: Rozszerzenie maszyny wirtualnej usługi Azure Network Watcher Agent dla systemu Linux
description: Wdróż agenta Network Watcher na maszynie wirtualnej z systemem Linux przy użyciu rozszerzenia maszyny wirtualnej.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/14/2017
ms.openlocfilehash: bc252e560df782625d795b30c6688a34f5c2bd79
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563595"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozszerzenie agenta usługi Network Watcher dla maszyny wirtualnej dla systemu Linux

## <a name="overview"></a>Omówienie

[Azure Network Watcher](../../network-watcher/index.yml) to usługa monitorowania wydajności sieci, diagnostyki i analizy, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej (VM) agenta Network Watcher jest wymagane dla niektórych funkcji Network Watcher na maszynach wirtualnych platformy Azure, takich jak przechwytywanie ruchu sieciowego na żądanie i inne zaawansowane funkcje.

Ten artykuł zawiera szczegółowe informacje na temat obsługiwanych platform i opcji wdrażania rozszerzenia maszyny wirtualnej agenta Network Watcher dla systemu Linux. Instalacja agenta nie zakłóca działania lub wymaga ponownego uruchomienia maszyny wirtualnej. Rozszerzenie można wdrożyć na maszynach wirtualnych, które wdrażasz. Jeśli maszyna wirtualna jest wdrażana przez usługę platformy Azure, zapoznaj się z dokumentacją usługi, aby określić, czy zezwala ona na instalowanie rozszerzeń na maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie agenta Network Watcher można skonfigurować dla następujących dystrybucji systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 i 8 |
| Red Hat | 6 i 7 |
| Oracle Linux | 6,8 + i 7 |
| SUSE Linux Enterprise Server | 11 i 12 |
| OpenSUSE przestępny | 42.3 + |
| CentOS | 6.5 + i 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje agenta Network Watcher wymagają, aby maszyna wirtualna była połączona z Internetem. Bez możliwości nawiązywania połączeń wychodzących niektóre funkcje agenta Network Watcher mogą działać nieprawidłowo lub staną się niedostępne. Aby uzyskać więcej informacji na temat funkcji Network Watcher, które wymagają agenta, zapoznaj się z[dokumentacją Network Watcher](../../network-watcher/index.yml).

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia agenta Network Watcher. Rozszerzenie nie wymaga ani nie obsługuje żadnych ustawień dostarczonych przez użytkownika. Rozszerzenie korzysta z konfiguracji domyślnej.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft. Azure. NetworkWatcher |
| typ | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrożyć przy użyciu szablonu Azure Resource Manager. Aby wdrożyć rozszerzenie agenta Network Watcher, użyj poprzedniego schematu JSON w szablonie.

## <a name="azure-classic-cli-deployment"></a>Wdrożenie klasycznego interfejsu wiersza polecenia platformy Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

W poniższym przykładzie wdrożono rozszerzenie maszyny wirtualnej Network Watcher Agent do istniejącej maszyny wirtualnej wdrożonej za pomocą klasycznego modelu wdrażania:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Poniższy przykład służy do wdrażania rozszerzenia maszyny wirtualnej Network Watcher Agent na istniejącej maszynie wirtualnej wdrożonej za pomocą Menedżer zasobów:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

Poniższy przykład przedstawia stan wdrożenia rozszerzenia NetworkWatcherAgentLinux dla maszyny wirtualnej wdrożonej za pośrednictwem Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz zapoznać się z [dokumentacją Network Watcher](../../network-watcher/index.yml)lub skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, zobacz [często zadawane pytania dotyczące pomocy technicznej w Microsoft Azure](https://azure.microsoft.com/support/faq/).
