---
title: Diagnozowanie problemu z routingiem maszyny wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak zdiagnozować problem z routingiem maszyny wirtualnej, wyświetlając skuteczne trasy dla maszyny wirtualnej.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: f84b74b054a073f2c1ae5ba2ac7d0d0a968367c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767677"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnozowanie problemu z routingiem maszyny wirtualnej

Z tego artykułu dowiesz się, jak zdiagnozować problem z routingiem, wyświetlając trasy, które są skuteczne dla interfejsu sieciowego na maszynie wirtualnej. Platforma Azure tworzy kilka tras domyślnych dla każdej podsieci sieci wirtualnej. Domyślne trasy platformy Azure można zastąpić, definiując trasy w tabeli tras, a następnie kojarząc tabelę tras z podsiecią. Połączenie tworzyć trasy, trasy domyślne platformy Azure i wszystkie trasy propagowane z sieci lokalnej za pośrednictwem bramy sieci VPN platformy Azure (jeśli sieć wirtualna jest połączona z siecią lokalną) za pośrednictwem protokołu BGP (Border Gateway Protocol) są skutecznymi trasami dla wszystkich interfejsów sieciowych w podsieci. Jeśli nie znasz pojęć związanych z siecią wirtualną, [](virtual-networks-overview.md)interfejsem sieciowym lub routingiem, zobacz Omówienie sieci wirtualnej, Interfejs [sieciowy](virtual-network-network-interface.md)i Routing — [omówienie.](virtual-networks-udr-overview.md)

## <a name="scenario"></a>Scenariusz

