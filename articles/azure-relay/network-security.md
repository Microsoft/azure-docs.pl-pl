---
title: Zabezpieczenia sieci dla Azure Relay
description: W tym artykule opisano sposób używania reguł zapory IP i prywatnych punktów końcowych z Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3aa3ffd119f65ec5181b0c382472cc4ef3c8bac4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263729"
---
# <a name="network-security-for-azure-relay"></a>Zabezpieczenia sieci dla Azure Relay 
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w Azure Relay: 

- Reguły zapory IP (wersja zapoznawcza)
- Prywatne punkty końcowe (wersja zapoznawcza)

> [!NOTE]
> Azure Relay nie obsługuje punktów końcowych usługi sieciowej. 


## <a name="ip-firewall"></a>Zapora IP 
Domyślnie przestrzenie nazw przekaźnika są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Ta funkcja jest przydatna w scenariuszach, w których Azure Relay powinny być dostępne tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł w celu akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład, jeśli używasz przekaźnika z [usługą Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), możesz utworzyć **regułę zapory** , aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej. 

Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw przekaźnika. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw przekaźnika, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować zaporę IP dla przestrzeni nazw przekaźnika](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Prywatne punkty końcowe

**Usługa link prywatny** platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Private link (wersja zapoznawcza)?](../private-link/private-link-overview.md)

**Prywatny punkt końcowy** to interfejs sieciowy, który pozwala obciążom uruchomionym w sieci wirtualnej łączenie się prywatnie i bezpiecznie z usługą, która ma **prywatny zasób linku** (na przykład przestrzeń nazw przekaźnika). Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, ExpressRoute, połączenia sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą przechodzi przez sieć szkieletową firmy Microsoft w celu wyeliminowania zagrożeń z publicznego Internetu. Możesz zapewnić poziom szczegółowości w kontroli dostępu, zezwalając na połączenia z określonymi przestrzeniami nazw Azure Relay.

> [!NOTE]
> Ta funkcja jest obecnie dostępna w **wersji zapoznawczej**. 

Aby uzyskać więcej informacji, zobacz [How to configure Private Endpoints](private-link-service.md)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Jak skonfigurować zaporę IP](ip-firewall-virtual-networks.md)
- [Jak skonfigurować prywatne punkty końcowe](private-link-service.md)
