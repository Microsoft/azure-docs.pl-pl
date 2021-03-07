---
title: Tworzenie obrazów maszyn wirtualnych dla urządzenia GPU z Azure Stack Edge
description: Zawiera opis sposobu tworzenia obrazów maszyn wirtualnych z systemem Linux lub Windows, które mają być używane z urządzeniem GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ee0587063c0fac67068869c58471ada58354fab7
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437676"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Tworzenie niestandardowych obrazów maszyn wirtualnych dla urządzenia z Azure Stack EDGE Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Aby wdrażać maszyny wirtualne na urządzeniu Azure Stack EDGE Pro, musisz mieć możliwość tworzenia niestandardowych obrazów maszyn wirtualnych, których można użyć do tworzenia maszyn wirtualnych. W tym artykule opisano kroki wymagane do utworzenia niestandardowych obrazów maszyn wirtualnych z systemem Linux lub Windows, których można użyć do wdrożenia maszyn wirtualnych na urządzeniu Azure Stack Edge.

## <a name="vm-image-workflow"></a>Przepływ pracy obrazu maszyny wirtualnej

Przepływ pracy wymaga utworzenia maszyny wirtualnej na platformie Azure, dostosowania maszyny wirtualnej, uogólnienia, a następnie pobrania wirtualnego dysku twardego odpowiadającego danej maszynie wirtualnej. Ten uogólniony wirtualny dysk twardy jest przekazywany do Azure Stack EDGE Pro. Dysk zarządzany jest tworzony na podstawie tego wirtualnego dysku twardego. Obraz jest tworzony z dysku zarządzanego. Ponadto maszyny wirtualne są tworzone na podstawie tego obrazu.

Aby uzyskać więcej informacji, przejdź do [wdrożenia maszyny wirtualnej na urządzeniu z systemem Azure Stack brzeg Pro przy użyciu Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej systemu Windows

Wykonaj następujące kroki, aby utworzyć obraz maszyny wirtualnej z systemem Windows.

1. Utwórz maszynę wirtualną z systemem Windows. Aby uzyskać więcej informacji, przejdź do [samouczka: Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Pobierz istniejący dysk systemu operacyjnego.

    - Wykonaj kroki opisane w sekcji [pobieranie dysku VHD](../virtual-machines/windows/download-vhd.md).

    - Użyj poniższego `sysprep` polecenia zamiast opisanego w poprzedniej procedurze.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Można także zapoznać się z [omówieniem narzędzia Sysprep (Przygotowanie systemu)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Użyj tego wirtualnego dysku twardego, aby teraz utworzyć i wdrożyć maszynę wirtualną na urządzeniu Azure Stack brzeg Pro.

## <a name="create-a-linux-custom-vm-image"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej z systemem Linux

Wykonaj następujące kroki, aby utworzyć obraz maszyny wirtualnej z systemem Linux.

1. Tworzenie maszyny wirtualnej z systemem Linux. Aby uzyskać więcej informacji, przejdź do [samouczka: Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/tutorial-manage-vm.md).

1. Anuluj aprowizację maszyny wirtualnej. Użyj agenta maszyny wirtualnej platformy Azure, aby usunąć pliki i dane specyficzne dla maszyny. Użyj `waagent` polecenia z `-deprovision+user` parametrem na ŹRÓDŁOWEJ maszynie wirtualnej z systemem Linux. Aby uzyskać więcej informacji, zobacz temat [Omówienie i używanie agenta systemu Linux platformy Azure](../virtual-machines/extensions/agent-linux.md).

    1. Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
    2. W oknie SSH wprowadź następujące polecenie:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > To polecenie można uruchomić tylko na maszynie wirtualnej, która zostanie przechwycona jako obraz. To polecenie nie gwarantuje, że obraz jest czyszczony dla wszystkich poufnych informacji lub jest odpowiedni do ponownej dystrybucji. Ten `+user` parametr usuwa także ostatnio zainicjowane konto użytkownika. Aby zachować poświadczenia konta użytkownika na maszynie wirtualnej, użyj tylko `-deprovision` .
     
    3. Wprowadź **y** , aby kontynuować. Można dodać parametr, `-force` Aby uniknąć tego kroku potwierdzenia.
    4. Po zakończeniu wykonywania polecenia wpisz **Exit** , aby zamknąć klienta SSH.  Maszyna wirtualna będzie nadal działać w tym momencie.


1. [Pobierz istniejący dysk systemu operacyjnego](../virtual-machines/linux/download-vhd.md).

Użyj tego wirtualnego dysku twardego, aby teraz utworzyć i wdrożyć maszynę wirtualną na urządzeniu Azure Stack brzeg Pro. Aby utworzyć niestandardowe obrazy systemu Linux, można użyć następujących dwóch obrazów witryny Azure Marketplace:

|Nazwa elementu  |Opis  |Publisher  |
|---------|---------|---------|
|[Serwer Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Serwer Ubuntu to najpopularniejsze w świecie środowisko Linux dla środowisk chmurowych.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux to jedna z najpopularniejszych dystrybucji systemu Linux.     |credativ|

Aby zapoznać się z pełną listą obrazów portalu Azure Marketplace, które mogą być używane (nie zostały przetestowane), przejdź do [pozycji Azure Marketplace dostępne dla Azure Stack centrum](/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910&preserve-view=true).


## <a name="next-steps"></a>Następne kroki

[Wdróż maszyny wirtualne na urządzeniu z systemem Azure Stack brzeg Pro](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).