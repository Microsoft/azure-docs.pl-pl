---
title: Tworzenie komunikacji równorzędnej sieci wirtualnej platformy Azure — różne modele wdrażania — różne subskrypcje
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć sieć wirtualną sieci równorzędnej między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania platformy Azure, które istnieją w różnych subskrypcjach platformy Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 88e576231e0231a105cd9ec303f63307b5eaff89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87051629"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Tworzenie komunikacji równorzędnej sieci wirtualnej — różne modele wdrażania i subskrypcje

W ramach tego samouczka nauczysz się tworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Sieci wirtualne istnieją w różnych subskrypcjach. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasobom w różnych sieciach wirtualnych komunikowanie się ze sobą za pomocą tej samej przepustowości i opóźnienia, tak jakby zasoby znajdowały się w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacji równorzędnej sieci wirtualnych](virtual-network-peering-overview.md).

Procedurę tworzenia komunikacji równorzędnej sieci wirtualnej różnią się w zależności od tego, czy sieci wirtualne znajdują się w tym samym systemie, czy w różnych subskrypcjach i [modelu wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , w którym są tworzone sieci wirtualne. Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej w innych scenariuszach, klikając scenariusz w poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Resource Manager — w obu przypadkach](tutorial-connect-virtual-networks-portal.md) |Ta sama|
|[Resource Manager — w obu przypadkach](create-peering-different-subscriptions.md) |Różne|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models.md) |Ta sama|

