---
title: Architektura sieci platformy Azure
description: Ten artykuł zawiera ogólny opis sieci infrastruktury Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "68727170"
---
# <a name="azure-network-architecture"></a>Architektura sieci platformy Azure
Architektura sieci platformy Azure jest zgodna ze zmodyfikowaną wersją standardowego modelu/dystrybucji/dostępu w branży, z odrębnymi warstwami sprzętowymi. Dostępne są następujące warstwy:

- Rdzeń (routery centrum danych)
- Dystrybucja (routery dostępu i agregacja L2). Warstwa dystrybucji oddziela Routing P3 od przełączania L2.
- Dostęp (przełączniki hosta L2)

Architektura sieci ma dwa poziomy przełączników warstwy 2. Jedna warstwa agreguje ruch z innej warstwy. Druga warstwa pętle w celu uwzględnienia nadmiarowości. Architektura zapewnia bardziej elastyczne rozmiary sieci VLAN i usprawnia skalowanie portów. Architektura utrzymuje różne wartości L2 i L3, co umożliwia korzystanie z sprzętu w każdej z odrębnych warstw w sieci i minimalizuje błąd w jednej warstwie, aby wpływać na inne warstwy. Korzystanie z magistrali umożliwia udostępnianie zasobów, takich jak łączność z infrastrukturą L3.

## <a name="network-configuration"></a>Konfiguracja sieci
Architektura sieci klastra platformy Azure w ramach centrum danych obejmuje następujące urządzenia:

- Routery (centrum danych, router dostępu i routery liści obramowania)
- Przełączniki (agregacja i górne przełączenia w stojaku)
- Usługi Digi CMs
- Jednostki dystrybucji

Platforma Azure ma dwie oddzielne architektury. Niektórzy klienci platformy Azure i udostępnione usługi znajdują się w domyślnej architekturze sieci LAN (DLA), natomiast nowe regiony i klienci wirtualne znajdują się w architekturze Quantum 10 (Q10). DLA architektury jest tradycyjne projektowanie drzewa z aktywnymi/pasywnymi routerami dostępu i listami kontroli dostępu (ACL) zabezpieczeń zastosowanymi do routerów dostępu. Architektura Quantum 10 to projekt bliski/siatkowy, w którym listy ACL nie są stosowane na routerach. Zamiast tego listy ACL są stosowane poniżej routingu, za pomocą oprogramowania do równoważenia obciążenia lub zdefiniowanych przez oprogramowanie sieci VLAN.

Na poniższym diagramie przedstawiono ogólne omówienie architektury sieci w ramach klastra platformy Azure:

