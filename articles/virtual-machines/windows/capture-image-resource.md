---
title: Tworzenie obrazu zarządzanego na platformie Azure
description: Utwórz zarządzany obraz uogólnionej maszyny wirtualnej lub wirtualnego dysku twardego na platformie Azure. Obrazy mogą służyć do tworzenia wielu maszyn wirtualnych korzystających z dysków zarządzanych.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 751fa9f9fe2ba17a982b71a6332be302804f0dcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89047296"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Tworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure

Zasób obrazu zarządzanego można utworzyć na podstawie uogólnionej maszyny wirtualnej, która jest przechowywana jako dysk zarządzany albo dysk niezarządzany na koncie magazynu. Obraz może następnie służyć do tworzenia wielu maszyn wirtualnych. Aby uzyskać informacje dotyczące sposobu rozliczania zarządzanych obrazów, zobacz [Managed disks Cennik](https://azure.microsoft.com/pricing/details/managed-disks/). 

Jeden zarządzany obraz obsługuje maksymalnie 20 równoczesnych wdrożeń. Próba utworzenia więcej niż 20 maszyn wirtualnych współbieżnie z tego samego obrazu zarządzanego może spowodować przekroczenie limitu czasu oczekiwania z ograniczeniami wydajności magazynu pojedynczego wirtualnego dysku twardego. Aby jednocześnie utworzyć więcej niż 20 maszyn wirtualnych, Użyj obrazu [udostępnionego galerii obrazów](shared-image-galleries.md) skonfigurowanego z 1 repliką dla każdego 20 współbieżnych wdrożeń maszyn wirtualnych.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Uogólnianie maszyny wirtualnej z systemem Windows za pomocą narzędzia Sysprep

Program Sysprep usuwa wszystkie informacje o koncie osobistym i zabezpieczeniach, a następnie przygotuje maszynę do użycia jako obraz. Aby uzyskać informacje o programie Sysprep, zobacz [Omówienie narzędzia Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) i [nieobsługiwanych scenariuszy](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Po uruchomieniu programu Sysprep na maszynie wirtualnej maszyna wirtualna jest traktowana jako *uogólniona* i nie można jej uruchomić ponownie. Proces uogólniania maszyny wirtualnej jest nieodwracalny. Jeśli musisz zachować oryginalną maszynę wirtualną, Utwórz [kopię maszyny wirtualnej](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) i Uogólnij jej kopię. 
>
>Program Sysprep wymaga całkowicie odszyfrowania dysków. Jeśli na maszynie wirtualnej włączono szyfrowanie, przed uruchomieniem programu Sysprep należy wyłączyć szyfrowanie.
>
> Jeśli planujesz uruchomienie programu Sysprep przed przekazaniem wirtualnego dysku twardego (VHD) do platformy Azure po raz pierwszy, upewnij się, że [maszyna wirtualna została przygotowana](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Aby uogólnić maszynę wirtualną z systemem Windows, wykonaj następujące kroki:

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
   
2. Otwórz okno wiersza polecenia jako administrator. 

3. Usuń katalog Panther (C:\Windows\Panther). Następnie zmień katalog na%windir%\System32\Sysprep, a następnie uruchom polecenie `sysprep.exe` .
   
4. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz opcję **Wprowadź system out-of-Box Experience (OOBE)** i zaznacz pole wyboru **generalize** .
   
5. W obszarze **Opcje zamykania**wybierz pozycję **Zamknij**.
   
6. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Po zakończeniu działania narzędzia Sysprep zamyka ono maszynę wirtualną. Nie uruchamiaj ponownie maszyny wirtualnej.

> [!TIP]
> **Opcjonalne** Użyj [narzędzia DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) , aby zoptymalizować obraz i skrócić czas pierwszego rozruchu maszyny wirtualnej.
>
> Aby zoptymalizować obraz, zainstaluj dysk VHD przez dwukrotne kliknięcie go w Eksploratorze Windows, a następnie uruchom narzędzie DISM z `/optimize-image` parametrem.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Gdzie D: jest ścieżką zainstalowanego wirtualnego dysku twardego.
>
> Uruchomiona `DISM /optimize-image` powinna być ostatnią modyfikacją dysku VHD. Jeśli wprowadzisz jakiekolwiek zmiany na wirtualnym dysku twardym przed wdrożeniem, musisz uruchomić polecenie `DISM /optimize-image` ponownie.

## <a name="create-a-managed-image-in-the-portal"></a>Tworzenie obrazu zarządzanego w portalu 

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać obrazem maszyny wirtualnej. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Z listy wybierz maszynę wirtualną.

3. Na stronie **maszyna wirtualna** dla maszyny wirtualnej w górnym menu wybierz pozycję **Przechwyć**.

   Zostanie wyświetlona strona **Tworzenie obrazu** .

4. W polu **Nazwa**Zaakceptuj wstępnie wypełnioną nazwę lub wprowadź nazwę, której chcesz użyć dla obrazu.

5. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** i wprowadź nazwę lub wybierz grupę zasobów do użycia z listy rozwijanej.

6. Jeśli chcesz usunąć źródłową maszynę wirtualną po utworzeniu obrazu, zaznacz opcję **automatycznie Usuń tę maszynę wirtualną po utworzeniu obrazu**.

7. Jeśli chcesz, aby program używał obrazu w dowolnej [strefie dostępności](../../availability-zones/az-overview.md), wybierz pozycję **Włącz** **odporność strefy**.

8. Wybierz pozycję **Utwórz**, aby utworzyć obraz.

Po utworzeniu obrazu można go znaleźć jako zasób **obrazu** na liście zasobów w grupie zasobów.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Tworzenie obrazu maszyny wirtualnej przy użyciu programu PowerShell

 

Utworzenie obrazu bezpośrednio z maszyny wirtualnej zapewnia, że obraz zawiera wszystkie dyski skojarzone z maszyną wirtualną, w tym dysk systemu operacyjnego i wszystkie dyski z danymi. Ten przykład pokazuje, jak utworzyć obraz zarządzany na podstawie maszyny wirtualnej korzystającej z dysków zarządzanych.

Przed rozpoczęciem upewnij się, że masz najnowszą wersję modułu Azure PowerShell. Aby znaleźć wersję, uruchom polecenie `Get-Module -ListAvailable Az` w programie PowerShell. Jeśli musisz przeprowadzić uaktualnienie, zobacz [instalowanie Azure PowerShell w systemie Windows za pomocą PowerShellGet](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure.


> [!NOTE]
> Jeśli chcesz przechowywać obraz w magazynie strefowo nadmiarowym, musisz go utworzyć w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i dołączyć `-ZoneResilient` parametr w konfiguracji obrazu ( `New-AzImageConfig` polecenie).

Aby utworzyć obraz maszyny wirtualnej, wykonaj następujące kroki:

1. Utwórz pewne zmienne.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Upewnij się, że maszyna wirtualna została cofnięta.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ustaw stan maszyny wirtualnej na **uogólnione**. 
   
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

    
1. Utwórz pewne zmienne. 

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Tworzenie obrazu na podstawie migawki przy użyciu programu PowerShell

Można utworzyć obraz zarządzany na podstawie migawki uogólnionej maszyny wirtualnej, wykonując następujące czynności:

    
1. Utwórz pewne zmienne. 

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


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Tworzenie obrazu na podstawie maszyny wirtualnej korzystającej z konta magazynu

Aby utworzyć obraz zarządzany na podstawie maszyny wirtualnej, która nie korzysta z usługi Managed disks, musisz mieć identyfikator URI wirtualnego dysku twardego systemu operacyjnego na koncie magazynu w następującym formacie: https://*mojekontomagazynu*. blob.Core.Windows.NET/*vhdcontainer* / *vhdfilename. VHD*. W tym przykładzie wirtualny dysk twardy znajduje się w *mojekontomagazynu*, w kontenerze o nazwie *vhdcontainer*, a nazwa pliku VHD to *vhdfilename. VHD*.


1.  Utwórz pewne zmienne.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Zatrzymaj/Cofnij przydział maszyny wirtualnej.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Oznacz maszynę wirtualną jako uogólnioną.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Utwórz obraz przy użyciu uogólnionego wirtualnego dysku twardego systemu operacyjnego.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Następne kroki
- [Utwórz maszynę wirtualną na podstawie zarządzanego obrazu](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    
