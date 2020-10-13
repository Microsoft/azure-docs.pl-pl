---
title: Sieci wirtualne
description: Informacje o sieci, które są związane z tworzeniem maszyn wirtualnych z systemem Linux na platformie Azure.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 723b556a2e5fd1fae32d50a382c99025ad726bff
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977361"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Sieci wirtualne i maszyny wirtualne na platformie Azure 

Utworzenie maszyny wirtualnej (VM) platformy Azure wymaga utworzenia [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) (VNet) lub użycia istniejącej sieci wirtualnej. Należy także określić sposób dostępu do maszyn wirtualnych w sieci wirtualnej. Ważne jest [zaplanowanie tworzenia zasobów](../virtual-network/virtual-network-vnet-plan-design-arm.md) i dokładne zapoznanie się z [limitami zasobów sieciowych](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Na poniższej ilustracji przedstawiono maszyny wirtualne jako serwery sieci Web i serwery baz danych. Poszczególne zestawy maszyn wirtualnych są przypisane do oddzielnych podsieci w sieci wirtualnej.

![Sieć wirtualna Azure](./media/virtual-machines-common-network-overview/vnetoverview.png)

Sieć wirtualną można utworzyć przed utworzeniem maszyny wirtualnej lub podczas tworzenia maszyny wirtualnej. Należy utworzyć następujące zasoby, umożliwiające obsługę komunikacji z maszyną wirtualną:

- Interfejsy sieciowe
- Adresy IP
- Sieć wirtualna i podsieci

Oprócz tych podstawowych zasobów należy również rozważyć utworzenie poniższych opcjonalnych zasobów:

- Grupy zabezpieczeń sieci
- Moduły równoważenia obciążenia 

## <a name="network-interfaces"></a>Interfejsy sieciowe

[Interfejs sieciowy (karta sieciowa)](../virtual-network/virtual-network-network-interface.md) to połączenie między maszyną wirtualną a siecią wirtualną (VNET). Maszyna wirtualna musi mieć co najmniej jedną kartę sieciową. Może jednak mieć więcej kart, w zależności od rozmiaru podanego przy jej tworzeniu. Dowiedz się więcej o liczbie kart sieciowych obsługiwanych przez poszczególne rozmiary maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych](sizes.md).

W trakcie cyklu życia maszyny wirtualnej można utworzyć maszynę wirtualną z wieloma interfejsami sieciowymi oraz dodawać lub usuwać interfejsy sieciowe. Wiele interfejsów sieciowych umożliwia maszynie wirtualnej łączenie się z różnymi podsieciami oraz wysyłanie i odbieranie ruchu przez najbardziej odpowiedni interfejs. Maszyny wirtualne z dowolną liczbą interfejsów sieciowych mogą istnieć w tym samym zestawie dostępności do liczby obsługiwanej przez rozmiar maszyny wirtualnej. 

Wszystkie karty sieciowe dołączone do danej maszyny wirtualnej muszą znajdować się w tej samej lokalizacji i subskrypcji co maszyna wirtualna. Wszystkie karty sieciowe muszą być podłączone do sieci wirtualnej, która znajduje się w tej samej lokalizacji i subskrypcji platformy Azure co karta sieciowa. Możesz zmienić podsieć, z którą jest połączona maszyna wirtualna po jej utworzeniu, ale nie możesz zmienić sieci wirtualnej. Każda karta sieciowa dołączona do maszyny wirtualnej ma przypisany adres MAC, który pozostaje stały do momentu usunięcia maszyny wirtualnej.

Poniższa tabela zawiera listę metod, których można użyć do utworzenia interfejsu sieciowego.

