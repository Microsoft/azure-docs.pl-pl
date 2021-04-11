---
title: Jak mapować dyski platformy Azure na dyski gościa maszyny wirtualnej z systemem Windows
description: Jak określić dyski platformy Azure, które underlay dyski gościa maszyny wirtualnej z systemem Windows.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560994"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Jak mapować dyski platformy Azure na dyski gościa maszyny wirtualnej z systemem Windows

Może być konieczne określenie dysków platformy Azure, których kopie zapasowe są dyskami gościa maszyny wirtualnej. W niektórych scenariuszach można porównać rozmiar dysku lub woluminu na rozmiar dołączonych dysków platformy Azure. W scenariuszach, w których istnieje wiele dysków platformy Azure o tym samym rozmiarze dołączonym do maszyny wirtualnej, należy użyć numeru jednostki logicznej (LUN) dysków danych. 

## <a name="what-is-a-lun"></a>Co to jest jednostka LUN?

Numer jednostki logicznej (LUN) to numer używany do identyfikowania konkretnego urządzenia magazynującego. Każdemu urządzeniu magazynującemu jest przypisany unikatowy identyfikator liczbowy, zaczynający się od zera. Pełna ścieżka do urządzenia jest reprezentowana przez numer magistrali, numer identyfikatora docelowego i numer jednostki logicznej (LUN). 

Na przykład: ***magistrala Number 0, identyfikator docelowy 0, LUN 3***

W naszym ćwiczeniu wystarczy użyć jednostki LUN.

## <a name="finding-the-lun"></a>Znajdowanie jednostki LUN

Istnieją dwie metody znajdowania jednostki LUN, która jest zależna od tego, czy jest używana funkcja [miejsca do magazynowania](/windows-server/storage/storage-spaces/overview) .

### <a name="disk-management"></a>Zarządzanie dyskami

Jeśli nie używasz pul magazynów, możesz użyć [przystawki Zarządzanie dyskami](/windows-server/storage/disk-management/overview-of-disk-management) , aby znaleźć jednostkę LUN.

1. Połącz się z maszyną wirtualną i Otwórz przystawkę Zarządzanie dyskami a. Kliknij prawym przyciskiem myszy przycisk Start i wybierz polecenie "Zarządzanie dyskami" a. Możesz również wpisać `diskmgmt.msc` w polu Rozpocznij wyszukiwanie
1. W dolnym okienku kliknij prawym przyciskiem myszy dowolny dysk i wybierz polecenie "właściwości".
1. Jednostka LUN zostanie wyświetlona na liście we właściwości "Location" na karcie "ogólne".

### <a name="storage-pools"></a>Pule magazynów

1. Nawiąż połączenie z maszyną wirtualną i Otwórz Menedżer serwera
1. Wybieranie "usługi plików i magazynowania", "woluminy", "Pule magazynów"
1. W prawym dolnym rogu Menedżer serwera będzie sekcja "dyski fizyczne". Dyski tworzące pulę magazynów są wyświetlane w tym miejscu oraz w jednostkach LUN dla każdego dysku.

## <a name="finding-the-lun-for-the-azure-disks"></a>Znajdowanie jednostki LUN dla dysków platformy Azure

Jednostkę LUN dla dysku platformy Azure można zlokalizować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Znajdowanie jednostki LUN dysku platformy Azure w Azure Portal

1. W Azure Portal wybierz pozycję "Virtual Machines", aby wyświetlić listę Virtual Machines
1. Wybierz maszynę wirtualną
1. Wybierz pozycję "dyski"
1. Wybierz dysk z danymi z listy dołączonych dysków.
1. Jednostka LUN dysku zostanie wyświetlona w okienku szczegółów dysku. Wyświetlana w tym miejscu jednostka LUN korelacji z jednostkami LUN, które zostały wyszukane w gościu przy użyciu Device Manager lub Menedżer serwera.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Znajdowanie jednostki LUN dysku platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---