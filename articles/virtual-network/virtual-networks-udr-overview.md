---
title: Routing ruchu w sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak platforma Azure wybiera trasę ruchu w sieci wirtualnej i jak możesz dostosować routing na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2021
ms.author: aldomel
ms.openlocfilehash: 0dd053fa268e88c281c1fe6c00339fe6a6edf27a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732605"
---
# <a name="virtual-network-traffic-routing"></a>Routing ruchu w sieci wirtualnej

Dowiedz się więcej na temat sposobu kierowania ruchu platformy Azure między platformą Azure a zasobami lokalnymi i internetowymi. Platforma Azure automatycznie tworzy tabelę tras dla każdej podsieci w sieci wirtualnej platformy Azure i dodaje do tabeli domyślne trasy systemu. Aby dowiedzieć się więcej na temat sieci wirtualnych i podsieci, zobacz [Virtual network overview (Omówienie sieci wirtualnych)](virtual-networks-overview.md). Możesz zastąpić niektóre trasy systemowe platformy Azure [trasami niestandardowymi](#custom-routes) i dodać dodatkowe trasy niestandardowe do tabel tras. Platforma Azure kieruje ruch wychodzący z podsieci na podstawie tras w tabeli tras podsieci.

## <a name="system-routes"></a>Trasy systemowe

Platforma Azure automatycznie tworzy trasy systemowe i przypisuje trasy do każdej podsieci w sieci wirtualnej. Nie możesz tworzyć tras systemowych ani nie możesz ich usuwać, ale możesz zastąpić niektóre trasy systemowe [trasami niestandardowymi](#custom-routes). Platforma Azure tworzy domyślne trasy systemowe dla każdej podsieci i dodaje dodatkowe [opcjonalne trasy domyślne](#optional-default-routes) do określonych podsieci lub do każdej podsieci, gdy korzystasz z określonych możliwości platformy Azure.

### <a name="default"></a>Domyślne

Każda trasa zawiera prefiks adresu i typ następnego przeskoku. Gdy ruch opuszczający podsieć jest wysyłany na adres IP w ramach prefiksu adresu trasy, trasa zawierająca prefiks jest trasą używaną przez platformę Azure. Dowiedz się więcej o [sposobie wybierania trasy przez platformę Azure](#how-azure-selects-a-route), gdy wiele tras zawiera te same prefiksy lub nakładające się prefiksy. Przy każdym utworzeniu sieci wirtualnej platforma Azure automatycznie tworzy następujące domyślne trasy systemowe dla każdej podsieci w sieci wirtualnej:

|Źródło |Prefiksy adresów                                        |Typ następnego przeskoku  |
|-------|---------                                               |---------      |
|Domyślne|Unikatowy dla sieci wirtualnej                           |Sieć wirtualna|
|Domyślne|0.0.0.0/0                                               |Internet       |
|Domyślne|10.0.0.0/8                                              |Brak           |
|Domyślne|192.168.0.0/16                                          |Brak           |
|Domyślne|100.64.0.0/10                                           |Brak           |

Typy następnego przeskoku wymienione w powyższej tabeli określają sposób, w jaki platforma Azure kieruje ruch przeznaczony dla wymienionego prefiksu adresu. Poniżej znajdują się objaśnienia typów następnego przeskoku:

* **Sieć wirtualna**: kieruje ruchem między zakresami adresów w obrębie [przestrzeni adresowej](manage-virtual-network.md#add-or-remove-an-address-range) sieci wirtualnej. Platforma Azure tworzy trasę z prefiksem adresu odpowiadającym każdemu zakresowi adresów zdefiniowanemu w przestrzeni adresowej sieci wirtualnej. Jeśli przestrzeń adresowa sieci wirtualnej ma zdefiniowane wiele zakresów adresów, platforma Azure tworzy oddzielną trasę dla każdego zakresu adresów. Platforma Azure automatycznie kieruje ruchem między podsieciami za pomocą tras utworzonych dla każdego zakresu adresów. Nie musisz definiować bram dla platformy Azure, aby kierować ruchem między podsieciami. Chociaż sieć wirtualna zawiera podsieci, a każda podsieć ma zdefiniowany zakres adresów, platforma Azure *nie* tworzy tras domyślnych dla zakresów adresów podsieci, ponieważ każdy zakres adresów podsieci mieści się w ramach zakresu adresów przestrzeni adresowej sieci wirtualnej.<br>
* **Internet**: kieruje określonym przez prefiks adresu ruchem do Internetu. Domyślna trasa systemowa określa prefiks adresu 0.0.0.0/0. Jeśli nie zastąpisz domyślnych tras platformy Azure, platforma Azure kieruje ruch dla każdego adresu, który nie został określony przez zakres adresów w ramach sieci wirtualnej, do Internetu z jednym wyjątkiem. Jeśli adres docelowy jest przeznaczony dla jednej z usług platformy Azure, platforma Azure kieruje ruch bezpośrednio do usługi za pośrednictwem sieci szkieletowej platformy Azure zamiast kierować ruch do Internetu. Ruch między usługami Azure nie przechodzi przez Internet niezależnie od tego, w którym regionie platformy Azure istnieje sieć wirtualna lub w którym regionie platformy Azure zostało wdrożone wystąpienie usługi platformy Azure. Można zastąpić domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 za pomocą [trasy niestandardowej](#custom-routes).<br>
* **Brak**: ruch kierowany do typu następnego przeskoku **Brak** jest porzucany, a nie kierowany poza podsieć. Platforma Azure automatycznie tworzy trasy domyślne dla następujących prefiksów adresów:<br>

    * **10.0.0.0/8 i 192.168.0.0/16**: zarezerwowane do użytku prywatnego w RFC 1918.<br>
    * **100.64.0.0/10**: zarezerwowane w dokumencie RFC 6598.

    Po przypisaniu dowolnego z poprzednich zakresów adresów w przestrzeni adresowej sieci wirtualnej platforma Azure automatycznie zmieni typ następnego przeskoku trasy z **Brak** na **Sieć wirtualna**. Po przypisaniu zakresu adresów do przestrzeni adresowej sieci wirtualnej, która obejmuje, ale nie jest taka sama jak, jeden z czterech zarezerwowanych prefiksów adresów, platforma Azure usuwa trasę dla tego prefiksu i dodaje trasę dla dodanego przez Ciebie prefiksu adresu, przy czym **Sieć wirtualna** jest typem następnego przeskoku.

### <a name="optional-default-routes"></a>Opcjonalne trasy domyślne

Platforma Azure dodaje dodatkowe domyślne trasy systemowe dla różnych funkcji platformy Azure, ale tylko po włączeniu tych funkcji przez Ciebie. W zależności od możliwości platforma Azure dodaje opcjonalne trasy domyślne do określonych podsieci w sieci wirtualnej albo do wszystkich podsieci w sieci wirtualnej. Do dodatkowych tras systemowych i typów następnych przeskoków, które platforma Azure może dodać, gdy włączysz różne możliwości, należą:

|Źródło                 |Prefiksy adresów                       |Typ następnego przeskoku|Podsieć w sieci wirtualnej, której trasa jest dodawana|
|-----                  |----                                   |---------                    |--------|
|Domyślne                |Unikatowy dla sieci wirtualnej, na przykład: 10.1.0.0/16|Komunikacja równorzędna sieci wirtualnych                 |Wszystko|
|Brama sieci wirtualnej|Prefiksy anonsowane lokalnie za pośrednictwem protokołu BGP lub skonfigurowane w bramie sieci lokalnej     |Brama sieci wirtualnej      |Wszystko|
|Domyślne                |Wiele                               |VirtualNetworkServiceEndpoint|Tylko podsieć, dla której jest włączony punkt końcowy usługi.|

* **Komunikacja równorzędna sieci wirtualnej (VNet)**: podczas tworzenia komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi dodawana jest trasa dla każdego zakresu adresów w obrębie przestrzeni adresowej w każdej sieci wirtualnej, dla której jest tworzona komunikacja równorzędna. Dowiedz się więcej o [komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md).<br>
* **Brama sieci wirtualnej**: co najmniej jedna trasa w ramach *bramy sieci wirtualnej* wymienionej jako typ następnego przeskoku jest dodawana po dodaniu bramy sieci wirtualnej do sieci wirtualnej. Elementem źródłowym jest również *brama sieci wirtualnej*, ponieważ brama dodaje trasy do podsieci. Jeśli Brama sieci lokalnej wymienia trasy protokołu[BGP](#border-gateway-protocol)(Border Gateway Protocol) z bramą sieci wirtualnej platformy Azure, trasa jest dodawana do każdej trasy propagowanej z bramy sieci lokalnej. Zaleca się zsumowanie tras lokalnych do największych możliwych zakresów adresów, aby jak najmniejsza liczba adresów była propagowana do bramy sieci wirtualnej platformy Azure. Istnieją ograniczenia liczby tras, które możesz propagować do bramy sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).<br>
* **VirtualNetworkServiceEndpoint**: publiczne adresy IP dla niektórych usług są dodawane do tabeli tras przez platformę Azure po włączeniu punktu końcowego usługi do usługi. Punkty końcowe usługi są włączane dla poszczególnych podsieci w sieci wirtualnej, więc trasa jest dodawana tylko do tabeli tras podsieci, dla której jest włączony punkt końcowy usługi. Publiczne adresy IP usług platformy Azure są okresowo zmieniane. Platforma Azure zarządza adresami w tabeli tras automatycznie po zmianie adresów. Dowiedz się więcej o [punktach końcowych usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md) i usługach, dla których można utworzyć punkty końcowe usługi.<br>

    > [!NOTE]
    > Typy następnego przeskoku **Komunikacja równorzędna sieci wirtualnej** i **VirtualNetworkServiceEndpoint** są dodawane tylko do tabel tras podsieci w sieciach wirtualnych utworzonych za pośrednictwem modelu wdrażania przy użyciu usługi Azure Resource Manager. Typy następnych przeskoków nie są dodawane do tabel tras, które są skojarzone z podsieciami sieci wirtualnej utworzonymi za pośrednictwem klasycznego modelu wdrażania. Dowiedz się więcej o [modelach wdrażania](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure.

## <a name="custom-routes"></a>Trasy niestandardowe

Tworzenie niestandardowych tras przez tworzenie tras [zdefiniowanych przez użytkownika](#user-defined) albo przez wymianę tras [protokołu BGP](#border-gateway-protocol) między Twoją bramą sieci lokalnej i bramą sieci wirtualnej platformy Azure. 

### <a name="user-defined"></a>Zdefiniowane przez użytkownika

Można utworzyć niestandardowe lub zdefiniowane przez użytkownika (statyczne) trasy na platformie Azure, aby zastąpić domyślne trasy systemu platformy Azure lub dodać dodatkowe trasy do tabeli tras podsieci. Na platformie Azure tworzysz tabelę tras, a następnie kojarzysz ją przynajmniej z zerową liczbą podsieci sieci wirtualnej. Każda podsieć może mieć skojarzoną ze sobą żadną lub jedną tabelę tras. Aby dowiedzieć się więcej o maksymalnej liczbie tras, które możesz dodać do tabeli tras, oraz o maksymalnej liczbie tabel tras zdefiniowanych przez użytkownika, które można utworzyć dla subskrypcji platformy Azure, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Jeśli utworzysz tabelę tras i skojarzysz ją z podsiecią, to trasy w niej są łączone z trasami domyślnymi lub zastępują trasy domyślne, które platforma Azure domyślnie dodaje do podsieci.

Podczas tworzenia tras zdefiniowanych przez użytkownika możesz określić poniższe typy następnych przeskoków:

* **Urządzenie wirtualne**: urządzenie wirtualne to maszyna wirtualna, na której zwykle działa aplikacja sieci, taka jak zapora. Aby dowiedzieć się więcej o różnych wstępnie skonfigurowanych sieciowych urządzeniach wirtualnych, które możesz wdrożyć w sieci wirtualnej, zobacz witrynę [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Podczas tworzenia trasy z typem przeskoku **Urządzenie wirtualne** należy określić także adres IP następnego przeskoku. Adresem IP może być:

    * [Prywatny adres IP](./private-ip-addresses.md) interfejsu sieciowego dołączonego do maszyny wirtualnej. Każdy interfejs sieciowy dołączony do maszyny wirtualnej, która przesyła dalej ruch sieciowy do adresu innego niż własny, musi mieć w tym celu włączoną opcję *Włącz przekazywanie IP* platformy Azure. To ustawienie wyłącza sprawdzanie przez platformę Azure elementu źródłowego i docelowego interfejsu sieciowego. Dowiedz się więcej o tym, jak [włączyć przekazywanie IP dla interfejsu sieciowego](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Chociaż pozycja *Włącz przekazywanie adresu IP* to ustawienie platformy Azure, włączenie przekazywania adresu IP w ramach systemu operacyjnego maszyny wirtualnej może być konieczne, aby urządzenie przekazywało dalej ruch między prywatnymi adresami IP przypisanami do interfejsów sieciowych platformy Azure. Jeśli urządzenie musi kierować ruch do publicznego adresu IP, ruch musi zostać przekierowany przy użyciu serwera proxy albo adres sieciowy musi przetłumaczyć prywatny adres IP źródła na własny prywatny adres IP, który następnie platforma Azure tłumaczy przy użyciu adresu sieciowego na publiczny adres IP przed wysłaniem ruchu do Internetu. Aby ustalić wymagane ustawienia maszyny wirtualnej, zobacz dokumentację swojego systemu operacyjnego lub aplikacji sieciowej. Aby lepiej zrozumieć połączenia wychodzące na platformie Azure, zobacz [Understanding outbound connections (Opis połączeń wychodzących)](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>

        > [!NOTE]
        > Wdróż urządzenie wirtualne w podsieci innej niż ta, gdzie są wdrożone zasoby, które są przesyłane przez urządzenie wirtualne. Wdrożenie urządzenia wirtualnego w tej samej podsieci, a następnie zastosowanie tabeli tras do podsieci, która kieruje ruch przez urządzenie wirtualne, może spowodować zapętlenie tras, w którym ruch nigdy nie opuszcza podsieci.

    * Prywatny adres IP [wewnętrznego modułu równoważenia obciążenia](../load-balancer/quickstart-load-balancer-standard-internal-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure. Moduł równoważenia obciążenia jest często używany jako część [strategii wysokiej dostępności sieciowych urządzeń wirtualnych](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    Możesz określić trasę 0.0.0.0/0 jako prefiks adresu i typ następnego przeskoku urządzenia wirtualnego, umożliwiając urządzeniu sprawdzanie ruchu i określanie, czy przekazać ruch dalej, czy też go porzucić. Jeśli zamierzasz utworzyć trasę zdefiniowaną przez użytkownika, która zawiera prefiks adresu 0.0.0.0/0, przeczytaj najpierw [0.0.0.0/0 address prefix (Prefiks adresu 0.0.0.0/0)](#default-route).

* **Brama sieci wirtualnej**: określ, kiedy ruch przeznaczony dla określonych prefiksów adresów ma być kierowany do bramy sieci wirtualnej. Brama sieci wirtualnej musi zostać utworzona z typem **VPN**. W zdefiniowanej przez użytkownika trasie nie możesz określić bramy sieci wirtualnej utworzonej jako typ **ExpressRoute**, ponieważ dla usługi ExpressRoute musisz użyć protokołu BGP dla tras niestandardowych. Możesz zdefiniować trasę, która kieruje ruch przeznaczony dla prefiksu adresu 0.0.0.0/0 do bramy sieci wirtualnej [opartej na trasach](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). W swojej lokalizacji możesz mieć urządzenie, które sprawdza ruch i określa, czy przekazać go dalej, czy też go porzucić. Jeśli zamierzasz utworzyć zdefiniowaną przez użytkownika trasę dla prefiksu adresu 0.0.0.0/0, przeczytaj najpierw [0.0.0.0/0 address prefix (Prefiks adresu 0.0.0.0/0)](#default-route). Zamiast konfigurować zdefiniowaną przez użytkownika trasę dla prefiksu adresu 0.0.0.0/0, możesz anonsować trasę z prefiksem 0.0.0.0/0 za pomocą protokołu BGP, jeśli [masz włączony protokół BGP dla bramy sieci wirtualnej sieci VPN](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>
* **Brak**: określ, kiedy chcesz porzucić ruch do prefiksu adresu, zamiast przekazywać ten ruch do miejsca docelowego. Jeśli nie skonfigurowano w pełni możliwości, platforma Azure może wyświetlać pozycję *Brak* dla niektórych opcjonalnych tras systemowych. Jeśli na przykład pozycja *Brak* zostanie wyświetlona jako **Adres IP następnego przeskoku** dla **Typu następnego przeskoku** równego *Brama sieci wirtualnej* lub *Urządzenie wirtualne*, może to wynikać z tego, że urządzenie nie jest uruchomione lub nie jest w pełni skonfigurowane. Platforma Azure tworzy [domyślne trasy](#default) systemowe dla zarezerwowanych prefiksów adresów mające pozycję **Brak** jako typ następnego przeskoku.<br>
* **Sieć wirtualna**: określ, kiedy chcesz zastąpić domyślny routing w sieci wirtualnej. Zobacz [przykład routingu](#routing-example), aby zapoznać się z przykładem, dlaczego możesz utworzyć trasę z typem przeskoku **Sieć wirtualna**.<br>
* **Internet**: określ, kiedy chcesz jawnie skierować ruch przeznaczony do prefiksu adresu do Internetu lub czy chcesz, aby ruch przeznaczony do usług platformy Azure mających publiczne adresy IP pozostał w sieci szkieletowej platformy Azure.

Typu **Komunikacja równorzędna sieci wirtualnej** lub **VirtualNetworkServiceEndpoint** nie możesz określić jako typu następnego przeskoku w trasach zdefiniowanych przez użytkownika. Trasy z typami następnego przeskoku **Komunikacja równorzędna sieci wirtualnej** lub **VirtualNetworkServiceEndpoint** są tworzone tylko przez platformę Azure podczas konfigurowania komunikacji równorzędnej sieci wirtualnej lub punktu końcowego usługi.

### <a name="service-tags-for-user-defined-routes-preview"></a>Tagi usług dla tras zdefiniowanych przez użytkownika (wersja zapoznawcza)

Teraz można określić [tag usługi](service-tags-overview.md) jako prefiks adresu dla trasy zdefiniowanej przez użytkownika zamiast jawnego zakresu adresów IP. Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, co minimalizuje złożoność częstych aktualizacji tras zdefiniowanych przez użytkownika i zmniejsza liczbę tras, które należy utworzyć. W każdej tabeli tras można obecnie utworzyć 25 lub mniej tras za pomocą tagów usługi. </br>

> [!IMPORTANT]
> Znaczniki usługi dla tras zdefiniowanych przez użytkownika są obecnie dostępne w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

#### <a name="exact-match"></a>Dokładne dopasowanie
W przypadku dokładnego dopasowania prefiksu między trasą z jawnym prefiksem IP a trasą z tagiem usługi preferencja jest przyznawana trasie z jawnym prefiksem. Gdy wiele tras z tagami usług ma pasujące prefiksy IP, trasy będą oceniane w następującej kolejności: 

   1. Tagi regionalne (np. Storage. wschód, AppService. AustraliaCentral)
   2. Tagi najwyższego poziomu (np. Magazyn, AppService)
   3. AzureCloud Tagi regionalne (np. AzureCloud. canadacentral, AzureCloud. eastasia)
   4. Tag AzureCloud </br></br>

Aby użyć tej funkcji, należy określić nazwę tagu usługi dla parametru prefiksu adresu w poleceniach tabeli tras. Na przykład w programie PowerShell można utworzyć nową trasę do kierowania ruchu wysyłanego do prefiksu IP usługi Azure Storage do urządzenia wirtualnego za pomocą: </br>

```azurepowershell-interactive
New-AzRouteConfig -Name "StorageRoute" -AddressPrefix "Storage" -NextHopType "VirtualAppliance" -NextHopIpAddress "10.0.100.4"
```

To samo polecenie dla interfejsu wiersza polecenia będzie: </br>

```azurecli-interactive
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n StorageRoute --address-prefix Storage --next-hop-type VirtualAppliance --next-hop-ip-address 10.0.100.4
```
</br>


> [!NOTE] 
> W publicznej wersji zapoznawczej istnieje kilka ograniczeń. Ta funkcja nie jest obecnie obsługiwana w witrynie Azure Portal i jest dostępna tylko za pomocą programu PowerShell i interfejsu wiersza polecenia. Nie jest obsługiwane używanie z kontenerami. 

## <a name="next-hop-types-across-azure-tools"></a>Typy następnego przeskoku dla narzędzi platformy Azure

Nazwa wyświetlana i przywoływana dla typów następnego przeskoku jest różna dla witryny Azure Portal i narzędzi wiersza polecenia oraz usługi Azure Resource Manager i klasycznych modeli wdrażania. W poniższej tabeli wymieniono nazwy używane w odwołaniu do każdego typu następnego przeskoku dla różnych narzędzi i [modeli wdrażania](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Typ następnego przeskoku                   |Interfejs wiersza polecenia platformy Azure 2.0 i program PowerShell (Resource Manager) |Klasyczny interfejs wiersza polecenia platformy Azure i program PowerShell (wersja klasyczna)|
|-------------                   |---------                                       |-----|
|Brama sieci wirtualnej         |VirtualNetworkGateway                           |VPNGateway|
|Sieć wirtualna                 |VNetLocal                                       |VNETLocal (niedostępne w wersji klasycznej interfejsu wiersza polecenia w trybie asm)|
|Internet                        |Internet                                        |Internet (niedostępny w wersji klasycznej interfejsu wiersza polecenia w trybie asm)|
|Urządzenie wirtualne               |VirtualAppliance                                |VirtualAppliance|
|Brak                            |Brak                                            |Null (niedostępne w wersji klasycznej interfejsu wiersza polecenia w trybie asm)|
|Komunikacja równorzędna sieci wirtualnej         |Komunikacja równorzędna sieci wirtualnych                                    |Nie dotyczy|
|Punkt końcowy usługi dla sieci wirtualnej|VirtualNetworkServiceEndpoint                   |Nie dotyczy|



### <a name="border-gateway-protocol"></a>Protokół BGP (Border Gateway Protocol)

Brama sieci lokalnej może wymieniać trasy z bramą sieci wirtualnej platformy Azure przy użyciu protokołu BGP. Korzystanie z protokołu BGP z bramą sieci wirtualnej platformy Azure zależy od typu wybranego podczas tworzenia bramy. Jeśli wybrany typ to:

* **ExpressRoute**: musisz użyć protokołu BGP, aby anonsować trasy lokalne do routera Microsoft Edge. Nie możesz utworzyć tras zdefiniowanych przez użytkownika w celu wymuszania ruchu do bramy sieci wirtualnej usługi ExpressRoute, jeśli wdrożysz bramę sieci wirtualnej wdrożoną jako typ ExpressRoute. Możesz użyć tras zdefiniowanych przez użytkownika w celu wymuszania ruchu z usługi Express Route na przykład do sieciowego urządzenia wirtualnego.<br>
* **VPN**: opcjonalnie możesz użyć protokołu BGP. Aby uzyskać więcej informacji, zobacz [BGP with site-to-site VPN connections (Protokół BGP przy użyciu połączeń sieci VPN lokacja-lokacja)](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W przypadku, gdy wymieniasz trasy z platformą Azure przy użyciu protokołu BGP, oddzielna trasa jest dodawana do tabeli tras wszystkich podsieci w sieci wirtualnej dla każdego anonsowanego prefiksu. Trasa jest dodawana z *bramą sieci wirtualnej* wymienioną jako element źródłowy i typ następnego przeskoku. 

Propagacja trasy ER i VPN Gateway można wyłączyć w podsieci przy użyciu właściwości w tabeli tras. W przypadku wymiany tras z platformą Azure przy użyciu protokołu BGP trasy nie są dodawane do tabeli tras wszystkich podsieci z wyłączonym propagacją trasy bramy sieci wirtualnej. Łączność z połączeniami sieci VPN jest uzyskiwana przy użyciu [tras niestandardowych](#custom-routes), których typ następnego przeskoku to *brama sieci wirtualnej*. **Propagacja trasy nie powinna być wyłączona w GatewaySubnet. Brama nie będzie działać w przypadku wyłączenia tego ustawienia.** Aby uzyskać szczegółowe informacje, zobacz [Jak wyłączyć propagację trasy bramy sieci wirtualnej](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Jak platforma Azure wybiera trasę

Po wysłaniu ruchu wychodzącego z podsieci platforma Azure wybiera trasę na podstawie docelowego adresu IP przy użyciu algorytmu dopasowania najdłuższego prefiksu. Na przykład tabela tras ma dwie trasy: jedna trasa określa prefiks adresu 10.0.0.0/24, zaś druga trasa określa prefiks adresu 10.0.0.0/16. Platforma Azure kieruje ruch przeznaczony dla 10.0.0.5 do typu następnego przeskoku określonego dla trasy z prefiksem adresu 10.0.0.0/24, ponieważ prefiks 10.0.0.0/24 jest dłuższym prefiksem niż 10.0.0.0/16, chociaż 10.0.0.5 mieści się w obu prefiksach adresów. Platforma Azure kieruje ruch do 10.0.1.5 do typu następnego przeskoku określonego dla trasy z prefiksem adresu 10.0.0.0/16, ponieważ 10.0.1.5 nie jest zawarty w prefiksie adresu 10.0.0.0/24, w związku z czym trasa z prefiksem adresu 10.0.0.0/16 jest najdłuższym zgodnym prefiksem.

Jeśli wiele tras zawiera ten sam prefiks adresu, platforma Azure wybiera typ trasy na podstawie następującego priorytetu:

1. Trasa zdefiniowana przez użytkownika
1. Trasa protokołu BGP
1. Trasa systemowa

> [!NOTE]
> Trasy systemowe dla ruchu związanego z siecią wirtualną, komunikacja równorzędna sieci wirtualnych lub punkty końcowe usługi dla sieci wirtualnej są preferowanymi trasami, nawet jeśli trasy protokołu BGP są bardziej szczegółowe.

Na przykład tabela tras zawiera następujące trasy:


|Źródło   |Prefiksy adresów  |Typ następnego przeskoku           |
|---------|---------         |-------                 |
|Domyślne  | 0.0.0.0/0        |Internet                |
|Użytkownik     | 0.0.0.0/0        |Brama sieci wirtualnej |

Jeśli ruch jest przeznaczony dla adresu IP poza prefiksami adresów jakichkolwiek innych tras w tabeli tras, platforma Azure wybiera trasę z elementem źródłowym **Użytkownik**, ponieważ trasy zdefiniowane przez użytkownika mają wyższy priorytet niż trasy domyślne systemu.

Zobacz [przykład routingu](#routing-example), aby zapoznać się z uniwersalną tabelą routingu wraz z objaśnieniami tras w tabeli.

## <a name="00000-address-prefix"></a><a name="default-route"></a> prefiks adresu 0.0.0.0/0

Trasa z prefiksem adresu 0.0.0.0/0 informuje platformę Azure, jak kierować ruch przeznaczony dla adresu IP, który nie znajduje się w ramach prefiksu adresu jakiejkolwiek innej trasy w tabeli tras podsieci. Po utworzeniu podsieci platforma Azure tworzy [domyślną](#default) trasę do prefiksu adresu 0.0.0.0/0 z typem następnego przeskoku **Internet**. Jeśli nie zastąpisz tej trasy, platforma Azure kieruje do Internetu cały ruch przeznaczony dla adresów IP nieuwzględnionych w prefiksie adresu jakiejkolwiek innej trasy. Wyjątkiem jest to, że ruch do publicznych adresów IP usług platformy Azure pozostaje w sieci szkieletowej platformy Azure i nie jest kierowany do Internetu. Jeśli zastąpisz tę trasę trasą [niestandardową](#custom-routes), ruch przeznaczony do adresów nieznajdujących się w ramach prefiksów adresów jakiejkolwiek innej trasy w tabeli tras jest wysyłany do wirtualnego urządzenia sieciowego lub bramy sieci wirtualnej w zależności od tego, co zostało określone w trasie niestandardowej.

Gdy zastąpisz prefiks adresu 0.0.0.0/0, oprócz ruchu wychodzącego z podsieci przepływającego przez bramę sieci wirtualnej lub urządzenie wirtualne, będą miały miejsce następujące zmiany w domyślnym routingu platformy Azure: 

* Platforma Azure wysyła cały ruch do typu następnego przeskoku określonego w trasie, uwzględniając ruch przeznaczony dla publicznych adresów IP usług platformy Azure. Gdy typ następnego przeskoku trasy z prefiksem adresu 0.0.0.0/0 to **Internet**, wówczas ruch z podsieci przeznaczony dla publicznego adresu IP usług platformy Azure nigdy nie opuszcza sieci szkieletowej platformy Azure niezależnie od regionu platformy Azure, w którym istnieje sieć wirtualna lub zasób usługi platformy Azure. Jednak po utworzeniu trasy zdefiniowanej przez użytkownika lub protokołu BGP z typem następnego przeskoku **Brama sieci wirtualnej** lub **Urządzenie wirtualne** cały ruch, uwzględniając ruch wysyłany do publicznych adresów IP usług platformy Azure, dla którego nie zostały włączone [punkty końcowe usługi](virtual-network-service-endpoints-overview.md), jest wysyłany do typu następnego przeskoku określonego w trasie. Jeśli włączysz punkt końcowy usługi dla usługi, ruch do usługi nie jest kierowany do typu następnego przeskoku na trasie z prefiksem adresu 0.0.0.0/0, ponieważ prefiksy adresów dla usługi zostały określone w trasie tworzonej przez platformę Azure podczas włączania punktu końcowego usługi, zaś prefiksy adresów dla usługi są dłuższe niż 0.0.0.0/0.
* Nie możesz już bezpośrednio uzyskać dostępu do zasobów w podsieci z Internetu. Możesz pośrednio uzyskać dostęp do zasobów w podsieci z Internetu, jeśli ruch przychodzący przechodzi przez urządzenia określone przez typ następnego przeskoku dla trasy z prefiksem adresu 0.0.0.0/0 przed osiągnięciem zasobu w sieci wirtualnej. Jeśli trasa zawiera następujące wartości typu następnego przeskoku:<br>

    * **Urządzenie wirtualne**: urządzenie musi:<br>

        * być dostępne z Internetu;<br>
        * mieć przypisany do siebie publiczny adres IP;<br>
        * nie mieć skojarzonej ze sobą reguły grupy zabezpieczeń sieci uniemożliwiającej komunikację z urządzeniem;<br>
        * nie odmawiać komunikacji;<br>
        * mieć możliwość translacji i przekazywania dalej adresu sieciowego lub służenia jako serwer proxy ruchu do zasobu docelowego w podsieci, a następnie zwracać ruch z powrotem do Internetu.

    * **Brama sieci wirtualnej**: w przypadku bramy będącej bramą sieci wirtualnej usługi ExpressRoute lokalne urządzenie połączone z Internetem może dokonywać translacji i przekazywania dalej adresów sieciowych lub służyć jako serwer proxy ruchu do zasobu docelowego w podsieci za pośrednictwem [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering) usługi ExpressRoute. 

Jeśli Twoja sieć wirtualna jest podłączona do bramy sieci VPN platformy Azure, nie należy kojarzyć tabeli tras z [podsiecią bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) zawierającą trasy z adresem docelowym 0.0.0.0/0. Zaniedbanie tego może spowodować nieprawidłowe działanie bramy. Aby poznać więcej szczegółów, zobacz pytanie *Dlaczego niektóre porty są otwarte w mojej bramie sieci VPN?* w temacie [Brama VPN Gateway — często zadawane pytania](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports).

Zobacz [Strefa DMZ między platformą Azure i lokalnym centrum danych,](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) Aby uzyskać szczegółowe informacje dotyczące implementacji w przypadku używania bram sieci wirtualnych między Internetem a platformą Azure.

## <a name="routing-example"></a>Przykład routingu

W celu zilustrowania koncepcji podanych w tym artykule w kolejnych sekcjach opisano:

* Scenariusz wraz z wymaganiami<br>
* Trasy niestandardowe niezbędne do spełnienia wymagań<br>
* Tabelę tras, która istnieje dla jednej podsieci obejmującej domyślne i niestandardowe trasy niezbędne do spełnienia wymagań

> [!NOTE]
> Tego przykładu nie należy traktować jako implementacji zalecanej ani opartej na najlepszych rozwiązaniach. Został on raczej podany tylko w celu zilustrowania koncepcji w tym artykule.

### <a name="requirements"></a>Wymagania

1. Implementacja dwóch sieci wirtualnych w tym samym regionie platformy Azure i umożliwienie zasobom komunikacji między sieciami wirtualnymi.
1. Umożliwienie sieci lokalnej bezpiecznego komunikowania się z oboma sieciami wirtualnymi przy użyciu tunelu VPN za pośrednictwem Internetu. *Alternatywnie można użyć połączenia usługi ExpressRoute, ale w tym przykładzie jest używane połączenie sieci VPN.*
1. Dla jednej podsieci w jednej sieci wirtualnej:

    * Wymuś, aby cały ruch wychodzący z podsieci, z wyjątkiem tego do usługi Azure Storage i w obrębie podsieci, przepływał przez urządzenie wirtualne sieci w celu kontroli i rejestrowania.<br>
    * Nie kontroluj ruchu między prywatnymi adresami IP w podsieci, ale zezwalaj na przepływ ruchu bezpośrednio między wszystkimi zasobami.<br>
    * Porzucaj wszelki ruch wychodzący przeznaczony dla innej sieci wirtualnej.<br>
    * Włącz przepływ ruchu wychodzącego do usługi Azure Storage bezpośrednio do magazynu bez wymuszania jego przepływu przez urządzenie wirtualne sieci.

1. Zezwalaj na cały ruch między wszystkimi innymi podsieciami i sieciami wirtualnymi.

### <a name="implementation"></a>Implementacja

Na poniższej ilustracji przedstawiono implementację za pośrednictwem modelu wdrażania przy użyciu usługi Azure Resource Manager, która spełnia poprzednie wymagania:

![Diagram sieci](./media/virtual-networks-udr-overview/routing-example.png)

Strzałki oznaczają przepływu ruchu. 

### <a name="route-tables"></a>Tabele tras

#### <a name="subnet1"></a>Subnet1

Tabela tras dla podsieci *Subnet1* na ilustracji zawiera następujące trasy:

|ID (Identyfikator)  |Element źródłowy |Stan  |Prefiksy adresów    |Typ następnego przeskoku          |Adres IP następnego przeskoku|Nazwa trasy zdefiniowanej przez użytkownika| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Domyślne|Nieprawidłowy|10.0.0.0/16         |Sieć wirtualna        |                   |              |
|2   |Użytkownik   |Aktywna |10.0.0.0/16         |Urządzenie wirtualne      |10.0.100.4         |W ramach sieci VNet1  |
|3   |Użytkownik   |Aktywna |10.0.0.0/24         |Sieć wirtualna        |                   |W ramach podsieci Subnet1|
|4   |Domyślne|Nieprawidłowy|10.1.0.0/16         |Komunikacja równorzędna sieci wirtualnych           |                   |              |
|5   |Domyślne|Nieprawidłowy|10.2.0.0/16         |Komunikacja równorzędna sieci wirtualnych           |                   |              |
|6   |Użytkownik   |Aktywna |10.1.0.0/16         |Brak                   |                   |ToVNet2-1-porzuć|
|7   |Użytkownik   |Aktywna |10.2.0.0/16         |Brak                   |                   |ToVNet2-2-porzuć|
|8   |Domyślne|Nieprawidłowy|10.10.0.0/16        |Brama sieci wirtualnej|[X.X.X.X]          |              |
|9   |Użytkownik   |Aktywna |10.10.0.0/16        |Urządzenie wirtualne      |10.0.100.4         |Do lokalnego    |
|10  |Domyślne|Aktywna |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Domyślne|Nieprawidłowy|0.0.0.0/0           |Internet               |                   |              |
|12  |Użytkownik   |Aktywna |0.0.0.0/0           |Urządzenie wirtualne      |10.0.100.4         |Domyślne NVA   |

Objaśnienia każdego identyfikatora trasy są następujące:

1. Platforma Azure automatycznie dodała tę trasę dla wszystkich podsieci w sieci *Virtual-network-1*, ponieważ 10.0.0.0/16 jest jedynym zakresem adresów zdefiniowanym w przestrzeni adresowej dla sieci wirtualnej. Jeśli w trasie ID2 nie została utworzona trasa zdefiniowana przez użytkownika, ruch wysyłany do dowolnego adresu od 10.0.0.1 do 10.0.255.254 zostanie przekierowany do sieci wirtualnej, ponieważ prefiks jest dłuższy niż 0.0.0.0/0 i nie mieści się w ramach prefiksów adresów żadnej innej trasy. Platforma Azure automatycznie zmieniła stan z *Aktywny* na *Nieprawidłowy*, gdy została dodana zdefiniowana przez użytkownika trasa ID2, ponieważ ma ona ten sam prefiks co trasa domyślna, a trasy zdefiniowane przez użytkownika zastępują trasy domyślne. Stan tej trasy jest nadal *Aktywny* dla podsieci *Subnet2*, ponieważ tabela tras, w której znajduje się zdefiniowana przez użytkownika trasa ID2, nie jest skojarzona z podsiecią *Subnet2*.
2. Platforma Azure dodała tę trasę, gdy zdefiniowana przez użytkownika trasa dla prefiksu adresu 10.0.0.0/16 została skojarzona z podsiecią *Subnet1* w sieci wirtualnej *Virtual-network-1*. Zdefiniowana przez użytkownika trasa określa 10.0.100.4 jako adres IP urządzenia wirtualnego, ponieważ adresem jest prywatny adres IP przypisany do urządzenia wirtualnego maszyny wirtualnej. Tabela tras, w której istnieje ta trasa, nie jest skojarzona z podsiecią *Subnet2*, więc nie występuje w tabeli tras dla podsieci *Subnet2*. Ta trasa zastępuje domyślną trasę dla prefiksu 10.0.0.0/16 (ID1), który automatycznie skierował ruch zaadresowany do 10.0.0.1 i 10.0.255.254 w ramach sieci wirtualnej za pośrednictwem typu następnego przeskoku sieci wirtualnej. Ta trasa istnieje, aby spełnić [wymaganie](#requirements) 3 i wymusić skierowanie całego ruchu wychodzącego przez urządzenie wirtualne.
3. Platforma Azure dodała tę trasę, gdy zdefiniowana przez użytkownika trasa dla prefiksu adresu 10.0.0.0/24 została skojarzona z podsiecią *Subnet1*. Ruch skierowany do adresów od 10.0.0.1 do 10.0.0.254 pozostaje w podsieci, a nie jest kierowany do urządzenia wirtualnego określonego w poprzedniej regule (ID2), ponieważ ma ono dłuższy prefiks niż trasa ID2. Ta trasa nie jest skojarzona z podsiecią *Subnet2*, więc nie występuje ona w tabeli tras dla podsieci *Subnet2*. Ta trasa skutecznie zastępuje trasę ID2 dla ruchu w ramach podsieci *Subnet1*. Ta trasa istnieje, aby spełnić [wymaganie](#requirements) 3.
4. Platforma Azure automatycznie dodała trasy dla identyfikatorów 4 i 5 dla wszystkich podsieci w sieci *Virtual-network-1*, gdy sieć wirtualna została równorzędnie połączona z siecią *Virtual-network-2.* Sieć *Virtual-network-2* ma dwa zakresy adresów w swojej przestrzeni adresowej: 10.1.0.0/16 i 10.2.0.0/16, więc platforma Azure dodała trasę dla każdego zakresu. Jeśli w trasie z identyfikatorem 6 i 7 nie zostały utworzone trasy zdefiniowane przez użytkownika, ruch wysyłany do dowolnego adresu z zakresu 10.1.0.1–10.1.255.254 i 10.2.0.1–10.2.255.254 zostanie przekierowany do równorzędnie połączonej sieci wirtualnej, ponieważ prefiks jest dłuższy niż 0.0.0.0/0 i nie mieści się w ramach prefiksów adresów żadnej innej trasy. Platforma Azure automatycznie zmieniła stan z *Aktywny* na *Nieprawidłowy*, gdy zostały dodane trasy o identyfikatorach 6 i 7, ponieważ mają one te same prefiksy, jak trasy o identyfikatorach 4 i 5, a trasy zdefiniowane przez użytkownika zastępują domyślne trasy. Stan tras w identyfikatorach 4 i 5 jest nadal *aktywny* dla *podsieci subnet2*, ponieważ tabela tras, że trasy zdefiniowane przez użytkownika w identyfikatorach 6 i 7 znajdują się w, nie są skojarzone z *podsieci subnet2*. Wirtualne sieci równorzędne zostały utworzone w celu spełnienia [wymagania](#requirements) 1.
5. Takie samo objaśnienie, jak dla ID4.
6. Platforma Azure dodała tę trasę i trasę w ID7, gdy zdefiniowane przez użytkownika trasy dla prefiksów adresów 10.1.0.0/16 i 10.2.0.0/16 zostały skojarzone z podsiecią *Subnet1*. Ruchu skierowany do adresów z zakresów 10.1.0.1-10.1.255.254 i 10.2.0.1-10.2.255.254 został porzucony przez platformę Azure i nie został skierowany do wirtualnej sieci równorzędnej, ponieważ trasy zdefiniowane przez użytkownika zastępują trasy domyślne. Trasy nie są skojarzone z podsiecią *Subnet2*, więc nie występują one w tabeli tras dla podsieci *Subnet2*. Trasy zastępują trasy ID4 i ID5 dla ruchu opuszczającego podsieć *Subnet1*. Trasy ID6 i ID7 istnieją w celu spełnienia [wymagania](#requirements) 3 mówiącego o porzuceniu ruchu kierowanego do innych sieci wirtualnych.
7. Takie samo objaśnienie jak dla ID6.
8. Platforma Azure automatycznie dodała tę trasę dla wszystkich podsieci w sieci *Virtual-network-1*, gdy brama sieci wirtualnej typu VPN została utworzona w ramach sieci wirtualnej. Platforma Azure dodała publiczny adres IP bramy sieci wirtualnej do tabeli tras. Ruch wysyłany do dowolnego adresu z zakresu od 10.10.0.1 do 10.10.255.254 jest kierowany do bramy sieci wirtualnej. Prefiks jest dłuższy niż 0.0.0.0/0 i nie mieści się w prefiksach adresów jakichkolwiek innych tras. Brama sieci wirtualnej została utworzona w celu spełnienia [wymagania](#requirements) 2.
9. Platforma Azure dodała tę trasę, gdy zdefiniowana przez użytkownika trasa dla prefiksu adresu 10.10.0.0/16 została dodana do tabeli tras skojarzonej z podsiecią *Subnet1*. Ta trasa zastępuje ID8. Trasa wysyła cały ruch przeznaczony dla sieci lokalnej do NVA w celu kontroli i nie realizuje routingu ruchu bezpośrednio lokalnie. Ta trasa została utworzona w celu spełnienia [wymagania](#requirements) 3.
10. Gdy punkt końcowy usługi dla usługi platformy Azure został włączony dla podsieci, platforma Azure automatycznie dodała tę trasę do podsieci. Platforma Azure kieruje ruch z podsieci na publiczny adres IP usługi za pośrednictwem sieci infrastruktury platformy Azure. Prefiks jest dłuższy niż 0.0.0.0/0 i nie mieści się w prefiksach adresów jakichkolwiek innych tras. Punkt końcowy usługi został utworzony w celu spełnienia [wymagania](#requirements) 3, aby umożliwić przepływ ruchu kierowanego do usługi Azure Storage bezpośrednio do usługi Azure Storage.
11. Platforma Azure automatycznie dodała tę trasę do tabeli tras wszystkich podsieci w sieci *Virtual-network-1* i *Virtual-network-2.* Prefiks adresu 0.0.0.0/0 jest najkrótszym prefiksem. Wszelki ruch wysyłany na adresy w ramach dłuższego prefiksu adresu jest kierowany na podstawie innych tras. Domyślnie platforma Azure kieruje do Internetu cały ruch skierowany do adresów innych niż adresy określone w jednej z innych tras. Platforma Azure automatycznie zmieniła stan z *Aktywny* na *Nieprawidłowy* dla podsieci *Subnet1*, gdy trasa zdefiniowana przez użytkownika dla prefiksu adresu 0.0.0.0/0 (ID12) została skojarzona z podsiecią. Stan tej trasy jest nadal *Aktywny* dla wszystkich innych podsieci w obu sieciach wirtualnych, ponieważ trasa nie jest skojarzona z żadną inną podsiecią w ramach innych sieci wirtualnych.
12. Platforma Azure dodała tę trasę, gdy zdefiniowana przez użytkownika trasa dla prefiksu adresu 0.0.0.0/0 została skojarzona z podsiecią *Subnet1*. Zdefiniowana przez użytkownika trasa określa 10.0.100.4 jako adres IP urządzenia wirtualnego. Ta trasa nie jest skojarzona z podsiecią *Subnet2*, więc nie występuje ona w tabeli tras dla podsieci *Subnet2*. Cały ruch dla dowolnego adresu, który nie jest objęty prefiksami adresów wszelkich innych tras, jest wysyłany do urządzenia wirtualnego. Dodanie tej trasy zmieniło stan domyślnej trasy dla prefiksu adresu 0.0.0.0/0 (ID11) z *Aktywny* na *Nieprawidłowy* dla podsieci *Subnet1*, ponieważ zdefiniowana przez użytkownika trasa zastępuje domyślną trasę. Ta trasa istnieje, aby spełnić trzecie [wymaganie](#requirements).

#### <a name="subnet2"></a>Subnet2

Tabela tras dla podsieci *Subnet2* na ilustracji zawiera następujące trasy:

|Element źródłowy  |Stan  |Prefiksy adresów    |Typ następnego przeskoku             |Adres IP następnego przeskoku|
|------- |-------|------              |-------                   |--------           
|Domyślne |Aktywna |10.0.0.0/16         |Sieć wirtualna           |                   |
|Domyślne |Aktywna |10.1.0.0/16         |Komunikacja równorzędna sieci wirtualnych              |                   |
|Domyślne |Aktywna |10.2.0.0/16         |Komunikacja równorzędna sieci wirtualnych              |                   |
|Domyślne |Aktywna |10.10.0.0/16        |Brama sieci wirtualnej   |[X.X.X.X]          |
|Domyślne |Aktywna |0.0.0.0/0           |Internet                  |                   |
|Domyślne |Aktywna |10.0.0.0/8          |Brak                      |                   |
|Domyślne |Aktywna |100.64.0.0/10       |Brak                      |                   |
|Domyślne |Aktywna |192.168.0.0/16      |Brak                      |                   |

Tabela tras dla podsieci *Subnet2* zawiera wszystkie domyślne trasy utworzone przez platformę Azure oraz opcjonalne równorzędne sieci wirtualne i opcjonalne trasy bramy sieci wirtualnej. Platforma Azure dodała opcjonalne trasy do wszystkich podsieci w sieci wirtualnej, gdy brama i komunikacja równorzędna zostały dodane do sieci wirtualnej. Platforma Azure usunęła trasy dla prefiksów adresu 10.0.0.0/8, 192.168.0.0/16 i 100.64.0.0/10 z tabeli tras *Subnet1* , gdy zdefiniowana przez użytkownika trasa dla prefiksu adresu 0.0.0.0/0 została dodana do *Subnet1*.  

## <a name="next-steps"></a>Następne kroki

* [Create a user-defined route table with routes and a network virtual appliance (Tworzenie tabeli tras zdefiniowanej przez użytkownika z trasami i urządzeniem wirtualnym sieci)](tutorial-create-route-table-portal.md)<br>
* [Configure BGP for an Azure VPN Gateway (Konfigurowanie protokołu BGP dla bramy sieci VPN platformy Azure)](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
* [Use BGP with ExpressRoute (Używanie protokołu BGP z usługą ExpressRoute)](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)<br>
* [View all routes for a subnet (Wyświetlanie wszystkich tras dla podsieci)](diagnose-network-routing-problem.md). Tabela tras zdefiniowanych przez użytkownika wyświetla tylko trasy zdefiniowane przez użytkownika, a nie trasy domyślne ani trasy protokołu BGP dla podsieci. Wyświetlanie wszystkich tras pokazuje trasy domyślne, protokołu BGP i zdefiniowane przez użytkownika dla podsieci, w której znajduje się interfejs sieciowy.<br>
* [Określanie typu następnego przeskoku](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) między maszyną wirtualną i docelowym adresem IP. Funkcja następnego przeskoku usługi Azure Network Watcher umożliwia określenie, czy ruch opuszcza podsieć i jest kierowany tam, gdzie go oczekujesz.