![Diagram sieci platformy Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Urządzenia Quantum 10
Projekt Quantum 10 przeprowadza przełączanie warstwy 3 na wiele urządzeń w projekcie zamykanym/siatkowym. Zalety projektu Q10 obejmują większą możliwość i większą możliwość skalowania istniejącej infrastruktury sieciowej. Projekt korzysta z routerów liścia obramowania, przełączników i routerów z górnym stojakiem w celu przekazywania ruchu do klastrów w wielu trasach, co pozwala na odporność na uszkodzenia. Oprogramowanie do równoważenia obciążenia oprogramowania, a nie urządzeń sprzętowych, obsługuje usługi zabezpieczeń, takie jak translator adresów sieciowych.

### <a name="access-routers"></a>Routery dostępu
Routery do dystrybucji/uzyskiwania dostępu (ARs) wykonują podstawowe funkcje routingu dla warstw dystrybucji i dostępu. Te urządzenia są wdrażane jako para i są bramą domyślną dla podsieci. Każda para AR może obsługiwać wiele par przełączników agregacji P2, w zależności od pojemności. Maksymalna liczba zależy od pojemności urządzenia, a także domen błędów. Typowa liczba to trzy pary przełączników agregacji L2 na jedną parę.

### <a name="l2-aggregation-switches"></a>Przełączniki agregacji L2  
Te urządzenia służą jako punkt agregacji dla ruchu L2. Są one warstwą dystrybucji dla sieci szkieletowej L2 i mogą obsługiwać duże ilości danych. Ponieważ te urządzenia łączą ruch, wymagają funkcji q 802.1 oraz technologii o wysokiej przepustowości, takich jak agregacja portów i 10GE.

### <a name="l2-host-switches"></a>Przełączniki hosta L2
Hosty łączą się bezpośrednio z tymi przełącznikami. Mogą być przełącznikami montowanymi w stojaku lub wdrożeniami obudów. Standard 802.1 q umożliwia wyznaczenie jednej sieci VLAN jako natywnej sieci VLAN, traktując tę sieć VLAN jako normalną (nieoznakowaną) ramką Ethernet. W normalnych warunkach ramki na natywnej sieci VLAN są przesyłane i odbierane jako nieoznaczone jako nieoznaczone w porcie magistrali q 802.1. Ta funkcja została zaprojektowana na potrzeby migracji do 802.1 q i zgodności z urządzeniami z obsługą funkcji pytań i odpowiedzi, które nie są 802.1. W tej architekturze tylko infrastruktura sieci używa natywnej sieci VLAN.

Ta architektura określa standardowy wybór natywnej sieci VLAN. Standard gwarantuje, w miarę możliwości, że AR urządzenia mają unikatowe, natywne sieci VLAN dla każdej magistrali i L2Aggregation do L2Aggregationów. Szyby przełączania L2Aggregation do L2Host mają niedomyślną natywną sieć VLAN.

### <a name="link-aggregation-8023ad"></a>Agregacja linków (802.3 AD)
Agregacja łączy umożliwia łączenie wielu pojedynczych łączy i traktuje je jako pojedyncze łącze logiczne. Aby ułatwić debugowanie operacyjne, numer używany do wyznaczania interfejsów kanału portowego powinien być znormalizowany. Pozostała część sieci używa tego samego numeru na obu końcach kanału portu.

Liczby określone dla przełącznika L2Agg do L2Host to numery kanałów portów używane na stronie L2Agg. Ponieważ zakres liczb jest bardziej ograniczony po stronie L2Host, standardem jest użycie cyfr 1 i 2 po stronie L2Host. Odnoszą się one do kanału portu po stronie "a" i strony "b".

### <a name="vlans"></a>Sieci VLAN
Architektura sieci używa sieci VLAN do grupowania serwerów razem w jednej domenie emisji. Numery sieci VLAN są zgodne z 802.1 q Standard, który obsługuje sieci VLAN numerowane od 1 do 4094.

### <a name="customer-vlans"></a>Sieci VLAN klienta
Dostępne są różne opcje implementacji sieci VLAN, które można wdrożyć za pomocą Azure Portal w celu spełnienia wymagań dotyczących rozbarwień i architektury rozwiązania. Te rozwiązania są wdrażane za poorednictwem maszyn wirtualnych. Przykłady architektury referencyjnej klienta znajdują się w temacie [architektury referencyjne platformy Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architektura brzegowa
Centra danych platformy Azure są oparte na wysoce nadmiarowych i dobrze zainicjowanych infrastrukturach sieciowych. Firma Microsoft implementuje sieci w centrach danych platformy Azure przy użyciu "potrzebnych i jednej" (N + 1) architektur nadmiarowości lub lepszego. Pełna funkcja trybu failover w ramach programów i między centrami danych pomaga zapewnić dostępność sieci i usług. Zewnętrznie centra danych są obsługiwane przez dedykowane obwody sieciowe o wysokiej przepustowości. Te obwody nadmiarowo łączą właściwości z ponad 1200 dostawców usług internetowych w wielu punktach komunikacji równorzędnej. Zapewnia to ponad 2 000 GB/s pojemności potencjalnej krawędzi w sieci.

Routery filtrujące w warstwie brzegowej i dostępnym w sieci platformy Azure zapewniają dobre zabezpieczenia na poziomie pakietu i uniemożliwiają nieautoryzowane próby nawiązania połączenia z platformą Azure. Routery pomagają upewnić się, że rzeczywista zawartość pakietów zawiera dane w oczekiwanym formacie i jest zgodna z oczekiwanym schematem komunikacji klienta/serwera. Platforma Azure implementuje architekturę warstwową składającą się z następujących składników podziału sieci i kontroli dostępu:

- **Routery brzegowe.** Te rozdzielą środowisko aplikacji z Internetu. Routery brzegowe zostały zaprojektowane w celu zapewnienia ochrony przed fałszowaniem i ograniczenia dostępu przy użyciu list ACL.
- **Routery dystrybucji (dostęp).** Umożliwiają one tylko zatwierdzone adresy IP firmy Microsoft, zapewnienie ochrony przed fałszowaniem i nawiązanie połączeń przy użyciu list ACL.

### <a name="ddos-mitigation"></a>DDOS środki zaradcze
Rozproszone ataki typu "odmowa usługi" (DDoS) nadal stwarzają rzeczywiste zagrożenie dla niezawodności Usługi online. Ponieważ ataki stają się bardziej skomplikowane i zaawansowane, a ponieważ usługi firmy Microsoft stają się bardziej geograficznie zróżnicowane, identyfikowanie i minimalizowanie wpływu tych ataków jest wysokim priorytetem.

[Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) zapewnia ochronę przed atakami DDoS. Zobacz [Azure DDoS Protection: najlepsze rozwiązania i architektury referencyjne,](ddos-best-practices.md) aby dowiedzieć się więcej.

> [!NOTE]
> Firma Microsoft udostępnia domyślnie ochronę DDoS wszystkim klientom platformy Azure.
>
>

## <a name="network-connection-rules"></a>Reguły połączeń sieciowych
W swojej sieci platforma Azure wdraża routery brzegowe zapewniające bezpieczeństwo na poziomie pakietu, aby uniemożliwić nieautoryzowane podejmowanie prób nawiązania połączenia z platformą Azure. Routery brzegowe zapewniają, że rzeczywista zawartość pakietów zawiera dane w oczekiwanym formacie i jest zgodna z oczekiwanym schematem komunikacji klienta/serwera.

Routery brzegowe dzielą środowisko aplikacji z Internetu. Te routery zostały zaprojektowane w celu zapewnienia ochrony przed fałszowaniem i ograniczania dostępu przy użyciu list ACL. Firma Microsoft konfiguruje routery brzegowe za pomocą podejścia warstwowego listy kontroli dostępu, aby ograniczyć protokoły sieciowe, które mogą przetransferować routery brzegowe i routery dostępu.

Firma Microsoft umieszcza urządzenia sieciowe w miejscach dostępu i lokalizacji granicznych, tak aby działały jako punkty graniczne, w których stosowane są filtry ruchu przychodzącego lub wychodzącego.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co firma Microsoft pomaga w zabezpieczeniu infrastruktury platformy Azure, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)


