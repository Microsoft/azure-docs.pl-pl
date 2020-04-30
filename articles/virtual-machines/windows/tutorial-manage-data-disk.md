---
title: Samouczek — zarządzanie dyskami platformy Azure za pomocą programu Azure PowerShell
description: Z tego samouczka dowiesz się, jak tworzyć dyski platformy Azure dla maszyn wirtualnych i zarządzać nimi za pomocą programu Azure PowerShell
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c9f514b70eda7d74950576a1a6f3a1199cddb232
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82100332"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Samouczek — zarządzanie dyskami platformy Azure za pomocą programu Azure PowerShell

Maszyny wirtualne platformy Azure przechowują swoje systemy operacyjne, aplikacje i dane na dyskach. Ważne jest, aby podczas tworzenia maszyny wirtualnej wybrać rozmiar dysku i konfigurację odpowiadające oczekiwanemu obciążeniu. W tym samouczku opisano wdrażanie dysków maszyn wirtualnych i zarządzanie nimi. Zapoznasz się z informacjami na temat następujących zagadnień:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="default-azure-disks"></a>Domyślne dyski platformy Azure

Do tworzonej maszyny wirtualnej platformy Azure automatycznie dołączane są dwa dyski. 

**Dysk systemu operacyjnego** — dyski systemu operacyjnego mogą mieć rozmiar do 4 terabajtów i hostują system operacyjny maszyny wirtualnej. Jeśli tworzysz nową maszynę wirtualną z obrazu [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) , zwykle 127 GB (ale niektóre obrazy mają mniejsze rozmiary dysków systemu operacyjnego). Dysk systemu operacyjnego ma domyślnie przypisaną literę dysku *C:*. Konfiguracja buforowania dysku systemu operacyjnego jest zoptymalizowana pod kątem wydajności systemu operacyjnego. Dysk systemu operacyjnego **nie powinien** hostować aplikacji ani danych. Na potrzeby aplikacji i danych należy użyć dysku z danymi, które zostanie szczegółowo opisany w dalszej części tego artykułu.

**Dysk tymczasowy** — dyski tymczasowe używają dysku SSD, który znajduje się na tym samym hoście platformy Azure co maszyna wirtualna. Dyski tymczasowe są wysoce wydajne i można przy ich użyciu wykonywać operacje takie jak przetwarzanie danych tymczasowych. Jednak jeśli maszyna wirtualna zostanie przeniesiona do nowego hosta, wszystkie dane przechowywane na dysku tymczasowym zostaną usunięte. Rozmiar dysku tymczasowego zależy od [rozmiaru maszyny wirtualnej](sizes.md). Dyski tymczasowe mają domyślnie przypisaną literę dysku *D:*.

## <a name="azure-data-disks"></a>Dyski z danymi platformy Azure

Dodatkowe dyski z danymi można dodać w celu instalowania aplikacji i przechowywania danych. Dyski z danymi powinny być używane w sytuacji, gdy potrzebny jest trwały i dynamiczny magazyn danych. Liczba dysków z danymi, które można dołączyć do maszyny wirtualnej, zależy od jej rozmiaru.

## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej

Na platformie Azure dostępne są dwa typy dysków.

**Dyski w warstwie Standardowa** — bazują na dyskach twardych (HDD) i stanowią ekonomiczne, chociaż wciąż wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.

**Dyski w warstwie Premium** — wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Usługa Premium Storage obsługuje maszyny wirtualne z serii DS, DSv2, GS i FS. Istnieje pięć typów dysków w warstwie Premium (P10, P20, P30, P40, P50); rozmiar dysku określa typ dysku. Podczas wybierania rozmiaru dysku wartość jest zaokrąglana do następnego typu. Jeśli na przykład rozmiar jest mniejszy niż 128 GB, typ dysku to P10, jeśli rozmiar przypada między 129 GB i 512 GB, rozmiar dysku to P20.

### <a name="premium-disk-performance"></a>Wydajność dysku w warstwie Premium
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

W powyższej tabeli podano maksymalną liczbę operacji wejścia/wyjścia na sekundę na dysk, ale wyższą wydajność można osiągnąć przez stosowanie wielu dysków z danymi. Na przykład do maszyny wirtualnej Standard_GS5 można dołączyć 64 dyski z danymi. Jeśli każdy z tych dysków ma rozmiar P30, można osiągnąć maksymalnie 80 000 operacji we/wy na sekundę. Aby uzyskać szczegółowe informacje na temat maksymalnej liczby operacji we/wy na sekundę dla maszyny wirtualnej, zobacz [VM types and sizes (Typy i rozmiary maszyn wirtualnych)](./sizes.md).

## <a name="create-and-attach-disks"></a>Tworzenie i dołączanie dysków

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby utwórz maszynę wirtualną za pomocą poniższych poleceń.

Ustaw nazwę użytkownika i hasło potrzebne dla konta administratora na maszynie wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):


Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Pojawi się monit o podanie nazwy użytkownika i hasła dla konta administratorów maszyny wirtualnej.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Do tworzenia konfiguracji początkowej służy polecenie [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig). W poniższym przykładzie jest konfigurowany dysk o rozmiarze 128 gigabajtów.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Do tworzenia dysku danych służy polecenie [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Do pobierania maszyny wirtualnej, do której chcesz dodać dysk z danymi, służy polecenie [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Do dodawania dysku z danymi do konfiguracji maszyny wirtualnej służy polecenie [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Do aktualizowania maszyny wirtualnej służy polecenie [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Przygotowywanie dysków z danymi

Po dołączeniu dysku do maszyny wirtualnej należy skonfigurować system operacyjny do korzystania z tego dysku. Poniższy przykład pokazuje, jak ręcznie skonfigurować pierwszy dysk dodany do maszyny wirtualnej. Proces ten można też zautomatyzować przy użyciu [niestandardowego rozszerzenia skryptu](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Konfiguracja ręczna

Utwórz połączenie RDP z maszyną wirtualną. Otwórz program PowerShell i uruchom ten skrypt.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Weryfikowanie dysku z danymi

Aby sprawdzić, czy dysk z danymi został dołączony, wyświetl profil `StorageProfile` dla dołączonych dysków `DataDisks`.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Dane wyjściowe powinny wyglądać następująco:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące dysków maszyn wirtualnych, takie jak:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat automatyzowania konfigurowania maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Automatyzowanie konfiguracji maszyn wirtualnych](./tutorial-automate-vm-deployment.md)
