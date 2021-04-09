---
title: Rozszerzanie dysku systemu operacyjnego maszyny wirtualnej z systemem Windows na platformie Azure
description: Zwiększ rozmiar dysku systemu operacyjnego maszyny wirtualnej przy użyciu Azure PowerShell w modelu wdrażania Menedżer zasobów.
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: af57bc396349286c3edcaefc8385ddbaec066226
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199472"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Sposób rozszerzania dysku systemu operacyjnego maszyny wirtualnej

Podczas tworzenia nowej maszyny wirtualnej w grupie zasobów przez wdrożenie obrazu z [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/)domyślny dysk systemu operacyjnego jest często 127 GB (domyślnie niektóre obrazy mają mniejsze rozmiary dysków systemu operacyjnego). Mimo że można dodać dyski danych do maszyny wirtualnej (liczba zależy od wybranej jednostki SKU), a firma Microsoft zaleca zainstalowanie aplikacji i obciążeń intensywnie korzystających z procesora CPU na tych dyskach uzupełnienie, często klienci muszą rozszerzyć dysk systemu operacyjnego w celu obsługi określonych scenariuszy:

- Aby obsługiwać starsze aplikacje instalujące składniki na dysku systemu operacyjnego.
- Aby przeprowadzić migrację fizycznego komputera lub maszyny wirtualnej z miejsca lokalnego na większy dysk systemu operacyjnego.

