---
title: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej VNet — samouczek Azure Portal
description: W tym samouczku dowiesz się, jak połączyć sieci wirtualne za pomocą komunikacji równorzędnej sieci wirtualnych z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: e95441aab6c8ce7de37ba5f6b08d5f7d54e13347
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017920"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Samouczek: łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych z użyciem witryny Azure Portal

Sieci wirtualne możesz łączyć ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Te sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (określane także jako globalne wirtualne sieci równorzędne). Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w obu sieciach wirtualnych mogą komunikować się ze sobą przy takim samym opóźnieniu i z taką samą przepustowością, jakby zasoby były w tej samej sieci wirtualnej. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie dwóch sieci wirtualnych
> * Łączenie dwóch sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych
> * Wdrażanie maszyny wirtualnej w każdej sieci wirtualnej
> * Nawiązywanie komunikacji między maszynami wirtualnymi

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-connect-virtual-networks-cli.md) lub [programu Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Na karcie **podstawowe** wprowadź lub wybierz poniższe informacje, a następnie zaakceptuj ustawienia domyślne dla pozostałych ustawień:

    |Ustawienie|Wartość|
    |---|---|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *myResourceGroup*.|
    |Region (Region)| Wybierz pozycję **Wschodnie stany USA**.|
    |Nazwa|myVirtualNetwork1|

4. Na karcie **adresy IP** wprowadź wartość 10.0.0.0/16 dla pola **przestrzeń adresowa** . Kliknij przycisk **Dodaj podsieć** poniżej i wprowadź *Subnet1* dla **nazwy podsieci** i 10.0.0.0/24 dla **zakresu adresów podsieci**.
5. Wybierz pozycję **Recenzja + Utwórz** , a następnie wybierz pozycję **Utwórz**.
   
5. Wykonaj ponownie kroki 1–5 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myVirtualNetwork2|
    |Przestrzeń adresowa|10.1.0.0/16|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Nazwa podsieci | Subnet2|
    |Zakres adresów podsieci|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Tworzenia komunikacji równorzędnej sieci wirtualnych

1. W polu wyszukiwania w górnej części witryny Azure Portal rozpocznij wpisywanie ciągu *myVirtualNetwork1*. Gdy pozycja **myVirtualNetwork1** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Komunikacja równorzędna**, w obszarze **Ustawienia**, a następnie wybierz pozycję **Dodaj**, jak pokazano na poniższej ilustracji:

    ![Tworzenie komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**.

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa komunikacji równorzędnej z myVirtualNetwork1 do zdalnej sieci wirtualnej|myVirtualNetwork1-myVirtualNetwork2 — po pierwszym załadowaniu strony w tym miejscu zobaczysz frazę "zdalna Sieć wirtualna". Po wybraniu zdalnej sieci wirtualnej fraza "zdalna Sieć wirtualna" zostanie zastąpiona nazwą zdalnej sieci wirtualnej.|
    |Subskrypcja| Wybierz subskrypcję.|
    |Sieć wirtualna|myVirtualNetwork2 — aby wybrać sieć wirtualną *myVirtualNetwork2* , wybierz pozycję **Sieć wirtualna**, a następnie wybierz pozycję **myVirtualNetwork2 (moja ResourceName)**. Możesz wybrać sieć wirtualną znajdującą się w tym samym regionie lub w innym regionie.|
    |Nazwa komunikacji równorzędnej z myVirtualNetwork2 do myVirtualNetwork1|myVirtualNetwork2-myVirtualNetwork1|

    ![Ustawienia komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    **Stan komunikacji równorzędnej** jest *połączony*, jak pokazano na poniższej ilustracji:

    ![Stan komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Jeśli stan nie jest widoczny, odśwież przeglądarkę.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną w każdej sieci wirtualnej, dzięki czemu będzie można komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. Możesz wybrać inny system operacyjny, ale w pozostałych krokach założono, że wybierzesz system **Windows Server 2016 Datacenter**. 
3. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Nazwa|myVm1|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   
4. Wybierz rozmiar maszyny wirtualnej dla opcji **rozmiar** .
5. Wybierz następujące wartości w obszarze **Sieć**:

    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| myVirtualNetwork1 — Jeśli nie została jeszcze wybrana, wybierz pozycję **Sieć wirtualna** , a następnie wybierz pozycję **myVirtualNetwork1**.|
    |Podsieć| Subnet1 — Jeśli nie została jeszcze wybrana, wybierz pozycję **podsieć** , a następnie wybierz pozycję **Subnet1**.|
   
6. Wybierz pozycję **Sieć**. Wybierz opcję **Zezwalaj na wybrane porty** dla **publicznych portów przychodzących** . Wybierz pozycję **RDP** dla opcji **Wybierz porty przychodzące** poniżej. 

7. Wybierz przycisk **Recenzja + Utwórz** w dolnym lewym rogu, aby rozpocząć wdrażanie maszyny wirtualnej.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Wykonaj ponownie kroki 1–6 z następującymi zmianami:

|Ustawienie|Wartość|
|---|---|
|Nazwa | myVm2|
|Sieć wirtualna | myVirtualNetwork2|

Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. W polu *Szukaj* w górnej części portalu rozpocznij wpisywanie ciągu *myVm1*. Gdy pozycja **myVm1** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Utwórz połączenie pulpitu zdalnego z maszyną wirtualną *myVm1*, wybierając pozycję **Połącz**, jak pokazano na poniższym obrazie:

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Aby nawiązać połączenie z maszyną wirtualną, otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.
4. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (może okazać się konieczne wybranie pozycji **Więcej opcji** oraz **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej), a następnie wybierz przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie.
6. W kolejnym kroku polecenie ping jest używane do komunikacji z maszyną wirtualną *myVm2* z poziomu maszyny wirtualnej *myVm1*. Polecenie ping używa protokołu ICMP (Internet Control Message Protocol), którego ruch jest domyślnie blokowany przez zaporę systemu Windows. Na maszynie wirtualnej *myVm1* wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, dzięki czemu w kolejnym kroku będzie można wykonać polecenie ping względem tej maszyny wirtualnej z poziomu maszyny wirtualnej *myVm2* przy użyciu programu PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    Chociaż w tym samouczku do komunikacji między maszynami wirtualnymi jest używane polecenie ping, nie zaleca się zezwalania na komunikację za pomocą protokołu ICMP w zaporze systemu Windows we wdrożeniach produkcyjnych.

7. Aby nawiązać połączenie z maszyną wirtualną *myVm2*, wprowadź następujące polecenie w wierszu polecenia maszyny wirtualnej *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Ponieważ polecenie ping zostało włączone na maszynie wirtualnej *myVm1*, możesz teraz wykonać polecenie ping przy użyciu adresu IP:

    ```
    ping 10.0.0.4
    ```
    
9. Odłącz swoje sesje protokołu RDP do obu maszyn wirtualnych, *myVm1* i *myVm2*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją wraz ze wszystkimi zasobami, które zawiera: 

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób połączenia dwóch sieci w tym samym regionie świadczenia usługi Azure za pomocą komunikacji równorzędnej sieci wirtualnych. Możesz też nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcjach platformy Azure](create-peering-different-subscriptions.md#portal), a także utworzyć [projekty sieci w topologii gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) z komunikacją równorzędną. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Virtual network peering overview (Omówienie komunikacji równorzędnej sieci wirtualnych)](virtual-network-peering-overview.md) i [Manage virtual network peerings (Zarządzanie komunikacją równorzędną sieci wirtualnych)](virtual-network-manage-peering.md).

Aby połączyć własny komputer z siecią wirtualną za pośrednictwem sieci VPN i mieć możliwość interakcji z zasobami w sieci wirtualnej lub w sieciach wirtualnych z komunikacją równorzędną, zobacz [Connect your computer to a virtual network (Łączenie komputera z siecią wirtualną)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