Nie można utworzyć komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi wdrożonymi przy użyciu klasycznego modelu wdrażania. W tym samouczku są używane sieci wirtualne istniejące w tym samym regionie. Ten samouczek umożliwia równorzędne sieci wirtualne w tym samym regionie. Można także równorzędne sieci wirtualne w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region). Zaleca się zapoznanie się z [wymaganiami i ograniczeniami komunikacji równorzędnej](virtual-network-manage-peering.md#requirements-and-constraints) przed równorzędnymi sieciami wirtualnymi.

Podczas tworzenia komunikacji równorzędnej sieci wirtualnej między sieciami wirtualnymi, które istnieją w różnych subskrypcjach, subskrypcje mogą być skojarzone z tą samą dzierżawą Azure Active Directory. Jeśli nie masz jeszcze dzierżawy Azure Active Directory, możesz ją szybko [utworzyć](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant).

Do utworzenia komunikacji równorzędnej sieci wirtualnej można użyć [Azure Portal](#portal), [interfejsu wiersza polecenia](#cli) platformy Azure lub [programu Azure PowerShell](#powershell) . Kliknij dowolne z poprzednich linków narzędzi, aby przejść bezpośrednio do procedury tworzenia komunikacji równorzędnej sieci wirtualnej przy użyciu wybranego przez siebie narzędzia.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Tworzenie komunikacji równorzędnej — Azure Portal

W tym samouczku są stosowane różne konta dla każdej subskrypcji. Jeśli używasz konta z uprawnieniami do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć procedurę rejestrowania w portalu i pominąć procedurę przypisywania innych uprawnień użytkownika do sieci wirtualnych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako usera. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
2. Kliknij pozycję **+ Nowy**, kliknij pozycję **Sieć**, a następnie kliknij pozycję **Sieć wirtualna**.
3. W bloku **Utwórz sieć wirtualną** wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij pozycję **Utwórz**:
    - **Nazwa**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *Domyślna*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: wybierz pozycję subskrypcja A.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i wprowadź *myResourceGroupA*
    - **Lokalizacja**: *Wschodnie stany USA*
4. W polu **Wyszukaj zasoby** w górnej części portalu wpisz *myVnetA*. Kliknij pozycję **myVnetA** , gdy pojawi się w wynikach wyszukiwania. Zostanie wyświetlony blok dla sieci wirtualnej **myVnetA** .
5. W wyświetlonym bloku **myVnetA** kliknij pozycję **Kontrola dostępu (IAM)** z pionowej listy opcji po lewej stronie bloku.
6. W wyświetlonym bloku **myVnetA-Access Control (IAM)** kliknij pozycję **+ Dodaj przypisanie roli**.
7. W wyświetlonym bloku **Dodaj przypisanie roli** wybierz opcję **współautor sieci** w polu **rola** .
8. W polu **Wybierz** wybierz pozycję UserB lub wpisz adres e-mail UserB, aby go wyszukać. Lista wyświetlanych użytkowników pochodzi z tego samego Azure Active Directory dzierżawy, co sieć wirtualna, dla której konfigurujesz komunikację równorzędną. Kliknij pozycję UserB, gdy pojawi się na liście.
9. Kliknij przycisk **Zapisz**.
10. Wyloguj się z portalu jako UserA, a następnie zaloguj się jako UserB.
11. Kliknij pozycję **+ Nowy**, wpisz *sieć wirtualną* w polu **Wyszukaj w witrynie Marketplace** , a następnie w wynikach wyszukiwania kliknij pozycję **Sieć wirtualna** .
12. W wyświetlonym bloku **Virtual Network** wybierz pozycję **klasyczny** w polu **Wybierz model wdrażania** , a następnie kliknij przycisk **Utwórz**.
13. W wyświetlonym oknie Utwórz sieć wirtualną (klasyczną) wprowadź następujące wartości:

    - **Nazwa**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *Domyślna*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: wybierz subskrypcję B.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i wprowadź *myResourceGroupB*
    - **Lokalizacja**: *Wschodnie stany USA*

14. W polu **Wyszukaj zasoby** w górnej części portalu wpisz *myVnetB*. Kliknij pozycję **myVnetB** , gdy pojawi się w wynikach wyszukiwania. Zostanie wyświetlony blok dla sieci wirtualnej **myVnetB** .
15. W wyświetlonym bloku **myVnetB** kliknij pozycję **Właściwości** z pionowej listy opcji po lewej stronie bloku. Skopiuj **Identyfikator zasobu**, który jest używany w późniejszym kroku. Identyfikator zasobu jest podobny do następującego przykładu: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Wykonaj kroki 5-9 dla myVnetB, wprowadzając **usera** w kroku 8.
17. Wyloguj się z portalu jako UserB i zaloguj się jako UserA.
18. W polu **Wyszukaj zasoby** w górnej części portalu wpisz *myVnetA*. Kliknij pozycję **myVnetA** , gdy pojawi się w wynikach wyszukiwania. Zostanie wyświetlony blok dla sieci wirtualnej **myVnet** .
19. Kliknij pozycję **myVnetA**.
20. W wyświetlonym bloku **myVnetA** kliknij pozycję **Komunikacja równorzędna** z listy pionowych opcji po lewej stronie bloku.
21. W bloku **myVnetA-Peers** , który pojawił się, kliknij pozycję **+ Dodaj** .
22. W wyświetlonym bloku **Dodaj komunikację równorzędną** wprowadź lub wybierz poniższe opcje, a następnie kliknij przycisk **OK**:
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrażania sieci wirtualnej**: wybierz pozycję **klasyczne**.
     - **Wiemy o identyfikatorze zasobu**: zaznacz to pole wyboru.
     - **Identyfikator zasobu**: Wprowadź identyfikator zasobu myVnetB z kroku 15.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że jest zaznaczona **Funkcja włączona** .
    W tym samouczku nie są używane żadne inne ustawienia. Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej, zobacz [Zarządzanie równorzędnymi sieciami wirtualnymi](virtual-network-manage-peering.md#create-a-peering).
23. Po kliknięciu przycisku **OK** w poprzednim kroku blok **Dodawanie komunikacji równorzędnej** zostanie zamknięty i ponownie zobaczysz blok **myVnetA-Peers** . Po kilku sekundach utworzone połączenie równorzędne zostanie wyświetlone w bloku. **Połączono** w kolumnie **Stan komunikacji równorzędnej** dla utworzonej komunikacji równorzędnej **myVnetAToMyVnetB** . Komunikacja równorzędna została ustanowiona. Nie ma potrzeby komunikacji równorzędnej z siecią wirtualną (klasyczną) do sieci wirtualnej (Menedżer zasobów).

    Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie mogą rozpoznawać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
25. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj kroki opisane w sekcji [usuwanie zasobów](#delete-portal) w tym artykule.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Tworzenie komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure

W tym samouczku są stosowane różne konta dla każdej subskrypcji. Jeśli używasz konta z uprawnieniami do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć procedurę rejestrowania z platformy Azure i usunąć wiersze skryptu, które tworzą przypisania roli użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptach nazwami użytkowników, których używasz do usera i UserB. Wykonaj następujące kroki, korzystając z klasycznego interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia platformy Azure. Kroki opisane w Azure Cloud Shell można wykonać, zaznaczając przycisk **Wypróbuj** w jednym z następujących kroków lub instalując [klasyczny interfejs wiersza polecenia](/cli/azure/install-classic-cli) i [interfejs wiersza](/cli/azure/install-azure-cli) polecenia i uruchamiając polecenie na komputerze lokalnym.

1. Jeśli używasz Cloud Shell, przejdź do kroku 2, ponieważ Cloud Shell automatycznie loguje się do platformy Azure. Otwórz sesję polecenia i zaloguj się do platformy Azure przy użyciu `azure login` polecenia.
2. Uruchom klasyczny interfejs wiersza polecenia w trybie zarządzania usługami, wprowadzając `azure config mode asm` polecenie.
3. Wprowadź następujące klasyczne polecenie interfejsu wiersza polecenia, aby utworzyć sieć wirtualną (klasyczną):

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. Pozostałe kroki należy wykonać przy użyciu powłoki bash z interfejsem wiersza polecenia platformy Azure (nie z klasycznym interfejsem wiersza polecenia).
5. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<SubscriptionB-Id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` polecenie. Wartość **identyfikatora** w danych wyjściowych to identyfikator subskrypcji. Skopiuj zmodyfikowany skrypt, wklej go w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Po utworzeniu sieci wirtualnej (klasycznej) w kroku 4 platforma Azure utworzyła sieć wirtualną w grupie zasobów *Sieć domyślna* .
6. Rejestruj UserB z platformy Azure i zaloguj się jako UserA w interfejsie wiersza polecenia.
7. Utwórz grupę zasobów i sieć wirtualną (Menedżer zasobów). Skopiuj następujący skrypt, wklej go w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` .

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Utwórz komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<SubscriptionB-id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` polecenie. Wartość **identyfikatora** w danych wyjściowych to identyfikator subskrypcji. platforma Azure utworzyła sieć wirtualną (klasyczną) utworzoną w kroku 4 w grupie zasobów o nazwie *default-Networking*. Wklej zmodyfikowany skrypt w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter` .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Po wykonaniu skryptu Przejrzyj komunikację równorzędną dla sieci wirtualnej (Menedżer zasobów). Skopiuj poniższy skrypt, a następnie wklej go w sesji interfejsu wiersza polecenia:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    Dane wyjściowe są **wyświetlane w** kolumnie **PeeringState** .

    Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie mogą rozpoznawać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
11. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj czynności opisane w temacie [usuwanie zasobów](#delete-cli) w tym artykule.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Tworzenie komunikacji równorzędnej — PowerShell

W tym samouczku są stosowane różne konta dla każdej subskrypcji. Jeśli używasz konta z uprawnieniami do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć procedurę rejestrowania z platformy Azure i usunąć wiersze skryptu, które tworzą przypisania roli użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptach nazwami użytkowników, których używasz do usera i UserB. 

1. Zainstaluj najnowszą wersję środowiska PowerShell [platformy Azure](https://www.powershellgallery.com/packages/Azure) i [AZ](https://www.powershellgallery.com/packages/Az) modules. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Rozpocznij sesję programu PowerShell.
3. W programie PowerShell Zaloguj się do subskrypcji UserB jako UserB, wprowadzając `Add-AzureAccount` polecenie. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
4. Aby utworzyć sieć wirtualną (klasyczną) przy użyciu programu PowerShell, należy utworzyć nową lub zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się [, jak eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujący element **VirtualNetworkSite** dla sieci wirtualnej używanej w tym samouczku:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Zaloguj się do subskrypcji usługi UserB jako UserB, aby użyć poleceń Menedżer zasobów, wprowadzając `Connect-AzAccount` polecenie.
6. Przypisz uprawnienia UserA do sieci wirtualnej B. Skopiuj następujący skrypt do edytora tekstów na komputerze i Zastąp ciąg `<SubscriptionB-id>` identyfikatorem subskrypcji B. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby je wyświetlić. Wartość **identyfikatora** w zwracanym wyniku to identyfikator subskrypcji. Platforma Azure utworzyła sieć wirtualną (klasyczną) utworzoną w kroku 4 w grupie zasobów o nazwie *default-Networking*. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go w programie PowerShell, a następnie naciśnij klawisz `Enter` .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Wyloguj się z platformy Azure jako UserB i zaloguj się do subskrypcji UserA jako UserA, wprowadzając `Connect-AzAccount` polecenie. Konto, za pomocą którego logujesz się, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Listę uprawnień można znaleźć w temacie [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).
8. Utwórz sieć wirtualną (Menedżer zasobów), kopiując następujący skrypt, wklejając go w programie PowerShell, a następnie naciskając `Enter` :

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Przypisz uprawnienia UserB do myVnetA. Skopiuj następujący skrypt do edytora tekstu na komputerze i Zastąp ciąg `<SubscriptionA-Id>` identyfikatorem subskrypcji a. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby je wyświetlić. Wartość **identyfikatora** w zwracanym wyniku to identyfikator subskrypcji. Wklej zmodyfikowaną wersję skryptu w programie PowerShell, a następnie naciśnij `Enter` ją, aby wykonać operację.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Skopiuj następujący skrypt do edytora tekstów na komputerze i Zastąp ciąg `<SubscriptionB-id>` identyfikatorem subskrypcji B. Aby myVnetA równorzędny do myVNetB, skopiuj zmodyfikowany skrypt, wklej go w programie PowerShell, a następnie naciśnij klawisz `Enter` .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Aby wyświetlić stan komunikacji równorzędnej myVnetA, Skopiuj następujący skrypt, wklejając go do programu PowerShell i naciskając klawisz `Enter` .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stan jest **połączony**. Zmiana zostanie **połączona** po skonfigurowaniu komunikacji równorzędnej na MyVnetA z myVnetB.

    Wszystkie zasoby platformy Azure utworzone w ramach jednej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieci wirtualnych nie mogą rozpoznawać nazw w sieciach wirtualnych. Jeśli chcesz rozwiązać nazwy między sieciami wirtualnymi w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jedną maszyną wirtualną w celu sprawdzenia łączności.
13. **Opcjonalne**: Aby usunąć zasoby utworzone w ramach tego samouczka, wykonaj czynności opisane w temacie [usuwanie zasobów](#delete-powershell) w tym artykule.

## <a name="delete-resources"></a><a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka możesz chcieć usunąć zasoby utworzone w samouczku, dzięki czemu nie zostaną naliczone opłaty za użycie. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie zasobów.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. W polu wyszukiwania portalu wprowadź **myResourceGroupA**. W wynikach wyszukiwania kliknij pozycję **myResourceGroupA**.
2. W bloku **myResourceGroupA** kliknij ikonę **Usuń** .
3. Aby potwierdzić usunięcie, w polu **wpisz nazwę grupy zasobów** wpisz **myResourceGroupA**, a następnie kliknij przycisk **Usuń**.
4. W polu **Wyszukaj zasoby** w górnej części portalu wpisz *myVnetB*. Kliknij pozycję **myVnetB** , gdy pojawi się w wynikach wyszukiwania. Zostanie wyświetlony blok dla sieci wirtualnej **myVnetB** .
5. W bloku **myVnetB** kliknij pozycję **Usuń**.
6. Aby potwierdzić usunięcie, kliknij przycisk **tak** w polu **Usuń sieć wirtualną** .

### <a name="azure-cli"></a><a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia, aby usunąć sieć wirtualną (Menedżer zasobów) za pomocą następującego polecenie:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Zaloguj się do platformy Azure przy użyciu klasycznego interfejsu wiersza polecenia, aby usunąć sieć wirtualną (klasyczną) przy użyciu następujących poleceń:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Program PowerShell

1. W wierszu polecenia programu PowerShell wprowadź następujące polecenie, aby usunąć sieć wirtualną (Menedżer zasobów):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Aby usunąć sieć wirtualną (klasyczną) przy użyciu programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się [, jak eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używanej w tym samouczku:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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
