---
title: Często zadawane pytania dotyczące usługi Azure Route Server
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485423"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (wersja zapoznawcza) — często zadawane pytania

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Co to jest usługa Azure Route Server?

Azure Route Server to w pełni zarządzana usługa, która umożliwia łatwe zarządzanie routingiem między urządzeniem wirtualnym sieci (urządzenie WUS) i siecią wirtualną.

### <a name="is-azure-route-server-just-a-vm"></a>Czy serwer usługi Azure Route jest tylko maszyną wirtualną?

Nie. Azure Route Server to usługa o wysokiej dostępności. Jeśli zostanie ona wdrożona w regionie świadczenia usługi Azure, który obsługuje [strefy dostępności](../availability-zones/az-overview.md), będzie mieć nadmiarowość poziomu strefy.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Jakie protokoły routingu obsługuje usługa Azure Route Server?

Serwer tras platformy Azure obsługuje tylko Border Gateway Protocol (BGP). URZĄDZENIE WUS musi obsługiwać zewnętrzny protokół BGP z wieloskokiem, ponieważ należy wdrożyć serwer usługi Azure Route w dedykowanej podsieci w sieci wirtualnej. Wybrany numer [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) musi być inny niż ten, który jest używany przez serwer usługi Azure Route podczas KONFIGUROWANIA protokołu BGP na urządzenie WUS.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Czy serwer tras platformy Azure kieruje ruchem danych między moją urządzenie WUS i maszynami wirtualnymi?

Nie. Serwer usługi Azure Route wymienia tylko trasy BGP z urządzenie WUS. Ruch danych przechodzi bezpośrednio z urządzenie WUS na wybraną maszynę wirtualną i bezpośrednio z maszyny wirtualnej do urządzenie WUS.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Jeśli usługa Azure Route Server odbiera tę samą trasę z więcej niż jednego urządzenie WUS, będzie programować wszystkie kopie trasy (ale z innym następnym przeskokiem) do maszyn wirtualnych w sieci wirtualnej?

Tak, tylko wtedy, gdy trasa ma taką samą długość jak ścieżka. Gdy maszyny wirtualne wysyłają ruch do miejsca docelowego tej trasy, hosty maszyn wirtualnych będą wykonywać Equal-Cost routingu wielościeżkowego (ECMP). Jeśli jednak jedna urządzenie WUS wysyła trasę o długości ścieżki krótszej niż inne urządzeń WUS. Usługa Azure Route Server będzie programować tylko trasę, która ma ustawiony następny przeskok do urządzenie WUS na maszynach wirtualnych w sieci wirtualnej.

### <a name="does-azure-route-server-support-vnet-peering"></a>Czy serwer tras platformy Azure obsługuje komunikację równorzędną sieci wirtualnych?

Tak. W przypadku komunikacji równorzędnej z siecią wirtualną obsługującą serwer tras platformy Azure w innej sieci wirtualnej i włączasz korzystanie z bramy zdalnej w danej sieci wirtualnej. Serwer usługi Azure Route pouczy się przestrzeni adresowych tej sieci wirtualnej i wyśle je do wszystkich urządzeń WUS komunikacji równorzędnej.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Jakie numery systemu autonomicznego (ASN) można użyć?

Możesz użyć własnego publicznego WPW lub prywatnego WPW w urządzeniu wirtualnym sieci. Nie można używać zakresów zarezerwowanych przez platformę Azure ani organizację IANA.
Następujące numery ASN są zarezerwowane przez platformę Azure lub organizację IANA:

* WPW zarezerwowane przez platformę Azure:
    * Publiczne numery ASN: 8074, 8075, 12076
    * Prywatne numery ASN: 65515, 65517, 65518, 65519, 65520
* WPW [zarezerwowane przez organizację IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Czy mogę użyć 32-bitowego (4-bajtowego) WPW?

Nie, usługa Azure Route Server obsługuje tylko 16-bitowe (2 bajty) WPW.

## <a name="route-server-limits"></a><a name = "limitations"></a>Limity serwera tras

Serwer usługi Azure Route ma następujące limity (na wdrożenie).

| Zasób | Limit |
|----------|-------|
| Liczba obsługiwanych elementów równorzędnych BGP | 8 |
| Liczba tras, które każdy element równorzędny protokołu BGP może anonsować na serwerze usługi Azure Route | 200 |
| Liczba tras, które serwer usługi Azure Route może anonsować do ExpressRoute lub bramy sieci VPN | 200 |

Jeśli urządzenie WUS anonsuje więcej tras niż limit, sesja protokołu BGP zostanie usunięta. W takim przypadku w przypadku bramy i usługi Azure Route Server utracisz łączność z sieci lokalnej na platformę Azure. Aby uzyskać więcej informacji, zobacz [diagnozowanie problemu z routingiem maszyny wirtualnej platformy Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować serwer usługi Azure Route](quickstart-configure-route-server-powershell.md).
