---
title: Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure, które korzystają z wielu kart sieciowych
description: Dowiedz się, jak utworzyć maszynę wirtualną systemu Windows, do których jest dołączonych wiele kart sieciowych, przy użyciu szablonów programu Azure PowerShell lub Resource Manager, i zarządzanie nią.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 6651ae21694022be86d8db08737c609aed3df569
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870275"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Tworzenie maszyny wirtualnej systemu Windows z wieloma kartami sieciowymi i zarządzanie nią
Maszyny wirtualne (VM) na platformie Azure mogą mieć podłączone do nich wiele kart interfejsu sieci wirtualnej (NIC). Typowym scenariuszem jest mieć różne podsieci dla łączności front-end i back-end. Można skojarzyć wiele kart sieciowych na maszynie wirtualnej z wieloma podsieciami, ale wszystkie te podsieci muszą znajdować się w tej samej sieci wirtualnej (vNet). W tym artykule opisano, jak utworzyć maszynę wirtualną, która ma wiele kart sieciowych dołączonych do niego. Dowiesz się również, jak dodać lub usunąć karty sieciowe z istniejącej maszyny Wirtualnej. Różne [rozmiary maszyn wirtualnych](sizes.md) obsługują różną liczbę kart sieciowych, więc odpowiednio rozmiar maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów to *myResourceGroup*, *myVnet*i *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi
Najpierw utwórz grupę zasobów. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *EastUs:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Tworzenie sieci wirtualnej i podsieci
Typowym scenariuszem jest dla sieci wirtualnej mieć co najmniej dwie podsieci. Jedna podsieć może być dla ruchu front-end, druga dla ruchu zaplecza. Aby połączyć się z obiema podsieciami, należy użyć wielu kart sieciowych na maszynie wirtualnej.

