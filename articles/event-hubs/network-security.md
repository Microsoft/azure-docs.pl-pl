---
title: Zabezpieczenia sieciowe dla Event Hubs platformy Azure
description: W tym artykule opisano sposób konfigurowania dostępu z prywatnych punktów końcowych
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: ae6cbdc8258cde9bb2da961cb452f996f0797cfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767791"
---
# <a name="network-security-for-azure-event-hubs"></a>Zabezpieczenia sieciowe dla Event Hubs platformy Azure 
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w usłudze Azure Event Hubs: 

- Tagi usługi
- Reguły zapory adresów IP
- Punkty końcowe usługi sieciowej
- Prywatne punkty końcowe


## <a name="service-tags"></a>Tagi usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [Omówienie tagów usług](../virtual-network/service-tags-overview.md).

Za pomocą tagów usługi można definiować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](../virtual-network/security-overview.md#security-rules)   lub w [zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **EventHub**) w odpowiednim polu *źródłowym*   lub *docelowym*   reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.

| Tag usługi | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Event Hubs platformy Azure. | Outbound | Tak | Tak |


## <a name="ip-firewall"></a>Zapora IP 
Domyślnie obszary nazw Event Hubs są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Ta funkcja jest przydatna w scenariuszach, w których usługa Azure Event Hubs powinna być dostępna tylko z niektórych dobrze znanych lokacji. Reguły zapory umożliwiają konfigurowanie reguł w celu akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład jeśli używasz Event Hubs z usługą [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), możesz utworzyć **regułę zapory** , aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej. 

Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw Event Hubs. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Event Hubs, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować zaporę IP dla centrum zdarzeń](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Punkty końcowe usługi sieciowej
Integracja Event Hubs z [punktami końcowymi usługi Virtual Network (VNET)](../virtual-network/virtual-network-service-endpoints-overview.md) umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń, takich jak maszyny wirtualne powiązane z sieciami wirtualnymi, z zabezpieczoną ścieżką ruchu sieciowego na obu końcach.

Po skonfigurowaniu powiązania z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednia przestrzeń nazw Event Hubs nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie przestrzeni nazw Event Hubs z punktem końcowym usługi konfiguruje odizolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów. 

Wynikiem jest relacja między obciążeniami powiązanymi z podsiecią i odpowiadającą jej przestrzenią nazw Event Hubs, w odróżnieniu od pozostałego adresu sieciowego punktu końcowego usługi obsługi komunikatów znajdującego się w zakresie publicznego adresu IP. Wystąpił wyjątek dotyczący tego zachowania. Domyślnie włączenie punktu końcowego usługi powoduje włączenie `denyall` reguły w [zaporze IP](event-hubs-ip-filtering.md) skojarzonej z siecią wirtualną. Można dodać określone adresy IP w zaporze IP, aby umożliwić dostęp do publicznego punktu końcowego centrum zdarzeń. 

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w warstwach **Standardowa** i **Dedykowana** usługi Event Hubs. Ta wartość nie jest obsługiwana w warstwie **podstawowa** .

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń obsługujące integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłych i compartmentalized zabezpieczeń, a wirtualne podsieci sieci zapewniają segmentację między usługami compartmentalized, nadal muszą mieć ścieżki komunikacyjne między usługami znajdującymi się w tych przedziałach.

Wszystkie bezpośrednie trasy IP między przedziałami, w tym przenoszące HTTPS za pośrednictwem protokołu TCP/IP, obejmują ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi komunikatów zapewniają izolowane ścieżki komunikacyjne, w przypadku których komunikaty są nawet zapisywane na dysku podczas przejścia między stronami. Obciążenia w dwóch odrębnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem Event Hubs mogą komunikować się efektywnie i niezawodnie za pośrednictwem komunikatów, podczas gdy jest zachowywana odpowiednia integralność granic izolacji sieci.
 
Oznacza to, że rozwiązania w chmurze dotyczące zabezpieczeń nie tylko uzyskują dostęp do wiodących i skalowalnych w branży asynchronicznych funkcji obsługi komunikatów, ale mogą teraz używać komunikatów do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązania, które są najbardziej bezpieczne, niż to, co jest osiągalne w trybie komunikacji równorzędnej, w tym protokołu HTTPS i innych protokołów gniazd zabezpieczonych za pomocą protokołu TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Powiązywanie centrów zdarzeń z sieciami wirtualnymi

**Reguły sieci wirtualnej** to funkcja zabezpieczeń zapory, która kontroluje, czy przestrzeń nazw usługi Azure Event Hubs akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie przestrzeni nazw Event Hubs z siecią wirtualną jest procesem dwuetapowym. Najpierw musisz utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włączyć go dla elementu **Microsoft. EventHub** zgodnie z opisem w artykule [Przegląd punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) . Po dodaniu punktu końcowego usługi należy powiązać z nim przestrzeń nazw Event Hubs z **regułą sieci wirtualnej**.

Reguła sieci wirtualnej jest skojarzeniem przestrzeni nazw Event Hubs z podsiecią sieci wirtualnej. Chociaż reguła istnieje, wszystkie obciążenia powiązane z podsiecią mają udzielony dostęp do przestrzeni nazw Event Hubs. Sama Event Hubs nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie udzielono dostępu do podsieci przez włączenie tej reguły.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować punkty końcowe usługi sieci wirtualnej dla centrum zdarzeń](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Prywatne punkty końcowe

[Usługa link prywatny platformy Azure](../private-link/private-link-overview.md) umożliwia dostęp do usług platformy Azure (na przykład Azure Event Hubs, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

Prywatny punkt końcowy to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

> [!NOTE]
> Ta funkcja jest obsługiwana tylko w przypadku warstwy **dedykowanej** . Aby uzyskać więcej informacji na temat warstwy dedykowanej, zobacz [omówienie Event Hubs — warstwa dedykowana](event-hubs-dedicated-overview.md). 

Aby uzyskać więcej informacji, zobacz [jak skonfigurować prywatne punkty końcowe dla centrum zdarzeń](private-link-service.md)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Jak skonfigurować zaporę IP dla centrum zdarzeń](event-hubs-ip-filtering.md)
- [Jak skonfigurować punkty końcowe usługi sieci wirtualnej dla centrum zdarzeń](event-hubs-service-endpoints.md)
- [Jak skonfigurować prywatne punkty końcowe dla centrum zdarzeń](private-link-service.md)