> [!IMPORTANT]
> Zmiany rozmiarów systemu operacyjnego lub dysku danych maszyny wirtualnej platformy Azure wymagają cofnięcia przydziału maszyny wirtualnej.
>
> Zmniejszanie istniejącego dysku nie jest obsługiwane i może spowodować utratę danych.
> 
> Po rozwinięciu dysków należy [rozszerzyć wolumin w systemie operacyjnym](#expand-the-volume-within-the-os) , aby korzystać z większego dysku.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Zmień rozmiar dysku zarządzanego w Azure Portal

1. W [Azure Portal](https://portal.azure.com)przejdź do maszyny wirtualnej, w której chcesz rozszerzyć dysk. Wybierz pozycję **Zatrzymaj** , aby cofnąć przydział maszyny wirtualnej.
2. Po zatrzymaniu maszyny wirtualnej w menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **dyski**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Zrzut ekranu przedstawiający opcję dyski wybraną w sekcji Ustawienia w menu.":::

 
3. W obszarze **Nazwa dysku** wybierz dysk, którego rozmiar chcesz zmienić.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Zrzut ekranu przedstawiający okienko dyski z wybraną nazwą dysku.":::

4. W menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **rozmiar i wydajność**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Zrzut ekranu przedstawiający opcję rozmiaru i wydajności wybraną w sekcji Ustawienia w menu.":::

5. W obszarze **rozmiar i wydajność** wybierz żądany rozmiar dysku.
   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona liczba dysków systemu operacyjnego to 2 048 GB. (Istnieje możliwość rozszerzenia obiektu BLOB dysku VHD poza ten rozmiar, ale system operacyjny działa tylko z pierwszym 2 048 GB miejsca).
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Zrzut ekranu przedstawiający okienko rozmiaru i wydajności z wybranym rozmiarem dysku.":::

6. Wybierz pozycję **Zmień rozmiar** w dolnej części strony.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Zrzut ekranu pokazujący okienko rozmiaru i wydajności z wybranym przyciskiem Zmień rozmiar.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Zmiana rozmiaru dysku zarządzanego przy użyciu programu PowerShell

Otwórz okno programu PowerShell ISE lub PowerShell w trybie administracyjnym i wykonaj następujące czynności:

1. Zaloguj się do konta Microsoft Azure w trybie zarządzania zasobami i wybierz swoją subskrypcję:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Ustaw nazwę grupy zasobów i nazwę maszyny wirtualnej:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Uzyskaj odwołanie do maszyny wirtualnej:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Zatrzymaj maszynę wirtualną przed zmianą wielkości dysku:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Uzyskaj odwołanie do zarządzanego dysku systemu operacyjnego. Ustaw rozmiar zarządzanego dysku systemu operacyjnego na żądaną wartość i zaktualizuj dysk:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona liczba dysków systemu operacyjnego to 2 048 GB. (Możliwe jest rozszerzenie obiektu BLOB dysku VHD poza ten rozmiar, ale system operacyjny działa tylko z pierwszym 2 048 GB miejsca).
    > 
         
6. Aktualizacja maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia Uruchom ponownie maszynę wirtualną:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

I to wszystko. Teraz połącz protokół RDP z maszyną wirtualną, otwórz okno Zarządzanie komputerem (lub Zarządzanie dyskiem) i rozszerz dysk przy użyciu nowo przydzielonego miejsca.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Zmiana rozmiaru dysku niezarządzanego za pomocą programu PowerShell

Otwórz okno programu PowerShell ISE lub PowerShell w trybie administracyjnym i wykonaj następujące czynności:

1. Zaloguj się do konta Microsoft Azure w trybie zarządzania zasobami i wybierz swoją subskrypcję:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Ustaw nazwę grupy zasobów i nazwy maszyn wirtualnych:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Uzyskaj odwołanie do maszyny wirtualnej:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Zatrzymaj maszynę wirtualną przed zmianą wielkości dysku:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Ustaw rozmiar niezarządzanego dysku systemu operacyjnego na pożądaną wartość i zaktualizuj maszynę wirtualną:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona liczba dysków systemu operacyjnego to 2 048 GB. (Możliwe jest rozszerzenie obiektu BLOB dysku VHD poza ten rozmiar, ale system operacyjny będzie mógł działać tylko z pierwszym 2 048 GB miejsca).
    > 
    > 
   
6. Aktualizacja maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia Uruchom ponownie maszynę wirtualną:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Skrypty dysku systemu operacyjnego

Poniżej znajduje się kompletny skrypt dotyczący odwołania zarówno dla dysków zarządzanych, jak i niezarządzanych:


**Dyski zarządzane**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Dyski niezarządzane**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Zmienianie rozmiarów dysków z danymi

Ten artykuł koncentruje się głównie na rozszerzaniu dysku systemu operacyjnego maszyny wirtualnej, ale można również użyć tego skryptu do rozwinięcia dysków danych dołączonych do maszyny wirtualnej. Aby na przykład rozszerzyć pierwszy dysk danych dołączony do maszyny wirtualnej, zamień obiekt `OSDisk` elementu `StorageProfile` na tablicę `DataDisks` i przy użyciu indeksu liczbowego uzyskaj odwołanie do pierwszego dołączonego dysku danych, jak pokazano poniżej:

**Dysk zarządzany**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Dysk niezarządzany**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Analogicznie można odwoływać się do innych dysków z danymi dołączonych do maszyny wirtualnej przy użyciu indeksu, jak pokazano powyżej, lub właściwość **Nazwa** dysku:


**Dysk zarządzany**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Dysk niezarządzany**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Rozwiń wolumin w systemie operacyjnym

Po rozwinięciu dysku dla maszyny wirtualnej należy przejść do systemu operacyjnego i rozwinąć wolumin, aby obejmował nowe miejsce. Istnieje kilka metod rozszerzania partycji. Ta sekcja obejmuje łączenie maszyny wirtualnej przy użyciu połączenia RDP w celu rozszerzenia partycji za pomocą **narzędzia DiskPart**.

1. Otwórz połączenie RDP z maszyną wirtualną.

2. Otwórz wiersz polecenia i wpisz **diskpart**.

3. W wierszu polecenia **narzędzia DiskPart** wpisz polecenie `list volume` . Zanotuj wolumin, który chcesz zwiększyć.

4. W wierszu polecenia **narzędzia DiskPart** wpisz polecenie `select volume <volumenumber>` . Spowoduje to wybranie *volumenumber* woluminu, który ma zostać rozbudowany do ciągłego, pustego miejsca na tym samym dysku.

5. W wierszu polecenia **narzędzia DiskPart** wpisz polecenie `extend [size=<size>]` . Spowoduje to rozszerzenie wybranego woluminu o *rozmiar* w megabajtach (MB).


## <a name="next-steps"></a>Następne kroki

Dyski można również dołączyć przy użyciu [Azure Portal](attach-managed-disk-portal.md).
