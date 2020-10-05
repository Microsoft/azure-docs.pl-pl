---
title: Azure Virtual Network | Microsoft Docs
description: Dowiedz się więcej na temat pojęć i funkcji usługi Azure Virtual Network, takich jak przestrzeń adresowa, podsieci, regiony i subskrypcje.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 480e22b319edd03dc9bb9d666dd43718fb3c841b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88507034"
---
# <a name="what-is-azure-virtual-network"></a>Co to jest usługa Azure Virtual Network?

Usługa Azure Virtual Network (VNet) to podstawowy blok konstrukcyjny dla sieci prywatnej na platformie Azure. Sieć wirtualna umożliwia bezpieczne komunikowanie się ze sobą za pomocą Internetu i sieci lokalnych w wielu typach zasobów platformy Azure, takich jak Azure Virtual Machines (VM). Sieć wirtualna jest podobna do tradycyjnej sieci, która działa w Twoim centrum danych, ale oferuje dodatkowe korzyści wynikające z infrastruktury platformy Azure, takiej jak skalowanie, dostępność i izolacja.

## <a name="vnet-concepts"></a>Pojęcia sieci wirtualnej

- **Przestrzeń adresowa:** Podczas tworzenia sieci wirtualnej należy określić niestandardową prywatną przestrzeń adresową IP przy użyciu adresów Public i Private (RFC 1918). Platforma Azure przypisze zasobom w sieci wirtualnej prywatny adres IP z przydzielonej przestrzeni adresowej. Na przykład jeśli maszyna wirtualna jest wdrażana w sieci wirtualnej z przestrzenią adresową 10.0.0.0/16, do maszyny wirtualnej zostanie przypisany prywatny adres IP, taki jak 10.0.0.4.
- **Podsieci:** Podsieci umożliwiają segmentację sieci wirtualnej w co najmniej jedną podsiecią i przydzielenie części przestrzeni adresowej sieci wirtualnej do każdej podsieci. Następnie można wdrożyć zasoby platformy Azure w określonej podsieci. Podobnie jak w przypadku tradycyjnego sieci, podsieci umożliwiają segmentację przestrzeni adresowej sieci wirtualnej na segmenty, które są odpowiednie dla sieci wewnętrznej w organizacji. Zwiększa to również efektywność alokacji adresów. Zasoby w podsieciach można zabezpieczyć za pomocą sieciowych grup zabezpieczeń. Aby uzyskać więcej informacji, zobacz [sieciowe grupy zabezpieczeń](security-overview.md).
- **Regiony**: Sieć wirtualna jest objęta zakresem pojedynczego regionu/lokalizacji; Jednak wiele sieci wirtualnych z różnych regionów można połączyć ze sobą za pomocą komunikacji równorzędnej Virtual Network.
- **Subskrypcja:** Sieć wirtualna jest objęta zakresem subskrypcji. W każdej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) platformy Azure oraz w każdym [regionie](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) świadczenia usługi Azure możesz zaimplementować wiele sieci wirtualnych.

## <a name="best-practices"></a>Najlepsze rozwiązania

Podczas tworzenia sieci na platformie Azure ważne jest, aby pamiętać o następujących zasadach dotyczących projektowania uniwersalnego:

- Upewnij się, że przestrzenie adresowe nie nakładają się na siebie. Upewnij się, że przestrzeń adresowa sieci wirtualnej (blok CIDR) nie nakłada się na inne zakresy sieci w organizacji.
- Podsieci nie powinny obejmować całej przestrzeni adresowej sieci wirtualnej. Planuj z wyprzedzeniem i Zarezerwuj pewną przestrzeń adresową na przyszłość.
- Zalecane jest używanie mniejszej ilości dużych sieci wirtualnych zamiast wielu małych sieci wirtualnychów. Pozwoli to zapobiec obciążeniu zarządzania.
- Zabezpiecz sieć wirtualną przez przypisanie sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) do podsieci poniżej.

## <a name="communicate-with-the-internet"></a>Komunikacja z Internetem

