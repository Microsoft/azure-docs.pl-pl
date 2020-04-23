---
title: Tworzenie obrazu zarządzanego na platformie Azure
description: Utwórz zarządzany obraz uogólnionej maszyny Wirtualnej lub Wirtualnej Wersji 1 na platformie Azure. Obrazy mogą służyć do tworzenia wielu maszyn wirtualnych, które używają dysków zarządzanych.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 258bddec85e4ab182ff0b07c49cdc93f92264f95
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084468"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Tworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure

Zasób obrazu zarządzanego można utworzyć na podstawie uogólnionej maszyny wirtualnej, która jest przechowywana jako dysk zarządzany albo dysk niezarządzany na koncie magazynu. Obraz może następnie służyć do tworzenia wielu maszyn wirtualnych. Aby uzyskać informacje na temat sposobu rozliczania obrazów [zarządzanych, zobacz Ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Uogólnianie maszyny wirtualnej z systemem Windows za pomocą narzędzia Sysprep

Program Sysprep usuwa wszystkie informacje osobiste i informacje zabezpieczające, a następnie przygotowuje urządzenie do użycia jako obraz. Aby uzyskać informacje o sysprep, zobacz [Sysprep przegląd](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) i [scenariuszy nieobsługiwałych](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Po uruchomieniu sysprep na maszynie wirtualnej, że maszyna wirtualna jest uważany *uogólniony* i nie można ponownie uruchomić. Proces uogólniania maszyny wirtualnej jest nieodwracalny. Jeśli chcesz zachować działanie oryginalnej maszyny Wirtualnej, należy utworzyć [kopię maszyny Wirtualnej](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) i uogólnić jej kopię. 
>
> Jeśli planujesz uruchomić narzędzie Sysprep przed przesłaniem wirtualnego dysku twardego (VHD) na platformę Azure po raz pierwszy, upewnij się, że [maszyna wirtualna została przygotowana.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  
> 
> 

Aby uogólnić maszynę wirtualną systemu Windows, wykonaj następujące czynności:

1. Zaloguj się do maszyny Wirtualnej systemu Windows.
   
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na %windir%\system32\sysprep, a `sysprep.exe`następnie uruchom .
   
3. W oknie **dialogowym Narzędzie przygotowania systemu** wybierz pozycję **Enter System Out-of-Box Experience (OOBE)** i zaznacz pole wyboru **Generalize** .
   
4. W obszarze **Opcje zamykania**wybierz **pozycję Shutdown**.
   
5. Kliknij przycisk **OK**.
   
    ![Uruchamianie sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Po zakończeniu sysprep zamyka maszynę wirtualną. Nie uruchamiaj ponownie maszyny wirtualnej.

> [!TIP]
> **Opcjonalnie** Użyj [dism,](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) aby zoptymalizować obraz i zmniejszyć swój pierwszy czas rozruchu maszyny Wirtualnej.
>
> Aby zoptymalizować obraz, zamontuj dysk VHD, klikając go dwukrotnie w Eksploratorze Windows, a następnie uruchom program DISM z parametrem. `/optimize-image`
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Gdzie D: jest zamontowaną ścieżką VHD.
>
> Bieganie `DISM /optimize-image` powinno być ostatnią modyfikacją wykonywaną w dysku VHD. Jeśli przed wdrożeniem wniesiesz jakiekolwiek zmiany w `DISM /optimize-image` dysku VHD, musisz uruchomić go ponownie.

## <a name="create-a-managed-image-in-the-portal"></a>Tworzenie obrazu zarządzanego w portalu 

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać obrazem maszyny Wirtualnej. Wyszukaj i wybierz **maszyny wirtualne**.

2. Wybierz maszynę wirtualną z listy.

3. Na stronie **Maszyny wirtualnej** maszyny wirtualnej w górnym menu wybierz polecenie **Przechwyć**.

   Zostanie wyświetlona strona **Utwórz obraz.**

4. W polu **Nazwa**zaakceptuj wstępnie wypełnioną nazwę lub wprowadź nazwę, której chcesz użyć dla obrazu.

5. W przypadku **grupy zasobów**wybierz pozycję **Utwórz nowy** i wprowadź nazwę lub wybierz grupę zasobów, która ma być używana z listy rozwijanej.

6. Jeśli chcesz usunąć źródłową maszynę wirtualną po utworzeniu obrazu, wybierz opcję **Automatycznie usuń tę maszynę wirtualną po utworzeniu obrazu**.

7. Jeśli chcesz mieć możliwość używania obrazu w dowolnej [strefie dostępności,](../../availability-zones/az-overview.md)wybierz opcję **Włącz** dla **odporności strefy**.

8. Wybierz **pozycję Utwórz,** aby utworzyć obraz.

Po utworzeniu obrazu można go znaleźć jako zasób **Obrazu** na liście zasobów w grupie zasobów.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Tworzenie obrazu maszyny wirtualnej przy użyciu programu Powershell

 

Tworzenie obrazu bezpośrednio z maszyny Wirtualnej zapewnia, że obraz zawiera wszystkie dyski skojarzone z maszyną wirtualną, w tym dysk systemu operacyjnego i dyski z danymi. W tym przykładzie pokazano, jak utworzyć obraz zarządzany z maszyny Wirtualnej, która używa dysków zarządzanych.

Przed rozpoczęciem upewnij się, że masz najnowszą wersję modułu programu Azure PowerShell. Aby znaleźć wersję, `Get-Module -ListAvailable Az` uruchom w programie PowerShell. Jeśli chcesz uaktualnić, zobacz [Instalowanie programu Azure PowerShell w systemie Windows za pomocą programu PowerShellGet](/powershell/azure/install-az-ps). Jeśli korzystasz z programu PowerShell `Connect-AzAccount` lokalnie, uruchom, aby utworzyć połączenie z platformą Azure.


> [!NOTE]
> Jeśli chcesz przechowywać obraz w magazynie strefowo nadmiarowym, musisz utworzyć go w regionie, `-ZoneResilient` który obsługuje strefy`New-AzImageConfig` [dostępności](../../availability-zones/az-overview.md) i uwzględnia parametr w konfiguracji obrazu ( polecenie).

Aby utworzyć obraz maszyny Wirtualnej, wykonaj następujące kroki:

1. Utwórz kilka zmiennych.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Upewnij się, że maszyna wirtualna została cofnięto alokacji.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ustaw stan maszyny wirtualnej na **Uogólniony**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Pobierz maszynę wirtualną. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Utwórz obraz.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Tworzenie obrazu z dysku zarządzanego przy użyciu programu PowerShell

Jeśli chcesz utworzyć obraz tylko dysku systemu operacyjnego, określ identyfikator dysku zarządzanego jako dysk systemu operacyjnego:

    
1. Utwórz kilka zmiennych. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Pobierz maszynę wirtualną.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Pobierz identyfikator dysku zarządzanego.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Utwórz obraz.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Tworzenie obrazu z migawki przy użyciu programu Powershell

Obraz zarządzany można utworzyć na podstawie migawki uogólnionej maszyny Wirtualnej, wykonując następujące kroki:

    
1. Utwórz kilka zmiennych. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Pobierz migawkę.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Utwórz obraz.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Tworzenie obrazu z maszyny Wirtualnej korzystającej z konta magazynu

Aby utworzyć obraz zarządzany z maszyny Wirtualnej, która nie korzysta z dysków zarządzanych, potrzebny jest identyfikator URI dysku VHD systemu operacyjnego na koncie magazynu w następującym formacie: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. W tym przykładzie VHD znajduje się na *mystorageaccount*, w kontenerze o nazwie *vhdcontainer*, a nazwa pliku VHD to *vhdfilename.vhd*.


1.  Utwórz kilka zmiennych.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Zatrzymaj/przydziel alokację maszyny Wirtualnej.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Oznacz maszynę wirtualną jako uogólnioną.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Utwórz obraz przy użyciu uogólnionego dysku VHD systemu operacyjnego.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Następne kroki
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

