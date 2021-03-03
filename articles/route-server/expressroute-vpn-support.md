---
title: Informacje o usłudze Azure Route Server (wersja zapoznawcza) obsługuje ExpressRoute i sieć VPN platformy Azure
description: Dowiedz się, jak usługa Azure Route Server współpracuje z usługą ExpressRoute i bramami sieci VPN platformy Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680533"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Informacje o obsłudze usługi Azure Route Server (wersja zapoznawcza) dla ExpressRoute i sieci VPN platformy Azure

Usługa Azure Route Server obsługuje nie tylko wirtualne urządzenia sieciowe (urządzenie WUS) innych firm uruchomione na platformie Azure, ale również bezproblemowo integruje się z usługą ExpressRoute i bramami sieci VPN platformy Azure. Nie trzeba konfigurować komunikacji równorzędnej BGP ani zarządzać nią między bramą a serwerem usługi Azure Route. Można włączyć wymianę tras między bramą i serwerem usługi Azure Route z prostą [zmianą konfiguracji](quickstart-configure-route-server-powershell.md#route-exchange).

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Jak to działa?

W przypadku wdrażania serwera usługi Azure Route wraz z bramą ExpressRoute i urządzenie WUS w sieci wirtualnej domyślnie serwer usługi Azure Route nie propaguje tras odbieranych od bramy urządzenie WUS i ExpressRoute między sobą. Po włączeniu wymiany tras ExpressRoute i urządzenie WUS będą nauczyć się tras.

Na przykład na poniższym diagramie:

* Urządzenie SDWAN będzie odbierane z usługi Azure Route Server trasy od "on-Premium 2", która jest połączona z ExpressRoute, wraz z trasą sieci wirtualnej.

* Brama ExpressRoute otrzyma trasę od "on-Premium 1", która jest połączona z urządzeniem SDWAN, wraz z trasą sieci wirtualnej z serwera usługi Azure Route.

    ![Diagram przedstawiający ExpressRoute skonfigurowany z serwerem tras.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Urządzenie SDWAN można także zastąpić usługą Azure VPN Gateway. Ponieważ usługi Azure VPN Gateway i ExpressRoute są w pełni zarządzane, wystarczy włączyć wymianę trasy dla dwóch sieci lokalnych, aby komunikować się ze sobą.

> [!IMPORTANT] 
> Brama sieci VPN platformy Azure musi być skonfigurowana w trybie [**aktywny-aktywny**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) .
>

![Diagram przedstawiający ExpressRoute i bramę sieci VPN skonfigurowanej z serwerem tras.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Route Server](route-server-faq.md).
- Dowiedz się, jak [skonfigurować serwer usługi Azure Route](quickstart-configure-route-server-powershell.md).
- Dowiedz się więcej o [usłudze Azure ExpressRoute i współistnieniu sieci VPN platformy Azure](../expressroute/expressroute-howto-coexist-resource-manager.md).
