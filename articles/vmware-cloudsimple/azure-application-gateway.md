---
title: Korzystanie z usługi Azure Application Gateway z maszynami wirtualnymi VMware
description: Opisuje sposób korzystania z usługi Azure Application Gateway do zarządzania przychodzącym ruchem internetowym dla serwerów sieci Web działających na maszynach wirtualnych VMware środowisko chmury prywatnej CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897947"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Używanie Application Gateway platformy Azure z maszynami wirtualnymi VMware w środowisku chmury prywatnej CloudSimple

Za pomocą Application Gateway platformy Azure można zarządzać przychodzącym ruchem sieci Web dla serwerów sieci Web działających na maszynach wirtualnych VMware w środowisku chmury prywatnej CloudSimple.

Korzystając z usługi Azure Application Gateway w ramach prywatnego publicznego wdrożenia hybrydowego, można zarządzać ruchem w sieci Web do aplikacji, zapewniać bezpieczną fronton i przeciążać przetwarzanie protokołu TLS dla swoich usług działających w środowisku VMware. Usługa Azure Application Gateway kieruje ruch internetowy do wystąpień puli zaplecza znajdujących się w środowiskach VMware zgodnie ze skonfigurowanymi regułami i sondami kondycji.

To rozwiązanie Application Gateway platformy Azure wymaga:

* Mieć subskrypcję platformy Azure.
* Utwórz i skonfiguruj sieć wirtualną platformy Azure oraz podsieć w sieci wirtualnej.
* Utwórz i skonfiguruj reguły sieciowej grupy zabezpieczeń oraz równorzędną sieć wirtualną przy użyciu usługi ExpressRoute w chmurze prywatnej CloudSimple.
* Utwórz & Skonfiguruj swoją chmurę prywatną.
* Utwórz & Skonfiguruj Application Gateway platformy Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Scenariusz wdrażania Application Gateway platformy Azure

W tym scenariuszu Application Gateway platformy Azure są uruchamiane w sieci wirtualnej platformy Azure. Sieć wirtualna jest połączona z chmurą prywatną za pośrednictwem obwodu usługi ExpressRoute. Wszystkie podsieci w chmurze prywatnej są dostępne jako adresy IP z podsieci sieci wirtualnej.

![Moduł równoważenia obciążenia platformy Azure w usłudze Azure Virtual Network](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Jak wdrożyć rozwiązanie

Proces wdrażania obejmuje następujące zadania:

1. [Sprawdź, czy wymagania wstępne są spełnione](#1-verify-prerequisites)
2. [Połącz połączenie wirtualne platformy Azure z chmurą prywatną](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Wdrażanie usługi Azure Application Gateway](#3-deploy-an-azure-application-gateway)
4. [Utwórz i skonfiguruj pulę maszyn wirtualnych serwera sieci Web w chmurze prywatnej](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Sprawdź wymagania wstępne

Sprawdź, czy są spełnione następujące wymagania wstępne:

* Azure Resource Manager i Sieć wirtualna została już utworzona.
* Dedykowana podsieć (dla Application Gateway) w ramach sieci wirtualnej platformy Azure została już utworzona.
* Chmura prywatna CloudSimple została już utworzona.
* Nie ma konfliktu adresów IP między podsieciami IP w sieci wirtualnej i podsieci w chmurze prywatnej.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Połącz sieć wirtualną platformy Azure z chmurą prywatną

Aby połączyć sieć wirtualną platformy Azure z chmurą prywatną, postępuj zgodnie z tym procesem.

1. [W portalu CloudSimple skopiuj informacje o komunikacji równorzędnej ExpressRoute](virtual-network-connection.md).

2. [Skonfiguruj bramę sieci wirtualnej dla sieci wirtualnej platformy Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Połącz sieć wirtualną z obwodem ExpressRoute CloudSimple](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Użyj informacji o komunikacji równorzędnej, które zostały skopiowane, aby połączyć sieć wirtualną z obwodem ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Wdróż bramę aplikacji platformy Azure

Szczegółowe instrukcje dla tego programu są dostępne w temacie [Tworzenie bramy aplikacji z regułami routingu opartymi na ścieżce przy użyciu Azure Portal](../application-gateway/create-url-route-portal.md). Poniżej znajduje się podsumowanie wymaganych czynności:

1. Utwórz sieć wirtualną w ramach subskrypcji i grupy zasobów.
2. Utwórz podsieć (do użycia jako dedykowana podsieć) w ramach sieci wirtualnej.
3. Utwórz standardową Application Gateway (opcjonalnie Włącz WAF): na stronie głównej Azure Portal kliknij pozycję **zasoby**  >  **sieciowe**  >  **Application Gateway** w lewym górnym rogu strony. Wybierz standardową jednostkę SKU i rozmiar oraz podaj subskrypcję platformy Azure, grupę zasobów i informacje o lokalizacji. W razie potrzeby utwórz nowy publiczny adres IP dla tej bramy aplikacji i podaj szczegóły dotyczące sieci wirtualnej oraz dedykowanej podsieci bramy aplikacji.
4. Dodaj pulę zaplecza z maszynami wirtualnymi i Dodaj ją do bramy aplikacji.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Utwórz i skonfiguruj pulę maszyn wirtualnych serwera sieci Web w chmurze prywatnej

W programie vCenter Utwórz maszyny wirtualne z systemem operacyjnym i wybranym przez siebie serwerem sieci Web (na przykład Windows/IIS lub Linux/Apache). Wybierz podsieć/sieć VLAN wydaną dla warstwy sieci Web w chmurze prywatnej. Sprawdź, czy co najmniej jedna wirtualnej karty sieciowej maszyn wirtualnych serwera sieci Web znajduje się w podsieci warstwy sieci Web.
