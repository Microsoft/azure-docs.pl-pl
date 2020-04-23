---
title: Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku na platformie Azure
description: Utwórz nową maszynę wirtualną, dołączając wyspecjalizowany dysk niezarządzany w modelu wdrażania Menedżer zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 60b0a0f0d83b9b83c9cf8d530881508af591de59
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099652"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego wirtualnego dysku twardego na koncie magazynu

Utwórz nową maszynę wirtualną, dołączając wyspecjalizowany dysk niezarządzany jako dysk systemu operacyjnego przy użyciu programu PowerShell. Wyspecjalizowany dysk to kopia wirtualnego dysku twardego z istniejącej maszyny wirtualnej, która przechowuje konta użytkowników, aplikacje i inne dane stanu z oryginalnej maszyny wirtualnej. 

Dostępne są dwie opcje:
* [Przekazywanie wirtualnego dysku twardego](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopiowanie wirtualnego dysku twardego istniejącej maszyny wirtualnej platformy Azure](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Opcja 1: przekazywanie wyspecjalizowanego wirtualnego dysku twardego

Wirtualny dysk twardy można przekazać z wyspecjalizowanej maszyny wirtualnej utworzonej przy użyciu lokalnego narzędzia do wirtualizacji, takiego jak funkcja Hyper-V, lub maszyny wirtualnej wyeksportowanej z innej chmury.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Można przekazać wyspecjalizowany wirtualny dysk twardy, który został utworzony przy użyciu lokalnej maszyny wirtualnej lub wirtualnego dysku twardego wyeksportowanego z innej chmury. Wyspecjalizowany wirtualny dysk twardy obsługuje konta użytkowników, aplikacje i inne dane stanu z oryginalnej maszyny wirtualnej. Jeśli zamierzasz utworzyć nową maszynę wirtualną przy użyciu dysku VHD, upewnij się, że zostały wykonane następujące czynności. 
  
  * [Przygotuj wirtualny dysk twardy systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie należy** UOGÓLNIAĆ maszyny wirtualnej przy użyciu programu Sysprep.
  * Usuń wszystkie narzędzia i agenci wirtualizacji gościa, które są zainstalowane na maszynie wirtualnej (tj. narzędzia VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana do ściągania adresów IP i ustawień DNS za pośrednictwem protokołu DHCP. Dzięki temu serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania. 


### <a name="get-the-storage-account"></a>Pobierz konto magazynu
Do przechowywania przekazanego obrazu maszyny wirtualnej jest potrzebne konto magazynu na platformie Azure. Możesz użyć istniejącego konta magazynu lub utworzyć nowe. 

Aby wyświetlić dostępne konta magazynu, wpisz:

```powershell
Get-AzStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przechodzenie do sekcji przekazywanie obrazu maszyny wirtualnej.

Jeśli musisz utworzyć konto magazynu, wykonaj następujące czynności:

1. Wymagana jest nazwa grupy zasobów, w której ma zostać utworzone konto magazynu. Aby sprawdzić wszystkie grupy zasobów, które znajdują się w Twojej subskrypcji, wpisz:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie Moja **zasobów** w regionie **zachodnie stany USA** , wpisz:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mojekontomagazynu** w tej grupie zasobów za pomocą polecenia cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Przekazywanie wirtualnego dysku twardego do konta magazynu
Użyj polecenia cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) , aby przekazać obraz do kontenera na koncie magazynu. Ten przykład przekazuje plik **myVHD. VHD** z `"C:\Users\Public\Documents\Virtual hard disks\"` do konta magazynu o nazwie **mojekontomagazynu** w **grupie zasobów zasobu** . Plik zostanie umieszczony w kontenerze o nazwie Moja **kontener, a nowa** nazwa pliku będzie **myUploadedVHD. VHD**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Jeśli to się powiedzie, otrzymasz odpowiedź podobną do:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

W zależności od połączenia sieciowego i rozmiaru pliku VHD, wykonanie tego polecenia może potrwać trochę czasu.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opcja 2: kopiowanie wirtualnego dysku twardego z istniejącej maszyny wirtualnej platformy Azure

Wirtualny dysk twardy można skopiować do innego konta magazynu w celu użycia podczas tworzenia nowej, zduplikowanej maszyny wirtualnej.

### <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, że:

* Zawierają informacje o **kontach magazynu źródłowego i docelowego**. Dla źródłowej maszyny wirtualnej musisz mieć konto magazynu i nazwy kontenerów. Zwykle nazwa kontenera będzie **VHD**. Musisz również mieć docelowe konto magazynu. Jeśli jeszcze go nie masz, możesz go utworzyć przy użyciu portalu (**wszystkie usługi** > konta magazynu > Dodaj) lub przy użyciu polecenia cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) . 
* Pobrano i zainstalowano [Narzędzie AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Cofanie przydziału maszyny wirtualnej
Cofnij przydział maszyny wirtualnej, co zwalnia dysk VHD do skopiowania. 

* **Portal**: kliknij kolejno pozycje **maszyny** > wirtualne**myVM** > Zatrzymaj
* **PowerShell**: Użyj polecenia [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) , aby zatrzymać (cofnąć przydział) maszynę wirtualną o nazwie **myVM** **w grupie zasobów**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stan** maszyny wirtualnej w Azure Portal zmiany z **zatrzymane** na **zatrzymane (cofnięto przydział)**.

### <a name="get-the-storage-account-urls"></a>Pobierz adresy URL konta magazynu
Potrzebujesz adresów URL źródłowych i docelowych kont magazynu. Adresy URL wyglądają następująco `https://<storageaccount>.blob.core.windows.net/<containerName>/`:. Jeśli znasz już konto magazynu i nazwę kontenera, możesz po prostu zastąpić informacje w nawiasach, aby utworzyć adres URL. 

Aby uzyskać adres URL, można użyć Azure Portal lub programu Azure PowerShell:

* **Portal**: kliknij pozycję **>** dla **wszystkich usług** > **Magazyn konta** > *storage account* > magazynu**obiekty blob** i plik wirtualnego dysku twardego prawdopodobnie znajduje się w kontenerze **VHD** . Kliknij pozycję **Właściwości** dla kontenera i skopiuj tekst z etykietą **adres URL**. Potrzebne będą adresy URL zarówno kontenera źródłowego, jak i docelowego. 
* **PowerShell**: należy użyć polecenia [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) , aby uzyskać informacje o maszynie wirtualnej o nazwie **myVM** **w grupie zasobów**. W wynikach zapoznaj się z sekcją **profil magazynu** dla **identyfikatora URI dysku VHD**. Pierwsza część identyfikatora URI jest adresem URL kontenera, a ostatnią częścią jest nazwa wirtualnego dysku twardego systemu operacyjnego dla maszyny wirtualnej.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Pobierz klucze dostępu do magazynu
Znajdź klucze dostępu dla konta magazynu źródłowego i docelowego. Aby uzyskać więcej informacji o kluczach dostępu, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).

* **Portal**: kliknij kolejno pozycje **wszystkie usługi** > **konta** > magazyn**klucze dostępu do***konta* > . Skopiuj klucz oznaczony jako **Klucz1**.
* **PowerShell**: należy użyć polecenia [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) , aby uzyskać klucz magazynu dla konta magazynu **mojekontomagazynu** **w grupie zasobów**. Skopiuj klucz o nazwie **Klucz1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopiowanie wirtualnego dysku twardego
Można kopiować pliki między kontami magazynu za pomocą AzCopy. Jeśli kontener docelowy nie istnieje, zostanie on utworzony dla Ciebie. 

Aby użyć AzCopy, Otwórz wiersz polecenia na komputerze lokalnym i przejdź do folderu, w którym zainstalowano AzCopy. Będzie wyglądać podobnie do *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Aby skopiować wszystkie pliki w kontenerze, należy użyć przełącznika **/s** . Może to służyć do kopiowania wirtualnego dysku twardego systemu operacyjnego i wszystkich dysków danych, jeśli znajdują się w tym samym kontenerze. W tym przykładzie przedstawiono sposób kopiowania wszystkich plików z kontenera **mysourcecontainer** na koncie magazynu **mysourcestorageaccount** do kontenera **mydestinationcontainer** na koncie magazynu **mydestinationstorageaccount** . Zastąp nazwy kont magazynu i kontenerów własnymi. Zamień `<sourceStorageAccountKey1>` i `<destinationStorageAccountKey1>` z własnymi kluczami.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Jeśli chcesz skopiować tylko określony wirtualny dysk twardy do kontenera z wieloma plikami, możesz również określić nazwę pliku przy użyciu przełącznika/Pattern. W tym przykładzie zostanie skopiowany tylko plik o nazwie nazwa pliku **. VHD** .

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Po zakończeniu otrzymasz komunikat o podobnym wyglądzie:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów
* Jeśli używasz AZCopy, Jeśli zobaczysz błąd "serwer nie może uwierzytelnić żądania", upewnij się, że wartość nagłówka autoryzacji jest poprawnie sformułowana, łącznie z podpisem. Jeśli używasz klucza 2 lub pomocniczego klucza magazynu, spróbuj użyć podstawowego lub pierwszego klucza magazynu.

## <a name="create-the-new-vm"></a>Utwórz nową maszynę wirtualną 

Należy utworzyć sieć i inne zasoby maszyn wirtualnych, które będą używane przez nową maszynę wirtualną.

### <a name="create-the-subnet-and-vnet"></a>Tworzenie podsieci i sieci wirtualnej

Utworzyć sieć wirtualną i podsieć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. Ten przykład tworzy podsieć o nazwie Moja **podsieć** **, w grupie zasobów**Grupa zasobu i ustawia prefiks adresu podsieci na **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Utwórz sieć wirtualną. Ten przykład ustawia nazwę sieci wirtualnej na **myVnetName**, lokalizację na **zachodnie stany USA**oraz prefiks adresu sieci wirtualnej na **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły RDP
   Aby można było zalogować się do maszyny wirtualnej przy użyciu protokołu RDP, należy mieć regułę zabezpieczeń, która zezwala na dostęp protokołu RDP na porcie 3389. Ponieważ wirtualny dysk twardy dla nowej maszyny wirtualnej został utworzony na podstawie istniejącej wyspecjalizowanej maszyny wirtualnej, po utworzeniu maszyny wirtualnej można użyć istniejącego konta ze źródłowej maszyny wirtualnej, która ma uprawnienia do logowania się przy użyciu protokołu RDP.
   Należy to zakończyć przed utworzeniem interfejsu sieciowego, z którym zostanie skojarzona.  
   Ten przykład ustawia nazwę sieciowej grupy zabezpieczeń na **myNsg** i nazwę reguły RDP na **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Aby uzyskać więcej informacji na temat punktów końcowych i reguł sieciowej grupy zabezpieczeń, zobacz [otwieranie portów do maszyny wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty sieciowej
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Utwórz publiczny adres IP. W tym przykładzie nazwa publicznego adresu IP jest ustawiona na **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. W tym przykładzie nazwa karty sieciowej jest ustawiona na **myNicName**. Ten krok kojarzy również grupę zabezpieczeń sieci utworzoną wcześniej z tą kartą interfejsu sieciowego.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ustaw nazwę i rozmiar maszyny wirtualnej

Ten przykład ustawia nazwę maszyny wirtualnej na "myVM", a rozmiar maszyny wirtualnej na "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodaj kartę sieciową
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurowanie dysku systemu operacyjnego

1. Ustaw identyfikator URI przekazanego lub skopiowanego wirtualnego dysku twardego. W tym przykładzie plik VHD o nazwie **myOsDisk. VHD** jest przechowywany na koncie magazynu o nazwie **mojekontomagazynu** w kontenerze o nazwie **.**

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Dodaj dysk systemu operacyjnego. W tym przykładzie podczas tworzenia dysku systemu operacyjnego termin "osDisk" jest dołączany do nazwy maszyny wirtualnej w celu utworzenia nazwy dysku systemu operacyjnego. Ten przykład określa również, że ten wirtualny dysk twardy z systemem Windows powinien być dołączony do maszyny wirtualnej jako dysk systemu operacyjnego.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcjonalnie: Jeśli masz dyski danych, które muszą być dołączone do maszyny wirtualnej, Dodaj dyski danych przy użyciu adresów URL danych VHD i odpowiedniego numeru jednostki logicznej (LUN).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

W przypadku korzystania z konta magazynu adresy URL dysków danych i systemu operacyjnego wyglądają następująco: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Można to znaleźć w portalu, przechodząc do docelowego kontenera magazynu, klikając system operacyjny lub wirtualny dysk twardy danych, który został skopiowany, a następnie kopiując zawartość adresu URL.


### <a name="complete-the-vm"></a>Ukończ maszynę wirtualną 

Utwórz maszynę wirtualną przy użyciu właśnie utworzonych konfiguracji.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Jeśli to polecenie zakończyło się pomyślnie, zobaczysz dane wyjściowe podobne do tego:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Nowo utworzona maszyna wirtualna powinna zostać wyświetlona w [Azure Portal](https://portal.azure.com)w obszarze **wszystkie usługi** > **maszyny wirtualne**lub przy użyciu następujących poleceń programu PowerShell:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md).

