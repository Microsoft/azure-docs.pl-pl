---
title: Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci — Azure PowerShell
description: Utwórz maszynę wirtualną z systemem Windows przy użyciu przyspieszonej sieci, aby znacznie poprawić wydajność sieci.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919973"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Tworzenie maszyny wirtualnej z systemem Windows z przyspieszoną siecią przy użyciu programu Azure PowerShell

W tym samouczku dowiesz się, jak utworzyć maszynę wirtualną z systemem Windows przy użyciu przyspieszonej sieci.

> [!NOTE]
> Aby używać przyspieszonej sieci z maszyną wirtualną z systemem Linux, zobacz [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](create-vm-accelerated-networking-cli.md).

Przyspieszona sieć umożliwia wirtualizację we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej, co znacznie zwiększa wydajność sieci. Ta ścieżka o wysokiej wydajności pomija hosta ze ścieżki danych, co zmniejsza opóźnienia, wahania i użycie procesora dla najbardziej wymagających obciążeń sieciowych na obsługiwanych typach maszyn wirtualnych. Na poniższym diagramie przedstawiono, jak dwie maszyny wirtualne komunikują się z usługą i bez przyspieszonej sieci:

![Komunikacja między maszynami wirtualnymi platformy Azure z usługą i bez przyspieszonej sieci](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonej sieci cały ruch sieciowy z i do maszyny wirtualnej musi przechodzić przez hosta i przełącznik wirtualny. Przełącznik wirtualny zapewnia wszystkie wymuszanie zasad, takie jak sieciowe grupy zabezpieczeń, listy kontroli dostępu, izolacja i inne zwirtualizowane w sieci usługi do ruchu sieciowego.

> [!NOTE]
> Aby dowiedzieć się więcej o przełącznikach wirtualnych, zobacz [przełącznik wirtualny funkcji Hyper-V](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Dzięki przyspieszonej sieci ruch sieciowy dociera do interfejsu sieciowego maszyny wirtualnej, a następnie jest przekazywany do maszyny wirtualnej. Wszystkie zasady sieciowe stosowane przez przełącznik wirtualny są teraz odciążanie i stosowane do sprzętu. Ponieważ zasady są stosowane na sprzęcie, karta sieciowa może przekazywać ruch sieciowy bezpośrednio do maszyny wirtualnej. Karta sieciowa pomija hosta i przełącznika wirtualnego, podczas gdy zachowuje wszystkie zasady, które zostały zastosowane na hoście.

Zalety przyspieszonej sieci mają zastosowanie tylko do maszyny wirtualnej, na której ta funkcja jest włączona. Aby uzyskać najlepsze wyniki, Włącz tę funkcję na co najmniej dwóch maszynach wirtualnych podłączonych do tej samej sieci wirtualnej platformy Azure. Podczas komunikowania się między sieciami wirtualnymi lub łączenia lokalnego ta funkcja ma minimalny wpływ na ogólne opóźnienia.

## <a name="benefits"></a>Korzyści

- **Mniejsze opóźnienia/wyższe pakiety na sekundę (PPS)**: usunięcie przełącznika wirtualnego ze ścieżki danych powoduje usunięcie pakietów czasu poświęcanych przez hosta w celu przetworzenia zasad. Zwiększa również liczbę pakietów, które można przetworzyć w ramach maszyny wirtualnej.

- **Ograniczone wahania**: przetwarzanie przełącznika wirtualnego zależy od ilości zasad, które należy zastosować. Zależy również od obciążenia procesora, który przetwarza przetwarzanie. Odciążanie wymuszania zasad sprzętowo eliminuje tę zmienność przez dostarczanie pakietów bezpośrednio do maszyny wirtualnej. Odciążanie powoduje również usunięcie komunikacji między hostami i MASZYNami wirtualnymi, wszystkich przerwań oprogramowania i wszystkich przełączników kontekstowych.

- **Zmniejszone użycie procesora**: obejście przełącznika wirtualnego na hoście prowadzi do mniejszego użycia procesora CPU do przetwarzania ruchu sieciowego.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Następujące dystrybucje są obsługiwane bezpośrednio z galerii platformy Azure:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych

Przyspieszona sieć jest obsługiwana w większości ogólnego przeznaczenia i o rozmiarach wystąpień zoptymalizowanych pod kątem obliczeń przy użyciu co najmniej dwóch procesorów wirtualnych vCPU. W wystąpieniach, które obsługują wielowątkowość, przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych vCPU. 

Obsługę przyspieszonej sieci można znaleźć w dokumentacji poszczególnych [rozmiarów maszyn wirtualnych](../virtual-machines/sizes.md) . 

### <a name="custom-images"></a>Obrazy niestandardowe

Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszone sieci, upewnij się, że masz wymagane sterowniki obsługujące karty sieciowe Mellanox ConnectX-3 i ConnectX-4 LX na platformie Azure.

### <a name="regions"></a>Regiony

Przyspieszona sieć jest dostępna we wszystkich globalnych regionach platformy Azure i w chmurze Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie przyspieszonej sieci na uruchomionej maszynie wirtualnej

W przypadku obsługiwanego rozmiaru maszyny wirtualnej bez przyspieszonej sieci można włączyć funkcję włączoną tylko wtedy, gdy została zatrzymana i cofnięta alokacja.

### <a name="deployment-through-azure-resource-manager"></a>Wdrożenie za Azure Resource Manager

Maszyn wirtualnych (klasycznych) nie można wdrożyć przy użyciu przyspieszonej sieci.

## <a name="vm-creation-using-the-portal"></a>Tworzenie maszyny wirtualnej przy użyciu portalu

Chociaż w tym artykule przedstawiono procedurę tworzenia maszyny wirtualnej z przyspieszoną siecią przy użyciu Azure PowerShell, można także [użyć Azure Portal do utworzenia maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , która umożliwia przyspieszenie sieci. Podczas tworzenia maszyny wirtualnej w portalu na stronie **Tworzenie maszyny wirtualnej** wybierz kartę **Sieć** . Ta karta zawiera opcję dla **przyspieszonej sieci**. W przypadku wybrania [obsługiwanego systemu operacyjnego](#supported-operating-systems) i [rozmiaru maszyny wirtualnej](#supported-vm-instances)ta opcja jest automatycznie ustawiana **na wartość włączone**. W przeciwnym razie opcja jest **wyłączona**, a platforma Azure wyświetli powód, dla którego nie można jej włączyć.

> [!NOTE]
> Tylko obsługiwane systemy operacyjne można włączyć za pomocą portalu. Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszone sieci, Utwórz maszynę wirtualną przy użyciu interfejsu wiersza polecenia lub programu PowerShell. 

Po utworzeniu maszyny wirtualnej można sprawdzić, czy jest włączona szybsza sieć. Postępuj zgodnie z tymi instrukcjami:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać maszynami wirtualnymi. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Na liście maszyn wirtualnych wybierz nową MASZYNę wirtualną.

3. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć**.

W oknie Informacje o interfejsie sieciowym obok skróconej etykiety **sieciowej** w portalu zostanie wyświetlona wartość **wyłączone** lub **włączone** dla przyspieszonego stanu sieci.

## <a name="vm-creation-using-powershell"></a>Tworzenie maszyny wirtualnej przy użyciu programu PowerShell

Przed kontynuowaniem Zainstaluj program [Azure PowerShell](/powershell/azure/install-az-ps) w wersji 1.0.0 lub nowszej. Aby znaleźć aktualnie zainstalowaną wersję, uruchom polecenie `Get-Module -ListAvailable Az` . Jeśli musisz zainstalować lub uaktualnić, zainstaluj najnowszą wersję AZ module z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az). W sesji programu PowerShell Zaloguj się do konta platformy Azure przy użyciu polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów dołączone do *zasobów*, *myNic* i *myVM*.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Następujące polecenie tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *centralnej* :

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Utwórz konfigurację podsieci przy użyciu elementu [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Następujące polecenie tworzy podsieć o nazwie Moja *podsieć*:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Utwórz sieć wirtualną z poleceniem [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)z *podsiecią* podsieci.

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
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

2. Utwórz sieciową grupę zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) i przypisz do niej regułę *Zezwalaj-RDP-All* . Poza regułą *Zezwalaj-RDP-All* , sieciowa Grupa zabezpieczeń zawiera kilka reguł domyślnych. Jedna reguła domyślna powoduje wyłączenie całego dostępu przychodzącego z Internetu. Po jego utworzeniu zostanie przypisana reguła *Zezwalaj-RDP-All* do sieciowej grupy zabezpieczeń, aby można było zdalnie połączyć się z maszyną wirtualną.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Skojarz sieciową grupę zabezpieczeń z *podsiecią* o [wartości Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Reguła w sieciowej grupie zabezpieczeń jest skuteczna dla wszystkich zasobów wdrożonych w podsieci.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Tworzenie interfejsu sieciowego przy użyciu przyspieszonej sieci

1. Utwórz publiczny adres IP przy użyciu polecenia [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Publiczny adres IP jest niezbędny, jeśli nie planujesz uzyskać dostępu do maszyny wirtualnej z Internetu. Jednak wymagane jest wykonanie kroków opisanych w tym artykule.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Utwórz interfejs sieciowy przy użyciu polecenie [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) z włączoną obsługą przyspieszonej sieci i przypisz publiczny adres IP do interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w podsieci z *podsiecią* sieci wirtualnej *myVnet* , przypisując do niego publiczny adres IP *myPublicIp* :

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Tworzenie maszyny wirtualnej i dołączanie interfejsu sieciowego

1. Skonfiguruj poświadczenia maszyny wirtualnej w `$cred` zmiennej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential), które wyświetli komunikat z prośbą o zalogowanie się:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Zdefiniuj swoją maszynę wirtualną za pomocą [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Następujące polecenie definiuje maszynę wirtualną o nazwie *myVM* z ROZMIAREM maszyny wirtualnej, który obsługuje przyspieszone sieci (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Aby uzyskać listę wszystkich rozmiarów maszyn wirtualnych i ich charakterystyk, zobacz [rozmiary maszyn wirtualnych z systemem Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Utwórz resztę konfiguracji maszyny wirtualnej za pomocą [opcji Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) i [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Następujące polecenie tworzy maszynę wirtualną z systemem Windows Server 2016:

    ```azurepowershell
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

4. Dołącz interfejs sieciowy, który został wcześniej utworzony przy użyciu [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Utwórz maszynę wirtualną przy użyciu [nowego AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Potwierdź, że kontroler Ethernet jest zainstalowany na maszynie wirtualnej z systemem Windows

Po utworzeniu maszyny wirtualnej na platformie Azure Nawiąż połączenie z maszyną wirtualną i upewnij się, że kontroler Ethernet jest zainstalowany w systemie Windows.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać maszynami wirtualnymi. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Na liście maszyn wirtualnych wybierz nową MASZYNę wirtualną.

3. Na stronie Przegląd maszyny wirtualnej, jeśli **stan** maszyny wirtualnej jest wyświetlany jako **Tworzenie**, zaczekaj na zakończenie tworzenia maszyny wirtualnej przez platformę Azure. **Stan** zostanie zmieniony na **uruchomiony** po zakończeniu tworzenia maszyny wirtualnej.

4. Na pasku narzędzi przegląd maszyny wirtualnej wybierz pozycję **Połącz**  >  **RDP**  >  **Pobierz plik RDP**.

5. Otwórz plik RDP, a następnie zaloguj się do maszyny wirtualnej przy użyciu poświadczeń wprowadzonych w sekcji [Tworzenie maszyny wirtualnej i dołączanie interfejsu sieciowego](#create-a-vm-and-attach-the-network-interface) . Jeśli nigdy nie nawiązano połączenia z maszyną wirtualną z systemem Windows na platformie Azure, zobacz [nawiązywanie połączenia z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. Gdy zostanie wyświetlona sesja pulpitu zdalnego dla maszyny wirtualnej, kliknij prawym przyciskiem myszy przycisk Start systemu Windows i wybierz **Device Manager**.

7. W oknie **Device Manager** rozwiń węzeł **karty sieciowe** .

8. Upewnij się, że zostanie wyświetlona **karta Ethernet funkcji wirtualnej Mellanox ConnectX-3** , jak pokazano na poniższej ilustracji:

    ![Karta Ethernet Mellanox ConnectX-3 z funkcją wirtualną, Nowa karta sieciowa dla przyspieszonej sieci, Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

Przyspieszona sieć jest teraz włączona dla maszyny wirtualnej.

> [!NOTE]
> Jeśli nie można uruchomić karty Mellanox, Otwórz monit administratora w sesji pulpitu zdalnego, a następnie wprowadź następujące polecenie:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włączanie przyspieszonej sieci na istniejących maszynach wirtualnych

Jeśli maszyna wirtualna została utworzona bez przyspieszonej sieci, możesz ją włączyć na istniejącej maszynie wirtualnej. Maszyna wirtualna musi obsługiwać przyspieszone sieci, spełniając poniższe wymagania wstępne, które są również opisane powyżej:

* Maszyna wirtualna musi być obsługiwanym rozmiarem dla przyspieszonej sieci.
* Maszyna wirtualna musi być obsługiwanym obrazem galerii platformy Azure (i wersją jądra dla systemu Linux).
* Przed włączeniem przyspieszonej sieci na dowolnej karcie sieciowej należy zatrzymać lub cofnąć alokację wszystkich maszyn wirtualnych w zestawie dostępności lub w zestawie skalowania maszyn wirtualnych.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Poszczególne maszyny wirtualne i maszyny wirtualne w zestawie dostępności

1. Zatrzymaj lub Zwolnij maszynę wirtualną lub, jeśli zestaw dostępności, wszystkie maszyny wirtualne w zestawie:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Podczas tworzenia maszyny wirtualnej oddzielnie, bez zestawu dostępności, należy zatrzymać lub cofnąć alokację pojedynczej maszyny wirtualnej, aby umożliwić szybsze działanie sieci. Jeśli maszyna wirtualna została utworzona z zestawem dostępności, należy zatrzymać lub cofnąć alokację wszystkich maszyn wirtualnych zawartych w zestawie dostępności przed włączeniem przyspieszonej sieci na dowolnej z kart sieciowych, aby maszyny wirtualne kończyły się w klastrze obsługującym przyspieszone sieci. Wymaganie zatrzymania lub cofnięcia alokacji jest niepotrzebne w przypadku wyłączenia sieci przyspieszonej, ponieważ Klastry obsługujące przyspieszone sieci również pracują z kartami sieciowymi, które nie używają przyspieszonej łączności sieciowej.

2. Włącz przyspieszone sieci na karcie sieciowej maszyny wirtualnej:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Uruchom ponownie maszynę wirtualną lub, jeśli znajduje się w zestawie dostępności, wszystkie maszyny wirtualne w zestawie i upewnij się, że włączona jest funkcja przyspieszonej sieci:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Zestaw skalowania maszyn wirtualnych

Zestaw skalowania maszyn wirtualnych jest nieco inny, ale znajduje się w tym samym przepływie pracy.

1. Zatrzymaj maszyny wirtualne:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Zaktualizuj Właściwość "przyspieszone sieci" w interfejsie sieciowym:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Ustaw stosowane aktualizacje automatycznie, aby zmiany zostały natychmiast pobrane:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Zestaw skalowania ma uaktualnienia maszyny wirtualnej, które stosują aktualizacje przy użyciu trzech różnych ustawień: automatycznych, stopniowanych i ręcznych. W tych instrukcjach zasady są ustawiane automatycznie, więc zestaw skalowania pobiera zmiany natychmiast po ponownym uruchomieniu.

4. Uruchom ponownie zestaw skalowania:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Po ponownym uruchomieniu Zaczekaj na zakończenie uaktualniania. Po zakończeniu uaktualnień funkcja wirtualna (VF) zostanie wyświetlona wewnątrz maszyny wirtualnej. Upewnij się, że używasz obsługiwanego systemu operacyjnego i rozmiaru maszyny wirtualnej.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmienianie rozmiarów istniejących maszyn wirtualnych za pomocą przyspieszonej sieci

Jeśli maszyna wirtualna ma włączoną funkcję przyspieszonej sieci, można zmienić jej rozmiar na maszynę wirtualną, która obsługuje przyspieszone sieci.  

Nie można zmienić rozmiaru maszyny wirtualnej z włączoną obsługą przyspieszonej sieci do wystąpienia maszyny wirtualnej, które nie obsługuje przyspieszonej sieci przy użyciu operacji zmiany rozmiaru. Zamiast tego, aby zmienić rozmiar jednej z tych maszyn wirtualnych:

1. Zatrzymaj lub Cofnij przydział maszyny wirtualnej. W przypadku zestawu dostępności lub zestawu skalowania Zatrzymaj lub Zwolnij wszystkie maszyny wirtualne w zestawie dostępności lub w zestawie skalowania.

2. Wyłączenie przyspieszonej sieci na karcie sieciowej maszyny wirtualnej. W przypadku zestawu dostępności lub zestawu skalowania należy wyłączyć przyspieszone sieci na kartach sieciowych wszystkich maszyn wirtualnych w zestawie dostępności lub w zestawie skalowania.

3. Po wyłączeniu przyspieszonej sieci Przenieś maszynę wirtualną, zestaw dostępności lub zestaw skalowania do nowego rozmiaru, który nie obsługuje przyspieszonej sieci, a następnie uruchom je ponownie.