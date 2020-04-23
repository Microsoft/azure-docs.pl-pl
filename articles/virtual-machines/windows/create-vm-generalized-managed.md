---
title: Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego na platformie Azure
description: Utwórz maszynę wirtualną systemu Windows na podstawie uogólnionego obrazu zarządzanego przy użyciu programu Azure PowerShell lub portalu.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: b0c6689b66037067a4c5174738945b7c6fabd5b5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086321"
---
# <a name="create-a-vm-from-a-managed-image"></a>Tworzenie maszyny wirtualnej na podstawie zarządzanego obrazu

Można utworzyć wiele maszyn wirtualnych (VM) z obrazu maszyny wirtualnej zarządzanej platformy Azure przy użyciu witryny Azure portal lub programu PowerShell. Obraz zarządzanej maszyny Wirtualnej zawiera informacje niezbędne do utworzenia maszyny Wirtualnej, w tym dyski z systemu operacyjnego i danych. Wirtualne dyski twarde (VHD), które tworzą obraz, w tym dyski systemu operacyjnego i dyski z danymi, są przechowywane jako dyski zarządzane. 

Przed utworzeniem nowej maszyny Wirtualnej należy [utworzyć obraz zarządzanej maszyny Wirtualnej,](capture-image-resource.md) aby użyć go jako obrazu źródłowego i udzielić dostępu do odczytu na obrazie każdemu użytkownikowi, który powinien mieć dostęp do obrazu. 


## <a name="use-the-portal"></a>Używanie portalu

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby znaleźć obraz zarządzany. Wyszukaj i wybierz pozycję **Obrazy**.
3. Wybierz obraz, którego chcesz użyć, z listy. Zostanie otwarta strona **Przegląd** obrazu.
4. Z menu **wybierz polecenie Utwórz maszynę wirtualną.**
5. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu będą używane do logowania się do maszyny wirtualnej. Po zakończeniu wybierz przycisk **OK**. Można utworzyć nową maszynę wirtualną w istniejącej grupie zasobów lub wybrać **pozycję Utwórz nową,** aby utworzyć nową grupę zasobów do przechowywania maszyny wirtualnej.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. 
7. W obszarze **Ustawienia**w razie potrzeby wprowadzać zmiany i **wybierać przycisk OK**. 
8. Na stronie podsumowania powinna zostać wyświetlona nazwa obrazu na liście **jako obraz prywatny**. Wybierz **przycisk Ok,** aby rozpocząć wdrażanie maszyny wirtualnej.


## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Za pomocą programu PowerShell można utworzyć maszynę wirtualną na podstawie obrazu przy użyciu uproszczonego zestawu parametrów dla polecenia cmdlet [New-AzVm.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Obraz musi znajdować się w tej samej grupie zasobów, w której utworzysz maszynę wirtualną.

 

Uproszczony zestaw parametrów dla [new-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) wymaga tylko podania nazwy, grupy zasobów i nazwy obrazu w celu utworzenia maszyny Wirtualnej z obrazu. New-AzVm użyje wartości **parametru -Name** jako nazwy wszystkich zasobów, które tworzy automatycznie. W tym przykładzie udostępniamy bardziej szczegółowe nazwy dla każdego z zasobów, ale pozwól, aby polecenie cmdlet tworzył je automatycznie. Można również utworzyć zasoby wcześniej, takie jak sieć wirtualna, i przekazać nazwę zasobu do polecenia cmdlet. New-AzVm użyje istniejących zasobów, jeśli można je znaleźć według ich nazwy.

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVMFromImage*, w grupie zasobów *myResourceGroup,* z obrazu o nazwie *myImage*. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Następne kroki
[Tworzenie maszyn wirtualnych systemu Windows i zarządzanie nimi za pomocą modułu programu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

