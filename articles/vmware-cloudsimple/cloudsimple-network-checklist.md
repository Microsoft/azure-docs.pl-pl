---
title: Rozwiązanie VMware platformy Azure według CloudSimple — Lista kontrolna sieci
description: Lista kontrolna alokacji CIDR sieci w rozwiązaniu Azure VMware przez CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025014"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Wymagania wstępne dotyczące sieci dla rozwiązań VMware platformy Azure według CloudSimple

Rozwiązanie VMware firmy Azure według CloudSimple oferuje środowisko chmury prywatnej VMware, które jest dostępne dla użytkowników i aplikacji ze środowisk lokalnych, urządzeń zarządzanych przez przedsiębiorstwo i zasobów platformy Azure. Łączność jest dostarczana za pośrednictwem usług sieciowych, takich jak sieci VPN i połączenia usługi Azure ExpressRoute. Niektóre z tych usług sieciowych wymagają określenia zakresów adresów sieciowych umożliwiających włączenie usług. 

Tabele w tym artykule opisują zestaw zakresów adresów i odpowiadające im usługi korzystające z określonych adresów. Niektóre z tych adresów są obowiązkowe, a niektóre z nich zależą od usług, które chcesz wdrożyć. Te przestrzenie adresowe nie mogą nakładać się na żadne podsieci lokalne, podsieci platformy Azure Virtual Network ani podsieci obciążeń planowanych CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Zakresy adresów sieciowych wymagane do utworzenia chmury prywatnej

Podczas tworzenia usługi CloudSimple i chmury prywatnej należy przestrzegać określonych zakresów klasy sieci routingu między domenami (CIDR) w następujący sposób.

| Nazwa/użyta dla     | Opis                                                                                                                            | Zakres adresów            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR bramy      | Wymagane dla usług brzegowych (bramy sieci VPN).  Ten CIDR jest wymagany podczas tworzenia usługi CloudSimple i musi pochodzić z przestrzeni RFC 1918. | /28                      |
| vSphere/sieci vSAN CIDR | Wymagane w przypadku sieci zarządzania VMware. Ten CIDR należy określić podczas tworzenia chmury prywatnej.                                    | /24 lub/23 lub/22 lub/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Zakres adresów sieciowych wymagany dla połączenia sieciowego platformy Azure z siecią lokalną

Połączenie z [sieci lokalnej do sieci prywatnej chmury za pośrednictwem usługi ExpressRoute](on-premises-connection.md) powoduje nawiązanie połączenia Global REACH.  Połączenie używa protokołu Border Gateway Protocol (BGP) do wymiany tras między siecią lokalną, siecią w chmurze prywatnej i sieciami platformy Azure.

| Nazwa/użyta dla             | Opis                                                                                                                                                                             | Zakres adresów |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute komunikacji równorzędnej | Wymagany w przypadku korzystania z ExpressRoute Global Reach dla połączeń lokalnych. Ten CIDR należy podać, gdy Global Reach żądanie połączenia za pomocą biletu pomocy technicznej. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Zakres adresów sieciowych wymagany do korzystania z połączenia sieci VPN typu lokacja-lokacja z siecią lokalną

Połączenie z [sieci lokalnej do sieci prywatnej chmury przy użyciu sieci VPN typu lokacja-lokacja](vpn-gateway.md) wymaga następujących adresów IP:, sieci lokalnej i identyfikatorów. 

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adres IP elementu równorzędnego               | Publiczny adres IP lokalnej bramy sieci VPN. Wymagane do ustanowienia połączenia sieci VPN typu lokacja-lokacja między lokalnym centrum danych a regionem usługi CloudSimple. Ten adres IP jest wymagany podczas tworzenia bramy sieci VPN typu lokacja-lokacja.                                         |
| Identyfikator elementu równorzędnego       | Identyfikator równorzędny lokalnej bramy sieci VPN. Jest to zwykle takie samo jak **adres IP elementu równorzędnego**.  Jeśli unikatowy identyfikator jest określony w lokalnej bramie sieci VPN, należy określić identyfikator.  Identyfikator elementu równorzędnego jest wymagany podczas tworzenia bramy sieci VPN typu lokacja-lokacja.   |
| Sieci lokalne   | Lokalne prefiksy, które wymagają dostępu do sieci CloudSimple w regionie.  Uwzględnij wszystkie prefiksy z sieci lokalnej, które będą miały dostęp do sieci CloudSimple, w tym z sieci klienta, z której użytkownicy będą uzyskiwać dostęp do sieci.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Zakres adresów sieciowych wymagany do korzystania z połączeń sieci VPN typu punkt-lokacja

Połączenie sieci VPN typu punkt-lokacja umożliwia dostęp do sieci CloudSimple z komputera klienckiego.  [Aby skonfigurować sieć VPN typu punkt-lokacja](vpn-gateway.md), należy określić następujący zakres adresów sieciowych.

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsieć klienta         | Adresy DHCP są udostępniane przez podsieć klienta podczas nawiązywania połączenia przy użyciu sieci VPN typu punkt-lokacja. Ta podsieć jest wymagana podczas tworzenia bramy sieci VPN typu punkt-lokacja w portalu CloudSimple.  Sieć jest podzielona na dwie podsieci; jeden dla połączenia UDP i drugi dla połączeń TCP. |

## <a name="next-steps"></a>Następne kroki

* [Lokalna konfiguracja zapory do uzyskiwania dostępu do chmury prywatnej](on-premises-firewall-configuration.md)
* [Szybki Start — Tworzenie usługi CloudSimple](quickstart-create-cloudsimple-service.md)
* [Szybki Start — Konfigurowanie chmury prywatnej](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [usłudze Azure Network Connections](cloudsimple-azure-network-connection.md)
* Dowiedz się więcej o [bramach sieci VPN](cloudsimple-vpn-gateways.md)
