---
title: Jak mapować dyski platformy Azure na dyski gościa maszyny wirtualnej z systemem Linux
description: Jak określić dyski platformy Azure, które underlay dyski gościa maszyny wirtualnej z systemem Linux.
author: timbasham
ms.service: virtual-machines-linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 4f0e48bf1c14728c54d4e89f30700017b0420d7d
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523618"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Jak mapować dyski platformy Azure na dyski gościa maszyny wirtualnej z systemem Linux

Może być konieczne określenie dysków platformy Azure, których kopie zapasowe są dyskami gościa maszyny wirtualnej. W niektórych scenariuszach można porównać rozmiar dysku lub woluminu na rozmiar dołączonych dysków platformy Azure. W scenariuszach, w których istnieje wiele dysków platformy Azure o tym samym rozmiarze dołączonym do maszyny wirtualnej, należy użyć numeru jednostki logicznej (LUN) dysków danych. 

## <a name="what-is-a-lun"></a>Co to jest jednostka LUN?

Numer jednostki logicznej (LUN) to numer używany do identyfikowania konkretnego urządzenia magazynującego. Każdemu urządzeniu magazynującemu jest przypisany unikatowy identyfikator liczbowy, zaczynający się od zera. Pełna ścieżka do urządzenia jest reprezentowana przez numer magistrali, numer identyfikatora docelowego i numer jednostki logicznej (LUN). 

Na przykład: ***numer magistrali 0, identyfikator docelowy 0, numer LUN 3** _

W naszym ćwiczeniu wystarczy użyć jednostki LUN.

## <a name="finding-the-lun"></a>Znajdowanie jednostki LUN

Poniżej wymieniono dwie metody znajdowania jednostki LUN dysku w systemie Linux.

### <a name="lsscsi"></a>lsscsi

1. Łączenie z maszyną wirtualną
1. `sudo lsscsi`

Pierwsza z wymienionych kolumn będzie zawierać numer LUN, format: [Host. Channel: target: _ * LUN * *].

### <a name="listing-block-devices"></a>Wyświetlanie listy urządzeń blokowych

1. Łączenie z maszyną wirtualną
1. `sudo ls -l /sys/block/*/device`

Ostatnia znajdująca się na liście kolumna będzie zawierać numer LUN. format to [Host: Channel: target:**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>Znajdowanie jednostki LUN dla dysków platformy Azure

Jednostkę LUN dla dysku platformy Azure można zlokalizować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Znajdowanie jednostki LUN dysku platformy Azure w Azure Portal

1. W Azure Portal wybierz pozycję "Virtual Machines", aby wyświetlić listę Virtual Machines
1. Wybierz maszynę wirtualną
1. Wybierz pozycję "dyski"
1. Wybierz dysk z danymi z listy dołączonych dysków.
1. Jednostka LUN dysku zostanie wyświetlona w okienku szczegółów dysku. Wyświetlana w tym miejscu jednostka LUN jest skorelowana z jednostkami LUN, które zostały wyszukane w gościu przy użyciu usługi lsscsi, lub z listą zablokowanych urządzeń.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Znajdowanie jednostki LUN dysku platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
