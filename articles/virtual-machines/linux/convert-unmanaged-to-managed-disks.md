---
title: Konwertowanie maszyny wirtualnej z systemem Linux z dysków niezarządzanych na dyski zarządzane
description: Jak skonwertować maszynę wirtualną z systemem Linux z dysków niezarządzanych do dysków zarządzanych przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 89035d26e05ca4b93dc271f02fef1afea24b0f20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88815338"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertowanie maszyny wirtualnej z systemem Linux z dysków niezarządzanych na dyski zarządzane

Jeśli masz istniejące maszyny wirtualne z systemem Linux, które korzystają z dysków niezarządzanych, możesz przekonwertować maszyny wirtualne tak, aby korzystały z [platformy Azure Managed disks](../managed-disks-overview.md). Ten proces powoduje przekonwertowanie dysku systemu operacyjnego i dołączonych dysków danych.

W tym artykule opisano sposób konwertowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Przejrzyj [często zadawane pytania dotyczące migracji do Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Oryginalne wirtualne dyski twarde i konto magazynu używane przez maszynę wirtualną przed konwersją nie są usuwane. Nadal będą za nie naliczane opłaty. Aby uniknąć naliczania opłat za te artefakty, usuń oryginalne obiekty blob dysków VHD po upewnieniu się, że konwersja została zakończona. Jeśli chcesz znaleźć niedołączone dyski, aby je usunąć, zobacz artykuł [Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych platformy Azure](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Konwertowanie maszyn wirtualnych z pojedynczym wystąpieniem
W tej sekcji opisano sposób konwersji maszyn wirtualnych platformy Azure z jednym wystąpieniem z dysków niezarządzanych na dyski zarządzane. (Jeśli maszyny wirtualne znajdują się w zestawie dostępności, zobacz następną sekcję). Tego procesu można użyć do konwersji maszyn wirtualnych z dysków niezarządzanych w warstwie Premium (SSD) na dyski zarządzane w warstwie Premium lub dysków niezarządzanych w warstwie Standardowa (dyski twarde) do dysków zarządzanych w warstwie Standardowa.

1. Cofnij przydział maszyny wirtualnej przy użyciu polecenia [AZ VM deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Przekonwertuj maszynę wirtualną na dyski zarządzane przy użyciu polecenia [AZ VM Convert](/cli/azure/vm). Poniższy proces konwertuje maszynę wirtualną o nazwie `myVM` , w tym dysk systemu operacyjnego i wszystkie dyski danych:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Uruchom maszynę wirtualną po konwersji na dyski zarządzane przy użyciu polecenia [AZ VM Start](/cli/azure/vm). Poniższy przykład uruchamia maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konwertowanie maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które mają zostać przekonwertowane na dyski zarządzane, znajdują się w zestawie dostępności, należy najpierw skonwertować zestaw dostępności na zarządzany zestaw dostępności.

Przed przekonwertowaniem zestawu dostępności wszystkie maszyny wirtualne w zestawie dostępności muszą zostać cofnięte. Zaplanuj konwersję wszystkich maszyn wirtualnych na dyski zarządzane po przekonwertowaniu samego zestawu dostępności na zarządzany zestaw dostępności. Następnie uruchom wszystkie maszyny wirtualne i Kontynuuj działanie jako normalne.

1. Wyświetl listę wszystkich maszyn wirtualnych w zestawie dostępności za pomocą polecenia [AZ VM Availability-Set list](/cli/azure/vm/availability-set). Poniższy przykład wyświetla listę wszystkich maszyn wirtualnych w zestawie dostępności o nazwie `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup` :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Cofnij przydział wszystkich maszyn wirtualnych przy użyciu polecenia [AZ VM deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Przekonwertuj zestaw dostępności za pomocą polecenia [AZ VM Availability-Set Convert](/cli/azure/vm/availability-set). Poniższy przykład konwertuje zestaw dostępności o nazwie `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup` :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Przekonwertuj wszystkie maszyny wirtualne na dyski zarządzane przy użyciu polecenia [AZ VM Convert](/cli/azure/vm). Poniższy proces konwertuje maszynę wirtualną o nazwie `myVM` , w tym dysk systemu operacyjnego i wszystkie dyski danych:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Uruchom wszystkie maszyny wirtualne po konwersji na dyski zarządzane przy użyciu polecenia [AZ VM Start](/cli/azure/vm). Poniższy przykład uruchamia maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Konwertuj przy użyciu Azure Portal

Możesz również skonwertować dyski niezarządzane na dyski zarządzane przy użyciu Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę wirtualną z listy maszyn wirtualnych w portalu.
3. W bloku maszyny wirtualnej wybierz pozycję **dyski** z menu.
4. W górnej części bloku **dyski** wybierz pozycję **Migruj do dysków zarządzanych**.
5. Jeśli maszyna wirtualna znajduje się w zestawie dostępności, pojawi się ostrzeżenie w bloku **Migrowanie do dysków zarządzanych** , aby najpierw skonwertować zestaw dostępności. Ostrzeżenie powinno mieć link, który można kliknąć, aby przekonwertować zestaw dostępności. Po przekonwertowaniu zestawu dostępności lub jeśli maszyna wirtualna nie znajduje się w zestawie dostępności, kliknij pozycję **Migruj** , aby rozpocząć proces migrowania dysków do dysków zarządzanych.

Maszyna wirtualna zostanie zatrzymana i uruchomiona ponownie po zakończeniu migracji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji magazynu, zobacz [Omówienie usługi Azure Managed disks](../managed-disks-overview.md).
