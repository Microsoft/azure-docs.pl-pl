---
title: Tworzenie obrazów maszyn wirtualnych dla urządzenia z Azure Stack Edge
description: Opisuje sposób tworzenia obrazów maszyn wirtualnych z systemem Linux lub Windows, które mają być używane z urządzeniem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 41f94b8f500656f75831970baa4437607fe911f7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088394"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-device"></a>Tworzenie niestandardowych obrazów maszyn wirtualnych dla urządzenia z Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Aby wdrożyć maszyny wirtualne na urządzeniu z systemem Azure Stack Edge, musisz mieć możliwość tworzenia niestandardowych obrazów maszyn wirtualnych, których można użyć do tworzenia maszyn wirtualnych. W tym artykule opisano kroki wymagane do utworzenia niestandardowych obrazów maszyn wirtualnych z systemem Linux lub Windows, których można użyć do wdrożenia maszyn wirtualnych na urządzeniu Azure Stack Edge.

## <a name="vm-image-workflow"></a>Przepływ pracy obrazu maszyny wirtualnej

Przepływ pracy wymaga utworzenia maszyny wirtualnej na platformie Azure, dostosowania maszyny wirtualnej, uogólnienia, a następnie pobrania wirtualnego dysku twardego odpowiadającego danej maszynie wirtualnej. Ten uogólniony wirtualny dysk twardy jest przekazywany do Azure Stack Edge, dysk zarządzany jest tworzony na podstawie tego wirtualnego dysku twardego, a na końcu tworzone są maszyny wirtualne utworzone na podstawie tego obrazu.   

Aby uzyskać więcej informacji, przejdź do [wdrożenia maszyny wirtualnej na urządzeniu z systemem Azure Stack Edge przy użyciu Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej systemu Windows

Wykonaj następujące kroki, aby utworzyć obraz maszyny wirtualnej z systemem Windows.

1. Utwórz maszynę wirtualną z systemem Windows. Aby uzyskać więcej informacji, przejdź do [samouczka: Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Pobierz istniejący dysk systemu operacyjnego.

    - Wykonaj kroki opisane w sekcji [pobieranie dysku VHD](../virtual-machines/windows/download-vhd.md).

    - Użyj poniższego `sysprep` polecenia zamiast opisanego w poprzedniej procedurze.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Można także zapoznać się z [omówieniem narzędzia Sysprep (Przygotowanie systemu)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Użyj tego wirtualnego dysku twardego, aby teraz utworzyć i wdrożyć maszynę wirtualną na urządzeniu Azure Stack Edge.

## <a name="create-a-linux-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej z systemem Linux

Wykonaj następujące kroki, aby utworzyć obraz maszyny wirtualnej z systemem Linux.

1. Tworzenie maszyny wirtualnej z systemem Linux. Aby uzyskać więcej informacji, przejdź do [samouczka: Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/tutorial-manage-vm.md).

2. [Pobierz istniejący dysk systemu operacyjnego](../virtual-machines/linux/download-vhd.md).

Użyj tego wirtualnego dysku twardego, aby teraz utworzyć i wdrożyć maszynę wirtualną na urządzeniu Azure Stack Edge. Aby utworzyć niestandardowe obrazy systemu Linux, można użyć następujących dwóch obrazów witryny Azure Marketplace:

|Nazwa elementu  |Opis  |Publisher  |
|---------|---------|---------|
|[Serwer Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Serwer Ubuntu to najpopularniejsze w świecie środowisko Linux dla środowisk chmurowych.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux to jedna z najpopularniejszych dystrybucji systemu Linux.     |credativ|

Aby zapoznać się z pełną listą obrazów portalu Azure Marketplace, które mogą być używane (nie zostały przetestowane), przejdź do [pozycji Azure Marketplace dostępne dla Azure Stack centrum](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Następne kroki

[Wdróż maszyny wirtualne na urządzeniu z systemem Azure Stack Edge](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
