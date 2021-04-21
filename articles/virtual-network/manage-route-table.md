---
title: Tworzenie, zmienianie lub usuwanie tabeli tras platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, gdzie można znaleźć informacje o routingu ruchu w sieci wirtualnej oraz tworzyć, zmieniać lub usuwać tabelę tras.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: cdf702abb10b7330a4ca0f5478751df4bce3d7f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783381"
---
# <a name="create-change-or-delete-a-route-table"></a>Tworzenie, zmienianie i usuwanie tabeli routingu

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić dowolny domyślny routing platformy Azure, możesz to zrobić, tworząc tabelę tras. Jeśli dopiero poznajemy routing w sieciach wirtualnych, możesz dowiedzieć się więcej na jego temat w te tematach dotyczących [routingu](virtual-networks-udr-overview.md) ruchu w sieci wirtualnej lub kończąc [samouczek](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Następnie wykonaj jedno z tych zadań przed rozpoczęciem kroków w dowolnej sekcji tego artykułu:

- **Użytkownicy portalu:** zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell:** uruchom polecenia w Azure Cloud Shell [lub](https://shell.azure.com/powershell)uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell przeglądarki znajdź  listę rozwijaną Wybierz środowisko, a następnie wybierz program **PowerShell,** jeśli nie został jeszcze wybrany.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom również narzędzie `Connect-AzAccount` , aby utworzyć połączenie z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure:** uruchamiaj polecenia w witrynie [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiaj interfejs wiersza polecenia z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej, jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom również narzędzie `az login` , aby utworzyć połączenie z platformą Azure.

Konto, na które się logujesz lub z platformą Azure łączysz [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) się z platformą Azure, musi być przypisane do roli Współautor sieci lub Roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w [tece Uprawnienia.](#permissions) [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Istnieje limit liczby tabel tras, które można utworzyć dla lokalizacji i subskrypcji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Limity sieci — Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wprowadź *tabelę tras*. Gdy **tabela tras** pojawi się w wynikach wyszukiwania, wybierz ją.

1. Na stronie **Tabela tras** wybierz pozycję **Utwórz**.

1. W **oknie dialogowym Tworzenie tabeli** tras:

    1. Wprowadź **nazwę** tabeli tras.
    1. Wybierz **subskrypcję**.
    1. Wybierz istniejącą **grupę zasobów** lub wybierz **pozycję Utwórz nową,** aby utworzyć nową grupę zasobów.
    1. Wybierz **lokalizację**.
    1. Jeśli planujesz skojarzyć tabelę tras z podsiecią w sieci wirtualnej połączonej z siecią lokalną za pośrednictwem bramy sieci VPN i nie chcesz  propagować tras lokalnych do interfejsów sieciowych w podsieci, ustaw propagację trasy bramy sieci wirtualnej na **wartość Wyłączone.**

1. Wybierz **pozycję Utwórz,** aby utworzyć nową tabelę tras.

### <a name="create-route-table---commands"></a>Tworzenie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Wyświetlanie tabel tras

Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **pozycję Tabele tras.** Zostaną wyświetlone tabele tras, które istnieją w subskrypcji.

### <a name="view-route-table---commands"></a>Wyświetlanie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table list](/cli/azure/network/route-table#az_network_route_table_list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Wyświetlanie szczegółów tabeli tras

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **pozycję Tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras, dla której chcesz wyświetlić szczegóły.

1. Na stronie tabeli tras w obszarze  **Ustawienia** wyświetl trasy  w tabeli tras lub podsieciach, z których jest skojarzona tabela tras.

Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące informacje:

- [Dziennik aktywności](../azure-monitor/essentials/platform-logs-overview.md)
- [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
- [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skrypt usługi Automation](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Wyświetlanie szczegółów tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table show](/cli/azure/network/route-table#az_network_route_table_show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Zmienianie tabeli tras

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras, którą chcesz zmienić.

Najbardziej typowe zmiany [](#create-a-route) to dodawanie [tras,](#delete-a-route) usuwanie [tras,](#associate-a-route-table-to-a-subnet) kojarzenie tabel tras z podsieciami lub usuwanie skojarzeń tabel tras z podsieciami. [](#dissociate-a-route-table-from-a-subnet)

### <a name="change-a-route-table---commands"></a>Zmienianie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table update](/cli/azure/network/route-table#az_network_route_table_update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Opcjonalnie można skojarzyć tabelę tras z podsiecią. Tabela tras może być skojarzona z zero lub większą podsiecią. Ponieważ tabele tras nie są skojarzone z sieciami wirtualnymi, należy skojarzyć tabelę tras z każdą podsiecią, z którą ma być skojarzona tabela tras. Platforma Azure kieruje cały ruch opuszczając podsieć na podstawie tras utworzonych w tabelach [tras,](virtual-networks-udr-overview.md#default)tras domyślnych i tras propagowanych z sieci lokalnej, jeśli sieć wirtualna jest połączona z bramą sieci wirtualnej platformy Azure (expressRoute lub VPN). Tabelę tras można skojarzyć tylko z podsieciami w sieciach wirtualnych, które istnieją w tej samej lokalizacji i subskrypcji platformy Azure co tabela tras.

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **pozycję Sieci wirtualne.**

1. Na liście sieć wirtualna wybierz sieć wirtualną zawierającą podsieć, z którą chcesz skojarzyć tabelę tras.

1. Na pasku menu sieci wirtualnej wybierz pozycję **Podsieci**.

1. Wybierz podsieć, z którą chcesz skojarzyć tabelę tras.

1. W **tabeli tras** wybierz tabelę tras, którą chcesz skojarzyć z podsiecią.

1. Wybierz pozycję **Zapisz**.

Jeśli sieć wirtualna jest połączona z bramą sieci VPN platformy [](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) Azure, nie należy kojarzyć tabeli tras z podsiecią bramy, która zawiera trasę z lokalizacją docelową *0.0.0.0/0.* Zaniedbanie tego może spowodować nieprawidłowe działanie bramy. Aby uzyskać więcej informacji na temat używania *0.0.0.0/0* na trasie, zobacz Routing ruchu [w sieci wirtualnej](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Kojarzenie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Gdy skojarzysz tabelę tras z podsiecią, platforma Azure kieruje ruch na podstawie jej [tras domyślnych.](virtual-networks-udr-overview.md#default)

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **Sieci wirtualne.**

1. Z listy sieć wirtualna wybierz sieć wirtualną zawierającą podsieć, z której chcesz usunąć skojarzenie tabeli tras.

1. Na pasku menu sieci wirtualnej wybierz pozycję **Podsieci.**

1. Wybierz podsieć, z której chcesz usunąć skojarzenie tabeli tras.

1. W **tabeli Route (Trasa)** wybierz pozycję **None (Brak).**

1. Wybierz pozycję **Zapisz**.

### <a name="dissociate-a-route-table---commands"></a>Skojarzenie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Nie można usunąć tabeli tras skojarzonej z żadnymi podsieciami. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabeli tras z wszystkimi podsieciami, zanim spróbujesz ją usunąć.

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **pozycję Tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras, którą chcesz usunąć.

1. Wybierz **pozycję Usuń,** a następnie wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

### <a name="delete-a-route-table---commands"></a>Usuwanie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table delete](/cli/azure/network/route-table#az_network_route_table_delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Tworzenie trasy

Istnieje limit liczby tras na tabelę tras, które można utworzyć dla lokalizacji i subskrypcji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Limity sieci — Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras, do której chcesz dodać trasę.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy**  >  **Dodaj**.

1. Wprowadź **unikatową nazwę** trasy dla trasy w tabeli tras.

1. Wprowadź prefiks **adresu** w notacji Inter-Domain routingu bez klas (CIDR), do którego chcesz przekierować ruch. Prefiksu nie można duplikować w więcej niż jednej trasie w tabeli tras, chociaż prefiks może sięgać do innego prefiksu. Jeśli na przykład *zdefiniowano prefiks 10.0.0.0/16* w jednej trasie, nadal można zdefiniować inną trasę z prefiksem adresu *10.0.0.0/22.* Platforma Azure wybiera trasę dla ruchu na podstawie najdłuższego dopasowania prefiksu. Aby dowiedzieć się więcej, zobacz [Jak platforma Azure wybiera trasę.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Wybierz typ **następnego przeskoku**. Aby dowiedzieć się więcej na temat typów następnego przeskoku, zobacz [Routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).

1. W przypadku wybrania typu **Następne przeskok urządzenia** **wirtualnego** wprowadź adres IP dla adresu **Następne przeskok**.

1. Wybierz przycisk **OK**.

### <a name="create-a-route---commands"></a>Tworzenie trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Wyświetlanie tras

Tabela tras zawiera zero lub więcej tras. Aby dowiedzieć się więcej na temat informacji wymienionych podczas wyświetlania tras, zobacz [Routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras, dla której chcesz wyświetlić trasy.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

### <a name="view-routes---commands"></a>Wyświetlanie tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table route list](/cli/azure/network/route-table/route#az_network_route_table_route_list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras zawierającą trasę, dla której chcesz wyświetlić szczegóły.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

1. Wybierz trasę, dla której chcesz wyświetlić szczegóły.

### <a name="view-details-of-a-route---commands"></a>Wyświetlanie szczegółów trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table route show](/cli/azure/network/route-table/route#az_network_route_table_route_show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Zmienianie trasy

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras zawierającą trasę, którą chcesz zmienić.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

1. Wybierz trasę, którą chcesz zmienić.

1. Zmień istniejące ustawienia na nowe, a następnie wybierz pozycję **Zapisz.**

### <a name="change-a-route---commands"></a>Zmienianie trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table route update](/cli/azure/network/route-table/route#az_network_route_table_route_update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Usuwanie trasy

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **pozycję Tabele tras.**

1. Na liście tabeli tras wybierz tabelę tras zawierającą trasę, którą chcesz usunąć.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

1. Wybierz trasę, którą chcesz usunąć.

1. Wybierz **pozycję Usuń,** a następnie wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

### <a name="delete-a-route---commands"></a>Usuwanie trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table route delete](/cli/azure/network/route-table/route#az_network_route_table_route_delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Wyświetlanie efektywnych tras

Skuteczne trasy dla każdego interfejsu sieciowego dołączonego do maszyny wirtualnej to kombinacja utworzonych tabel tras, tras domyślnych platformy Azure i wszystkich tras propagowanych z sieci lokalnych za pośrednictwem protokołu Border Gateway Protocol (BGP) za pośrednictwem bramy sieci wirtualnej platformy Azure. Zrozumienie efektywnych tras dla interfejsu sieciowego jest przydatne podczas rozwiązywania problemów z routingiem. Możesz wyświetlić skuteczne trasy dla dowolnego interfejsu sieciowego dołączonego do uruchomionej maszyny wirtualnej.

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać maszynami wirtualnych. Wyszukaj i wybierz **pozycję Maszyny wirtualne.**

1. Na liście maszyn wirtualnych wybierz maszynę wirtualną, dla której chcesz wyświetlić skuteczne trasy.

1. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć.**

1. Wybierz nazwę interfejsu sieciowego.

1. Na pasku menu interfejsu sieciowego wybierz pozycję **Skuteczne trasy.**

1. Przejrzyj listę efektywnych tras, aby sprawdzić, czy istnieje prawidłowa trasa dla miejsca, do którego chcesz przekierowyć ruch. Dowiedz się więcej o typach następnego przeskoku, które widzisz na tej liście w tece [Routing ruchu w sieci wirtualnej.](virtual-networks-udr-overview.md)

### <a name="view-effective-routes---commands"></a>Wyświetlanie skutecznych tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Weryfikowanie routingu między dwoma punktami końcowymi

Typ następnego przeskoku można określić między maszyną wirtualną a adresem IP innego zasobu platformy Azure, zasobu lokalnego lub zasobu w Internecie. Określenie routingu platformy Azure jest przydatne podczas rozwiązywania problemów z routingiem. Aby wykonać to zadanie, musisz mieć istniejącą usługę Network Watcher. Jeśli nie masz istniejącej usługi Network Watcher, utwórz usługę , wykonując kroki opisane w Network Watcher [usługi](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać swoimi usługami network watcher. Wyszukaj i wybierz **pozycję Network Watcher**.

1. Na pasku menu usługi Network Watcher wybierz pozycję **Następny przeskok**.

1. W **Network Watcher | Strona następnego przeskoku:**

    1. Wybierz **subskrypcję i** **grupę zasobów** źródłowej maszyny wirtualnej, z której chcesz zweryfikować routing.

    1. Wybierz **maszynę wirtualną** i **interfejs sieciowy** dołączony do maszyny wirtualnej.
    
    1. Wprowadź źródłowy **adres IP przypisany** do interfejsu sieciowego, z którego chcesz zweryfikować routing.

    1. Wprowadź docelowy **adres IP,** do którego chcesz zweryfikować routing.

1. Wybierz **pozycję Następny przeskok**.

Po krótkim czasie oczekiwania platforma Azure informuje o typie następnego przeskoku i identyfikatorze trasy, która przekierowyła ruch. Dowiedz się więcej o typach następnego przeskoku, które są zwracane w routingu ruchu [w sieci wirtualnej.](virtual-networks-udr-overview.md)

### <a name="validate-routing-between-two-endpoints---commands"></a>Weryfikowanie routingu między dwoma punktami końcowymi — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w tabelach tras i trasach, Twoje konto [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) musi być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w poniższej tabeli: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

| Akcja                                                          |   Nazwa                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Odczytywanie tabeli tras                                    |
| Microsoft.Network/routeTables/write                             |   Tworzenie lub aktualizowanie tabeli tras                        |
| Microsoft.Network/routeTables/delete                            |   Usuwanie tabeli tras                                  |
| Microsoft.Network/routeTables/join/action                       |   Kojarzenie tabeli tras z podsiecią                   |
| Microsoft.Network/routeTables/routes/read                       |   Odczytywanie trasy                                          |
| Microsoft.Network/routeTables/routes/write                      |   Tworzenie lub aktualizowanie trasy                              |
| Microsoft.Network/routeTables/routes/delete                     |   Usuwanie trasy                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Pobierz tabelę efektywnych tras dla interfejsu sieciowego |
| Microsoft.Network/networkWatchers/nextHop/action                |   Pobiera następny przeskok z maszyny wirtualnej                           |

## <a name="next-steps"></a>Następne kroki

- Tworzenie tabeli tras przy użyciu [przykładowych skryptów programu PowerShell](powershell-samples.md) lub interfejsu wiersza polecenia platformy [Azure](cli-samples.md) albo szablonów usługi Azure [Resource Manager azure](template-samples.md)
- Tworzenie i [przypisywanie Azure Policy sieci](./policy-reference.md) wirtualnych