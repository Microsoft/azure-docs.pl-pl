---
title: Używanie konfiguracji żądanego stanu z Virtual Machine Scale Sets
description: Konfigurowanie maszyn wirtualnych przy użyciu Virtual Machine Scale Sets z rozszerzeniem konfiguracji żądanego stanu platformy Azure.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 20e5bff87d5cd0d6e0a35a558462bb5598bfe3f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87080492"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Używanie Virtual Machine Scale Sets z rozszerzeniem DSC platformy Azure
[Virtual Machine Scale Sets](./overview.md) może być używana z obsługą rozszerzenia [konfiguracji żądanego stanu (DSC) platformy Azure](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Zestawy skalowania maszyn wirtualnych zapewniają sposób wdrażania dużej liczby maszyn wirtualnych i zarządzania nimi, a także umożliwiają elastyczne skalowanie i wychodzące w odpowiedzi na obciążenie. Konfiguracja DSC służy do konfigurowania maszyn wirtualnych, gdy są one dostępne w trybie online, tak aby działały oprogramowanie produkcyjne.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Różnice między wdrażaniem do Virtual Machines i Virtual Machine Scale Sets
Źródłowa struktura szablonów zestawu skalowania maszyn wirtualnych różni się nieco od jednej maszyny wirtualnej. W przypadku pojedynczej maszyny wirtualnej wdraża rozszerzenia w węźle "virtualMachines". Istnieje wpis typu "rozszerzenia", w którym do szablonu jest dodawana Konfiguracja DSC

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Węzeł zestawu skalowania maszyn wirtualnych zawiera sekcję "właściwości" z atrybutem "VirtualMachineProfile", "extensionProfile". Konfiguracja DSC jest dodawana do pozycji "rozszerzenia"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Zachowanie zestawu skalowania maszyn wirtualnych
Zachowanie zestawu skalowania maszyn wirtualnych jest identyczne z zachowaniem pojedynczej maszyny wirtualnej. Po utworzeniu nowej maszyny wirtualnej zostanie ona automatycznie zainicjowana przy użyciu rozszerzenia DSC. Jeśli jest wymagana nowsza wersja pliku WMF, maszyna wirtualna zostanie ponownie uruchomiona przed przełączeniem do trybu online. Gdy jest on w trybie online, pobiera plik konfiguracji DSC. zip i udostępnia go na maszynie wirtualnej. Więcej szczegółów można znaleźć w temacie [Omówienie rozszerzenia usługi Azure DSC](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [szablonem Azure Resource Manager dla rozszerzenia DSC](../virtual-machines/extensions/dsc-template.md?toc=/azure/virtual-machines/windows/toc.json).

Dowiedz się, jak [rozszerzenie DSC bezpiecznie obsługuje poświadczenia](../virtual-machines/extensions/dsc-credentials.md?toc=/azure/virtual-machines/windows/toc.json). 

Aby uzyskać więcej informacji na temat procedury obsługi rozszerzeń DSC platformy Azure, zobacz [wprowadzenie do programu obsługi rozszerzenia konfiguracji żądanego stanu platformy Azure](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json). 

Więcej informacji na temat programu PowerShell DSC [można znaleźć w centrum dokumentacji programu PowerShell](/powershell/scripting/dsc/overview/overview). 