Wszystkie zasoby w sieci wirtualnej mogą domyślnie komunikować się z Internetem. Z zasobem w ruchu przychodzącym możesz komunikować się przez przypisanie publicznego adresu IP lub publicznego modułu równoważenia obciążenia. Publicznego adresu IP lub publicznego modułu równoważenia obciążenia możesz używać również do zarządzania połączeniami w ruchu wychodzącym.  Aby dowiedzieć się więcej na temat połączeń wychodzących na platformie Azure, zobacz [Połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md), [Publiczne adresy IP](virtual-network-public-ip-address.md) i [Moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>W przypadku korzystania tylko z wewnętrznej [usługi Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md) łączność wychodząca nie jest dostępna, dopóki nie zdefiniujesz współdziałania [połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md) z publicznym adresem IP na poziomie wystąpienia lub publicznym modułem równoważenia obciążenia.

## <a name="communicate-between-azure-resources"></a>Komunikacja między zasobami platformy Azure

Zasoby platformy Azure komunikują się bezpiecznie ze sobą nawzajem, korzystając z jednego z następujących sposobów:

- **Za pośrednictwem sieci wirtualnej**: możesz wdrożyć maszyny wirtualne i kilka innych typów zasobów platformy Azure w sieci wirtualnej, np. środowiskach Azure App Service Environment, usłudze Azure Kubernetes Service (AKS) oraz zestawach usługi Azure Virtual Machine Scale Sets. Aby wyświetlić kompletną listę zasobów platformy Azure, które można wdrożyć w sieci wirtualnej, zobacz [Integracja sieci wirtualnej z usługą](virtual-network-for-azure-services.md).
- **Za pośrednictwem punktu końcowego usługi sieci wirtualnej**: Zwiększ prywatną przestrzeń adresową sieci wirtualnej i tożsamość sieci wirtualnej do zasobów usługi platformy Azure, takich jak konta usługi Azure Storage i Azure SQL Database, za pośrednictwem bezpośredniego połączenia. Punkty końcowe usługi umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnej. Aby dowiedzieć się więcej, zobacz [Omówienie punktów końcowych usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md).
- **Za pośrednictwem komunikacji równorzędnej**sieci wirtualnych: można połączyć między sobą między sobą, aby umożliwić komunikację między sieciami wirtualnymi za pomocą komunikacji równorzędnej sieci wirtualnych. Łączone sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach świadczenia usługi Azure. Aby dowiedzieć się więcej, zobacz [Komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Komunikacja z zasobami lokalnymi

Komputery i sieci lokalne możesz połączyć z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

- **Wirtualna sieć prywatna typu punkt-lokacja:** sieć określana pomiędzy siecią wirtualną i jednym komputerem w Twojej sieci. Każdy komputer, który chce nawiązać łączność z siecią wirtualną, musi skonfigurować swoje połączenie. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Komunikacja pomiędzy komputerem i siecią wirtualną jest wysyłana przez szyfrowany tunel za pośrednictwem Internetu. Aby dowiedzieć się więcej, zobacz [Sieć VPN typu punkt-lokacja](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **Sieć VPN typu lokacja-lokacja:** określana pomiędzy lokalnym urządzeniem VPN i bramą Azure VPN Gateway, która jest wdrażana w sieci wirtualnej. Ten typ połączenia umożliwia dowolnym zasobom lokalnym, które uzyskają autoryzację, uzyskiwanie dostępu do sieci wirtualnej. Komunikacja między lokalnym urządzeniem VPN i bramą Azure VPN Gateway jest wysyłana przez szyfrowany tunel za pośrednictwem Internetu. Aby dowiedzieć się więcej, zobacz [Sieć VPN typu lokacja-lokacja](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** połączenie nawiązywane pomiędzy siecią i platformą Azure za pośrednictwem partnera usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Więcej informacji znajduje się w artykule [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="filter-network-traffic"></a>Filtrowanie ruchu sieciowego

Ruch sieciowy pomiędzy podsieciami możesz filtrować przy użyciu jednej lub obu poniższych opcji:

- **Sieciowe grupy zabezpieczeń:** Sieciowe grupy zabezpieczeń i grupy zabezpieczeń aplikacji mogą zawierać wiele reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego, które umożliwiają filtrowanie ruchu do i z zasobów za pomocą źródłowego i docelowego adresu IP, portu i protokołu. Aby dowiedzieć się więcej, zobacz [Sieciowe grupy zabezpieczeń](security-overview.md#network-security-groups) i [Grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups).
- **Wirtualne urządzenia sieciowe:** wirtualne urządzenie sieciowe to maszyna wirtualna wykonująca funkcję sieciową, np. funkcję zapory, optymalizacji WAN lub inną. Aby wyświetlić listę dostępnych wirtualnych urządzeń sieciowych, które możesz wdrożyć w sieci wirtualnej, zobacz witrynę [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Routing ruchu sieciowego

Platforma Azure domyślnie kieruje ruchem pomiędzy podsieciami, połączonymi sieciami wirtualnymi, sieciami lokalnymi oraz Internetem. Możesz zaimplementować jedną lub obie poniższe opcje, aby zastąpić domyślne trasy tworzone przez platformę Azure:

- **Tabele tras:** możesz utworzyć niestandardowe tabele tras z trasami kontrolującymi przekierowywanie ruchu do każdej podsieci. Dowiedz się więcej o [tabelach tras](virtual-networks-udr-overview.md#user-defined).
- **Trasy protokołu BGP (Border Gateway Protocol):** jeśli łączysz sieć wirtualną z siecią lokalną przy użyciu bramy Azure VPN Gateway lub połączenia ExpressRoute, możesz propagować trasy lokalne BGP do sieci wirtualnych. Dowiedz się więcej o użyciu protokołu BGP z bramą [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i usługą [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="virtual-network-integration-for-azure-services"></a>Integracja z siecią wirtualną dla usług platformy Azure

Integracja usług platformy Azure z usługą Azure Virtual Network umożliwia prywatnym dostęp do usługi z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.
Usługi platformy Azure w sieci wirtualnej można zintegrować z następującymi opcjami:
- Wdrażanie [dedykowanych wystąpień usługi](virtual-network-for-azure-services.md) w sieci wirtualnej. Usługi mogą być następnie dostępne do prywatnego dostępu w ramach sieci wirtualnej i z sieci lokalnych.
- Korzystanie z [prywatnego linku](../private-link/private-link-overview.md) w celu uzyskania dostępu do prywatnego określonego wystąpienia usługi z sieci wirtualnej i sieci lokalnych.
- Możesz również uzyskać dostęp do usługi za pomocą publicznych punktów końcowych, rozszerzając sieć wirtualną do usługi za pośrednictwem [punktów końcowych usługi](virtual-network-service-endpoints-overview.md). Punkty końcowe usługi umożliwiają zabezpieczenie zasobów usługi w sieci wirtualnej.
 

## <a name="azure-vnet-limits"></a>Limity sieci wirtualnej platformy Azure

Istnieją pewne ograniczenia dotyczące liczby zasobów platformy Azure, które można wdrożyć. Większość limitów sieci platformy Azure znajduje się na maksymalnych wartościach. Można jednak [zwiększyć niektóre limity sieci](../azure-portal/supportability/networking-quota-requests.md) , zgodnie z [limitem na stronie limity Sieć wirtualna](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Cennik

Za korzystanie z sieci wirtualnej platformy Azure nie są naliczane opłaty. Opłaty standardowe są stosowane do zasobów, takich jak Virtual Machines (maszyny wirtualne) i inne produkty. Aby dowiedzieć się więcej, zobacz [Cennik sieci VNET](https://azure.microsoft.com/pricing/details/virtual-network/) i [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)platformy Azure.

## <a name="next-steps"></a>Następne kroki

 Aby rozpocząć korzystanie z sieci wirtualnej, utwórz ją, wdróż w niej kilka maszyn wirtualnych i rozpocznij komunikację pomiędzy maszynami wirtualnymi. Aby dowiedzieć się więcej, zobacz przewodnik Szybki start [Tworzenie sieci wirtualnej](quick-create-portal.md).
