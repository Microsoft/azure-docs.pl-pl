---
title: Dodawanie rozszerzenia zestawu skalowania maszyn wirtualnych do Service Fabric typu węzła klastra zarządzanego (wersja zapoznawcza)
description: Poniżej przedstawiono sposób dodawania rozszerzenia zestawu skalowania maszyn wirtualnych Service Fabric typu węzła klastra zarządzanego
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 10487bad4fce41c68b4e2cb90c311b986d709eee
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410322"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Dodawanie rozszerzenia zestawu skalowania maszyn wirtualnych do Service Fabric typu węzła klastra zarządzanego (wersja zapoznawcza)

Każdy typ węzła w klastrze zarządzanym Service Fabric jest obsługiwany przez zestaw skalowania maszyn wirtualnych. Dzięki temu można dodać [rozszerzenia zestawu skalowania maszyn wirtualnych](../virtual-machines/extensions/overview.md) do Service Fabric typów węzłów klastra zarządzanego.

Rozszerzenie zestawu skalowania maszyn wirtualnych można dodać do typu węzła za pomocą polecenia programu PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](https://docs.microsoft.com/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension?view=azps-4.7.0&preserve-view=true) .

Alternatywnie możesz mieć rozszerzenie zestawu skalowania maszyn wirtualnych na Service Fabric typu węzła klastra zarządzanego w Azure Resource Manager szablonie, na przykład:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Aby uzyskać więcej informacji na temat konfigurowania Service Fabric zarządzanych typów węzłów klastra, zobacz [Typ węzła klastra zarządzanego](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Fabric zarządzanych klastrów, zobacz:

> [!div class="nextstepaction"]
> [Service Fabric często zadawane pytania dotyczące klastrów zarządzanych](./faq-managed-cluster.md)
