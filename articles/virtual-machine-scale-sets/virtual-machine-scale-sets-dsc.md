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
ms.openlocfilehash: 6939339ce8d0f324fbd9e14ba31d23a6febcfcbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373901"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Używanie Virtual Machine Scale Sets z rozszerzeniem DSC platformy Azure
[Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md) może być używana z obsługą rozszerzenia [konfiguracji żądanego stanu (DSC) platformy Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Zestawy skalowania maszyn wirtualnych zapewniają sposób wdrażania dużej liczby maszyn wirtualnych i zarządzania nimi, a także umożliwiają elastyczne skalowanie i wychodzące w odpowiedzi na obciążenie. Konfiguracja DSC służy do konfigurowania maszyn wirtualnych, gdy są one dostępne w trybie online, tak aby działały oprogramowanie produkcyjne.

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
Zachowanie zestawu skalowania maszyn wirtualnych jest identyczne z zachowaniem pojedynczej maszyny wirtualnej. Po utworzeniu nowej maszyny wirtualnej zostanie ona automatycznie zainicjowana przy użyciu rozszerzenia DSC. Jeśli jest wymagana nowsza wersja pliku WMF, maszyna wirtualna zostanie ponownie uruchomiona przed przełączeniem do trybu online. Gdy jest on w trybie online, pobiera plik konfiguracji DSC. zip i udostępnia go na maszynie wirtualnej. Więcej szczegółów można znaleźć w temacie [Omówienie rozszerzenia usługi Azure DSC](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [szablonem Azure Resource Manager dla rozszerzenia DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dowiedz się, jak [rozszerzenie DSC bezpiecznie obsługuje poświadczenia](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Aby uzyskać więcej informacji na temat procedury obsługi rozszerzeń DSC platformy Azure, zobacz [wprowadzenie do programu obsługi rozszerzenia konfiguracji żądanego stanu platformy Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Więcej informacji na temat programu PowerShell DSC [można znaleźć w centrum dokumentacji programu PowerShell](/powershell/scripting/dsc/overview/overview). 

