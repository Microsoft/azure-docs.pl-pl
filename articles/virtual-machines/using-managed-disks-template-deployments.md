---
title: Wdrażanie dysków przy użyciu szablonów Azure Resource Manager
description: Szczegóły dotyczące korzystania z dysków zarządzanych i niezarządzanych w szablonach Azure Resource Manager dla maszyn wirtualnych platformy Azure.
documentationcenter: ''
author: jboeshart
manager: ''
ms.service: virtual-machines
ms.topic: how-to
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.subservice: disks
ms.openlocfilehash: 298c6e4f33fff99fff01b6b911ec77c78f1fbd26
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666555"
---
# <a name="using-disks-in-azure-resource-manager-templates"></a>Używanie dysków w szablonach Azure Resource Manager

Ten dokument zawiera instrukcje dotyczące różnic między dyskami zarządzanymi i niezarządzanymi przy użyciu szablonów Azure Resource Manager do obsługi maszyn wirtualnych. Przykłady ułatwiają aktualizowanie istniejących szablonów, które używają dysków niezarządzanych do dysków zarządzanych. Aby uzyskać informacje na temat programu, użyj szablonu [101-VM-Simple-Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) jako przewodnika. Możesz wyświetlić szablon przy użyciu dysków [zarządzanych](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) i wcześniejszej wersji za pomocą [dysków niezarządzanych](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) , jeśli chcesz je bezpośrednio porównać.

## <a name="unmanaged-disks-template-formatting"></a>Formatowanie szablonu dysków niezarządzanych

Aby rozpocząć, przyjrzyjmy się rozmieszczeniu dysków niezarządzanych. Podczas tworzenia dysków niezarządzanych wymagane jest konto magazynu do przechowywania plików VHD. Możesz utworzyć nowe konto magazynu lub użyć już istniejącego konta. W tym artykule pokazano, jak utworzyć nowe konto magazynu. Utwórz zasób konta magazynu w bloku zasoby, jak pokazano poniżej.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

W ramach obiektu maszyny wirtualnej Dodaj zależność na koncie magazynu, aby upewnić się, że jest ona utworzona przed maszyną wirtualną. W `storageProfile` sekcji Określ pełny identyfikator URI lokalizacji wirtualnego dysku twardego, która odwołuje się do konta magazynu i jest wymagana dla dysku systemu operacyjnego i dysków z danymi.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formatowanie szablonu dysków zarządzanych

W przypadku usługi Azure Managed Disks dysk zostanie zasobem najwyższego poziomu i nie będzie już wymagał konta magazynu, które ma zostać utworzone przez użytkownika. Dyski zarządzane zostały najpierw ujawnione w `2016-04-30-preview` wersji interfejsu API, są dostępne we wszystkich kolejnych wersjach interfejsu API i są teraz domyślnym typem dysku. W poniższych sekcjach opisano ustawienia domyślne i szczegółowe informacje dotyczące sposobu dostosowywania dysków.

> [!NOTE]
> Zalecane jest korzystanie z wersji interfejsu API nowszej niż w przypadku, `2016-04-30-preview` gdy wystąpią istotne zmiany między programami `2016-04-30-preview` i `2017-03-30` .
>
>

### <a name="default-managed-disk-settings"></a>Domyślne ustawienia dysku zarządzanego

Aby utworzyć maszynę wirtualną z dyskami zarządzanymi, nie musisz już tworzyć zasobów konta magazynu. Odwołujące się do poniższego przykładu szablonu istnieją pewne różnice między poprzednimi niezarządzanymi przykładami dysków, które należy zauważyć:

- `apiVersion`Jest to wersja, która obsługuje dyski zarządzane.
- `osDisk``dataDisks`nie odwołują się już do określonego identyfikatora URI dla wirtualnego dysku twardego.
- Podczas wdrażania bez określania dodatkowych właściwości dysk będzie używał typu magazynu na podstawie rozmiaru maszyny wirtualnej. Na przykład jeśli używasz rozmiaru maszyny wirtualnej obsługującej usługę Premium Storage (rozmiary z "s" w nazwie, takiej jak Standard_D2s_v3), dyski Premium zostaną skonfigurowane domyślnie. Można to zmienić przy użyciu ustawienia SKU dysku, aby określić typ magazynu.
- Jeśli nazwa dysku nie zostanie określona, pobiera format `<VMName>_OsDisk_1_<randomstring>` dla dysku systemu operacyjnego i `<VMName>_disk<#>_<randomstring>` dla każdego dysku z danymi.
  - Jeśli maszyna wirtualna jest tworzona na podstawie obrazu niestandardowego, domyślne ustawienia typu konta magazynu i nazwy dysku są pobierane z właściwości dysku zdefiniowanych w zasobie obrazu niestandardowego. Można je przesłonić, określając wartości tych w szablonie.