| Metoda | Opis |
| ------ | ----------- |
| Azure Portal | Interfejs sieciowy jest tworzony automatycznie podczas tworzenia maszyny wirtualnej w witrynie Azure Portal (nie można użyć oddzielnie utworzonej karty sieciowej). Maszyna wirtualna utworzona w portalu może mieć tylko jedną kartę sieciową. Jeśli chcesz utworzyć maszynę wirtualną zawierającą więcej niż jedną kartę sieciową, musisz użyć innej metody. |
| [Azure PowerShell](./windows/multiple-nics.md) | Użyj parametru [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) z parametrem **-PublicIpAddressId** , aby podać identyfikator wcześniej utworzonego publicznego adresu IP. |
| [Interfejs wiersza polecenia platformy Azure](./linux/multiple-nics.md) | Aby podać identyfikator utworzonego wcześniej publicznego adresu IP, użyj [AZ Network nic Create](/cli/azure/network/nic) z parametrem **--Public-IP-Address** . |
| [Szablon](../virtual-network/template-samples.md) | Przewodnik [Interfejs sieciowy w sieci wirtualnej z publicznym adresem IP](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) ułatwia wdrożenie interfejsu sieciowego przy użyciu szablonu. |

## <a name="ip-addresses"></a>Adresy IP 

Platforma Azure umożliwia przypisanie do karty sieciowej następujących typów [adresów IP](../virtual-network/public-ip-addresses.md):

- **Publiczne adresy IP** — używane do komunikacji przychodzącej i wychodzącej (bez użycia translatora adresów sieciowych [NAT]) z Internetem i innymi zasobami platformy Azure niepołączonymi z siecią wirtualną. Przypisanie publicznego adresu IP do karty sieciowej jest opcjonalne. Publiczne adresy IP mają opłata nominalną, a maksymalna liczba, która może być używana dla każdej subskrypcji.
- **Prywatne adresy IP** — używane do komunikacji w sieci wirtualnej i lokalnej oraz z Internetem (z użyciem translatora adresów sieciowych). Do maszyny wirtualnej należy przypisać co najmniej jeden prywatny adres IP. Aby dowiedzieć się więcej o użyciu translatora adresów sieciowych na platformie Azure, zapoznaj się z tematem [Informacje o połączeniach wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md).

Publiczne adresy IP można przypisywać do maszyn wirtualnych lub modułów równoważenia obciążenia dostępnych z Internetu. Prywatne adresy IP można przypisywać do maszyn wirtualnych i wewnętrznych modułów równoważenia obciążenia. Przypisywanie adresów IP do maszyn wirtualnych odbywa się przy użyciu interfejsu sieciowego.

Istnieją dwie metody przydzielania adresu IP do zasobu — dynamiczna i statyczna. Domyślną metodą alokacji jest metoda dynamiczna, w której adres IP nie jest przydzielany w czasie jego tworzenia. Zamiast tego adres IP jest przydzielany podczas tworzenia maszyny wirtualnej lub uruchamiania zatrzymanej maszyny wirtualnej. Adres IP jest zwalniany, gdy zatrzymujesz lub usuwasz maszynę wirtualną. 

Aby mieć pewność, że adres IP maszyny wirtualnej pozostaje taki sam, ustaw metodę alokacji jawnie jako statyczną. W takim przypadku adres IP jest przypisywany natychmiast. Jest on zwalniany tylko wtedy, gdy usuniesz maszynę wirtualną lub zmienisz jej metodę alokacji na dynamiczną.
    
Poniższa tabela zawiera listę metod, których można użyć do utworzenia adresu IP.

