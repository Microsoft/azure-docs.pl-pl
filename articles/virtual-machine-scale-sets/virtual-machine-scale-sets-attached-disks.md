---
title: Virtual Machine Scale Sets dołączone dyski danych platformy Azure
description: Dowiedz się, jak używać dołączonych dysków danych z zestawami skalowania maszyn wirtualnych, korzystając z określonych przypadków użycia.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9e4bdf868d3f8ddf3a049509ead30a4b1ba341b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86527442"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Zestawy skalowania i dołączone dyski danych maszyn wirtualnych platformy Azure
Aby umożliwić rozbudowanie dostępnego magazynu, [zestawy skalowania maszyn wirtualnych](./index.yml) platformy Azure obsługują wystąpienia maszyn wirtualnych przy użyciu dołączonych dysków z danymi. Dyski z danymi można dołączać do istniejącego zestawu skalowania lub podczas tworzenia takiego zestawu.

> [!NOTE]
> Podczas tworzenia zestawu skalowania z dołączonymi dyskami z danymi konieczne jest zainstalowanie i sformatowanie dysków z poziomu maszyny wirtualnej w celu ich użycia (podobnie jak w przypadku autonomicznych maszyn wirtualnych platformy Azure). Wygodnym sposobem wykonania tych czynności jest użycie rozszerzenia niestandardowego skryptu, które wywołuje skrypt w celu podzielenia na partycje i sformatowania wszystkich dysków z danymi na maszynie wirtualnej. Aby zapoznać się z odpowiednimi przykładami, zobacz [Interfejs wiersza polecenia platformy Azure i ](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Tworzenie dysków i zarządzanie nimi w zestawie skalowania
Aby uzyskać szczegółowe informacje na temat sposobu tworzenia zestawu skalowania z dołączonymi dyskami z danymi, zobacz jeden z następujących samouczków:

- [Interfejs wiersza polecenia platformy Azure](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

W pozostałej części tego artykułu przedstawiono wybrane przypadki użycia, takie jak klastry usługi Service Fabric wymagające dysków z danymi i dołączanie istniejących dysków z danymi z zawartością do zestawu skalowania.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Tworzenie klastra usługi Service Fabric z dołączonymi dyskami danych
Każdy [typ węzła](../service-fabric/service-fabric-cluster-nodetypes.md) w klastrze usługi [Service Fabric](../service-fabric/index.yml) uruchomiony na platformie Azure jest obsługiwany przez zestaw skalowania maszyn wirtualnych. Za pomocą szablonu usługi Azure Resource Manager możesz dołączać dyski z danymi do zestawów skalowania tworzących klaster usługi Service Fabric. Jako punktu startowego możesz użyć [istniejącego szablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates). W szablonie dołącz sekcję _dataDisks_ w obszarze _storageProfile_ zasobów _Microsoft.Compute/virtualMachineScaleSets_, a następnie wdróż szablon. Poniższy przykład służy do dołączania dysku z danymi o rozmiarze 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Po wdrożeniu klastra możesz automatycznie tworzyć partycje na dyskach danych, a także formatować je i instalować. Dodaj rozszerzenie niestandardowego skryptu do sekcji _extensionProfile_ obszaru _virtualMachineProfile_ zestawów skalowania.

Aby automatycznie przygotować dyski danych w klastrze systemu Windows, dodaj następujący fragment kodu:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Aby automatycznie przygotować dyski danych w klastrze systemu Linux, dodaj następujący fragment kodu:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Dodawanie wstępnie wypełnionych dysków danych do istniejącego zestawu skalowania
Dyski danych określone w modelu zestawu skalowania zawsze są puste. Można jednak dołączyć istniejący dysk danych do określonej maszyny wirtualnej w zestawie skalowania. Jeśli chcesz propagować dane między wszystkimi maszynami wirtualnymi w zestawie skalowania, możesz zduplikować dysk danych i dołączyć go do każdej maszyny wirtualnej w zestawie skalowania lub utworzyć niestandardowy obraz zawierający dane i udostępnić zestaw skalowania na podstawie tego obrazu niestandardowego lub użyć Azure Files lub podobnej oferty magazynu danych.


## <a name="additional-notes"></a>Uwagi dodatkowe
Obsługa funkcji Dyski zarządzane platformy Azure i dołączonych dysków danych w zestawach skalowania jest dostępna w wersji [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) interfejsu API Microsoft.Compute i w nowszych wersjach.

Obsługa Azure Portal dołączonych dysków danych w zestawach skalowania jest ograniczona. W zależności od wymagań można zarządzać dyskami dołączonymi przy użyciu szablonów platformy Azure, interfejsu wiersza polecenia, programu PowerShell, zestawów SDK oraz interfejsu API REST.
