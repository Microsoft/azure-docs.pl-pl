---
title: Tworzenie komunikacji równorzędnej sieci wirtualnej platformy Azure — różne modele wdrażania — ta sama subskrypcja | Microsoft Docs
description: Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania platformy Azure, które znajdują się w tej samej subskrypcji platformy Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 148d57da549e8364620c8417cbd61d975cea1498
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87046095"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Tworzenie komunikacji równorzędnej sieci wirtualnej — różne modele wdrażania, ta sama subskrypcja

W ramach tego samouczka nauczysz się tworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Obie sieci wirtualne istnieją w tej samej subskrypcji. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasobom w różnych sieciach wirtualnych komunikowanie się ze sobą za pomocą tej samej przepustowości i opóźnienia, tak jakby zasoby znajdowały się w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacji równorzędnej sieci wirtualnych](virtual-network-peering-overview.md).

Procedurę tworzenia komunikacji równorzędnej sieci wirtualnej różnią się w zależności od tego, czy sieci wirtualne znajdują się w tym samym systemie, czy w różnych subskrypcjach i [modelu wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , w którym są tworzone sieci wirtualne. Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej w innych scenariuszach, klikając scenariusz w poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Resource Manager — w obu przypadkach](tutorial-connect-virtual-networks-portal.md) |Ta sama|
|[Resource Manager — w obu przypadkach](create-peering-different-subscriptions.md) |Różne|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi wdrożonymi przy użyciu klasycznego modelu wdrażania. Aby połączyć sieci wirtualne, które zostały utworzone przy użyciu klasycznego modelu wdrażania, można użyć [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure do połączenia sieci wirtualnych.

Ten samouczek umożliwia równorzędne sieci wirtualne w tym samym regionie. Można także równorzędne sieci wirtualne w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region). Zaleca się zapoznanie się z [wymaganiami i ograniczeniami komunikacji równorzędnej](virtual-network-manage-peering.md#requirements-and-constraints) przed równorzędnymi sieciami wirtualnymi.

Za pomocą Azure Portal, [interfejsu wiersza polecenia](#cli) platformy Azure, [programu Azure PowerShell](#powershell)lub szablonu Azure Resource Manager do tworzenia komunikacji równorzędnej sieci wirtualnej. Kliknij dowolne z poprzednich linków narzędzi, aby przejść bezpośrednio do procedury tworzenia komunikacji równorzędnej sieci wirtualnej przy użyciu wybranego przez siebie narzędzia.

## <a name="create-peering---azure-portal"></a>Tworzenie komunikacji równorzędnej — Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#requirements-and-constraints).
2. Kliknij pozycję **+ Nowy**, kliknij pozycję **Sieć**, a następnie kliknij pozycję **Sieć wirtualna**.
3. W bloku **Utwórz sieć wirtualną** wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij pozycję **Utwórz**:
    - **Nazwa**: *myVnet1*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *Domyślna*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: wybierz subskrypcję
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i *wprowadź grupę zasobów.*
    - **Lokalizacja**: *Wschodnie stany USA*
4. Kliknij pozycję **+ Nowy**. W polu **Wyszukaj w witrynie Marketplace** wpisz *sieć wirtualną*. Kliknij pozycję **Sieć wirtualna** , gdy zostanie wyświetlona w wynikach wyszukiwania.
5. W bloku **Sieć wirtualna** wybierz pozycję **klasyczny** w polu **Wybierz model wdrażania** , a następnie kliknij przycisk **Utwórz**.
6. W bloku **Utwórz sieć wirtualną** wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij pozycję **Utwórz**:
    - **Nazwa**: *myVnet2*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *Domyślna*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: wybierz subskrypcję
    - **Grupa zasobów**: wybierz pozycję **Użyj istniejącej** i wybierz pozycję Moja *resourceName*
    - **Lokalizacja**: *Wschodnie stany USA*
7. W polu **Wyszukaj zasoby** w górnej części portalu wpisz polecenie Moja *resourceName*. Kliknij pozycję Moja **zasobów** , gdy zostanie ona wyświetlona w wynikach wyszukiwania. Zostanie wyświetlony blok dla grupy **zasobów zasobu** . Grupa zasobów zawiera dwie sieci wirtualne utworzone w poprzednich krokach.
8. Kliknij pozycję **myVNet1**.
9. W wyświetlonym bloku **myVnet1** kliknij pozycję **Komunikacja równorzędna** z listy pionowych opcji po lewej stronie bloku.
10. W bloku **myVnet1-Peers** , który pojawił się, kliknij pozycję **+ Dodaj** .
11. W wyświetlonym bloku **Dodaj komunikację równorzędną** wprowadź lub wybierz poniższe opcje, a następnie kliknij przycisk **OK**:
     - **Nazwa**: *myVnet1ToMyVnet2*
     - **Model wdrażania sieci wirtualnej**: wybierz pozycję **klasyczne**.
     - **Subskrypcja**: wybierz subskrypcję
     - **Sieć wirtualna**: kliknij pozycję **Wybierz sieć wirtualną**, a następnie kliknij pozycję **myVnet2**.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że jest zaznaczona **Funkcja włączona** .
    W tym samouczku nie są używane żadne inne ustawienia. Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej, zobacz [Zarządzanie równorzędnymi sieciami wirtualnymi](virtual-network-manage-peering.md#create-a-peering).
12. Po kliknięciu przycisku **OK** w poprzednim kroku blok **Dodawanie komunikacji równorzędnej** zostanie zamknięty i ponownie zobaczysz blok **myVnet1-Peers** . Po kilku sekundach utworzone połączenie równorzędne zostanie wyświetlone w bloku. **Połączono** w kolumnie **Stan komunikacji równorzędnej** dla utworzonej komunikacji równorzędnej **myVnet1ToMyVnet2** .

    Komunikacja równorzędna została ustanowiona. Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie będą w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcjonalne**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
14. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj kroki opisane w sekcji [usuwanie zasobów](#delete-portal) w tym artykule.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Tworzenie komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure

Wykonaj następujące kroki, korzystając z klasycznego interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia platformy Azure. Kroki opisane w Azure Cloud Shell można wykonać, zaznaczając przycisk **Wypróbuj** w jednym z następujących kroków lub instalując [klasyczny interfejs wiersza polecenia](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) i [interfejs wiersza](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) polecenia i uruchamiając polecenie na komputerze lokalnym.

1. Jeśli używasz Cloud Shell, przejdź do kroku 2, ponieważ Cloud Shell automatycznie loguje się do platformy Azure. Otwórz sesję polecenia i zaloguj się do platformy Azure przy użyciu `azure login` polecenia.
2. Uruchom interfejs wiersza polecenia w trybie zarządzania usługami, wprowadzając `azure config mode asm` polecenie.
3. Wprowadź następujące polecenie, aby utworzyć sieć wirtualną (klasyczną):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Wykonaj następujący skrypt interfejsu wiersza polecenia bash przy użyciu interfejsu wiersza polecenia, a nie klasycznego interfejsu wiersza polecenia. Aby uzyskać opcje uruchamiania skryptów interfejsu wiersza polecenia bash na komputerze z systemem Windows, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Utwórz komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania przy użyciu interfejsu wiersza polecenia. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<subscription id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` polecenie. Wartość **identyfikatora** w danych wyjściowych to identyfikator subskrypcji. Wklej zmodyfikowany skrypt w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` .

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. Po wykonaniu skryptu Przejrzyj komunikację równorzędną dla sieci wirtualnej (Menedżer zasobów). Skopiuj następujące polecenie, wklej je w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` :

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Dane wyjściowe są **wyświetlane w** kolumnie **PeeringState** .

   Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie będą w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Opcjonalne**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
8. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj czynności opisane w temacie [usuwanie zasobów](#delete-cli) w tym artykule.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Tworzenie komunikacji równorzędnej — PowerShell

1. Zainstaluj najnowszą wersję środowiska PowerShell [platformy Azure](https://www.powershellgallery.com/packages/Azure) i [AZ](https://www.powershellgallery.com/packages/Az/) modules. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Rozpocznij sesję programu PowerShell.
3. W programie PowerShell Zaloguj się do platformy Azure, wprowadzając `Add-AzureAccount` polecenie. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#requirements-and-constraints).
4. Aby utworzyć sieć wirtualną (klasyczną) przy użyciu programu PowerShell, należy utworzyć nową lub zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się [, jak eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujący element **VirtualNetworkSite** dla sieci wirtualnej używanej w tym samouczku:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Zaimportowanie zmienionego pliku konfiguracji sieci może spowodować zmianę istniejących sieci wirtualnych (klasycznych) w Twojej subskrypcji. Upewnij się, że dodano tylko poprzednią sieć wirtualną, ale nie wszystkie istniejące sieci wirtualne nie są zmieniane ani usuwane z subskrypcji.
5. Zaloguj się do platformy Azure, aby utworzyć sieć wirtualną (Menedżer zasobów), wprowadzając `Connect-AzAccount` polecenie. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#requirements-and-constraints).
6. Utwórz grupę zasobów i sieć wirtualną (Menedżer zasobów). Skopiuj skrypt, wklej go do programu PowerShell, a następnie naciśnij klawisz `Enter` .

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Utwórz komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<subscription id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby je wyświetlić. Wartość **identyfikatora** w zwracanym wyniku to identyfikator subskrypcji. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt z edytora tekstów, a następnie kliknij prawym przyciskiem myszy sesję programu PowerShell, a następnie naciśnij klawisz `Enter` .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po wykonaniu skryptu Przejrzyj komunikację równorzędną dla sieci wirtualnej (Menedżer zasobów). Skopiuj następujące polecenie, wklej je w sesji programu PowerShell, a następnie naciśnij klawisz `Enter` :

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Dane wyjściowe są **wyświetlane w** kolumnie **PeeringState** .

    Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie będą w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcjonalne**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
10. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj czynności opisane w temacie [usuwanie zasobów](#delete-powershell) w tym artykule.

## <a name="delete-resources"></a><a name="delete"></a>Usuwanie zasobów

Po zakończeniu tego samouczka możesz chcieć usunąć zasoby utworzone w samouczku, dzięki czemu nie zostaną naliczone opłaty za użycie. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie zasobów.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. W polu wyszukiwania portalu wprowadź wartość **WebResource**. W wynikach wyszukiwania kliknij pozycję Moja **resourceName**.
2. W bloku moje **zasoby** kliknij ikonę **Usuń** .
3. Aby potwierdzić usunięcie, w polu **wpisz nazwę grupy zasobów** **Wprowadź,** a następnie kliknij przycisk **Usuń**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Użyj interfejsu wiersza polecenia platformy Azure, aby usunąć sieć wirtualną (Menedżer zasobów) za pomocą następującego polecenia:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Użyj klasycznego interfejsu wiersza polecenia, aby usunąć sieć wirtualną (klasyczną) przy użyciu następujących poleceń:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Wprowadź następujące polecenie, aby usunąć sieć wirtualną (Menedżer zasobów):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Aby usunąć sieć wirtualną (klasyczną) przy użyciu programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się [, jak eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używanej w tym samouczku:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Zaimportowanie zmienionego pliku konfiguracji sieci może spowodować zmianę istniejących sieci wirtualnych (klasycznych) w Twojej subskrypcji. Upewnij się, że usuniesz tylko poprzednią sieć wirtualną, ale nie zmienisz ani nie usuniesz żadnych innych istniejących sieci wirtualnych z subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Dokładnie zapoznaj się z ważnymi [ograniczeniami i zachowaniami komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem komunikacji równorzędnej sieci wirtualnej do użycia w środowisku produkcyjnym.
- Dowiedz się więcej na temat wszystkich [ustawień komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [utworzyć topologię sieci piasty i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) przy użyciu komunikacji równorzędnej sieci wirtualnych.