| Metoda | Opis |
| ------ | ----------- |
| [Witryna Azure Portal](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Domyślnie publiczne adresy IP są dynamiczne, a skojarzone z nimi adresy mogą ulec zmianie po zatrzymaniu lub usunięciu maszyny wirtualnej. Aby mieć pewność, że maszyna wirtualna zawsze używa tego samego publicznego adresu IP, utwórz statyczny publiczny adres IP. Domyślnie podczas tworzenia maszyny wirtualnej w portalu do karty sieciowej jest przypisywany dynamiczny prywatny adres IP. Ten adres IP można zmienić na statyczny po utworzeniu maszyny wirtualnej.|
| [Azure PowerShell](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Używasz [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) z parametrem **-metodę AllocationMethod jako** jako dynamicznym lub statycznym. |
| [Interfejs wiersza polecenia platformy Azure](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Użyj polecenia [az network public-ip create](/cli/azure/network/public-ip) z parametrem **--allocation-method** jako dynamicznym lub statycznym. |
| [Szablon](../virtual-network/template-samples.md) | Przewodnik [Interfejs sieciowy w sieci wirtualnej z publicznym adresem IP](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) ułatwia wdrożenie publicznego adresu IP przy użyciu szablonu. |

Po utworzeniu publicznego adresu IP można skojarzyć go z maszyną wirtualną, przypisując go do karty sieciowej.

## <a name="virtual-network-and-subnets"></a>Sieć wirtualna i podsieci

Podsieć jest zakresem adresów IP w sieci wirtualnej. Sieć wirtualną można podzielić na wiele podsieci w celu jej uporządkowania i zapewnienia bezpieczeństwa. Każda karta sieciowa w maszynie wirtualnej jest połączona z jedną podsiecią w jednej sieci wirtualnej. Karty sieciowe połączone z podsieciami (tymi samymi lub różnymi) w ramach sieci wirtualnej mogą komunikować się ze sobą bez dodatkowego konfigurowania.

Konfigurowanie sieci wirtualnej obejmuje określenie topologii, w tym dostępnych przestrzeni adresowych i podsieci. Jeśli sieć wirtualna ma być połączona z innymi sieciami wirtualnymi lub lokalnymi, musisz wybrać zakresy adresów, które nie nakładają się na siebie. Adresy IP są prywatne i nie można uzyskać do nich dostępu z Internetu, które były prawdziwe tylko dla adresów IP bez obsługi routingu, takich jak 10.0.0.0/8, 172.16.0.0/12 lub 192.168.0.0/16. Obecnie platforma Azure traktuje dowolny zakres adresów jako część przestrzeni prywatnych adresów IP w sieci wirtualnej. Przestrzeń ta jest dostępna w danej sieci wirtualnej oraz pozostałych, wzajemnie połączonych sieciach wirtualnych, a także z lokalizacji lokalnej. 

Jeśli w Twojej organizacji sieciami wewnętrznymi zajmuje się inna osoba, porozmawiaj z nią przed wybraniem przestrzeni adresowej. Upewnij się, że nie ma żadnych nakładania i poinformuj ich o miejscu, którego chcesz użyć, aby nie próbowały używać tego samego zakresu adresów IP. 

Domyślnie między podsieciami nie ma zabezpieczeń granicznych, co pozwala maszynom wirtualnym znajdującym się w poszczególnych podsieciach komunikować się ze sobą. Można jednak skonfigurować sieciowe grupy zabezpieczeń (NSG), które umożliwiają kontrolowanie ruchu przychodzącego do podsieci i maszyn wirtualnych oraz ruchu wychodzącego z nich. 

Poniższa tabela zawiera listę metod, których można użyć do utworzenia sieci wirtualnej i podsieci.    

| Metoda | Opis |
| ------ | ----------- |
| [Witryna Azure Portal](../virtual-network/quick-create-portal.md) | Jeśli wybierzesz opcję utworzenia sieci wirtualnej podczas tworzenia maszyny wirtualnej na platformie Azure, nazwa sieci wirtualnej będzie składać się z nazwy grupy zasobów zawierającej sieć wirtualną i ciągu **-vnet**. Przestrzeń adresowa to 10.0.0.0/24, wymagana nazwa podsieci to **domyślna**, a zakres adresów podsieci to 10.0.0.0/24. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | Do utworzenia podsieci i sieci wirtualnej służy funkcja [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) i [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) . Można również użyć [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) , aby dodać podsieć do istniejącej sieci wirtualnej. |
| [Interfejs wiersza polecenia platformy Azure](../virtual-network/quick-create-cli.md) | Podsieć i sieć wirtualna są tworzone jednocześnie. Użyj polecenia [az network vnet create](/cli/azure/network/vnet) z parametrem **--subnet-name**, podając nazwę podsieci. |
| Szablon | Najprostszym sposobem utworzenia sieci wirtualnej i podsieci jest pobranie istniejącego szablonu, takiego jak [Virtual Network z dwiema podsieciami](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), i zmodyfikowanie go w zależności od potrzeb. |

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

[Sieciowa grupa zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md) zawiera listę reguł listy kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy do podsieci i/lub kart sieciowych. Sieciowe grupy zabezpieczeń można kojarzyć z podsieciami lub poszczególnymi kartami sieciowymi połączonymi z podsiecią. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich maszyn wirtualnych w tej podsieci. Ponadto ruch do poszczególnych kart sieciowych można ograniczyć przez skojarzenie sieciowej grupy zabezpieczeń bezpośrednio z kartą sieciową.

Sieciowe grupy zabezpieczeń zawierają dwa zestawy reguł: zestaw reguł przychodzących i wychodzących. Priorytety reguł muszą być unikatowe w poszczególnych zestawach. Każda reguła ma właściwości protokołu, zakresów portów źródłowych i docelowych, prefiksów adresów, kierunku ruchu, priorytetu i typu dostępu. 

Wszystkie sieciowe grupy zabezpieczeń zawierają zestaw reguł domyślnych. Reguł domyślnych nie można usunąć, ale ponieważ mają przypisany najniższy priorytet, mogą być zastąpione przez tworzone zasady. 

Po skojarzeniu sieciowej grupy zabezpieczeń z kartą sieciową reguły dostępu do sieci w sieciowej grupie zabezpieczeń są stosowane tylko do tej karty sieciowej. Jeśli na maszynie wirtualnej z wieloma kartami sieciowymi sieciowa grupa zabezpieczeń jest stosowana do jednej karty sieciowej, nie ma to wpływu na ruch powiązany z innymi kartami sieciowymi. Można skojarzyć różne sieciowe grupy zabezpieczeń z kartą sieciową (lub maszyną wirtualną, w zależności od modelu wdrażania) i podsiecią, z którą jest powiązana karta sieciowa albo maszyna wirtualna. Priorytet zależy od kierunku ruchu.

Pamiętaj, aby [zaplanować](../virtual-network/virtual-network-vnet-plan-design-arm.md) sieciowe grupy zabezpieczeń podczas planowania sieci wirtualnej i maszyn wirtualnych.

Poniższa tabela zawiera listę metod, których można użyć do utworzenia sieciowej grupy zabezpieczeń.

| Metoda | Opis |
| ------ | ----------- |
| [Witryna Azure Portal](../virtual-network/tutorial-filter-network-traffic.md) | Utworzenie maszyny wirtualnej w witrynie Azure Portal obejmuje automatyczne utworzenie sieciowej grupy zabezpieczeń i skojarzenie jej z kartą sieciową. Nazwa sieciowej grupy zabezpieczeń składa się z nazwy maszyny wirtualnej i ciągu **-nsg**. Konfiguracja tej sieciowej grupy zabezpieczeń jest następująca: jedna reguła ruchu przychodzącego z priorytetem 1000, ustawienie usługi — RDP, ustawienie protokołu — TCP, ustawienie portu — 3389 i ustawienie akcji — Zezwalaj. Jeśli chcesz zezwolić na inne rodzaje ruchu przychodzącego do maszyny wirtualnej, musisz dodać dodatkowe reguły do sieciowej grupy zabezpieczeń. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | Użyj [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) i podaj informacje o wymaganych regułach. Użyj [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) , aby utworzyć sieciowej grupy zabezpieczeń. Użyj [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) , aby skonfigurować sieciowej grupy zabezpieczeń dla podsieci. Użyj [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) , aby dodać sieciowej grupy zabezpieczeń do sieci wirtualnej. |
| [Interfejs wiersza polecenia platformy Azure](../virtual-network/tutorial-filter-network-traffic-cli.md) | Użyj polecenia [az network nsg create](/cli/azure/network/nsg), aby utworzyć początkową sieciową grupę zabezpieczeń. Użyj polecenia [az network nsg rule create](/cli/azure/network/nsg/rule), aby dodać reguły do sieciowej grupy zabezpieczeń. Użyj polecenia [az network vnet subnet update](/cli/azure/network/vnet/subnet), aby dodać sieciową grupę zabezpieczeń do podsieci. |
| [Szablon](../virtual-network/template-samples.md) | Przewodnik [Tworzenie sieciowej grupy zabezpieczeń](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) ułatwia wdrożenie sieciowej grupy zabezpieczeń przy użyciu szablonu. |

## <a name="load-balancers"></a>Moduły równoważenia obciążenia

Usługa [Azure Load Balancer](../load-balancer/load-balancer-overview.md) zapewnia aplikacjom wysoką dostępność i wydajność sieci. Moduł równoważenia obciążenia można skonfigurować pod kątem [równoważenia przychodzącego ruchu internetowego](../load-balancer/components.md#frontend-ip-configurations) do maszyn wirtualnych lub [równoważenia ruchu między maszynami wirtualnymi w sieci wirtualnej](../load-balancer/components.md#frontend-ip-configurations). Moduł równoważenia obciążenia umożliwia również równoważenie ruchu między komputerami lokalnymi i maszynami wirtualnymi w sieci obejmującej różne lokalizacje oraz przekazywanie ruchu zewnętrznego do określonej maszyny wirtualnej.

Moduł równoważenia obciążenia mapuje ruch przychodzący i wychodzący między publicznym adresem IP i portem modułu równoważenia obciążenia a prywatnym adresem IP i portem maszyny wirtualnej.

Podczas tworzenia modułu równoważenia obciążenia należy również wziąć pod uwagę te elementy konfiguracji:

- **Konfiguracja adresu IP frontonu** — moduł równoważenia obciążenia może zawierać jeden lub więcej adresów IP frontonu. Te adresy IP są używane podczas transferu danych przychodzących.
- **Pula adresów zaplecza** — adresy IP skojarzone z kartą sieciową, do której jest dystrybuowane obciążenie.
- **[Przekazywanie portów](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** — definiuje sposób przepływu ruchu przychodzącego za pomocą adresu IP frontonu i dystrybuowania do adresu IP zaplecza wykorzystującego reguły NAT dla ruchu przychodzącego.
- **Reguły modułu równoważenia obciążenia** — mapują dany adres IP frontonu i kombinację portów na zestaw adresów IP zaplecza i kombinację portów. Moduł równoważenia obciążenia może mieć różne reguły równoważenia obciążenia. Każda reguła zawiera kombinację adresu IP frontonu i portu oraz adresu IP zaplecza i portu, które są powiązane z maszynami wirtualnymi.
- **[Sondy](../load-balancer/load-balancer-custom-probe-overview.md)** — monitorują kondycję maszyn wirtualnych. Jeśli sonda nie odpowiada, moduł równoważenia obciążenia zaprzestaje inicjowania nowych połączeń z maszyną wirtualną o złej kondycji. Nie ma to wpływu na istniejące połączenia. Nowe połączenia są inicjowane z maszynami wirtualnymi o prawidłowej kondycji.
- **[Reguły ruchu wychodzącego](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** — Reguła ruchu wychodzącego konfiguruje wychodzące translację adresów sieciowych (NAT) dla wszystkich maszyn wirtualnych lub wystąpień identyfikowanych przez pulę zaplecza usługa Load Balancer w warstwie Standardowa, które mają zostać przetłumaczone na fronton.

Poniższa tabela zawiera listę metod, których można użyć do utworzenia modułu równoważenia obciążenia dostępnego z Internetu.

| Metoda | Opis |
| ------ | ----------- |
| Azure Portal |  Można [równoważyć obciążenie ruchem internetowym do maszyn wirtualnych przy użyciu Azure Portal](../load-balancer/tutorial-load-balancer-standard-manage-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Aby podać identyfikator utworzonego wcześniej publicznego adresu IP, użyj polecenie [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) z parametrem **-PublicIpAddress** . Użyj [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) , aby utworzyć konfigurację puli adresów zaplecza. Użyj [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) , aby utworzyć reguły NAT dla ruchu przychodzącego skojarzone z utworzoną konfiguracją adresów IP frontonu. Użyj [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) , aby utworzyć sondy, które są potrzebne. Użyj [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) , aby utworzyć konfigurację modułu równoważenia obciążenia. Użyj [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) , aby utworzyć moduł równoważenia obciążenia.|
| [Interfejs wiersza polecenia platformy Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | Użyj polecenia [az network lb create](/cli/azure/network/lb), aby utworzyć początkową konfigurację modułu równoważenia obciążenia. Użyj polecenia [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip), aby dodać wcześniej utworzony publiczny adres IP. Użyj polecenia [az network lb address-pool create](/cli/azure/network/lb/address-pool), aby dodać konfigurację puli adresów zaplecza. Użyj polecenia [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule), aby dodać reguły NAT. Użyj polecenia [az network lb rule create](/cli/azure/network/lb/rule), aby dodać reguły modułu równoważenia obciążenia. Użyj polecenia [az network lb probe create](/cli/azure/network/lb/probe), aby dodać sondy. |
| [Szablon](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Przewodnik [Moduł równoważenia obciążenia z dwiema maszynami wirtualnymi oraz konfiguracja reguł NAT w module równoważenia obciążenia](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) ułatwia wdrożenie modułu równoważenia obciążenia przy użyciu szablonu. |
    
Poniższa tabela zawiera listę metod, których można użyć do utworzenia wewnętrznego modułu równoważenia obciążenia.

| Metoda | Opis |
| ------ | ----------- |
| Azure Portal | [W Azure Portal można zrównoważyć obciążenie ruchem wewnętrznym przy użyciu modułu równoważenia obciążenia](../load-balancer/quickstart-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Aby podać prywatny adres IP w podsieci sieciowej, użyj polecenie [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) z parametrem **-PrivateIpAddress** . Użyj [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) , aby utworzyć konfigurację puli adresów zaplecza. Użyj [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) , aby utworzyć reguły NAT dla ruchu przychodzącego skojarzone z utworzoną konfiguracją adresów IP frontonu. Użyj [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) , aby utworzyć sondy, które są potrzebne. Użyj [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) , aby utworzyć konfigurację modułu równoważenia obciążenia. Użyj [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) , aby utworzyć moduł równoważenia obciążenia.|
| [Interfejs wiersza polecenia platformy Azure](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | Użyj polecenia [az network lb create](/cli/azure/network/lb), aby utworzyć początkową konfigurację modułu równoważenia obciążenia. Użyj polecenia [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) z parametrem **--private-ip-address**, aby zdefiniować prywatny adres IP. Użyj polecenia [az network lb address-pool create](/cli/azure/network/lb/address-pool), aby dodać konfigurację puli adresów zaplecza. Użyj polecenia [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule), aby dodać reguły NAT. Użyj polecenia [az network lb rule create](/cli/azure/network/lb/rule), aby dodać reguły modułu równoważenia obciążenia. Użyj polecenia [az network lb probe create](/cli/azure/network/lb/probe), aby dodać sondy.|
| [Szablon](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Przewodnik [Moduł równoważenia obciążenia z dwiema maszynami wirtualnymi oraz konfiguracja reguł NAT w module równoważenia obciążenia](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) ułatwia wdrożenie modułu równoważenia obciążenia przy użyciu szablonu. |

### <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych

Aby uzyskać więcej informacji na temat modułu równoważenia obciążenia i zestawów skalowania maszyn wirtualnych, zobacz [sieci dla zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

## <a name="vms"></a>Maszyny wirtualne

Maszyny wirtualne można tworzyć w tej samej sieci wirtualnej i mogą one komunikować się ze sobą przy użyciu prywatnych adresów IP. Komunikacja między maszynami jest możliwa, nawet jeśli znajdują się one w różnych podsieciach — nie ma potrzeby konfigurowania bramy ani używania publicznych adresów IP. Aby połączyć maszyny wirtualne z siecią wirtualną, podczas tworzenia poszczególnych maszyn wirtualnych należy przypisać je do utworzonej sieci wirtualnej i podsieci. Ustawienia sieci maszyn wirtualnych są określane podczas wdrażania lub uruchamiania.  

Przypisywanie adresów IP odbywa się podczas wdrażania maszyn wirtualnych. Jeśli w sieci wirtualnej lub podsieci wdrożono wiele maszyn wirtualnych, uzyskują one adresy IP podczas uruchamiania. Możesz również przydzielić statyczny adres IP do maszyny wirtualnej. W przypadku przydzielenia statycznego adresu IP należy rozważyć użycie określonej podsieci, aby uniknąć przypadkowego ponownego użycia statycznego adresu IP dla innej maszyny wirtualnej.  

Migracja utworzonej maszyny wirtualnej do sieci wirtualnej nie obejmuje jedynie wprowadzenia prostej zmiany w konfiguracji. Maszynę wirtualną należy ponownie wdrożyć w sieci wirtualnej. Najprostszym sposobem ponownego wdrożenia jest usunięcie maszyny wirtualnej (ale bez usuwania jakichkolwiek dysków podłączonych do niej), a następnie ponowne utworzenie maszyny wirtualnej w sieci wirtualnej przy użyciu oryginalnych dysków. 

Poniższa tabela zawiera listę metod, których można użyć do utworzenia maszyny wirtualnej w sieci wirtualnej.

| Metoda | Opis |
| ------ | ----------- |
| [Witryna Azure Portal](./windows/quick-create-portal.md) | Używane są domyślne ustawienia sieci, takie jak podczas tworzenia maszyny wirtualnej z jedną kartą sieciową. Aby utworzyć maszynę wirtualną z wieloma kartami sieciowymi, należy użyć innej metody. |
| [Azure PowerShell](./windows/tutorial-manage-vm.md) | Obejmuje użycie [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) do dodania wcześniej utworzonej karty sieciowej do konfiguracji maszyny wirtualnej. |
| [Interfejs wiersza polecenia platformy Azure](./linux/create-cli-complete.md) | Utwórz i podłącz maszynę wirtualną do sieci wirtualnej, podsieci i karty sieciowej, która kompiluje się w miarę pojedynczych kroków. |
| [Szablon](./windows/ps-template.md) | Przewodnik [Bardzo proste wdrożenie maszyny wirtualnej z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) ułatwia wdrożenie maszyny wirtualnej przy użyciu szablonu. |

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z procedurami związanymi z maszyną wirtualną dotyczącymi zarządzania sieciami wirtualnymi platformy Azure dla maszyn wirtualnych, zobacz samouczki [systemu Windows](../virtual-machines/windows/tutorial-virtual-network.md) lub [Linux](../virtual-machines/linux/tutorial-virtual-network.md) .

Dostępne są również samouczki dotyczące równoważenia obciążenia maszyn wirtualnych i tworzenia aplikacji o wysokiej dostępności dla [systemu Windows](../virtual-machines/windows/tutorial-load-balancer.md) lub [Linux](../virtual-machines/linux/tutorial-load-balancer.md).

- Dowiedz się, jak skonfigurować [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md). 
- Dowiedz się, jak skonfigurować [połączenia między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Dowiedz się, jak [rozwiązywać problemy z trasami](../virtual-network/diagnose-network-routing-problem.md).
- Dowiedz się więcej o [przepustowości sieci maszyn wirtualnych](../virtual-network/virtual-machine-network-throughput.md).