---
title: Tworzenie maszyny wirtualnej platformy Azure przy użyciu przyspieszonej sieci — Azure PowerShell
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu przyspieszonej sieci.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 3807c1434e3758eafe299da7b30769b41d3ede87
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106308"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Tworzenie maszyny wirtualnej z systemem Windows za pomocą przyspieszonej sieci przy użyciu Azure PowerShell

W tym samouczku dowiesz się, jak utworzyć maszynę wirtualną z systemem Windows przy użyciu przyspieszonej sieci. Aby utworzyć maszynę wirtualną z systemem Linux przy użyciu przyspieszonej sieci, zobacz [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](create-vm-accelerated-networking-cli.md). Przyspieszona sieć umożliwia wirtualizację we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej, co znacznie zwiększa wydajność sieci. Ta ścieżka o wysokiej wydajności pomija hosta ze ścieżki datapath, skracając czas opóźnienia, wahania i użycie procesora CPU w celu użycia z najbardziej wymagającymi obciążeniami sieci na obsługiwanych typach maszyn wirtualnych. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi za pomocą i bez przyspieszonej sieci:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonej sieci cały ruch sieciowy z i do maszyny wirtualnej musi przechodzić przez hosta i przełącznik wirtualny. Przełącznik wirtualny zapewnia wszystkie wymuszanie zasad, takie jak sieciowe grupy zabezpieczeń, listy kontroli dostępu, izolacja i inne zwirtualizowane w sieci usługi do ruchu sieciowego. Aby dowiedzieć się więcej o przełącznikach wirtualnych, zobacz [Wirtualizacja sieci funkcji Hyper-V i przełącznik wirtualny](https://technet.microsoft.com/library/jj945275.aspx).

Dzięki przyspieszonej sieci ruch sieciowy dociera do interfejsu sieciowego maszyny wirtualnej, a następnie jest przekazywany do maszyny wirtualnej. Wszystkie zasady sieciowe stosowane przez przełącznik wirtualny są teraz odciążanie i stosowane do sprzętu. Zastosowanie zasad sprzętowych pozwala karcie sieciowej przekazywać ruch sieciowy bezpośrednio do maszyny wirtualnej, pomijając hosta i przełącznik wirtualny przy zachowaniu wszystkich zasad, które zostały zastosowane na hoście.

Zalety przyspieszonej sieci mają zastosowanie tylko do maszyny wirtualnej, na której jest włączona. Aby uzyskać najlepsze wyniki, najlepszym rozwiązaniem jest włączenie tej funkcji na co najmniej dwóch maszynach wirtualnych podłączonych do tego samego Virtual Network platformy Azure. Podczas komunikacji między usługą sieci wirtualnych lub połączeniem lokalnym ta funkcja ma minimalny wpływ na ogólne opóźnienia.

## <a name="benefits"></a>Korzyści
* **Mniejsze opóźnienia/wyższe pakiety na sekundę (PPS):** Usunięcie przełącznika wirtualnego ze ścieżki datapath usuwa pakiety czasu spędzane przez hosta w celu przetworzenia zasad i zwiększa liczbę pakietów, które można przetworzyć w ramach maszyny wirtualnej.
* **Ograniczone wahania:** Przetwarzanie przełącznika wirtualnego zależy od ilości zasad, które należy zastosować, oraz obciążenia procesora, które przetwarza. Odciążanie wymuszania zasad sprzętowo eliminuje te zmienności, dostarczając pakiety bezpośrednio do maszyny wirtualnej, usuwając hosta do komunikacji z maszyną wirtualną oraz wszystkie przerwy w oprogramowaniu i przełączenia kontekstu.
* **Zmniejszone użycie procesora CPU:** Obejście przełącznika wirtualnego na hoście prowadzi do mniejszego użycia procesora CPU do przetwarzania ruchu sieciowego.

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane z poziomu galerii platformy Azure:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości ogólnego przeznaczenia i o rozmiarach wystąpień zoptymalizowanych pod kątem obliczeń przy użyciu co najmniej dwóch procesorów wirtualnych vCPU.  Obsługiwane są następujące serie: D/DSv2 i F/FS

W wystąpieniach, które obsługują wielowątkowość, przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych vCPU. Obsługiwane są następujące serie: D/Dsv3, D/Dsv4, E/Esv3, EA/Easv4, Fsv2, Lsv2, MS/MMS i MS/Mmsv2.

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiony
Dostępne we wszystkich publicznych regionach świadczenia usługi Azure i w chmurze Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie przyspieszonej sieci na uruchomionej maszynie wirtualnej
W przypadku obsługiwanego rozmiaru maszyny wirtualnej bez przyspieszonej sieci można włączyć funkcję włączoną tylko wtedy, gdy została zatrzymana i cofnięta alokacja.

### <a name="deployment-through-azure-resource-manager"></a>Wdrożenie za Azure Resource Manager
Maszyn wirtualnych (klasycznych) nie można wdrożyć przy użyciu przyspieszonej sieci.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci platformy Azure
## <a name="portal-creation"></a>Tworzenie portalu
Mimo że ten artykuł zawiera instrukcje tworzenia maszyny wirtualnej z przyspieszoną siecią przy użyciu programu Azure PowerShell, można także [utworzyć maszynę wirtualną z przyspieszoną siecią przy użyciu Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej w portalu w bloku **Utwórz maszynę wirtualną** wybierz kartę **Sieć** .  Na tej karcie jest dostępna opcja dla **przyspieszonej sieci**.  W przypadku wybrania [obsługiwanego systemu operacyjnego](#supported-operating-systems) i [rozmiaru maszyny wirtualnej](#supported-vm-instances)ta opcja spowoduje automatyczne wypełnienie do "włączone".  Jeśli nie, spowoduje to wypełnienie opcji "off" dla przyspieszonej sieci i nadanie użytkownikowi przyczyny, dlaczego nie jest on włączony.   
* *Uwaga:* Tylko obsługiwane systemy operacyjne można włączyć za pomocą portalu.  Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszone sieci, Utwórz maszynę wirtualną przy użyciu interfejsu wiersza polecenia lub programu PowerShell. 

Po utworzeniu maszyny wirtualnej można potwierdzić, że przyspieszona sieć jest włączona, postępując zgodnie z instrukcjami w obszarze upewnij się, że włączona jest funkcja przyspieszonej sieci.

## <a name="powershell-creation"></a>Tworzenie programu PowerShell
## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zainstaluj program [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 1.0.0 lub nowszej. Aby znaleźć aktualnie zainstalowaną wersję, `Get-Module -ListAvailable Az`Uruchom polecenie. Jeśli musisz zainstalować lub uaktualnić, zainstaluj najnowszą wersję AZ module z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az). W sesji programu PowerShell Zaloguj się na konto platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów dołączone do *zasobów*, *myNic*i *myVM*.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *centralnej* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Najpierw utwórz konfigurację podsieci przy użyciu polecenie [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Poniższy przykład tworzy podsieć o nazwie Moja *podsieć*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Utwórz sieć wirtualną z poleceniem [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)z *podsiecią* podsieci.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Najpierw utwórz regułę sieciowej grupy zabezpieczeń z poleceniem [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Utwórz sieciową grupę zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) i przypisz do niej regułę *Zezwalaj-RDP-All* . Oprócz reguły *Zezwalaj-RDP-All* , sieciowa Grupa zabezpieczeń zawiera kilka reguł domyślnych. Jedna reguła domyślna powoduje wyłączenie całego dostępu przychodzącego z Internetu, co oznacza, że reguła *Zezwalaj-RDP-All* jest przypisana do sieciowej grupy zabezpieczeń, dzięki czemu można zdalnie połączyć się z maszyną wirtualną po jej utworzeniu.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Skojarz sieciową grupę zabezpieczeń z *podsiecią* o [wartości Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Reguła w sieciowej grupie zabezpieczeń jest skuteczna dla wszystkich zasobów wdrożonych w podsieci.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Tworzenie interfejsu sieciowego przy użyciu przyspieszonej sieci
Utwórz publiczny adres IP przy użyciu polecenia [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Publiczny adres IP nie jest wymagany, jeśli nie masz dostępu do maszyny wirtualnej z Internetu, ale do wykonania kroków opisanych w tym artykule jest to wymagane.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Utwórz interfejs sieciowy z opcją [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) z włączoną obsługą przyspieszonej sieci i przypisz publiczny adres IP do interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w podsieci z *podsiecią* sieci wirtualnej *myVnet* i przypisuje do niego publiczny adres IP *myPublicIp* :

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Skonfiguruj poświadczenia maszyny wirtualnej w `$cred` zmiennej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Najpierw Zdefiniuj swoją maszynę wirtualną za pomocą polecenie [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). W poniższym przykładzie zdefiniowano maszynę wirtualną o nazwie *myVM* z ROZMIAREM maszyny wirtualnej, który obsługuje przyspieszone sieci (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Aby uzyskać listę wszystkich rozmiarów maszyn wirtualnych i ich charakterystyk, zobacz [rozmiary maszyn wirtualnych z systemem Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Utwórz resztę konfiguracji maszyny wirtualnej za pomocą [opcji Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) i [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Poniższy przykład tworzy maszynę wirtualną z systemem Windows Server 2016:

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

Dołącz interfejs sieciowy, który został wcześniej utworzony przy użyciu [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Na koniec Utwórz maszynę wirtualną przy użyciu instrukcji [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Potwierdź, że sterownik jest zainstalowany w systemie operacyjnym

Po utworzeniu maszyny wirtualnej na platformie Azure Nawiąż połączenie z maszyną wirtualną i upewnij się, że sterownik jest zainstalowany w systemie Windows.

1. W przeglądarce internetowej Otwórz [Portal](https://portal.azure.com) Azure i zaloguj się przy użyciu konta platformy Azure.
2. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *myVm*. Gdy **myVm** pojawia się w wynikach wyszukiwania, kliknij je. Jeśli **Tworzenie** jest widoczne pod przyciskiem **Połącz** , platforma Azure nie zakończyła jeszcze tworzenia maszyny wirtualnej. Kliknij przycisk **Połącz** w lewym górnym rogu przeglądu, gdy nie widzisz już pozycji **Tworzenie** w obszarze przycisk **Połącz** .
3. Wprowadź nazwę użytkownika i hasło wprowadzone podczas [tworzenia maszyny wirtualnej](#create-the-virtual-machine). Jeśli nigdy nie nawiązano połączenia z maszyną wirtualną z systemem Windows na platformie Azure, zobacz [nawiązywanie połączenia z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Kliknij prawym przyciskiem myszy przycisk Start systemu Windows, a następnie kliknij przycisk **Menedżer urządzeń**. Rozwiń węzeł **karty sieciowe** . Upewnij się, że zostanie wyświetlona **karta Ethernet funkcji wirtualnej Mellanox ConnectX-3** , jak pokazano na poniższej ilustracji:

    ![Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

Przyspieszona sieć jest teraz włączona dla maszyny wirtualnej.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włączanie przyspieszonej sieci na istniejących maszynach wirtualnych
Jeśli utworzono maszynę wirtualną bez przyspieszonej sieci, można włączyć tę funkcję na istniejącej maszynie wirtualnej.  Maszyna wirtualna musi obsługiwać przyspieszone sieci, spełniając poniższe wymagania wstępne, które zostały opisane powyżej:

* Maszyna wirtualna musi być obsługiwanym rozmiarem dla przyspieszonej sieci
* Maszyna wirtualna musi być obsługiwanym obrazem galerii platformy Azure (i wersją jądra dla systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub VMSS muszą zostać zatrzymane/cofnięte alokacje przed włączeniem przyspieszonej sieci na dowolnej karcie sieciowej

### <a name="individual-vms--vms-in-an-availability-set"></a>Poszczególne maszyny wirtualne & maszyny wirtualne w zestawie dostępności
Najpierw Zatrzymaj/Cofnij przydział maszyny wirtualnej lub, jeśli zestaw dostępności, wszystkie maszyny wirtualne w zestawie:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Należy pamiętać, że jeśli maszyna wirtualna została utworzona osobno, bez zestawu dostępności, wystarczy zatrzymać/cofnąć alokację pojedynczej maszyny wirtualnej w celu włączenia przyspieszonej sieci.  Jeśli maszyna wirtualna została utworzona przy użyciu zestawu dostępności, wszystkie maszyny wirtualne znajdujące się w zestawie dostępności muszą zostać zatrzymane/cofnięte alokacje przed włączeniem przyspieszonej sieci na dowolnej z kart sieciowych. 

Po zatrzymaniu Włącz przyspieszone sieci na karcie sieciowej maszyny wirtualnej:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Uruchom ponownie maszynę wirtualną lub, jeśli znajduje się w zestawie dostępności, wszystkie maszyny wirtualne w zestawie i upewnij się, że włączona jest funkcja przyspieszonej sieci:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>Zestaw skalowania maszyn wirtualnych
VMSS jest nieco inny, ale następuje po tym samym przepływie pracy.  Najpierw Zatrzymaj maszyny wirtualne:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po zatrzymaniu maszyn wirtualnych zaktualizuj właściwość sieci przyspieszonej w interfejsie sieciowym:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Należy pamiętać, że VMSS ma uaktualnienia maszyny wirtualnej, które stosują aktualizacje przy użyciu trzech różnych ustawień, automatycznych, stopniowych i ręcznych.  W tych instrukcjach zasada jest ustawiona na automatyczne, dzięki czemu VMSS będzie pobierać zmiany natychmiast po ponownym uruchomieniu.  Aby ustawić automatyczne pobieranie zmian, aby zmiany zostały wprowadzone:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Na koniec Uruchom ponownie VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po ponownym uruchomieniu Zaczekaj na zakończenie uaktualnień, ale po jej zakończeniu zostanie wyświetlona w obrębie maszyny wirtualnej.  (Upewnij się, że korzystasz z obsługiwanego systemu operacyjnego i rozmiaru maszyny wirtualnej)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmienianie rozmiarów istniejących maszyn wirtualnych za pomocą przyspieszonej sieci

Zmiany rozmiaru maszyn wirtualnych z włączoną przyspieszoną siecią można zmienić tylko na maszyny wirtualne, które obsługują przyspieszone sieci.  

Nie można zmienić rozmiaru maszyny wirtualnej z włączoną obsługą przyspieszonej sieci do wystąpienia maszyny wirtualnej, która nie obsługuje przyspieszonej sieci przy użyciu operacji zmiany rozmiaru.  Zamiast tego, aby zmienić rozmiar jednej z tych maszyn wirtualnych:

* Zatrzymaj/Cofnij przydział maszyny wirtualnej lub jeśli znajduje się w zestawie dostępności/VMSS, Zatrzymaj/Cofnij przydział wszystkich maszyn wirtualnych w zestawie/VMSS.
* Przyspieszone sieci muszą być wyłączone na karcie sieciowej maszyny wirtualnej lub w zestawie dostępności/VMSS wszystkie maszyny wirtualne w zestawie/VMSS.
* Po wyłączeniu przyspieszonej sieci można przenieść zestaw VM/Availability/VMSS do nowego rozmiaru, który nie obsługuje przyspieszonej sieci i ponownego uruchomienia.
