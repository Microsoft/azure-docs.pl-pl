---
title: Informacje o strefach nadmiarowych bram sieci wirtualnych w Strefy dostępności platformy Azure
description: Wdróż bramy sieci VPN i ExpressRoute w Strefy dostępności platformy Azure, aby zapewnić odporność, skalowalność i wyższą dostępność do bram sieci wirtualnej.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2030469262baf406635fd170af384e154fec6ae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89401116"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Informacje o strefach nadmiarowych bram sieci wirtualnych w Strefy dostępności platformy Azure

Bramy sieci VPN i ExpressRoute można wdrożyć w [strefy dostępności platformy Azure](../availability-zones/az-overview.md). Zapewni to elastyczność, skalowalność i większą dostępność bram sieci wirtualnej. Wdrażanie bram w strefach dostępności platformy Azure fizycznie i logicznie dzieli bramy w danym regionie, chroniąc jednocześnie lokalną łączność sieci z platformą Azure przed błędami na poziomie strefy.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Bramy strefowo nadmiarowe

Aby automatycznie wdrażać bramy sieci wirtualnej w strefach dostępności, można użyć bram sieci wirtualnej nadmiarowej strefy. Za pomocą bram nadmiarowych stref można korzystać z odporności strefy w celu uzyskania dostępu do skalowalnych usług o kluczowym znaczeniu na platformie Azure.

<br>
<br>

![ilustracja przedstawiająca bramę strefowo nadmiarowe](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Bramy strefowe

Aby wdrożyć bramy w określonej strefie, można użyć bram strefowych. Podczas wdrażania bramy zona wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

<br>
<br>

![ilustracja przedstawiająca bramy stref](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Jednostki SKU bramy

Bramy strefowo nadmiarowe i wielostrefowe są dostępne jako nowe jednostki SKU bramy. Dodaliśmy nowe jednostki SKU bramy sieci wirtualnej w usłudze Azure AZ Regions. Te jednostki SKU są podobne do odpowiednich istniejących jednostek SKU dla ExpressRoute i VPN Gateway, z tą różnicą, że są one specyficzne dla bram strefowo nadmiarowych i strefowych. Można zidentyfikować te jednostki SKU przy użyciu "AZ" w nazwie jednostki SKU.

Aby uzyskać informacje o jednostkach SKU bramy, zobacz jednostki [SKU bramy sieci VPN](vpn-gateway-about-vpngateways.md#gwsku) i [jednostki SKU bramy ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>Jednostki SKU publicznego adresu IP

Bramy strefowo nadmiarowe i bramy strefowe są zależne *od zasobów jednostki* SKU publicznego adresu IP platformy Azure. Konfiguracja zasobu publicznego adresu IP platformy Azure decyduje o tym, czy wdrażana Brama jest strefowo nadmiarowa czy strefowo. Jeśli utworzysz zasób publicznego adresu IP z *podstawową* jednostką SKU, Brama nie będzie miała żadnej nadmiarowości strefy, a zasoby bramy będą regionalne.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Bramy strefowo nadmiarowe

W przypadku tworzenia publicznego adresu IP przy użyciu **standardowej** jednostki SKU publicznego adresu IP bez określania strefy zachowanie jest różne w zależności od tego, czy brama jest bramą sieci VPN czy bramą ExpressRoute. 

* W przypadku bramy sieci VPN dwa wystąpienia bramy zostaną wdrożone w dowolnym 2 z tych trzech stref w celu zapewnienia nadmiarowości strefy. 
* W przypadku bramy ExpressRoute, ponieważ może istnieć więcej niż dwa wystąpienia, Brama może obejmować wszystkie trzy strefy.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Bramy strefowe

Podczas tworzenia publicznego adresu IP przy użyciu **standardowej** jednostki SKU publicznego adresu IP i określania strefy (1, 2 lub 3) wszystkie wystąpienia bramy zostaną wdrożone w tej samej strefie.

### <a name="regional-gateways"></a><a name="piprg"></a>Bramy regionalne

W przypadku tworzenia publicznego adresu IP przy użyciu **podstawowej** jednostki SKU publicznego adresu IP Brama jest wdrażana jako brama regionalna i nie ma żadnej wbudowanej ze strefą nadmiarowości w bramie.

## <a name="faq"></a><a name="faq"></a>Często zadawane pytania

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co zmienią się, gdy wdrażam te nowe jednostki SKU?

Z perspektywy można wdrożyć bramy z nadmiarowością strefy. Oznacza to, że wszystkie wystąpienia bram zostaną wdrożone między Strefy dostępności platformy Azure, a każda strefa dostępności jest inną domeną błędów i aktualizacji. Dzięki temu bramy są bardziej niezawodne, dostępne i odporne na awarie stref.

### <a name="can-i-use-the-azure-portal"></a>Czy mogę użyć Azure Portal?

Tak, możesz użyć Azure Portal do wdrożenia nowych jednostek SKU. Te nowe jednostki SKU będą jednak widoczne tylko w tych regionach świadczenia usługi Azure, w których Strefy dostępności platformy Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jakie regiony są dostępne dla mnie, aby można było korzystać z nowych jednostek SKU?

Nowe jednostki SKU są dostępne w regionach świadczenia usługi Azure, które mają Strefy dostępności platformy Azure-środkowe stany USA, Francja środkowa, Europa Północna, Europa Zachodnia i zachodnie stany USA 2, Wschodnie stany USA, Wschodnie stany USA 2, Azja Południowo-Wschodnia, Japonia Wschodnia, Południowe Zjednoczone Królestwo. W przód firma Microsoft będzie udostępniać bramy nadmiarowe dla innych regionów publicznych platformy Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmienić/migrować/uaktualnić istniejące bramy sieci wirtualnej do bram nadmiarowych lub stref stref?

Migrowanie istniejących bram sieci wirtualnej do strefowo nadmiarowe lub bramy strefowe nie jest obecnie obsługiwane. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę nadmiarową lub strefowo.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy mogę wdrożyć bramy sieci VPN i usługi Express Route w tej samej sieci wirtualnej?

Obsługiwane są oba bramy sieci VPN i Express Route w tej samej sieci wirtualnej. Należy jednak zarezerwować zakres adresów IP/27 dla podsieci bramy.

## <a name="next-steps"></a>Następne kroki

[Tworzenie strefowo nadmiarowej bramy sieci wirtualnej](create-zone-redundant-vnet-gateway.md)
