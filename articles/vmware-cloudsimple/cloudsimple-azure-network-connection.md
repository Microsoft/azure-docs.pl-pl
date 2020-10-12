---
title: Rozwiązanie VMware według CloudSimple — połączenia sieciowe platformy Azure
description: Dowiedz się więcej o łączeniu sieci wirtualnej platformy Azure z siecią regionu CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025082"
---
# <a name="azure-network-connections-overview"></a>Omówienie połączeń sieciowych platformy Azure

Podczas tworzenia usługi CloudSimple w regionie i tworzenia węzłów można:

* Zażądaj obwodu usługi Azure ExpressRoute i dołącz go do sieci CloudSimple w tym regionie.
* Podłącz sieć regionu CloudSimple do sieci wirtualnej platformy Azure lub sieci lokalnej przy użyciu usługi Azure ExpressRoute.
* Zapewnianie dostępu do usług uruchomionych w ramach subskrypcji platformy Azure lub sieci lokalnej ze środowiska chmury prywatnej.

Połączenie ExpressRoute ma wysoką przepustowość dzięki małym opóźnieniu.

## <a name="benefits"></a>Korzyści

Połączenie sieciowe platformy Azure umożliwia:

* Użyj platformy Azure jako miejsca docelowego kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej.
* Wdróż serwery usługi KMS w ramach subskrypcji platformy Azure, aby zaszyfrować magazyn danych sieci vSAN w chmurze prywatnej.
* Używaj aplikacji hybrydowych, w których warstwa sieci Web aplikacji jest uruchamiana w chmurze publicznej, podczas gdy warstwy aplikacji i bazy danych działają w chmurze prywatnej.

## <a name="azure-virtual-network-connection"></a>Połączenie sieci wirtualnej platformy Azure

Chmury prywatne mogą być połączone z zasobami platformy Azure za pomocą ExpressRoute.  Połączenie ExpressRoute umożliwia dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej.  To połączenie umożliwia zwiększenie sieci prywatnej chmury do sieci wirtualnej platformy Azure.  Trasy z sieci CloudSimple są wymieniane z siecią wirtualną platformy Azure za pośrednictwem protokołu BGP.  Jeśli skonfigurowano komunikację równorzędną sieci wirtualnej, wszystkie równorzędne sieci wirtualne będą dostępne z sieci CloudSimple.

![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute połączenie z siecią lokalną

Istniejący obwód usługi Azure ExpressRoute można połączyć z regionem CloudSimple. Funkcja ExpressRoute Global Reach służy do łączenia obu obwodów.  Nawiązywane jest połączenie między obwodami lokalnymi i CloudSimple ExpressRoute.  To połączenie umożliwia zwiększenie sieci lokalnych do sieci prywatnej chmury. Trasy z sieci CloudSimple będą wymieniane za pośrednictwem protokołu BGP z siecią lokalną.

![Połączenie lokalne ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Połączenie z siecią lokalną i siecią wirtualną platformy Azure

Połączenia z siecią lokalną i siecią wirtualną platformy Azure mogą współistnieć z sieci CloudSimple.  Połączenie używa protokołu BGP do wymiany tras między siecią lokalną, usługą Azure Virtual Network i siecią CloudSimple.  Po połączeniu sieci CloudSimple z siecią wirtualną platformy Azure w obecności Global Reach połączenie trasy sieci wirtualnej platformy Azure będą widoczne w sieci lokalnej.  Wymiana trasy odbywa się na platformie Azure między routerami brzegowymi.

![Lokalne połączenie ExpressRoute z połączeniem sieci wirtualnej platformy Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Istotne zagadnienia

Połączenie z siecią CloudSimple z sieci lokalnej i z usługi Azure Virtual Network umożliwia wymianę między wszystkimi sieciami.

* Usługa Azure Virtual Network będzie widoczna zarówno w sieci lokalnej, jak i w sieci CloudSimple.
* Jeśli nawiązano połączenie z siecią wirtualną platformy Azure z sieci lokalnej, połączenie z siecią CloudSimple przy użyciu Global Reach umożliwi dostęp do sieci wirtualnych z sieci CloudSimple.
* Adresy podsieci **nie mogą** się nakładać między żadną z połączonych sieci.
* CloudSimple **nie** będzie anonsować trasy domyślnej do połączeń ExpressRoute
* Jeśli router lokalny anonsuje trasę domyślną, ruch z sieci CloudSimple i sieci wirtualnej platformy Azure będzie używał anonsowanej trasy domyślnej.  W związku z tym nie można uzyskać dostępu do maszyn wirtualnych na platformie Azure przy użyciu publicznych adresów IP.

## <a name="next-steps"></a>Następne kroki

* [Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute](virtual-network-connection.md)
* [Nawiązywanie połączenia z poziomu lokalnego z CloudSimple za pomocą ExpressRoute](on-premises-connection.md)
