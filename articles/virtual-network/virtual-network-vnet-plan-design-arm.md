---
title: Planowanie sieci wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się, jak zaplanować sieci wirtualne na podstawie wymagań dotyczących izolacji, łączności i lokalizacji.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 9ed060c1f3fc47aa7050c0f9b25c757953fa22fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220341"
---
# <a name="plan-virtual-networks"></a>Planowanie sieci wirtualnych

Tworzenie sieci wirtualnej na potrzeby eksperymentowania z programem jest wystarczająco proste, ale nie jest to możliwe, jednak wdrożenie wielu sieci wirtualnych odbywa się w miarę upływu czasu w celu wspierania potrzeb organizacji. W przypadku niektórych planów można wdrożyć sieci wirtualne i połączyć zasoby, które są bardziej efektywne. Informacje przedstawione w tym artykule są najbardziej przydatne, jeśli znasz już sieci wirtualne i masz pewne doświadczenie w pracy z nimi. Jeśli nie znasz sieci wirtualnych, zalecamy zapoznanie się z tematem [Omówienie usługi Virtual Network](virtual-networks-overview.md).

## <a name="naming"></a>Nazewnictwo

Wszystkie zasoby platformy Azure mają nazwę. Nazwa musi być unikatowa w zakresie, który może się różnić w zależności od typu zasobu. Na przykład nazwa sieci wirtualnej musi być unikatowa w ramach [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), ale może być zduplikowana w ramach [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) lub [regionu](https://azure.microsoft.com/regions/#services)świadczenia usługi Azure. Definiowanie konwencji nazewnictwa, która może być stosowana spójnie podczas określania nazw zasobów, jest przydatna podczas zarządzania kilkoma zasobami sieciowymi w czasie. Aby uzyskać sugestie, zobacz [konwencje nazewnictwa](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Regiony

Wszystkie zasoby platformy Azure są tworzone w regionie i w ramach subskrypcji platformy Azure. Zasób można utworzyć tylko w sieci wirtualnej, która istnieje w tym samym regionie i w ramach subskrypcji, co zasób. Można jednak łączyć sieci wirtualne istniejące w różnych subskrypcjach i regionach. Aby uzyskać więcej informacji, zobacz [łączność](#connectivity). Podczas decydowania o regionach, w których mają zostać wdrożone zasoby, należy wziąć pod uwagę, gdzie fizycznie znajdują się odbiorcy zasobów:

- Konsumenci zasobów zwykle chcą najniższe opóźnienie sieci do swoich zasobów. Aby określić względne opóźnienia między określoną lokalizacją a regionem świadczenia usługi Azure, zobacz [przeglądanie względnych opóźnień](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Czy masz dane dotyczące miejsca zamieszkania, suwerenności, zgodności lub odporności? W takim przypadku wybranie regionu, który jest wyrównany do wymagań ma krytyczne znaczenie. Aby uzyskać więcej informacji, zobacz [Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/).
- Czy potrzebujesz odporności między Strefy dostępności platformy Azureami w ramach tego samego regionu świadczenia usługi Azure w ramach wdrażanych zasobów? Zasoby, takie jak maszyny wirtualne (VM), można wdrażać w różnych strefach dostępności w ramach tej samej sieci wirtualnej. Nie wszystkie regiony platformy Azure obsługują jednak strefy dostępności. Aby dowiedzieć się więcej o strefach dostępności i regionach, które je obsługują, zobacz [strefy dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Subskrypcje

W ramach każdej subskrypcji można wdrożyć dowolną liczbę sieci wirtualnych, która jest wymagana do [limitu](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Niektóre organizacje mają różne subskrypcje dla różnych działów, na przykład. Aby uzyskać więcej informacji i zagadnień dotyczących subskrypcji, zobacz temat [Zarządzanie subskrypcjami](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentacja

Można utworzyć wiele sieci wirtualnych na subskrypcję i na region. W każdej sieci wirtualnej można utworzyć wiele podsieci. Poniższe zagadnienia ułatwiają określenie, ile sieci wirtualnych i podsieci są wymagane:

### <a name="virtual-networks"></a>Sieci wirtualne

Sieć wirtualna to wirtualna, izolowana część sieci publicznej platformy Azure. Każda sieć wirtualna jest dedykowana dla Twojej subskrypcji. Kwestie, które należy wziąć pod uwagę podczas decydowania, czy należy utworzyć jedną sieć wirtualną lub wiele sieci wirtualnych w ramach subskrypcji:

- Czy istnieją jakieś wymagania dotyczące zabezpieczeń organizacyjnych na potrzeby izolowania ruchu w oddzielnych sieciach wirtualnych? Można nawiązać połączenie z sieciami wirtualnymi. W przypadku łączenia sieci wirtualnych można zaimplementować sieciowe urządzenie wirtualne, takie jak zapora, aby sterować przepływem ruchu między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [zabezpieczenia](#security) i [łączność](#connectivity).
- Czy istnieją jakieś wymagania organizacyjne na potrzeby izolowania sieci wirtualnych w osobnych [subskrypcjach](#subscriptions) lub [regionach](#regions)?
- [Interfejs sieciowy](virtual-network-network-interface.md) umożliwia maszynie wirtualnej komunikowanie się z innymi zasobami. Każdy interfejs sieciowy ma przypisane co najmniej jeden prywatny adres IP. Ile interfejsów sieciowych i [prywatnych adresów IP](./private-ip-addresses.md) jest wymaganych w sieci wirtualnej? Istnieją [limity](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) liczby interfejsów sieciowych i prywatnych adresów IP, które mogą znajdować się w sieci wirtualnej.
- Czy chcesz połączyć sieć wirtualną z inną siecią wirtualną lub siecią lokalną? Niektóre sieci wirtualne można połączyć z każdą inną lub siecią lokalną, ale nie z innych. Aby uzyskać więcej informacji, zobacz [łączność](#connectivity). Każda sieć wirtualna, która jest podłączona do innej sieci wirtualnej lub sieci lokalnej, musi mieć unikatową przestrzeń adresową. Każda sieć wirtualna ma jeden lub więcej zakresów adresów publicznych lub prywatnych przypisanych do przestrzeni adresowej. Zakres adresów jest określony w formacie Classless Internet Domain Routing (CIDR), takim jak 10.0.0.0/16. Dowiedz się więcej o [zakresach adresów](manage-virtual-network.md#add-or-remove-an-address-range) dla sieci wirtualnych.
- Czy istnieją jakieś wymagania administracyjne organizacji dotyczące zasobów w różnych sieciach wirtualnych? Jeśli tak, możesz oddzielić zasoby do oddzielnej sieci wirtualnej, aby uprościć [przypisanie uprawnień](#permissions) do osób w organizacji lub przypisać różne zasady do różnych sieci wirtualnych.
- W przypadku wdrażania niektórych zasobów usługi platformy Azure w sieci wirtualnej tworzy własną sieć wirtualną. Aby ustalić, czy usługa platformy Azure tworzy własną sieć wirtualną, zobacz informacje dla każdej [usługi platformy Azure, którą można wdrożyć w sieci wirtualnej](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Podsieci

Sieć wirtualna może zostać ujęta w jedną lub więcej podsieci do [limitów](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Kwestie, które należy wziąć pod uwagę podczas decydowania, czy należy utworzyć jedną podsieć lub wiele sieci wirtualnych w ramach subskrypcji:

- Każda podsieć musi mieć unikatowy zakres adresów określony w formacie CIDR w przestrzeni adresowej sieci wirtualnej. Zakres adresów nie może pokrywać się z innymi podsieciami w sieci wirtualnej.
- Jeśli planujesz wdrożenie niektórych zasobów usługi platformy Azure w sieci wirtualnej, mogą oni potrzebować lub utworzyć własną podsieć, więc musi być wystarczająca ilość wolnego miejsca do wykonania. Aby ustalić, czy usługa platformy Azure tworzy własną podsieć, zobacz informacje dotyczące poszczególnych [usług platformy Azure, które można wdrożyć w sieci wirtualnej](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Jeśli na przykład Sieć wirtualna jest podłączona do sieci lokalnej przy użyciu VPN Gateway platformy Azure, Sieć wirtualna musi mieć dedykowaną podsieć dla bramy. Dowiedz się więcej o [podsieciach bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Platforma Azure domyślnie kieruje ruchem sieciowym między wszystkimi podsieciami w sieci wirtualnej. Można zastąpić domyślny Routing platformy Azure, aby zapobiec routingowi platformy Azure między podsieciami lub kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego. Jeśli potrzebujesz tego ruchu między zasobami w tym samym przepływie sieci wirtualnej za pomocą wirtualnego urządzenia sieciowego (urządzenie WUS), wdróż zasoby w różnych podsieciach. Dowiedz się więcej o [zabezpieczeniach](#security).
- Możesz ograniczyć dostęp do zasobów platformy Azure, takich jak konto usługi Azure Storage lub Azure SQL Database, do określonych podsieci za pomocą punktu końcowego usługi sieci wirtualnej. Ponadto możesz odmówić dostępu do zasobów z Internetu. Można utworzyć wiele podsieci i włączyć punkt końcowy usługi dla niektórych podsieci, ale nie do innych. Dowiedz się więcej o [punktach końcowych usługi](virtual-network-service-endpoints-overview.md)i zasobach platformy Azure, na których można je włączyć.
- Można skojarzyć zero lub jedną sieciową grupę zabezpieczeń z każdą podsiecią w sieci wirtualnej. Do każdej podsieci można skojarzyć tę samą lub inną sieciową grupę zabezpieczeń. Każda sieciowa Grupa zabezpieczeń zawiera reguły, które zezwalają na ruch do i ze źródeł i miejsc docelowych lub odmawiają go. Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](#traffic-filtering).

## <a name="security"></a>Zabezpieczenia

Ruch sieciowy do i z zasobów w sieci wirtualnej można filtrować przy użyciu sieciowych grup zabezpieczeń i sieciowych urządzeń wirtualnych. Można kontrolować sposób, w jaki platforma Azure kieruje ruchem z podsieci. Można także ograniczyć, kto w organizacji może współpracować z zasobami w sieciach wirtualnych.

### <a name="traffic-filtering"></a>Filtrowanie ruchu

- Ruch sieciowy między zasobami w sieci wirtualnej można filtrować przy użyciu sieciowej grupy zabezpieczeń, urządzenie WUS, która filtruje ruch sieciowy lub oba te elementy. Aby wdrożyć urządzenie WUS, na przykład zaporę, aby filtrować ruch sieciowy, zobacz [witrynę Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). W przypadku korzystania z urządzenie WUS, tworzysz również trasy niestandardowe do kierowania ruchu z podsieci do urządzenie WUS. Dowiedz się więcej o [routingu ruchu](#traffic-routing).
- Sieciowa Grupa zabezpieczeń zawiera kilka domyślnych reguł zabezpieczeń, które zezwalają na ruch do lub z zasobów lub odmawiają go. Sieciowa Grupa zabezpieczeń może być skojarzona z interfejsem sieciowym, podsiecią, w której znajduje się interfejs sieciowy, lub obie. Aby uprościć zarządzanie regułami zabezpieczeń, zaleca się skojarzenie sieciowej grupy zabezpieczeń z poszczególnymi podsieciami, a nie pojedynczymi interfejsami sieciowymi w podsieci, jeśli jest to możliwe.
- Jeśli na różnych maszynach wirtualnych w podsieci są wymagane inne reguły zabezpieczeń, można skojarzyć interfejs sieciowy w maszynie wirtualnej z co najmniej jedną grupą zabezpieczeń aplikacji. Reguła zabezpieczeń może określić grupę zabezpieczeń aplikacji w jej lokalizacji źródłowej, docelowej lub obu. Ta reguła ma zastosowanie tylko do interfejsów sieciowych, które są członkami grupy zabezpieczeń aplikacji. Dowiedz się więcej na temat [grup zabezpieczeń sieci](./network-security-groups-overview.md) i [grup zabezpieczeń aplikacji](./network-security-groups-overview.md#application-security-groups).
- Platforma Azure tworzy kilka domyślnych reguł zabezpieczeń w ramach każdej sieciowej grupy zabezpieczeń. Jedna reguła domyślna zezwala na przepływ całego ruchu między wszystkimi zasobami w sieci wirtualnej. Aby przesłonić to zachowanie, użyj sieciowych grup zabezpieczeń, routingu niestandardowego do kierowania ruchu do urządzenie WUS lub obu. Zaleca się zapoznanie się ze wszystkimi [domyślnymi regułami zabezpieczeń](./network-security-groups-overview.md#default-security-rules) platformy Azure i zrozumienie, jak reguły grupy zabezpieczeń sieci są stosowane do zasobu.

Można wyświetlić przykładowe projekty do wdrożenia sieci obwodowej (zwanej także strefą DMZ) między platformą Azure i Internetem przy użyciu [urządzenie WUS](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Routing ruchu

Platforma Azure tworzy kilka domyślnych tras dla ruchu wychodzącego z podsieci. Domyślne Routing platformy Azure można zastąpić, tworząc tabelę tras i kojarząc ją z podsiecią. Typowe przyczyny przesłania domyślnego routingu platformy Azure to:
- Ponieważ ruch między podsieciami jest potrzebny do przepływu przez urządzenie WUS. Aby dowiedzieć się więcej na temat sposobu [konfigurowania tabel tras w celu wymuszania ruchu przez urządzenie WUS](tutorial-create-route-table-portal.md).
- Ponieważ chcesz wymusić cały ruch związany z Internetem za pośrednictwem urządzenie WUS lub lokalnego, za pośrednictwem bramy sieci VPN platformy Azure. Wymuszanie lokalnego ruchu internetowego na potrzeby inspekcji i rejestrowania jest często określane jako Wymuszone tunelowanie. Dowiedz się więcej o konfigurowaniu [wymuszonego tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Jeśli musisz zaimplementować Routing niestandardowy, zalecamy zapoznanie się z [usługą Routing na platformie Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Łączność

Można połączyć sieć wirtualną z innymi sieciami wirtualnymi przy użyciu komunikacji równorzędnej sieci wirtualnej lub sieci lokalnej przy użyciu bramy sieci VPN platformy Azure.

### <a name="peering"></a>Komunikacja równorzędna

W przypadku korzystania z [komunikacji równorzędnej sieci](virtual-network-peering-overview.md)wirtualnych sieci wirtualne mogą znajdować się w tych samych lub różnych obsługiwanych regionach platformy Azure. Sieci wirtualne mogą znajdować się w tych samych lub różnych subskrypcjach platformy Azure (nawet w subskrypcjach należących do różnych dzierżawców Azure Active Directory). Przed utworzeniem komunikacji równorzędnej zaleca się zapoznanie się ze wszystkimi [wymaganiami dotyczącymi komunikacji równorzędnej i ograniczeniami](virtual-network-manage-peering.md#requirements-and-constraints). Przepustowość między zasobami w sieciach wirtualnych połączonych za pomocą komunikacji równorzędnej w tym samym regionie jest taka sama jak w przypadku, gdy zasoby znajdowały się w tej samej sieci wirtualnej.

### <a name="vpn-gateway"></a>Brama sieci VPN

Korzystając z [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure, można połączyć sieć wirtualną z siecią lokalną przy użyciu sieci [VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)lub za pomocą dedykowanego połączenia z usługą Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Można połączyć komunikację równorzędną i bramę sieci VPN, aby utworzyć [sieci Hub i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), gdzie sieci wirtualne szprych nawiązują połączenie z siecią wirtualną centralną, a centrum łączy się z siecią lokalną, na przykład.

### <a name="name-resolution"></a>Rozpoznawanie nazw

Zasoby w jednej sieci wirtualnej nie mogą rozpoznać nazw zasobów w równorzędnej sieci wirtualnej przy użyciu [wbudowanego systemu DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)platformy Azure. Aby rozwiązać nazwy w równorzędnej sieci wirtualnej, [Wdróż własny serwer DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)lub użyj Azure DNS [domen prywatnych](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Rozpoznawanie nazw między zasobami w sieci wirtualnej i w sieciach lokalnych wymaga również wdrożenia własnego serwera DNS.

## <a name="permissions"></a>Uprawnienia

Platforma Azure korzysta z [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na potrzeby zasobów. Uprawnienia są przypisywane do [zakresu](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) w następującej hierarchii: grupy zarządzania, subskrypcji, grupy zasobów i poszczególnych zasobów. Aby dowiedzieć się więcej o hierarchii, zobacz [organizowanie zasobów](../governance/management-groups/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby współpracować z sieciami wirtualnymi platformy Azure i wszystkimi związanymi z nimi funkcjami, takimi jak Komunikacja równorzędna, sieciowe grupy zabezpieczeń, punkty końcowe usługi i tabele tras, można przypisać członków organizacji do wbudowanych ról [właściciela](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [współautora](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)lub [współautorów sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) , a następnie przypisać rolę do odpowiedniego zakresu. Jeśli chcesz przypisać określone uprawnienia dla podzestawu możliwości sieci wirtualnej, Utwórz [rolę niestandardową](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i przypisz określone uprawnienia wymagane dla [sieci wirtualnych](manage-virtual-network.md#permissions), [podsieci i punktów końcowych usług](virtual-network-manage-subnet.md#permissions), [interfejsów sieciowych](virtual-network-network-interface.md#permissions), [komunikacji równorzędnej](virtual-network-manage-peering.md#permissions), [sieci i grup zabezpieczeń aplikacji](manage-network-security-group.md#permissions)lub [tabel tras](manage-route-table.md#permissions) do roli.

## <a name="policy"></a>Zasady

Azure Policy umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi. Definicje zasad wymuszają stosowanie różnych reguł do zasobów, dzięki czemu zasoby pozostają zgodne ze standardami organizacji i umowami dotyczącymi poziomu usług. Azure Policy uruchamia ocenę zasobów i skanuje zasoby, które nie są zgodne z definicjami zasad. Na przykład można zdefiniować i zastosować zasady, które umożliwiają tworzenie sieci wirtualnych tylko w określonej grupie zasobów lub regionie. Inne zasady mogą wymagać, aby do każdej podsieci była skojarzona sieciowa Grupa zabezpieczeń. Zasady są następnie oceniane podczas tworzenia i aktualizowania zasobów.

Zasady są stosowane do następującej hierarchii: grupy zarządzania, subskrypcji i grupy zasobów. Dowiedz się więcej na temat [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub Wdróż niektóre [definicje Azure Policy](./policy-reference.md)sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wszystkich zadań, ustawień i opcji dla [sieci wirtualnej](manage-virtual-network.md), [punktu końcowego podsieci i usługi](virtual-network-manage-subnet.md), [interfejsu sieciowego](virtual-network-network-interface.md), [komunikacji równorzędnej](virtual-network-manage-peering.md),  [sieci i grupy zabezpieczeń aplikacji](manage-network-security-group.md)lub [tabeli tras](manage-route-table.md).
