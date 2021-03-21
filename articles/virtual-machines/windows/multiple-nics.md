---
title: Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure, które korzystają z wielu kart sieciowych
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Windows z wieloma kartami sieciowymi i zarządzać nią przy użyciu szablonów Azure PowerShell lub Menedżer zasobów.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 2664d175818a6e29dcd3f704c6938987bae050cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555180"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Tworzenie maszyny wirtualnej z systemem Windows i zarządzanie nią z wieloma kartami sieciowymi
Maszyny wirtualne na platformie Azure mogą mieć dołączone do nich wiele kart interfejsów sieci wirtualnej (nic). Typowym scenariuszem jest posiadanie różnych podsieci na potrzeby łączności frontonu i zaplecza. Można skojarzyć wiele kart sieciowych na maszynie wirtualnej z wieloma podsieciami, ale te podsieci muszą znajdować się w tej samej sieci wirtualnej (vNet). W tym artykule szczegółowo opisano sposób tworzenia maszyny wirtualnej, do której dołączono wiele kart sieciowych. Dowiesz się również, jak dodawać i usuwać karty sieciowe z istniejącej maszyny wirtualnej. Różne [rozmiary maszyn wirtualnych](../sizes.md) obsługują różną liczbę kart sieciowych, dlatego należy odpowiednio zmienić rozmiar maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy *parametrów obejmują:* *MyVnet*, i *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi
Najpierw utwórz grupę zasobów. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Tworzenie sieci wirtualnej i podsieci
Typowy scenariusz polega na tym, że sieć wirtualna ma dwie lub więcej podsieci. Jedna podsieć może być dla ruchu frontonu, drugiego dla ruchu zaplecza. Aby nawiązać połączenie z obiema podsieciami, Użyj wielu kart sieciowych na maszynie wirtualnej.