Próbujesz nawiązać połączenie z maszyną wirtualną, ale połączenie kończy się niepowodzeniem. Aby ustalić, dlaczego nie można nawiązać połączenia z maszyną wirtualną, możesz wyświetlić efektywne trasy dla interfejsu sieciowego przy użyciu witryny Azure [Portal,](#diagnose-using-azure-portal)programu [PowerShell](#diagnose-using-powershell)lub interfejsu wiersza [polecenia platformy Azure.](#diagnose-using-azure-cli)

W poniższych krokach przyjęto założenie, że masz istniejącą maszynę wirtualną do wyświetlania obowiązujących tras. Jeśli nie masz istniejącej maszyny wirtualnej, najpierw wd wdrażaj maszynę wirtualną z systemem [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows,](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aby wykonać zadania z tego artykułu. Przykłady w tym artykule są dostępne dla maszyny wirtualnej o nazwie *myVM* z interfejsem sieciowym *o nazwie myVMNic1.* Maszyna wirtualna i interfejs sieciowy znajdują się w grupie zasobów o nazwie *myResourceGroup* i znajdują się w *regionie Wschodnie usa.* Zmień wartości w odpowiednich krokach dla maszyny wirtualnej, dla których diagnozujesz problem.

## <a name="diagnose-using-azure-portal"></a>Diagnozowanie przy użyciu Azure Portal

1. Zaloguj się do witryny Azure [Portal](https://portal.azure.com) przy użyciu konta platformy Azure z [niezbędnymi uprawnieniami.](virtual-network-network-interface.md#permissions)
2. W górnej części Azure Portal w polu wyszukiwania wprowadź nazwę maszyny wirtualnej, która jest w stanie uruchomienia. Gdy nazwa maszyny wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
3. W **obszarze** Ustawienia po lewej stronie wybierz **pozycję Sieć** i przejdź do zasobu interfejsu sieciowego, wybierając jego nazwę.
     ![Wyświetlanie interfejsów sieciowych](./media/diagnose-network-routing-problem/view-nics.png)
4. Po lewej stronie wybierz pozycję **Skuteczne trasy**. Skuteczne trasy dla interfejsu sieciowego o nazwie **myVMNic1** są pokazane na poniższej ilustracji: ![ Wyświetl efektywne trasy](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, możesz wyświetlić efektywne trasy dla dowolnego interfejsu sieciowego, wybierając go. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdy interfejs sieciowy może mieć różne skuteczne trasy.

    W przykładzie przedstawionym na poprzedniej ilustracji wymienione trasy są trasami domyślnymi, które platforma Azure tworzy dla każdej podsieci. Lista zawiera co najmniej te trasy, ale może mieć dodatkowe trasy, w zależności od możliwości, które można włączyć dla sieci wirtualnej, takich jak komunikacja równorzędna z inną siecią wirtualną lub połączenie z siecią lokalną za pośrednictwem bramy sieci VPN platformy Azure. Aby dowiedzieć się więcej o każdej z tras i innych trasach, które można zobaczyć dla interfejsu sieciowego, zobacz Routing ruchu [w sieci wirtualnej](virtual-networks-udr-overview.md). Jeśli lista zawiera dużą liczbę tras, łatwiejsze może być wybranie opcji **Pobierz,** aby pobrać plik CSV z listą tras.

Mimo że skuteczne trasy były przeglądane przez maszynę wirtualną w poprzednich krokach, można również wyświetlać skuteczne trasy za pośrednictwem:
- **Pojedynczy interfejs sieciowy:** dowiedz się, jak [wyświetlić interfejs sieciowy.](virtual-network-network-interface.md#view-network-interface-settings)
- **Pojedyncza tabela tras:** dowiedz [się, jak wyświetlić tabelę tras](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz uruchomić polecenia, które należy wykonać w [Azure Cloud Shell](https://shell.azure.com/powershell), lub uruchamiając program PowerShell z komputera. Interfejs Azure Cloud Shell jest bezpłatną interaktywną powłoką. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli uruchamiasz program PowerShell z komputera, potrzebujesz modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz również uruchomić narzędzie , aby zalogować się do platformy Azure przy użyciu konta `Connect-AzAccount` z [niezbędnymi uprawnieniami.](virtual-network-network-interface.md#permissions)

Pobierz efektywne trasy dla interfejsu sieciowego za pomocą [get-AzEffectiveRouteTable.](/powershell/module/az.network/get-azeffectiveroutetable) Poniższy przykład pobiera efektywne trasy dla interfejsu sieciowego o nazwie *myVMNic1,* który znajduje się w grupie zasobów *o nazwie myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Aby zrozumieć informacje zwracane w danych wyjściowych, zobacz [Routing overview (Omówienie routingu).](virtual-networks-udr-overview.md) Dane wyjściowe są zwracane tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomienia. Jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, możesz przejrzeć skuteczne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdy interfejs sieciowy może mieć różne skuteczne trasy. Jeśli nadal masz problem z komunikacją, zapoznaj się z [dodatkową diagnostyką](#additional-diagnosis) [i zagadnieniami.](#considerations)

Jeśli nie znasz nazwy interfejsu sieciowego, ale nie wiesz, do czego jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Otrzymasz dane wyjściowe podobne do następujących:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

W poprzednich danych wyjściowych nazwa interfejsu sieciowego to *myVMNic1.*

## <a name="diagnose-using-azure-cli"></a>Diagnozowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz uruchomić polecenia, które należy wykonać w Azure Cloud Shell  [,](https://shell.azure.com/bash)lub uruchamiając interfejs wiersza polecenia z komputera. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić narzędzie i zalogować się na platformie Azure przy użyciu konta z `az login` [niezbędnymi uprawnieniami.](virtual-network-network-interface.md#permissions)

Pobierz efektywne trasy dla interfejsu sieciowego za pomocą [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Poniższy przykład pobiera efektywne trasy dla interfejsu sieciowego o nazwie *myVMNic1,* który znajduje się w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Aby zrozumieć informacje zwracane w danych wyjściowych, zobacz [Routing overview (Omówienie routingu).](virtual-networks-udr-overview.md) Dane wyjściowe są zwracane tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomienia. Jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, możesz przejrzeć skuteczne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdy interfejs sieciowy może mieć różne skuteczne trasy. Jeśli nadal masz problem z komunikacją, zapoznaj się z [dodatkową diagnostyką](#additional-diagnosis) [i zagadnieniami.](#considerations)

Jeśli nie znasz nazwy interfejsu sieciowego, ale nie znasz nazwy maszyny wirtualnej, do która jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Rozwiązywanie problemu

Rozwiązywanie problemów z routingiem zwykle obejmuje:

- Dodanie trasy niestandardowej w celu zastąpienia jednej z tras domyślnych platformy Azure. Dowiedz się, [jak dodać trasę niestandardową.](manage-route-table.md#create-a-route)
- Zmień lub usuń trasę niestandardową, która może spowodować kierowanie do niepożądanej lokalizacji. Dowiedz się, [jak zmienić](manage-route-table.md#change-a-route) lub [usunąć](manage-route-table.md#delete-a-route) trasę niestandardową.
- Upewnienie się, że tabela tras zawierająca zdefiniowane trasy niestandardowe jest skojarzona z podsiecią, w która znajduje się interfejs sieciowy. Dowiedz się, [jak skojarzyć tabelę tras z podsiecią](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Zapewnienie obsługi urządzeń, takich jak brama sieci VPN platformy Azure lub wirtualne urządzenia sieciowe, które zostały wdrożone. Skorzystaj z [możliwości diagnostyki](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci VPN Network Watcher, aby określić problemy z bramą sieci VPN platformy Azure.

Jeśli nadal występują problemy z komunikacją, zobacz [Zagadnienia i](#considerations) dodatkowa diagnostyka.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z komunikacją należy wziąć pod uwagę następujące kwestie:

- Routing jest oparty na najdłuższym dopasowaniu prefiksów (LPM) między zdefiniowanymi trasami, protokołem BGP (Border Gateway Protocol) i trasami systemowych. Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, trasa jest wybierana na podstawie jej pochodzenia w kolejności wymienionej w te same [informacje o routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route). W przypadku efektywnych tras można zobaczyć tylko skuteczne trasy, które są zgodne z LPM, na podstawie wszystkich dostępnych tras. Zobaczenie sposobu oceniania tras dla interfejsu sieciowego znacznie ułatwia rozwiązywanie problemów z określonymi trasami, które mogą mieć wpływ na komunikację z maszyną wirtualną.
- Jeśli zdefiniowano trasy niestandardowe do wirtualnego urządzenia sieciowego (WUS) z urządzeniem wirtualnym jako typem następnego przeskoku, upewnij się, że na urządzeniu WUS odbieracym ruch jest włączone przekazywanie ip lub pakiety są porzucane.  Dowiedz się więcej na [temat włączania przekazywania IP dla interfejsu sieciowego.](virtual-network-network-interface.md#enable-or-disable-ip-forwarding) Ponadto system operacyjny lub aplikacja w ramach urządzenia WUS musi również mieć możliwość przekazywania ruchu sieciowego i musi być do tego skonfigurowana.
- Jeśli utworzono trasę do 0.0.0.0/0, cały wychodzący ruch internetowy jest przekierowyny do następnego określonego przeskoku, takiego jak urządzenie WUS lub brama sieci VPN. Tworzenie takiej trasy jest często określane jako wymuszone tunelowanie. Połączenia zdalne korzystające z protokołów RDP lub SSH z Internetu do maszyny wirtualnej mogą nie działać z tą trasą, w zależności od sposobu obsługi ruchu przez następny przeskok. Można włączyć tunelowanie wymuszone:
    - W przypadku korzystania z sieci VPN typu lokacja-lokacja przez utworzenie trasy z typem następnego przeskoku *VPN Gateway*. Dowiedz się więcej o [konfigurowaniu wymuszonego tunelowania.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - Jeśli trasa 0.0.0.0/0 (trasa domyślna) jest anonsowana za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej w przypadku korzystania z sieci VPN typu lokacja-lokacja lub obwodu usługi ExpressRoute. Dowiedz się więcej na temat używania protokołu BGP z siecią VPN typu [lokacja-lokacja](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [z usługą ExpressRoute.](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)
- Aby ruch komunikacji równorzędnej sieci wirtualnej działał prawidłowo,  musi istnieć trasa systemowa z typem następnego przeskoku Komunikacja równorzędna sieci wirtualnej dla zakresu prefiksów równorzędnej sieci wirtualnej. Jeśli taka trasa nie istnieje, a połączenie komunikacji równorzędnej sieci wirtualnej ma **związek Połączony:**
    - Zaczekaj kilka sekund i spróbuj ponownie. Jeśli jest to nowo ustanowione połączenie komunikacji równorzędnej, propagacja tras do wszystkich interfejsów sieciowych w podsieci czasami trwa dłużej. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz Omówienie komunikacji równorzędnej [sieci wirtualnych](virtual-network-peering-overview.md) i zarządzanie [wirtualną siecią równorzędną](virtual-network-manage-peering.md).
    - Reguły sieciowej grupy zabezpieczeń mogą mieć wpływ na komunikację. Aby uzyskać więcej informacji, zobacz Diagnozowanie problemu z filtrowaniem [ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md).
- Mimo że platforma Azure przypisuje trasy domyślne do każdego interfejsu sieciowego platformy Azure, jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, tylko podstawowy interfejs sieciowy ma przypisaną trasę domyślną (0.0.0.0/0) lub bramę w ramach systemu operacyjnego maszyny wirtualnej. Dowiedz się, jak utworzyć trasę domyślną dla pomocniczych interfejsów sieciowych dołączonych do maszyny [wirtualnej z systemem Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [lub Linux.](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) Dowiedz się więcej o [podstawowym i pomocniczym interfejsie sieciowym.](virtual-network-network-interface-vm.md#constraints)

## <a name="additional-diagnosis"></a>Dodatkowa diagnostyka

* Aby uruchomić szybki test w celu określenia typu następnego przeskoku dla ruchu przeznaczonego do lokalizacji, użyj funkcji Następny przeskok usługi Azure Network Watcher. [](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Następny przeskok informuje o typie następnego przeskoku dla ruchu przeznaczonego do określonej lokalizacji.
* Jeśli nie ma żadnych tras powodujących niepowodzenie komunikacji sieciowej maszyny wirtualnej, problem może być spowodowany oprogramowaniem zapory uruchomionym w systemie operacyjnym maszyny wirtualnej
* Jeśli wymuszasz tunelowanie ruchu do urządzenia lokalnego za pośrednictwem bramy sieci VPN lub urządzenia WUS, połączenie z maszyną wirtualną z Internetu może nie być możliwe, w zależności od konfiguracji routingu dla urządzeń. [](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Upewnij się, że routing skonfigurowany dla urządzenia kieruje ruch do publicznego lub prywatnego adresu IP maszyny wirtualnej.
* Funkcja [rozwiązywania problemów z](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) połączeniami umożliwia Network Watcher routingu, filtrowania i przyczyn problemów z komunikacją wychodzącą w systemach operacyjnych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wszystkich zadaniach, właściwościach i ustawieniach dla tabeli [tras i tras.](manage-route-table.md)
- Dowiedz się więcej na [temat wszystkich typów następnego przeskoku, tras systemowych i sposobu wybierania trasy przez platformę Azure.](virtual-networks-udr-overview.md)
