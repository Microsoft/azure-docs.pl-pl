---
title: Diagnozowanie problemu z routingiem maszyny wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak zdiagnozować problem z routingiem maszyny wirtualnej, wyświetlając efektywne trasy dla maszyny wirtualnej.
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
ms.openlocfilehash: 1c23244707179e05c63ed44b5915e58eefd3f4a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84705053"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnozowanie problemu z routingiem maszyny wirtualnej

W tym artykule dowiesz się, jak zdiagnozować problem z routingiem, wyświetlając trasy, które są efektywne dla interfejsu sieciowego na maszynie wirtualnej. Platforma Azure tworzy kilka domyślnych tras dla każdej podsieci sieci wirtualnej. Można zastąpić domyślne trasy platformy Azure przez zdefiniowanie tras w tabeli tras, a następnie skojarzenie tabeli tras z podsiecią. Połączenie tworzonych tras, domyślnych tras platformy Azure oraz wszelkich tras rozmnożonych przez sieć lokalną za pośrednictwem bramy sieci VPN platformy Azure (Jeśli sieć wirtualna jest połączona z siecią lokalną) za pośrednictwem protokołu BGP (Border Gateway Protocol), to obowiązujące trasy dla wszystkich interfejsów sieciowych w podsieci. Jeśli nie znasz pojęć dotyczących sieci wirtualnych, interfejsów sieciowych lub routingu, zapoznaj się z tematem Omówienie usługi [Virtual Network](virtual-networks-overview.md), [interfejsu sieciowego](virtual-network-network-interface.md)i [routingu](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenariusz

Podjęto próbę nawiązania połączenia z maszyną wirtualną, ale połączenie nie powiedzie się. Aby określić, dlaczego nie można nawiązać połączenia z maszyną wirtualną, można wyświetlić obowiązujące trasy dla interfejsu sieciowego za pomocą witryny Azure [Portal](#diagnose-using-azure-portal), [programu PowerShell](#diagnose-using-powershell)lub [interfejsu wiersza polecenia platformy Azure](#diagnose-using-azure-cli).

W poniższej procedurze przyjęto założenie, że masz istniejącą maszynę wirtualną do wyświetlania obowiązujących tras. Jeśli nie masz istniejącej maszyny wirtualnej, najpierw Wdróż maszynę wirtualną z systemem [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , aby wykonać zadania z tego artykułu. Przykłady w tym artykule dotyczą maszyny wirtualnej o nazwie *myVM* z interfejsem sieciowym o nazwie *myVMNic1*. Maszyna wirtualna i interfejs sieciowy znajdują się w grupie zasobów o nazwie Moja *zasobów* i znajdują się w regionie *Wschodnie stany USA* . W razie potrzeby zmień wartości w odpowiednich krokach dla maszyny wirtualnej, dla której jest diagnozowany problem.

## <a name="diagnose-using-azure-portal"></a>Diagnozuj przy użyciu Azure Portal

1. Zaloguj się do witryny Azure [Portal](https://portal.azure.com) przy użyciu konta platformy Azure, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).
2. W górnej części Azure Portal wprowadź nazwę maszyny wirtualnej, która jest w stanie uruchomionym, w polu wyszukiwania. Gdy nazwa maszyny wirtualnej zostanie wyświetlona w wynikach wyszukiwania, wybierz ją.
3. W obszarze **Ustawienia** po lewej stronie wybierz pozycję **Sieć**, a następnie przejdź do zasobu interfejs sieciowy, wybierając jego nazwę.
     ![Wyświetl interfejsy sieciowe](./media/diagnose-network-routing-problem/view-nics.png)
4. Po lewej stronie wybierz pozycję **efektywne trasy**. Na poniższej ilustracji przedstawiono efektywne trasy dla interfejsu sieciowego o nazwie **myVMNic1** ![ .](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, można wyświetlić efektywne trasy dla dowolnego interfejsu sieciowego, zaznaczając go. Ponieważ każdy interfejs sieciowy może znajdować się w innej podsieci, każdy interfejs sieciowy może mieć różne efektywne trasy.

    W przykładzie pokazanym na powyższym rysunku trasy są trasami domyślnymi tworzonymi przez platformę Azure dla każdej podsieci. Lista ma co najmniej te trasy, ale może mieć dodatkowe trasy, w zależności od możliwości, które mogły zostać włączone dla sieci wirtualnej, takich jak Komunikacja równorzędna z inną siecią wirtualną lub połączenie z siecią lokalną za pośrednictwem bramy sieci VPN platformy Azure. Aby dowiedzieć się więcej na temat każdej z tras i innych tras, które mogą być widoczne dla interfejsu sieciowego, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md). Jeśli lista zawiera dużą liczbę tras, można łatwiej wybrać pozycję **Pobierz**, aby pobrać plik CSV z listą tras.

Chociaż efektywne trasy były przeglądane za pośrednictwem maszyny wirtualnej w poprzednich krokach, można także wyświetlić efektywne trasy za pośrednictwem:
- **Pojedynczy interfejs sieciowy**: informacje o sposobie [wyświetlania interfejsu sieciowego](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabela poszczególnych tras**: informacje o sposobie [wyświetlania tabeli tras](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnozuj przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić następujące polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. W przypadku uruchomienia programu PowerShell z komputera potrzebny jest moduł Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` polecenie na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie, `Connect-AzAccount` Aby zalogować się do platformy Azure przy użyciu konta, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).

Uzyskaj efektywne trasy dla interfejsu sieciowego za pomocą [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). Poniższy przykład pobiera obowiązujące trasy dla interfejsu sieciowego o nazwie *myVMNic1*, który znajduje się w grupie zasobów o nazwie Moja *zasobów*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Aby zrozumieć informacje zwracane w danych wyjściowych, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe są zwracane tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomienia. Jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, możesz przejrzeć efektywne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może znajdować się w innej podsieci, każdy interfejs sieciowy może mieć różne efektywne trasy. Jeśli nadal masz problem z komunikacją, zobacz [dodatkową diagnostykę](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Otrzymasz dane wyjściowe podobne do następującego przykładu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

W poprzednich danych wyjściowych nazwa interfejsu sieciowego to *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnozuj przy użyciu interfejsu wiersza polecenia platformy Azure

Można uruchomić następujące polecenia w  [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić `az login` i zalogować się do platformy Azure przy użyciu konta, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).

Uzyskaj efektywne trasy dla interfejsu sieciowego za pomocą [AZ Network nic show-skuteczna-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Poniższy przykład pobiera obowiązujące trasy dla interfejsu sieciowego o nazwie *myVMNic1* , który znajduje się w grupie zasobów o nazwie Moja *zasobów*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Aby zrozumieć informacje zwracane w danych wyjściowych, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe są zwracane tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomienia. Jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, możesz przejrzeć efektywne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może znajdować się w innej podsieci, każdy interfejs sieciowy może mieć różne efektywne trasy. Jeśli nadal masz problem z komunikacją, zobacz [dodatkową diagnostykę](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Rozwiązywanie problemu

Rozwiązywanie problemów z routingiem zwykle składa się z:

- Dodawanie niestandardowej trasy w celu zastąpienia jednej z domyślnych tras platformy Azure. Dowiedz się, jak [dodać trasę niestandardową](manage-route-table.md#create-a-route).
- Zmień lub Usuń trasę niestandardową, która może spowodować kierowanie do niepożądanej lokalizacji. Dowiedz się, jak [zmienić](manage-route-table.md#change-a-route) lub [usunąć](manage-route-table.md#delete-a-route) trasę niestandardową.
- Upewnienie się, że tabela tras, która zawiera wszystkie zdefiniowane trasy niestandardowe, jest skojarzona z podsiecią, w której znajduje się interfejs sieciowy. Dowiedz się, jak [skojarzyć tabelę tras z podsiecią](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Zapewnianie, że urządzenia takie jak Brama sieci VPN platformy Azure lub wirtualne urządzenia sieciowe są obsługiwane. Aby określić problemy z bramą sieci VPN platformy Azure, Skorzystaj z funkcji [diagnostyki sieci vpn](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Network Watcher.

Jeśli nadal występują problemy z komunikacją, zapoznaj się z [zagadnieniami](#considerations) i dodatkową diagnostyką.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z komunikacją należy wziąć pod uwagę następujące kwestie:

- Routing jest oparty na najdłuższym dopasowaniu prefiksów (LPM) między określonymi trasami, protokołem BGP (Border Gateway Protocol) i trasami systemowymi. Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, wybierana jest trasa na podstawie jej źródła w kolejności podanej w temacie [Routing — Omówienie](virtual-networks-udr-overview.md#how-azure-selects-a-route). Przy użyciu efektywnych tras można zobaczyć tylko efektywne trasy, które są zgodne z menedżerem LPM, w oparciu o wszystkie dostępne trasy. Zobacz, jak trasy są oceniane dla interfejsu sieciowego, ułatwiają rozwiązywanie problemów dotyczących określonych tras, które mogą mieć wpływ na komunikację z maszyny wirtualnej.
- Jeśli zostały zdefiniowane niestandardowe trasy do sieciowego urządzenia wirtualnego (urządzenie WUS) z *urządzeniem wirtualnym* jako typ następnego przeskoku, upewnij się, że funkcja przesyłania dalej IP jest włączona na urządzenie WUS odbierający ruch, lub pakiety są porzucane. Dowiedz się więcej [na temat włączania przesyłania dalej IP dla interfejsu sieciowego](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Ponadto system operacyjny lub aplikacja w ramach urządzenie WUS muszą również przekazywać ruch sieciowy i skonfigurować w ten sposób.
- Jeśli utworzono trasę z wartością 0.0.0.0/0, cały ruch wychodzący z Internetu jest kierowany do określonego następnego skoku, na przykład do bramy urządzenie WUS lub VPN. Tworzenie takiej trasy jest często określane mianem wymuszonego tunelowania. Połączenia zdalne z użyciem protokołów RDP lub SSH z Internetu do maszyny wirtualnej mogą nie współpracować z tą trasą w zależności od tego, jak następny przeskok obsługuje ruch. Tunelowanie wymuszone może być włączone:
    - W przypadku korzystania z sieci VPN typu lokacja-lokacja Utwórz trasę z typem następnego przeskoku *VPN Gateway*. Dowiedz się więcej o [konfigurowaniu wymuszonego tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Jeśli wartość 0.0.0.0/0 (trasa domyślna) jest anonsowana za pośrednictwem protokołu BGP przez bramę sieci wirtualnej podczas korzystania z sieci VPN typu lokacja-lokacja lub obwodu usługi ExpressRoute. Dowiedz się więcej o korzystaniu z protokołu BGP z [siecią VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Aby ruch równorzędny sieci wirtualnej działał prawidłowo, trasa systemowa z typem następnego przeskoku *komunikacji równorzędnej* sieci wirtualnej musi istnieć dla zakresu prefiksu równorzędnej podsieci. Jeśli taka trasa nie istnieje, a łącze komunikacji równorzędnej sieci wirtualnej jest **połączone**:
    - Poczekaj kilka sekund i spróbuj ponownie. Jeśli jest to nowo ustanowione łącze komunikacji równorzędnej, okresowo trwa dłużej propagowanie tras do wszystkich interfejsów sieciowych w podsieci. Aby dowiedzieć się więcej o komunikacji równorzędnej sieci wirtualnych, zobacz [Omówienie komunikacji równorzędnej sieci wirtualnych](virtual-network-peering-overview.md) i [Zarządzanie równorzędnymi sieciami wirtualnymi](virtual-network-manage-peering.md).
    - Reguły sieciowej grupy zabezpieczeń mogą mieć wpływ na komunikację. Aby uzyskać więcej informacji, zobacz [diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md).
- Mimo że platforma Azure przypisuje domyślne trasy do poszczególnych interfejsów sieciowych platformy Azure, jeśli do maszyny wirtualnej jest dołączonych wiele interfejsów sieciowych, tylko podstawowy interfejs sieciowy jest przypisany do trasy domyślnej (0.0.0.0/0) lub bramy w ramach systemu operacyjnego maszyny wirtualnej. Dowiedz się, jak utworzyć trasę domyślną dla dodatkowych interfejsów sieciowych podłączonych do maszyny wirtualnej z [systemem Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) . Dowiedz się więcej na temat [podstawowych i pomocniczych interfejsów sieciowych](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Dodatkowa Diagnostyka

* Aby uruchomić szybki test w celu określenia typu następnego przeskoku dla ruchu kierowanego do lokalizacji, użyj funkcji [następnego przeskoku](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w usłudze Azure Network Watcher. Następny przeskok informuje o tym, jaki typ następnego przeskoku dotyczy ruchu kierowanego do określonej lokalizacji.
* Jeśli nie ma żadnych tras powodujących niepowodzenie komunikacji sieciowej maszyny wirtualnej, przyczyną problemu może być oprogramowanie zapory działające w ramach systemu operacyjnego maszyny wirtualnej.
* W przypadku [wymuszania tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ruchu do urządzenia lokalnego za pośrednictwem bramy sieci VPN lub urządzenie WUS może nie być możliwe nawiązanie połączenia z maszyną wirtualną z Internetu, w zależności od sposobu skonfigurowania routingu dla urządzeń. Upewnij się, że Routing skonfigurowany dla urządzenia kieruje ruch do publicznego lub prywatnego adresu IP dla maszyny wirtualnej.
* Funkcja [rozwiązywania problemów z połączeniami](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Network Watcher umożliwia określenie routingu, filtrowania i przyczyny problemów z komunikacją w ramach systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wszystkich zadaniach, właściwościach i ustawieniach trasy dla [tabeli tras i tras](manage-route-table.md).
- Dowiedz się więcej o wszystkich [typach następnego przeskoku, trasach systemowych i sposobach wybierania trasy przez platformę Azure](virtual-networks-udr-overview.md).