- Domyślnie usługa Azure Disk Encryption jest wyłączona.
- Domyślnie buforowanie dysków jest odczytywane i zapisywane dla dysku systemu operacyjnego, a nie dla dysków danych.
- W poniższym przykładzie nadal występuje zależność konta magazynu, ale jest to tylko w przypadku przechowywania danych diagnostycznych i nie jest wymagana w przypadku magazynu dyskowego.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Korzystanie z zasobu dysku zarządzanego najwyższego poziomu

Alternatywnie, aby określić konfigurację dysku w obiekcie maszyny wirtualnej, można utworzyć zasób dysku najwyższego poziomu i dołączyć go w ramach tworzenia maszyny wirtualnej. Można na przykład utworzyć zasób dyskowy, który będzie używany jako dysk danych.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

W obrębie obiektu maszyny wirtualnej odwołuje się do obiektu dysku, który ma zostać dołączony. Określenie identyfikatora zasobu dysku zarządzanego utworzonego we `managedDisk` Właściwości pozwala na zamocowanie dysku podczas tworzenia maszyny wirtualnej. `apiVersion`Dla zasobu maszyny wirtualnej jest ustawiana wartość `2017-03-30` . Zostanie dodany zależność od zasobu dysku, aby upewnić się, że został pomyślnie utworzony przed utworzeniem maszyny wirtualnej. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Tworzenie zarządzanych zestawów dostępności z maszynami wirtualnymi za pomocą usługi Managed disks

Aby utworzyć zarządzane zestawy dostępności z maszynami wirtualnymi za pomocą usługi Managed disks, Dodaj `sku` obiekt do zasobu zestawu dostępności i ustaw `name` Właściwość na `Aligned` . Ta właściwość zapewnia, że dyski dla każdej maszyny wirtualnej są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. Należy również pamiętać, że `apiVersion` dla zasobu zestawu dostępności jest ustawiona wartość `2018-10-01` .

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>SSD w warstwie Standardowa dyski

Poniżej znajdują się parametry, które są konieczne w szablonie Menedżer zasobów do tworzenia dysków SSD w warstwie Standardowa:

* *apiVersion* dla Microsoft. COMPUTE musi być ustawiona jako `2018-04-01` (lub nowsza)
* Określ *managedDisk. storageAccountType* jako `StandardSSD_LRS`

W poniższym przykładzie przedstawiono sekcję *Properties. obszarze storageprofile. osDisk* dla maszyny wirtualnej korzystającej z dysków SSD w warstwie Standardowa:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Aby zapoznać się z kompletnym przykładem szablonu dotyczącego tworzenia dysku SSD w warstwie Standardowa z szablonem, zobacz [Tworzenie maszyny wirtualnej na podstawie obrazu systemu Windows z dyskami danych SSD w warstwie Standardowa](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Dodatkowe scenariusze i dostosowania

Aby uzyskać pełne informacje na temat specyfikacji interfejsu API REST, zapoznaj się z [dokumentacją interfejsu API Rest tworzenia dysku zarządzanego](/rest/api/manageddisks/disks/disks-create-or-update). Można znaleźć dodatkowe scenariusze, a także domyślne i akceptowalne wartości, które można przesłać do interfejsu API za pomocą wdrożeń szablonów. 

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pełnymi szablonami, które korzystają z usługi Managed disks, odwiedź następujące linki do szybkiego startu
    * [Maszyna wirtualna z systemem Windows z dyskiem zarządzanym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Maszyna wirtualna z systemem Linux z dyskiem zarządzanym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Aby dowiedzieć się więcej o dyskach zarządzanych, odwiedź stronę z [omówieniem usługi Azure Managed disks](managed-disks-overview.md) .
* Zapoznaj się z dokumentacją dotyczącą szablonu zasobów maszyny wirtualnej, odwiedzając dokument [referencyjny szablonu Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) .
* Zapoznaj się z dokumentacją dotyczącą szablonu zasobów dyskowych, odwiedzając dokument [referencyjny szablonu Microsoft. COMPUTE/disks](/azure/templates/microsoft.compute/disks) .
* Aby uzyskać informacje na temat korzystania z dysków zarządzanych w usłudze Azure Virtual Machine Scale Sets, przejdź do dokumentu [Korzystanie z dysków danych z zestawami skalowania](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) .

