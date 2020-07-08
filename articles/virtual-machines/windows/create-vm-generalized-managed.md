---
title: Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego na platformie Azure
description: Utwórz maszynę wirtualną z systemem Windows na podstawie uogólnionego obrazu zarządzanego przy użyciu Azure PowerShell lub portalu.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: a028d597c3eb2a1c66df0e40266c2822e5cd7aab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83726965"
---
# <a name="create-a-vm-from-a-managed-image"></a>Tworzenie maszyny wirtualnej na podstawie zarządzanego obrazu

Można utworzyć wiele maszyn wirtualnych z obrazu maszyny wirtualnej zarządzanej przez platformę Azure przy użyciu Azure Portal lub programu PowerShell. Zarządzany obraz maszyny wirtualnej zawiera informacje niezbędne do utworzenia maszyny wirtualnej, w tym dysków systemu operacyjnego i danych. Wirtualne dyski twarde (VHD) tworzące obraz, w tym dyski systemu operacyjnego i dyski danych, są przechowywane jako dyski zarządzane. 

Przed utworzeniem nowej maszyny wirtualnej należy [utworzyć zarządzany obraz maszyny wirtualnej](capture-image-resource.md) , który będzie używany jako obraz źródłowy, i przyznać dostęp do odczytu do obrazu każdemu użytkownikowi, który powinien mieć dostęp do obrazu. 

Jeden zarządzany obraz obsługuje maksymalnie 20 równoczesnych wdrożeń. Próba utworzenia więcej niż 20 maszyn wirtualnych współbieżnie z tego samego obrazu zarządzanego może spowodować przekroczenie limitu czasu oczekiwania z ograniczeniami wydajności magazynu pojedynczego wirtualnego dysku twardego. Aby jednocześnie utworzyć więcej niż 20 maszyn wirtualnych, Użyj obrazu [udostępnionego galerii obrazów](shared-image-galleries.md) skonfigurowanego z 1 repliką dla każdego 20 współbieżnych wdrożeń maszyn wirtualnych.

## <a name="use-the-portal"></a>Używanie portalu

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć obraz zarządzany. Wyszukaj i wybierz pozycję **images**.
3. Wybierz z listy obraz, którego chcesz użyć. Zostanie otwarta strona **Przegląd** obrazu.
4. Wybierz pozycję **Utwórz maszynę wirtualną** z menu.
5. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu zostaną użyte do zalogowania się do maszyny wirtualnej. Po zakończeniu wybierz **przycisk OK**. Możesz utworzyć nową maszynę wirtualną w istniejącej grupie zasobów lub wybrać opcję **Utwórz nową** , aby utworzyć nową grupę zasobów do przechowywania maszyny wirtualnej.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. 
7. W obszarze **Ustawienia**wprowadź zmiany w razie potrzeby, a następnie wybierz **przycisk OK**. 
8. Na stronie Podsumowanie powinna zostać wyświetlona nazwa obrazu wyświetlana jako **obraz prywatny**. Wybierz **przycisk OK** , aby rozpocząć wdrażanie maszyny wirtualnej.


## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Za pomocą programu PowerShell można utworzyć maszynę wirtualną na podstawie obrazu przy użyciu uproszczonego parametru ustawianego dla polecenia cmdlet [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . Obraz musi znajdować się w tej samej grupie zasobów, w której zostanie utworzona maszyna wirtualna.

 

Uproszczony zestaw parametrów dla [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) wymaga podania nazwy, grupy zasobów i nazwy obrazu w celu utworzenia maszyny wirtualnej na podstawie obrazu. Funkcja New-AzVm będzie używać wartości parametru **-name** jako nazwy wszystkich zasobów, które tworzy automatycznie. W tym przykładzie udostępniamy bardziej szczegółowe nazwy dla każdego z zasobów, ale umożliwiają automatyczne tworzenie ich przez polecenie cmdlet. Możesz również utworzyć zasoby wcześniej, takie jak sieć wirtualna, i przekazać nazwę zasobu do polecenia cmdlet. Funkcja New-AzVm będzie używać istniejących zasobów, jeśli można je znaleźć według ich nazwy.

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVMFromImage*w grupie zasobów zasobu *webresources* z obrazu o nazwie Moja *obraz*. 


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
[Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

