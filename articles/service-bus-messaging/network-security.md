---
title: Zabezpieczenia sieci dla Azure Service Bus
description: W tym artykule opisano funkcje zabezpieczeń sieci, takie jak Tagi usług, reguły zapory adresów IP, punkty końcowe usługi i prywatne punkty końcowe.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: fb21c8beb6d48ecab04917525011cc4762c46ff3
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766400"
---
# <a name="network-security-for-azure-service-bus"></a>Zabezpieczenia sieci dla Azure Service Bus 
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w Azure Service Bus: 

- Tagi usługi
- Reguły zapory adresów IP
- Punkty końcowe usługi sieciowej
- Prywatne punkty końcowe


## <a name="service-tags"></a>Tagi usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [Omówienie tagów usług](../virtual-network/service-tags-overview.md).

Za pomocą tagów usługi można definiować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](../virtual-network/security-overview.md#security-rules) lub w [zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **ServiceBus**) w odpowiednim polu *źródłowym* lub *docelowym* reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.

| Tag usługi | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus ruch korzystający z warstwy usługi Premium. | Outbound | Tak | Tak |


> [!NOTE]
> Tagów usługi można używać tylko w przypadku przestrzeni nazw **Premium** . Jeśli używasz **standardowej** przestrzeni nazw, użyj adresu IP, który będzie widoczny podczas uruchamiania następującego polecenia: `nslookup <host name for the namespace>` . Przykład: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>Zapora IP 
Domyślnie obszary nazw Service Bus są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją do tylko zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas routingu między domenami)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Ta funkcja jest przydatna w scenariuszach, w których Azure Service Bus powinny być dostępne tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł w celu akceptowania ruchu pochodzącego z określonych adresów IPv4. Jeśli na przykład używasz Service Bus z [Azure Express Route] [Express-Route], możesz utworzyć **regułę zapory** , aby zezwolić na ruch tylko z adresów IP lub adresów lokalnych infrastruktury NAT firmy. 

Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw Service Bus. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Service Bus, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować zaporę IP dla przestrzeni nazw Service Bus](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Punkty końcowe usługi sieciowej
Integracja Service Bus z [punktami końcowymi usługi Virtual Network (VNET)](service-bus-service-endpoints.md) umożliwia bezpieczny dostęp do funkcji obsługi komunikatów z obciążeń takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, z zabezpieczoną ścieżką ruchu sieciowego na obu końcach.

Po skonfigurowaniu do powiązania z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednie przestrzenie nazw Service Bus nie będą już akceptować ruchu z dowolnego miejsca, ale autoryzowanych sieci wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie przestrzeni nazw Service Bus z punktem końcowym usługi konfiguruje odizolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów.

Wynikiem jest relacja między obciążeniami powiązanymi z podsiecią i odpowiadającą jej przestrzenią nazw Service Bus, w odróżnieniu od pozostałego adresu sieciowego punktu końcowego usługi obsługi komunikatów znajdującego się w zakresie publicznego adresu IP.

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane tylko w [warstwie Premium](service-bus-premium-messaging.md) Service Bus przestrzenie nazw.
> 
> W przypadku korzystania z punktów końcowych usługi sieci wirtualnej z Service Bus nie należy włączać tych punktów końcowych w aplikacjach, które mieszają warstwy Standardowa i Premium Service Bus przestrzenie nazw. Ponieważ warstwa standardowa nie obsługuje sieci wirtualnych. Punkt końcowy jest ograniczony tylko do przestrzeni nazw warstwy Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń obsługujące integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłych i compartmentalized zabezpieczeń, a wirtualne podsieci sieci zapewniają segmentację między usługami compartmentalized, zazwyczaj nadal potrzebują ścieżek komunikacji między usługami znajdującymi się w tych przedziałach.

Wszystkie bezpośrednie trasy IP między przedziałami, w tym przenoszące HTTPS za pośrednictwem protokołu TCP/IP, obejmują ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi komunikatów zapewniają całkowicie izolowane ścieżki komunikacyjne, w przypadku których komunikaty są nawet zapisywane na dysku podczas przejścia między stronami. Obciążenia w dwóch odrębnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem Service Bus mogą komunikować się efektywnie i niezawodnie za pośrednictwem komunikatów, podczas gdy jest zachowywana odpowiednia integralność granic izolacji sieci.
 
Oznacza to, że rozwiązania w chmurze dotyczące zabezpieczeń nie tylko uzyskują dostęp do wiodących i skalowalnych w branży asynchronicznych funkcji obsługi komunikatów, ale mogą teraz używać komunikatów do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązania, które są najbardziej bezpieczne, niż to, co jest osiągalne w trybie komunikacji równorzędnej, w tym protokołu HTTPS i innych protokołów gniazd zabezpieczonych za pomocą protokołu TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Powiąż Service Bus z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy serwer Azure Service Bus akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie przestrzeni nazw Service Bus z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi Virtual Network** w podsieci Virtual Network i włączyć ją dla elementu **Microsoft. ServiceBus** , zgodnie z opisem w temacie [Omówienie punktu końcowego usługi](service-bus-service-endpoints.md). Po dodaniu punktu końcowego usługi należy powiązać z nim przestrzeń nazw Service Bus z **regułą sieci wirtualnej**.

Reguła sieci wirtualnej jest skojarzeniem przestrzeni nazw Service Bus z podsiecią sieci wirtualnej. Chociaż reguła istnieje, wszystkie obciążenia powiązane z podsiecią mają udzielony dostęp do przestrzeni nazw Service Bus. Sama Service Bus nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie udzielono dostępu do podsieci przez włączenie tej reguły.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować punkty końcowe usługi sieci wirtualnej dla Service Bus przestrzeni nazw](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Prywatne punkty końcowe

Usługa link prywatny platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Service Bus, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

Prywatny punkt końcowy to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Private link?](../private-link/private-link-overview.md)

> [!NOTE]
> Ta funkcja jest obsługiwana w warstwie **premium** Azure Service Bus. Aby uzyskać więcej informacji na temat warstwy Premium, zobacz artykuł [Service Bus warstwy Premium i Standard Messaging](service-bus-premium-messaging.md) .


Aby uzyskać więcej informacji, zobacz [jak skonfigurować prywatne punkty końcowe dla Service Bus przestrzeni nazw](private-link-service.md)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Jak skonfigurować zaporę IP dla przestrzeni nazw Service Bus](service-bus-ip-filtering.md)
- [Jak skonfigurować punkty końcowe usługi sieci wirtualnej dla Service Bus przestrzeni nazw](service-bus-service-endpoints.md)
- [Jak skonfigurować prywatne punkty końcowe dla Service Bus przestrzeni nazw](private-link-service.md)
