---
title: Informacje o usłudze Azure ExpressRoute FastPath
description: Dowiedz się więcej o usłudze Azure ExpressRoute FastPath do wysyłania ruchu sieciowego przez obejście bramy
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: duau
ms.openlocfilehash: ba23319c35aed1d09da652e6f84b60e5f8e9495e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740889"
---
# <a name="about-expressroute-fastpath"></a>Informacje o usłudze ExpressRoute FastPath

Brama sieci wirtualnej ExpressRoute jest przeznaczona do wymiany tras sieciowych i kierowania ruchu sieciowego. FastPath zaprojektowano w celu poprawienia wydajności ścieżki danych między siecią lokalną i siecią wirtualną. Po włączeniu FastPath wysyła ruch sieciowy bezpośrednio do maszyn wirtualnych w sieci wirtualnej, pomijając bramę.

## <a name="requirements"></a>Wymagania

### <a name="circuits"></a>Elektrycznych

FastPath jest dostępny we wszystkich obwodach ExpressRoute.

### <a name="gateways"></a>Bramy

FastPath nadal wymaga utworzenia bramy sieci wirtualnej w celu wymiany tras między siecią wirtualną i siecią lokalną. Aby uzyskać więcej informacji o bramach sieci wirtualnych i ExpressRoute, w tym informacje o wydajności i jednostkach SKU bramy, zobacz [ExpressRoute bram sieci wirtualnej](expressroute-about-virtual-network-gateways.md).

Aby można było skonfigurować FastPath, Brama sieci wirtualnej musi być:

* Niezwykle wydajna
* ErGw3AZ

> [!IMPORTANT]
> Jeśli planujesz używać FastPath z prywatną komunikację równorzędną IPv6 za pośrednictwem ExpressRoute, upewnij się, że wybrano opcję ErGw3AZ dla **jednostki SKU**.
> 
>

## <a name="limitations"></a>Ograniczenia

Program FastPath obsługuje większość konfiguracji, ale nie obsługuje następujących funkcji:

* UDR w podsieci bramy: w przypadku zastosowania UDR do podsieci bramy sieci wirtualnej ruch sieciowy z sieci lokalnej będzie nadal wysyłany do bramy sieci wirtualnej.

* Komunikacja równorzędna sieci wirtualnych: w przypadku komunikacji równorzędnej z usługą, która jest połączona z ExpressRoute, ruch sieciowy z sieci lokalnej do innych sieci wirtualnych (tj. "szprychy" sieci wirtualnych) będzie nadal wysyłany do bramy sieci wirtualnej. Obejście polega na połączeniu wszystkich sieci wirtualnych bezpośrednio do obwodu ExpressRoute.

* Podstawowa Load Balancer: w przypadku wdrożenia podstawowego wewnętrznego modułu równoważenia obciążenia w sieci wirtualnej lub usługi Azure PaaS, która jest wdrażana w sieci wirtualnej, używa podstawowego wewnętrznego modułu równoważenia obciążenia, ruch sieciowy z sieci lokalnej do wirtualnych adresów IP hostowanych w ramach podstawowego modułu równoważenia obciążenia zostanie wysłany do bramy sieci wirtualnej. Rozwiązaniem jest uaktualnienie podstawowego modułu równoważenia obciążenia do usługi [równoważenia obciążenia w warstwie Standardowa](../load-balancer/load-balancer-overview.md).

* Link prywatny: w przypadku nawiązania połączenia z [prywatnym punktem końcowym](../private-link/private-link-overview.md) w sieci wirtualnej z sieci lokalnej połączenie przejdzie przez bramę sieci wirtualnej.
 
## <a name="next-steps"></a>Następne kroki

Aby włączyć FastPath, zobacz [łączenie sieci wirtualnej z ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
