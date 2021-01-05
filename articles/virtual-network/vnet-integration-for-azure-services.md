---
title: Integracja z siecią wirtualną usług platformy Azure na potrzeby izolacji sieci
titlesuffix: Azure Virtual Network
description: W tym artykule opisano różne metody integracji usługi platformy Azure z siecią wirtualną, która umożliwia bezpieczne uzyskiwanie dostępu do usługi platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: a25d6d0d1990682287b5962a7bd93a0c525db133
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882368"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrowanie usług platformy Azure z sieciami wirtualnymi na potrzeby izolacji sieci

Integracja Virtual Network (VNet) dla usługi platformy Azure umożliwia blokowanie dostępu do usługi wyłącznie do infrastruktury sieci wirtualnej. Infrastruktura sieci wirtualnej obejmuje również równorzędną sieć wirtualną i sieci lokalne.

Integracja z siecią wirtualną zapewnia usługom platformy Azure zalety izolacji sieci i można wykonać jedną lub więcej z następujących metod:
- [Wdrażanie dedykowanych wystąpień usługi w sieci wirtualnej](virtual-network-for-azure-services.md). Usługi mogą być następnie dostępne do prywatnego dostępu w ramach sieci wirtualnej i z sieci lokalnych.
- Korzystając z [prywatnego punktu końcowego](../private-link/private-endpoint-overview.md) , który nawiązuje połączenie prywatnie i bezpiecznie z usługą, korzystając z [prywatnego linku platformy Azure](../private-link/private-link-overview.md). Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej.
- Uzyskiwanie dostępu do usługi przy użyciu publicznych punktów końcowych przez rozszerzenie sieci wirtualnej do usługi za pośrednictwem [punktów końcowych usługi](virtual-network-service-endpoints-overview.md). Punkty końcowe usługi umożliwiają zabezpieczenie zasobów usługi w sieci wirtualnej.
- Używanie [tagów usługi](service-tags-overview.md) do zezwalania lub odmawiania ruchu do zasobów platformy Azure do i z punktów końcowych publicznych adresów IP.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Wdrażanie dedykowanych usług platformy Azure w sieciach wirtualnych

Podczas wdrażania dedykowanych usług platformy Azure w sieci wirtualnej można komunikować się z zasobami usługi prywatnie, za pomocą prywatnych adresów IP.

