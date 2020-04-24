---
title: Samouczek — Tworzenie niestandardowych obrazów maszyn wirtualnych przy użyciu Azure PowerShell
description: Z tego samouczka dowiesz się, jak utworzyć niestandardowy obraz maszyny wirtualnej na platformie Azure za pomocą programu Azure PowerShell
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 108ff8d89771217ed2833f2a47aa52ff05aa2f13
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100383"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Samouczek: tworzenie niestandardowego obrazu maszyny wirtualnej na platformie Azure za pomocą programu Azure PowerShell

Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą być używane do uruchamiania wdrożeń i zapewniania spójności na wielu maszynach wirtualnych. W tym samouczku utworzysz własny niestandardowy obraz maszyny wirtualnej platformy Azure przy użyciu programu PowerShell. Omawiane kwestie:

> [!div class="checklist"]
> * Używanie narzędzia Sysprep i uogólnianie maszyn wirtualnych
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu

W publicznej wersji zapoznawczej mamy usługę [Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) . Wystarczy opisać dostosowania w szablonie i obsłużyć kroki tworzenia obrazu w tym artykule. [Wypróbuj usługę Azure Image Builder (wersja zapoznawcza)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można utworzyć nowe wystąpienia maszyn wirtualnych.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby ten [przykładowy skrypt](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) może go utworzyć. Podczas pracy z tym samouczkiem zamień w odpowiednich przypadkach nazwy maszyn wirtualnych i grup zasobów.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="prepare-vm"></a>Przygotowywanie maszyny wirtualnej

Aby utworzyć obraz maszyny wirtualnej, należy przygotować źródłową maszynę wirtualną przez uogólnienie jej, cofnięcie przydziału, a następnie oznaczenie jej jako uogólnionej na platformie Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Uogólnianie maszyny wirtualnej z systemem Windows za pomocą narzędzia Sysprep

Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).


1. Nawiąż połączenie z maszyną wirtualną.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na *%windir%\system32\sysprep*, a następnie uruchom plik `sysprep.exe`.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.
4. W obszarze **Opcje zamykania** wybierz pozycję **Zamknij**, a następnie kliknij przycisk **OK**.
5. Gdy narzędzie Sysprep zakończy działanie, maszyna wirtualna zostanie wyłączona. **Nie uruchamiaj ponownie maszyny wirtualnej**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Cofanie przydziału i oznaczanie maszyny wirtualnej jako uogólnionej

Aby utworzyć obraz, należy cofnąć przydział maszyny wirtualnej i oznaczyć ją jako uogólnioną na platformie Azure.

Cofnij przydział maszyny wirtualnej za pomocą polecenia [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Ustaw stan maszyny wirtualnej na `-Generalized` za pomocą polecenia [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Tworzenie obrazu

Teraz możesz utworzyć obraz maszyny wirtualnej za pomocą poleceń [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) i [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). W poniższym przykładzie utworzono obraz o nazwie *myImage* za pomocą maszyny wirtualnej o nazwie *myVM*.

Pobierz maszynę wirtualną. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Utwórz konfigurację obrazu.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Utwórz obraz.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Tworzenie maszyn wirtualnych z obrazu

Teraz, gdy masz już obraz, możesz za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych. Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego jest podobne do tworzenia maszyny wirtualnej przy użyciu obrazu z witryny Marketplace. W przypadku obrazu z witryny Marketplace należy podać informacje dotyczące obrazu, dostawcy obrazu, oferty, jednostki SKU i wersji. W przypadku uproszczonego zestawu parametrów dla polecenia cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) wystarczy podać nazwę obrazu niestandardowego, o ile znajduje się on w tej samej grupie zasobów. Jeśli planujesz utworzyć maszynę wirtualną w innej grupie zasobów, podaj identyfikator zasobu obrazu dla parametru-ImageName.

W tym przykładzie jest tworzona maszyna wirtualna o nazwie *myVMfromImage* za pomocą obrazu o nazwie *myImage* w grupie zasobów *myResourceGroup*.


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

Zalecamy ograniczenie liczby współbieżnych wdrożeń do 20 maszyn wirtualnych z jednego obrazu. Jeśli planujesz współbieżne wdrożenia z ponad 20 maszyn wirtualnych z tego samego obrazu niestandardowego, użyj [udostępnionej galerii obrazów](shared-image-galleries.md) z wieloma replikami obrazu. 


## <a name="image-management"></a>Zarządzanie obrazami 

Oto kilka przykładów typowych zadań zarządzania obrazami i sposoby ich wykonania za pomocą programu PowerShell.

Wyświetl listę wszystkich obrazów według nazwy.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Usuwanie obrazu. W tym przykładzie obraz o nazwie *myImage* jest usuwany z grupy *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Używanie narzędzia Sysprep i uogólnianie maszyn wirtualnych
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć maszyny wirtualne o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Tworzenie maszyn wirtualnych o wysokiej dostępności](tutorial-availability-sets.md)



