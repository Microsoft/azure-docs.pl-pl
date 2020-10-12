---
title: Tworzenie maszyny wirtualnej na podstawie przekazanego uogólnionego wirtualnego dysku twardego
description: Przekaż uogólniony wirtualny dysk twardy do platformy Azure i użyj go do utworzenia nowych maszyn wirtualnych w modelu wdrażania Menedżer zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 9d5add1349650663b226023b9f46868f242ac4f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87265215"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Przekazywanie uogólnionego dysku VHD i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure

W tym artykule przedstawiono sposób użycia programu PowerShell do przekazywania dysku VHD uogólnionej maszyny wirtualnej do platformy Azure, tworzenia obrazu z dysku VHD i tworzenia nowej maszyny wirtualnej na podstawie tego obrazu. Można przekazać wirtualny dysk twardy wyeksportowany z lokalnego narzędzia do wirtualizacji lub z innej chmury. Używanie [Managed disks](managed-disks-overview.md) dla nowej maszyny wirtualnej upraszcza zarządzanie maszyną wirtualną i zapewnia lepszą dostępność, gdy maszyna wirtualna jest umieszczona w zestawie dostępności. 

Przykładowy skrypt zawiera [przykładowy skrypt służący do przekazywania wirtualnego dysku twardego do platformy Azure i tworzenia nowej maszyny wirtualnej](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- Przed przekazaniem dysku VHD na platformę Azure należy wykonać następujące czynności [Przygotuj plik VHD lub VHDX systemu Windows w celu przekazania go do platformy Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Przejrzyj [plan migracji do Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) przed rozpoczęciem migracji, aby [Managed disks](managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Uogólnij źródłową maszynę wirtualną przy użyciu programu Sysprep

Jeśli jeszcze tego nie zrobiono, przed przekazaniem wirtualnego dysku twardego do platformy Azure należy wykonać polecenie Sysprep na maszynie wirtualnej. Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać szczegółowe informacje o programie Sysprep, zobacz [Omówienie programu Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Jeśli planujesz uruchomienie programu Sysprep przed przekazaniem wirtualnego dysku twardego do platformy Azure po raz pierwszy, upewnij się, że [maszyna wirtualna została przygotowana](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na%windir%\System32\Sysprep, a następnie uruchom polecenie `sysprep.exe` .
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz opcję **Wprowadź system out-of-Box Experience (OOBE)** i upewnij się, że pole wyboru **generalize** jest włączone.
4. W obszarze **Opcje zamykania**wybierz pozycję **Zamknij**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po zakończeniu działania narzędzia Sysprep zamyka ono maszynę wirtualną. Nie uruchamiaj ponownie maszyny wirtualnej.


## <a name="upload-the-vhd"></a>Przekazywanie wirtualnego dysku twardego 

Możesz teraz przekazać dysk VHD bezpośrednio do dysku zarządzanego. Aby uzyskać instrukcje, zobacz [przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



Po przekazaniu dysku VHD na dysk zarządzany należy użyć polecenie [Get-AzDisk](/powershell/module/az.compute/get-azdisk) , aby pobrać dysk zarządzany.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Tworzenie obrazu
Utwórz obraz zarządzany na podstawie uogólnionego dysku zarządzanego systemu operacyjnego. Zastąp następujące wartości własnymi informacjami.

Najpierw ustaw pewne zmienne:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Utwórz obraz przy użyciu dysku zarządzanego.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Utwórz obraz.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Teraz, gdy masz już obraz, możesz za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych. Ten przykład tworzy maszynę wirtualną o nazwie *myVM* z *obrazu*w liście *zasobów*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Następne kroki

Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