1. Zdefiniuj dwie podsieci sieci wirtualnej za pomocą [programu New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Poniższy przykład definiuje podsieci dla *mySubnetFrontEnd* i *mySubnetBackEnd:*

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Utwórz swoją sieć wirtualną i podsieci za pomocą [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet:*

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Tworzenie wielu kart sieciowych
Utwórz dwie karty sieciowe z [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Dołącz jedną kartę sieciową do podsieci frontonu i jedną kartę sieciową do podsieci zaplecza. W poniższym przykładzie utworzy się karty sieciowe o nazwie *myNic1* i *myNic2:*

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Zazwyczaj można również utworzyć [grupę zabezpieczeń sieci](../../virtual-network/security-overview.md) do filtrowania ruchu sieciowego do maszyny wirtualnej i [moduł równoważenia obciążenia](../../load-balancer/load-balancer-overview.md) do dystrybucji ruchu na wielu maszynach wirtualnych.

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej
Teraz zacznij tworzyć konfigurację maszyny Wirtualnej. Każdy rozmiar maszyny Wirtualnej ma limit całkowitej liczby kart sieciowych, które można dodać do maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych systemu Windows](sizes.md).

1. Ustaw poświadczenia maszyny Wirtualnej `$cred` na zmienną w następujący sposób:

    ```powershell
    $cred = Get-Credential
    ```

2. Zdefiniuj maszynę wirtualną za pomocą [aplikacji New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). Poniższy przykład definiuje maszynę wirtualną o nazwie *myVM* i używa rozmiaru maszyny Wirtualnej, który obsługuje więcej niż dwie karty sieciowe *(Standard_DS3_v2):*

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Utwórz pozostałą część konfiguracji maszyny wirtualnej za pomocą [systemu Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) i [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). W poniższym przykładzie utworzy się maszynę wirtualną z systemem Windows Server 2016:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Dołącz dwie karty sieciowe utworzone wcześniej za pomocą [dodatku AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Tworzenie maszyny Wirtualnej z [New-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Dodaj trasy dla pomocniczych kart sieciowych do systemu operacyjnego, wykonując kroki opisane w [temacie Konfigurowanie systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Dodawanie karty sieciowej do istniejącej maszyny Wirtualnej
Aby dodać wirtualną kartę sieciową do istniejącej maszyny Wirtualnej, należy zdelokować maszynę wirtualną, dodać wirtualną kartę sieciową, a następnie uruchomić maszynę wirtualną. Różne [rozmiary maszyn wirtualnych](sizes.md) obsługują różną liczbę kart sieciowych, więc odpowiednio rozmiar maszyny wirtualnej. W razie potrzeby można [zmienić rozmiar maszyny Wirtualnej](resize-vm.md).

1. Przydziel maszynę wirtualną [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). W poniższym przykładzie wiele alokacji maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Pobierz istniejącą konfigurację maszyny Wirtualnej z [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Poniższy przykład pobiera informacje o maszynie wirtualnej o nazwie *myVM* w *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Poniższy przykład tworzy wirtualną kartę sieciową o nazwie [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) o nazwie *myNic3,* która jest dołączona do *programu mySubnetBackEnd*. Wirtualna karta sieciowa jest następnie dołączana do maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup* z [add-AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Podstawowe wirtualne karty sieciowe
    Jedna z kart sieciowych na maszynie wirtualnej z wieloma kartami sieciowym musi być podstawowa. Jeśli jedna z istniejących wirtualnych kart sieciowych na maszynie wirtualnej jest już ustawiona jako podstawowa, można pominąć ten krok. W poniższym przykładzie przyjęto założenie, że dwie wirtualne karty sieciowe`[0]`są teraz obecne na maszynie wirtualnej i chcesz dodać pierwszą kartę sieciową ( ) jako podstawową:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Uruchom maszynę wirtualną ze [start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Dodaj trasy dla pomocniczych kart sieciowych do systemu operacyjnego, wykonując kroki opisane w [temacie Konfigurowanie systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Usuwanie karty sieciowej z istniejącej maszyny Wirtualnej
Aby usunąć wirtualną kartę sieciową z istniejącej maszyny Wirtualnej, należy zdewastować przydzielenie maszyny Wirtualnej, usunąć wirtualną kartę sieciową, a następnie uruchomić maszynę wirtualną.

1. Przydziel maszynę wirtualną [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). W poniższym przykładzie wiele alokacji maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Pobierz istniejącą konfigurację maszyny Wirtualnej z [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Poniższy przykład pobiera informacje o maszynie wirtualnej o nazwie *myVM* w *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Uzyskaj informacje o usuwaniu karty sieciowej za pomocą [pliku Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). Poniższy przykład pobiera informacje o *myNic3:*

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Usuń kartę sieciową z [Remove-AzVMNetworkInterface,](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) a następnie zaktualizuj maszynę wirtualną za pomocą [aktualizacji AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). Poniższy przykład usuwa *myNic3,* jak uzyskano `$nicId` w poprzednim kroku:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Uruchom maszynę wirtualną ze [start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Tworzenie wielu kart sieciowych z szablonami
Szablony usługi Azure Resource Manager umożliwiają tworzenie wielu wystąpień zasobu podczas wdrażania, takich jak tworzenie wielu kart sieciowych. Szablony Menedżera zasobów używają deklaratywnych plików JSON do definiowania środowiska. Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md). Za pomocą *kopii* można określić liczbę wystąpień do utworzenia:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Aby uzyskać więcej informacji, zobacz [tworzenie wielu wystąpień przy użyciu *kopii*](../../resource-group-create-multiple.md). 

Można również `copyIndex()` dołączyć numer do nazwy zasobu. Następnie można utworzyć *myNic1*, *MyNic2* i tak dalej. Poniższy kod przedstawia przykład dołączania wartości indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pełny przykład tworzenia wielu kart sieciowych można odczytać [przy użyciu szablonów Menedżera zasobów](../../virtual-network/template-samples.md).

Dodaj trasy dla pomocniczych kart sieciowych do systemu operacyjnego, wykonując kroki opisane w [temacie Konfigurowanie systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych

Platforma Azure przypisuje bramę domyślną do pierwszego (podstawowego) interfejsu sieciowego podłączonego do maszyny wirtualnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Pomocnicze interfejsy sieciowe mogą jednak komunikować się z zasobami poza podsiecią, chociaż kroki umożliwiające komunikację są różne dla różnych systemów operacyjnych.

1. Z wiersza polecenia systemu `route print` Windows uruchom polecenie, które zwraca dane wyjściowe podobne do następującego danych wyjściowych dla maszyny wirtualnej z dwoma podłączonymi interfejsami sieciowymi:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    W tym przykładzie **karta sieciowa Microsoft Hyper-V #4** (interfejs 7) jest pomocniczym interfejsem sieciowym, który nie ma przypisanej bramy domyślnej.

2. W wierszu polecenia `ipconfig` uruchom polecenie, aby zobaczyć, który adres IP jest przypisany do pomocniczego interfejsu sieciowego. W tym przykładzie 192.168.2.4 jest przypisany do interfejsu 7. Dla pomocniczego interfejsu sieciowego nie jest zwracany domyślny adres bramy.

3. Aby przekierować cały ruch przeznaczony dla adresów spoza podsieci pomocniczego interfejsu sieciowego do bramy podsieci, uruchom następujące polecenie:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Adres bramy podsieci jest pierwszym adresem IP (kończącym się na .1) w zakresie adresów zdefiniowanym dla podsieci. Jeśli nie chcesz kierować całego ruchu poza podsieć, możesz dodać poszczególne trasy do określonych miejsc docelowych. Na przykład jeśli ruch z pomocniczego interfejsu sieciowego ma być kierowany tylko do sieci 192.168.3.0, należy wprowadzić polecenie:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Aby potwierdzić pomyślną komunikację z zasobem w sieci 192.168.3.0, wprowadź następujące polecenie do pingu 192.168.3.4 za pomocą interfejsu 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Może być konieczne otwarcie ICMP za pośrednictwem zapory systemu Windows urządzenia, na które pingujesz, za pomocą następującego polecenia:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Aby potwierdzić, że dodana trasa `route print` znajduje się w tabeli trasy, wprowadź polecenie, które zwraca dane wyjściowe podobne do następującego tekstu:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Trasa wymieniona z *192.168.1.1* w obszarze **Brama**jest trasą, która domyślnie znajduje się w podstawowym interfejsie sieciowym. Trasa z *192.168.2.1* pod **Gateway**, jest trasą, którą dodałeś.

## <a name="next-steps"></a>Następne kroki
Przejrzyj [rozmiary maszyn wirtualnych systemu Windows](sizes.md) podczas próby utworzenia maszyny Wirtualnej, która ma wiele kart sieciowych. Należy zwrócić uwagę na maksymalną liczbę kart sieciowych, które obsługuje każdy rozmiar maszyny Wirtualnej. 