1. Zdefiniuj dwie podsieci sieci wirtualnej za pomocą [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). W poniższym przykładzie zdefiniowano podsieci dla *mySubnetFrontEnd* i *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Utwórz sieć wirtualną i podsieci przy użyciu [nowego AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Tworzenie wielu kart sieciowych
Utwórz dwie karty sieciowe za pomocą [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Dołącz jedną kartę sieciową do podsieci frontonu i jedną kartę sieciową do podsieci zaplecza. Poniższy przykład tworzy karty sieciowe o nazwach *myNic1* i *myNic2*:

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

Zwykle należy również utworzyć [sieciową grupę zabezpieczeń](../../virtual-network/network-security-groups-overview.md) w celu przefiltrowania ruchu sieciowego do maszyny wirtualnej i [modułu równoważenia obciążenia](../../load-balancer/load-balancer-overview.md) w celu dystrybucji ruchu między wieloma maszynami wirtualnymi.

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej
Teraz zacznij tworzyć konfigurację maszyny wirtualnej. Każdy rozmiar maszyny wirtualnej ma limit łącznej liczby kart sieciowych, które można dodać do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych z systemem Windows](../sizes.md).

1. Skonfiguruj poświadczenia maszyny wirtualnej do `$cred` zmiennej w następujący sposób:

    ```powershell
    $cred = Get-Credential
    ```

2. Zdefiniuj swoją maszynę wirtualną za pomocą [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Poniższy przykład definiuje maszynę wirtualną o nazwie *myVM* i używa rozmiaru maszyny wirtualnej, który obsługuje więcej niż dwie karty sieciowe (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Utwórz resztę konfiguracji maszyny wirtualnej za pomocą [opcji Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) i [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Poniższy przykład tworzy maszynę wirtualną z systemem Windows Server 2016:

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

4. Dołącz dwie utworzone wcześniej karty sieciowe z [dodatkiem Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Utwórz maszynę wirtualną przy użyciu [nowego AzVM](/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Aby dodać trasy dla pomocniczych kart sieciowych do systemu operacyjnego, wykonaj kroki opisane w temacie [Konfigurowanie systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Dodawanie karty sieciowej do istniejącej maszyny wirtualnej
Aby dodać wirtualną kartę sieciową do istniejącej maszyny wirtualnej, Cofnij przydział maszyny wirtualnej, Dodaj wirtualną kartę sieciową, a następnie uruchom maszynę wirtualną. Różne [rozmiary maszyn wirtualnych](../sizes.md) obsługują różną liczbę kart sieciowych, dlatego należy odpowiednio zmienić rozmiar maszyny wirtualnej. W razie konieczności można [zmienić rozmiar maszyny wirtualnej](resize-vm.md).

1. Cofnij przydział maszyny wirtualnej za pomocą [stop-AzVM](/powershell/module/az.compute/stop-azvm). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie *myVM* w liście *zasobów*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Pobierz istniejącą konfigurację maszyny wirtualnej za pomocą elementu [Get-AzVm](/powershell/module/az.compute/get-azvm). Poniższy przykład pobiera informacje dotyczące maszyny wirtualnej o nazwie *myVM* w liście *zasobów*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Poniższy przykład tworzy wirtualną kartę sieciową z [nowym-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) o nazwie *myNic3* , która jest dołączona do *mySubnetBackEnd*. Wirtualna karta sieciowa jest następnie dołączona do maszyny wirtualnej o nazwie *myVM* w usłudze *resourceName* z [dodatkiem Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

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
    Jedną z kart sieciowych na maszynie wirtualnej z obsługą wielu kart sieciowych musi być podstawowa. Jeśli jedna z istniejących wirtualnych kart sieciowych na maszynie wirtualnej jest już ustawiona jako podstawowa, można pominąć ten krok. W poniższym przykładzie założono, że dwie wirtualne karty sieciowe są teraz obecne na maszynie wirtualnej i chcesz dodać pierwszą kartę sieciową ( `[0]` ) jako podstawową:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Uruchom maszynę wirtualną przy użyciu [menu Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Aby dodać trasy dla pomocniczych kart sieciowych do systemu operacyjnego, wykonaj kroki opisane w temacie [Konfigurowanie systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Usuwanie karty sieciowej z istniejącej maszyny wirtualnej
Aby usunąć wirtualną kartę sieciową z istniejącej maszyny wirtualnej, Cofnij przydział maszyny wirtualnej, Usuń wirtualną kartę sieciową, a następnie uruchom maszynę wirtualną.

1. Cofnij przydział maszyny wirtualnej za pomocą [stop-AzVM](/powershell/module/az.compute/stop-azvm). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie *myVM* w liście *zasobów*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Pobierz istniejącą konfigurację maszyny wirtualnej za pomocą elementu [Get-AzVm](/powershell/module/az.compute/get-azvm). Poniższy przykład pobiera informacje dotyczące maszyny wirtualnej o nazwie *myVM* w liście *zasobów*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Uzyskaj informacje dotyczące usuwania karty sieciowej za pomocą [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface). Poniższy przykład pobiera informacje o *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Usuń kartę sieciową za pomocą polecenie [Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface) , a następnie zaktualizuj maszynę wirtualną za pomocą polecenie [Update-AzVm](/powershell/module/az.compute/update-azvm). Poniższy przykład usuwa *myNic3* jako uzyskany `$nicId` w poprzednim kroku:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Uruchom maszynę wirtualną przy użyciu [menu Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Tworzenie wielu kart sieciowych z szablonami
Szablony Azure Resource Manager umożliwiają tworzenie wielu wystąpień zasobu podczas wdrażania, takich jak tworzenie wielu kart sieciowych. Szablony Menedżer zasobów używają deklaratywnych plików JSON do definiowania środowiska. Aby uzyskać więcej informacji, zobacz [omówienie Azure Resource Manager](../../azure-resource-manager/management/overview.md). Możesz użyć *kopii* , aby określić liczbę wystąpień do utworzenia:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Aby uzyskać więcej informacji, zobacz [Tworzenie wielu wystąpień przy *użyciu kopii*](../../azure-resource-manager/templates/copy-resources.md). 

Można również użyć `copyIndex()` do dołączenia numeru do nazwy zasobu. Następnie można utworzyć *myNic1*, *MyNic2* i tak dalej. Poniższy kod przedstawia przykład dołączania wartości indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Możesz zapoznać się z kompletnym przykładem [tworzenia wielu kart sieciowych za pomocą szablonów Menedżer zasobów](../../virtual-network/template-samples.md).

Aby dodać trasy dla pomocniczych kart sieciowych do systemu operacyjnego, wykonaj kroki opisane w temacie [Konfigurowanie systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych

Platforma Azure przypisuje bramę domyślną do pierwszego (podstawowego) interfejsu sieciowego dołączonego do maszyny wirtualnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowe interfejsy sieciowe mogą jednak komunikować się z zasobami poza ich podsiecią, mimo że kroki umożliwiające komunikację są różne dla różnych systemów operacyjnych.

1. W wierszu polecenia systemu Windows uruchom `route print` polecenie, które zwraca dane wyjściowe podobne do następujących danych wyjściowych dla maszyny wirtualnej z dwoma podłączonymi interfejsami sieciowymi:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    W tym przykładzie **Microsoft Hyper-V karcie sieciowej #4** (interfejs 7) to pomocniczy interfejs sieciowy, do którego nie jest przypisana Brama domyślna.

2. W wierszu polecenia Uruchom `ipconfig` polecenie, aby sprawdzić, który adres IP jest przypisany do pomocniczego interfejsu sieciowego. W tym przykładzie 192.168.2.4 jest przypisany do interfejsu 7. Dla pomocniczego interfejsu sieciowego nie jest zwracany żaden adres bramy domyślnej.

3. Aby skierować cały ruch przeznaczony dla adresów spoza podsieci pomocniczego interfejsu sieciowego do bramy podsieci, uruchom następujące polecenie:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Adres bramy podsieci to pierwszy adres IP (kończący się na dziesiąte) w zakresie adresów zdefiniowanym dla podsieci. Jeśli nie chcesz rozsyłać całego ruchu poza podsiecią, możesz zamiast tego dodać poszczególne trasy do określonych miejsc docelowych. Jeśli na przykład chcesz kierować ruchem z dodatkowego interfejsu sieciowego do sieci 192.168.3.0, wprowadź polecenie:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Aby potwierdzić Pomyślne nawiązanie komunikacji z zasobem w sieci 192.168.3.0, na przykład wprowadź następujące polecenie, aby wysłać polecenie ping do 192.168.3.4 przy użyciu interfejsu 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Może być konieczne otwarcie protokołu ICMP przez zaporę systemu Windows urządzenia, do którego wysyłasz polecenie ping przy użyciu następującego polecenia:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Aby potwierdzić, że dodana trasa znajduje się w tabeli tras, wprowadź `route print` polecenie, które zwraca dane wyjściowe podobne do następującego tekstu:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Trasa wymieniona przy użyciu *192.168.1.1* w obszarze **brama** jest trasą domyślną dla podstawowego interfejsu sieciowego. Trasa z *192.168.2.1* w obszarze **brama** jest dodaną trasą.

## <a name="next-steps"></a>Następne kroki
Sprawdź [rozmiary maszyn wirtualnych z systemem Windows](../sizes.md) , gdy próbujesz utworzyć maszynę wirtualną z wieloma kartami sieciowymi. Zwróć uwagę na maksymalną liczbę kart sieciowych obsługiwanych przez poszczególne rozmiary maszyn wirtualnych.