![Wdrażanie dedykowanych usług platformy Azure w sieciach wirtualnych](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrożenie dedykowanej usługi platformy Azure w sieci wirtualnej zapewnia następujące możliwości:
- Zasoby w ramach sieci wirtualnej mogą komunikować się ze sobą prywatnie, za pomocą prywatnych adresów IP. Przykładowo bezpośrednie przesyłanie danych między usługą HDInsight i SQL Server uruchomione na maszynie wirtualnej w sieci wirtualnej.
- Zasoby lokalne mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem sieci VPN typu lokacja-lokacja (VPN Gateway) lub ExpressRoute.
- Sieci wirtualne mogą być połączone za pomocą komunikacji równorzędnej, aby umożliwić komunikację między sieciami wirtualnymi przy użyciu prywatnych adresów IP.
- Wystąpienia usługi w sieci wirtualnej są zwykle w pełni zarządzane przez usługę platformy Azure. Obejmuje to monitorowanie kondycji zasobów i skalowanie z obciążeniem.
- Wystąpienia usługi są wdrażane w podsieci w sieci wirtualnej. Przychodzący i wychodzący dostęp do sieci dla podsieci musi być otwarty za pomocą sieciowych grup zabezpieczeń, zgodnie ze wskazówkami dostarczonymi przez usługę.
- Niektóre usługi również nakładają ograniczenia dotyczące podsieci, w których są wdrażane, ograniczają stosowanie zasad, trasy lub łączenie maszyn wirtualnych i zasobów usług w tej samej podsieci. Sprawdź każdą z usług zgodnie z określonymi ograniczeniami, ponieważ mogą one ulec zmianie w czasie. Przykładami takich usług są Azure NetApp Files, dedykowany moduł HSM Azure Container Instances, App Service.
- Opcjonalnie usługi mogą wymagać delegowanej podsieci jako jawnego identyfikatora, który może hostować określoną usługę w podsieci. Dzięki delegowaniu usługi uzyskują jawne uprawnienia do tworzenia zasobów specyficznych dla usługi w podsieci delegowanej.
- Zapoznaj się z przykładem odpowiedzi interfejsu API REST w sieci wirtualnej z delegowaną podsiecią. Kompleksowa Lista usług, które korzystają z delegowanego modelu podsieci, można uzyskać za pośrednictwem interfejsu API dostępne delegacje.

Aby uzyskać listę usług, które można wdrożyć w sieci wirtualnej, zobacz [wdrażanie dedykowanych usług platformy Azure w sieciach wirtualnych](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Prywatne łącze i prywatne punkty końcowe

Możesz użyć prywatnych punktów końcowych, aby umożliwić bezpieczne wykonywanie zdarzeń bezpośrednio z sieci wirtualnej do zasobów platformy Azure za pośrednictwem prywatnego linku bez pośrednictwa publicznego Internetu. Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla usługi platformy Azure w sieci wirtualnej. Gdy tworzysz prywatny punkt końcowy dla zasobu platformy Azure, zapewnia on bezpieczną łączność między klientami w sieci wirtualnej i zasobem platformy Azure. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą Azure używa bezpiecznego linku prywatnego.

W poniższym przykładzie przedstawiono prywatny dostęp do prywatnego punktu końcowego zasobu Event Grid, który zapewnia bezpieczną łączność między klientami w sieci wirtualnej a zasobem Event Grid.

![Prywatny dostęp do zasobu bazy danych SQL przy użyciu prywatnego punktu końcowego](./media/network-isolation/architecture-diagram.png)

Aby uzyskać więcej informacji na temat prywatnego linku i listy obsługiwanych usług platformy Azure, zobacz [co to jest link prywatny?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Punkty końcowe usługi
Punkt końcowy usługi sieci wirtualnej zapewnia bezpieczną i bezpośrednią łączność z usługami platformy Azure za pośrednictwem zoptymalizowanej trasy za pośrednictwem sieci szkieletowej platformy Azure. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Punkty końcowe usługi umożliwiają używanie prywatnych adresów IP w sieci wirtualnej w celu uzyskania dostępu do punktu końcowego usługi platformy Azure bez konieczności publicznego adresu IP w sieci wirtualnej.

![Zabezpieczanie usług platformy Azure w sieciach wirtualnych](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>Tagi usługi

Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Korzystając z tagów usługi, można zdefiniować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) lub w [zaporze platformy Azure](https://docs.microsoft.com/azure/firewall/service-tags). Określając nazwę tagu usługi (na przykład AzureEventGrid) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.

![Zezwalaj lub Odmawiaj ruchu przy użyciu tagów usługi](./media/network-isolation/service-tags.png)

Tagi usług umożliwiają uzyskanie izolacji sieci i ochronę zasobów platformy Azure z ogólnego Internetu podczas uzyskiwania dostępu do usług platformy Azure z publicznymi punktami końcowymi. Utwórz przychodzące/wychodzące reguły sieciowej grupy zabezpieczeń, aby odmówić ruch do/z **Internetu** i zezwolić na ruch do/z **AzureCloud** lub innych [dostępnych tagów usługi](service-tags-overview.md#available-service-tags) określonych usług platformy Azure.

Aby uzyskać więcej informacji na temat tagów usługi i usług platformy Azure, które je obsługują, zobacz [Omówienie tagów usług](service-tags-overview.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak zintegrować aplikację z siecią platformy Azure](../app-service/web-sites-integrate-with-vnet.md).
- Dowiedz się, jak [ograniczyć dostęp do zasobów przy użyciu tagów usługi](tutorial-restrict-network-access-to-resources.md).
- Dowiedz się, jak [połączyć się prywatnie z kontem usługi Azure Cosmos przy użyciu prywatnego linku platformy Azure](../private-link/create-private-endpoint-cosmosdb-portal.md).