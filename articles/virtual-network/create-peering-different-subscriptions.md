---
title: Tworzenie komunikacji równorzędnej sieci wirtualnej — różne subskrypcje
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć sieć wirtualną sieci równorzędnej między sieciami wirtualnymi utworzonymi za pomocą Menedżer zasobów, które istnieją w różnych subskrypcjach platformy Azure w tej samej lub innej dzierżawie Azure Active Directory.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 1f8b7162787e870337918794f353ab8dc94c1965
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518249"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>Tworzenie komunikacji równorzędnej sieci wirtualnej — Menedżer zasobów, różne subskrypcje i dzierżawy Azure Active Directory

W ramach tego samouczka nauczysz się tworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzonymi za pomocą Menedżer zasobów. Sieci wirtualne istnieją w różnych subskrypcjach, które mogą należeć do różnych dzierżaw Azure Active Directory (Azure AD). Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasobom w różnych sieciach wirtualnych komunikowanie się ze sobą za pomocą tej samej przepustowości i opóźnienia, tak jakby zasoby znajdowały się w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacji równorzędnej sieci wirtualnych](virtual-network-peering-overview.md).

Procedurę tworzenia komunikacji równorzędnej sieci wirtualnej różnią się w zależności od tego, czy sieci wirtualne znajdują się w tym samym systemie, czy w różnych subskrypcjach i [modelu wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , w którym są tworzone sieci wirtualne. Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej w innych scenariuszach, wybierając scenariusz z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Resource Manager — w obu przypadkach](tutorial-connect-virtual-networks-portal.md) |Ta sama|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models.md) |Ta sama|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi wdrożonymi przy użyciu klasycznego modelu wdrażania. Aby połączyć sieci wirtualne, które zostały utworzone przy użyciu klasycznego modelu wdrażania, można użyć [VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure do połączenia sieci wirtualnych.

Ten samouczek umożliwia równorzędne sieci wirtualne w tym samym regionie. Można także równorzędne sieci wirtualne w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region). Zaleca się zapoznanie się z [wymaganiami i ograniczeniami komunikacji równorzędnej](virtual-network-manage-peering.md#requirements-and-constraints) przed równorzędnymi sieciami wirtualnymi.

Za pomocą [Azure Portal](#portal), [interfejsu wiersza polecenia](#cli) platformy Azure, [programu Azure PowerShell](#powershell)lub [szablonu Azure Resource Manager](#template) do tworzenia komunikacji równorzędnej sieci wirtualnej. Wybierz dowolne z poprzednich linków narzędzi, aby przejść bezpośrednio do procedury tworzenia komunikacji równorzędnej sieci wirtualnej przy użyciu wybranego przez siebie narzędzia.

Jeśli sieci wirtualne znajdują się w różnych subskrypcjach, a subskrypcje są skojarzone z różnymi dzierżawcami Azure Active Directory, przed kontynuowaniem wykonaj następujące czynności:
1. Dodaj użytkownika z każdej Active Directory dzierżawy jako [gość](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w przypadku przeciwległej dzierżawy Azure Active Directoryowej.
1. Każdy użytkownik musi zaakceptować zaproszenie użytkownika-gościa od przeciwległej dzierżawy usługi Azure Active Directory.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Tworzenie komunikacji równorzędnej — Azure Portal

Poniższe kroki używają różnych kont dla każdej subskrypcji. Jeśli używasz konta z uprawnieniami do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć procedurę rejestrowania w portalu i pominąć procedurę przypisywania innych uprawnień użytkownika do sieci wirtualnych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako *usera*. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
2. Wybierz pozycję **+ Utwórz zasób**, wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wybierz lub wprowadź Poniższe przykładowe wartości dla następujących ustawień, a następnie wybierz pozycję **Utwórz**:
    - **Nazwa**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *Domyślna*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: wybierz pozycję subskrypcja A.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i wprowadź *myResourceGroupA*
    - **Lokalizacja**: *Wschodnie stany USA*
4. W polu **Wyszukaj zasoby** w górnej części portalu wpisz *myVnetA*. Wybierz pozycję **myVnetA** , gdy zostanie ona wyświetlona w wynikach wyszukiwania.
5. Wybierz pozycję **Kontrola dostępu (IAM)** z pionowej listy opcji po lewej stronie.
6. W obszarze **myVnetA — kontrola dostępu (IAM)** wybierz pozycję **+ Dodaj przypisanie roli**.
7. Wybierz opcję **współautor sieci** w polu **rola** .
8. W polu **Wybierz** wybierz pozycję *UserB* lub wpisz adres e-mail UserB, aby go wyszukać.
9. Wybierz pozycję **Zapisz**.
10. W obszarze **myVnetA — kontrola dostępu (IAM)** wybierz pozycję **Właściwości** z pionowej listy opcji po lewej stronie. Skopiuj **Identyfikator zasobu**, który jest używany w późniejszym kroku. Identyfikator zasobu jest podobny do następującego przykładu: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA` .
11. Wyloguj się z portalu jako UserA, a następnie zaloguj się jako UserB.
12. Wykonaj kroki 2-3, wprowadzając lub wybierając następujące wartości w kroku 3:

    - **Nazwa**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *Domyślna*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: wybierz subskrypcję B.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i wprowadź *myResourceGroupB*
    - **Lokalizacja**: *Wschodnie stany USA*

13. W polu **Wyszukaj zasoby** w górnej części portalu wpisz *myVnetB*. Wybierz pozycję **myVnetB** , gdy zostanie ona wyświetlona w wynikach wyszukiwania.
14. W obszarze **myVnetB** wybierz pozycję **Właściwości** z pionowej listy opcji po lewej stronie. Skopiuj **Identyfikator zasobu**, który jest używany w późniejszym kroku. Identyfikator zasobu jest podobny do następującego przykładu: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB` .
15. Wybierz pozycję **Kontrola dostępu (IAM)** w obszarze **myVnetB**, a następnie wykonaj kroki 5-10 dla myVnetB, wprowadzając **usera** w kroku 8.
16. Wyloguj się z portalu jako UserB i zaloguj się jako UserA.
17. W polu **Wyszukaj zasoby** w górnej części portalu wpisz *myVnetA*. Wybierz pozycję **myVnetA** , gdy zostanie ona wyświetlona w wynikach wyszukiwania.
18. Wybierz pozycję **myVnetA**.
19. W obszarze **Ustawienia** wybierz pozycję **Komunikacja równorzędna**.
20. W obszarze **myVnetA — Komunikacja równorzędna** wybierz pozycję **+ Dodaj** .
21. W obszarze **Dodaj komunikację równorzędną**, wprowadź lub wybierz poniższe opcje, a następnie wybierz przycisk **OK**:
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrażania sieci wirtualnej**: wybierz pozycję **Menedżer zasobów**.
     - **Wiemy o identyfikatorze zasobu**: zaznacz to pole wyboru.
     - **Identyfikator zasobu**: Wprowadź identyfikator zasobu z kroku 14.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że jest zaznaczona **Funkcja włączona** .
    W tym samouczku nie są używane żadne inne ustawienia. Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej, zobacz [Zarządzanie równorzędnymi sieciami wirtualnymi](virtual-network-manage-peering.md#create-a-peering).
22. Po wybraniu przycisku **OK** w poprzednim kroku pojawi się krótki czas oczekiwania. **Zainicjowany** jest wymieniony w kolumnie **Stan komunikacji równorzędnej** dla utworzonej komunikacji równorzędnej **myVnetAToMyVnetB** . Połączono z usługą myVnetA do myVnetB, ale teraz musisz być elementem równorzędnym myVnetB do myVnetA. Komunikacja równorzędna musi być utworzona w obu kierunkach, aby umożliwić komunikację między zasobami w sieciach wirtualnych.
23. Wyloguj się z portalu jako UserA i zaloguj się jako UserB.
24. Ponownie wykonaj kroki 17-21 dla myVnetB. W kroku 21 Nazwij pozycję Komunikacja równorzędna *myVnetBToMyVnetA*, wybierz pozycję *MyVnetA* dla **sieci wirtualnej**, a następnie wprowadź identyfikator z kroku 10 w polu **Identyfikator zasobu** .
25. Kilka sekund po wybraniu przycisku **OK** w celu utworzenia komunikacji równorzędnej dla myVnetB, utworzona Komunikacja równorzędna **myVnetBToMyVnetA** zostanie wyświetlona na liście **połączone** w kolumnie **Stan komunikacji równorzędnej** .
26. Wyloguj się z portalu jako UserB i zaloguj się jako UserA.
27. Ponownie wykonaj kroki 17-19. **Stan komunikacji równorzędnej** dla komunikacji równorzędnej **myVnetAToVNetB** jest teraz również **połączony**. Komunikacja równorzędna została pomyślnie ustanowiona po wyświetleniu **połączenia** w kolumnie **Stan komunikacji równorzędnej** dla obu sieci wirtualnych w komunikacji równorzędnej. Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie mogą rozpoznawać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
29. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj kroki opisane w sekcji [usuwanie zasobów](#delete-portal) w tym artykule.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Tworzenie komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure

W tym samouczku są stosowane różne konta dla każdej subskrypcji. Jeśli używasz konta z uprawnieniami do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć procedurę rejestrowania z platformy Azure i usunąć wiersze skryptu, które tworzą przypisania roli użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptach nazwami użytkowników, których używasz do usera i UserB.

Następujące skrypty:

- Wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli musisz przeprowadzić uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Działa w bash Shell. Aby uzyskać informacje na temat opcji uruchamiania skryptów interfejsu wiersza polecenia platformy Azure w ramach klienta systemu Windows, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).

Zamiast instalować interfejs wiersza polecenia i jego zależności, można użyć Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w skrypcie poniżej, który wywołuje Cloud Shell, do którego możesz zalogować się na koncie platformy Azure za pomocą usługi.

1. Otwórz sesję interfejsu wiersza polecenia i zaloguj się na platformie Azure jako UserA przy użyciu `azure login` poleceniu. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
2. Skopiuj następujący skrypt do edytora tekstu na komputerze, Zastąp ciąg `<SubscriptionA-Id>` identyfikatorem subskrypcji, a następnie skopiuj zmodyfikowany skrypt, wklej go w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` . Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` polecenie. Wartość **identyfikatora** w danych wyjściowych to identyfikator subskrypcji.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Wyloguj się z platformy Azure jako UserA za pomocą `az logout` polecenia, a następnie zaloguj się do platformy Azure jako UserB. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
4. Utwórz myVnetB. Skopiuj zawartość skryptu w kroku 2 do edytora tekstu na komputerze. Zamień na `<SubscriptionA-Id>` Identyfikator SubscriptionB. Zmień wartość 10.0.0.0/16 na 10.1.0.0/16, Zmień wszystkie jako na B i wszystkie BS na. Skopiuj zmodyfikowany skrypt, wklej go w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` .
5. Wyloguj się z platformy Azure jako UserB i zaloguj się na platformie Azure jako UserA.
6. Utwórz sieć wirtualną sieci równorzędnej z myVnetA do myVnetB. Skopiuj poniższą zawartość skryptu do edytora tekstu na komputerze. Zamień na `<SubscriptionB-Id>` Identyfikator SubscriptionB. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go do sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz ENTER.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Wyświetl stan komunikacji równorzędnej myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Stan został **zainicjowany**. **Po utworzeniu** komunikacji równorzędnej z MyVnetA z myVnetB zostanie ona zmieniona.

8. Wyloguj się z UserA z platformy Azure i zaloguj się na platformie Azure jako UserB.
9. Utwórz komunikację równorzędną od myVnetB do myVnetA. Skopiuj zawartość skryptu w kroku 6 do edytora tekstu na komputerze. Zamień na `<SubscriptionB-Id>` Identyfikator subskrypcji i Zmień wszystkie na wartość B i wszystkie BS na. Po wprowadzeniu zmian Skopiuj zmodyfikowany skrypt, wklej go do sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` .
10. Wyświetl stan komunikacji równorzędnej myVnetB. Skopiuj zawartość skryptu w kroku 7 do edytora tekstu na komputerze. Zmień wartość na B dla nazwy grupy zasobów i sieci wirtualnej, skopiuj skrypt, wklej zmodyfikowany skrypt w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` . Stan komunikacji równorzędnej jest **połączony**. Stan komunikacji równorzędnej myVnetA zmienia się na **połączony** po utworzeniu komunikacji równorzędnej z MyVnetB do myVnetA. Możesz zarejestrować UserA ponownie na platformie Azure i ponownie wykonać krok 7, aby zweryfikować stan komunikacji równorzędnej myVnetA.

    > [!NOTE]
    > Komunikacja równorzędna nie zostanie nawiązana, dopóki stan komunikacji równorzędnej nie zostanie **połączony** z obydwoma sieciami wirtualnymi.

11. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
12. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj czynności opisane w temacie [usuwanie zasobów](#delete-cli) w tym artykule.

Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie mogą rozpoznawać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Tworzenie komunikacji równorzędnej — PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym samouczku są stosowane różne konta dla każdej subskrypcji. Jeśli używasz konta z uprawnieniami do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć procedurę rejestrowania z platformy Azure i usunąć wiersze skryptu, które tworzą przypisania roli użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptach nazwami użytkowników, których używasz do usera i UserB.

1. Upewnij się, że masz Azure PowerShell w wersji 1.0.0 lub nowszej. Można to zrobić, uruchamiając `Get-Module -Name Az` zalecaną instalację najnowszej wersji polecenia [AZ module](/powershell/azure/install-az-ps)programu PowerShell. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Rozpocznij sesję programu PowerShell.
3. W programie PowerShell Zaloguj się do platformy Azure jako UserA, wprowadzając `Connect-AzAccount` polecenie. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
4. Utwórz grupę zasobów i sieć wirtualną A. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień na `<SubscriptionA-Id>` Identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby je wyświetlić. Wartość **identyfikatora** w zwracanym wyniku to identyfikator subskrypcji. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go w programie PowerShell, a następnie naciśnij klawisz `Enter` .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Wyloguj się z UserA z platformy Azure i zaloguj się w UserB. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
6. Skopiuj zawartość skryptu w kroku 4 do edytora tekstu na komputerze. Zamień na `<SubscriptionA-Id>` Identyfikator subskrypcji B. Zmień wartość 10.0.0.0/16 na 10.1.0.0/16. Zmień wartość wszystkie na B i wszystkie BS na. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej do programu PowerShell, a następnie naciśnij klawisz `Enter` .
7. Wyloguj się z UserB z platformy Azure i zaloguj się w UserA.
8. Utwórz komunikację równorzędną od myVnetA do myVnetB. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień na `<SubscriptionB-Id>` Identyfikator subskrypcji B. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej do programu PowerShell, a następnie naciśnij klawisz `Enter` .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Wyświetl stan komunikacji równorzędnej myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stan został **zainicjowany**. Zmiana zostanie **połączona** po skonfigurowaniu komunikacji równorzędnej na MyVnetA z myVnetB.

10. Wyloguj się z UserA z platformy Azure i zaloguj się w UserB.
11. Utwórz komunikację równorzędną od myVnetB do myVnetA. Skopiuj zawartość skryptu w kroku 8 do edytora tekstu na komputerze. Zamień na `<SubscriptionB-Id>` Identyfikator subskrypcji a i zmień wartość wszystkie na B i wszystkie BS na. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go w programie PowerShell, a następnie naciśnij klawisz `Enter` .
12. Wyświetl stan komunikacji równorzędnej myVnetB. Skopiuj zawartość skryptu w kroku 9 do edytora tekstu na komputerze. Zmień wartość na B dla nazwy grupy zasobów i sieci wirtualnej. Aby wykonać skrypt, wklej zmodyfikowany skrypt do programu PowerShell, a następnie naciśnij klawisz `Enter` . Stan jest **połączony**. Stan komunikacji równorzędnej **myVnetA** zmienia się na **połączony** po utworzeniu komunikacji równorzędnej z **myVnetB** do **myVnetA**. Możesz zarejestrować UserA ponownie na platformie Azure i ponownie wykonać krok 9, aby zweryfikować stan komunikacji równorzędnej myVnetA.

    > [!NOTE]
    > Komunikacja równorzędna nie zostanie nawiązana, dopóki stan komunikacji równorzędnej nie zostanie **połączony** z obydwoma sieciami wirtualnymi.

    Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie mogą rozpoznawać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
14. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj czynności opisane w temacie [usuwanie zasobów](#delete-powershell) w tym artykule.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Tworzenie komunikacji równorzędnej — szablon Menedżer zasobów

1. Aby utworzyć sieć wirtualną i przypisać odpowiednie [uprawnienia](virtual-network-manage-peering.md#permissions), wykonaj kroki opisane w sekcji [Portal](#portal), [interfejsu wiersza polecenia platformy Azure](#cli)lub programie [PowerShell](#powershell) .
2. Zapisz poniższy tekst w pliku na komputerze lokalnym. Zamień `<subscription ID>` na identyfikator subskrypcji usera. Plik można zapisać jako vnetpeeringA.jsna przykład.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Zaloguj się do platformy Azure jako UserA i Wdróż szablon przy użyciu [portalu](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [programu PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file)lub [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file). Określ nazwę pliku, na przykład tekst JSON zapisany w kroku 2 do.
4. Skopiuj przykładowy kod JSON z kroku 2 do pliku na komputerze i wprowadź zmiany w wierszach rozpoczynających się od:
   - **name**: Zmień *myVnetA/myVnetAToMyVnetB* na *myVnetB/myVnetBToMyVnetA*.
   - **Identyfikator**: ZAstąp `<subscription ID>` identyfikatorem subskrypcji UserB i Zmień *myVnetB* na *myVnetA*.
5. Wykonaj ponownie krok 3, zalogowany na platformie Azure jako UserB.
6. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
7. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj kroki opisane w sekcji [usuwanie zasobów](#delete) tego artykułu przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="delete-resources"></a><a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka możesz chcieć usunąć zasoby utworzone w samouczku, dzięki czemu nie zostaną naliczone opłaty za użycie. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie zasobów.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Zaloguj się do Azure Portal jako UserA.
2. W polu wyszukiwania portalu wprowadź **myResourceGroupA**. W wynikach wyszukiwania wybierz pozycję **myResourceGroupA**.
3. Wybierz pozycję **Usuń**.
4. Aby potwierdzić usunięcie, w polu **wpisz nazwę grupy zasobów** wpisz **myResourceGroupA**, a następnie wybierz pozycję **Usuń**.
5. Wyloguj się z portalu jako UserA i zaloguj się jako UserB.
6. Wykonaj kroki 2-4 dla myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Zaloguj się do platformy Azure jako UserA i wykonaj następujące polecenie:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Wyloguj się z platformy Azure jako UserA i zaloguj się jako UserB.
3. Uruchom następujące polecenie:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Zaloguj się do platformy Azure jako UserA i wykonaj następujące polecenie:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Wyloguj się z platformy Azure jako UserA i zaloguj się jako UserB.
3. Uruchom następujące polecenie:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Następne kroki

- Dokładnie zapoznaj się z ważnymi [ograniczeniami i zachowaniami komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem komunikacji równorzędnej sieci wirtualnej do użycia w środowisku produkcyjnym.
- Dowiedz się więcej na temat wszystkich [ustawień komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [utworzyć topologię sieci piasty i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) przy użyciu komunikacji równorzędnej sieci wirtualnych.