---
title: Informacje o strefie Edge platformy Azure w wersji zapoznawczej
description: 'Dowiedz się więcej o ofercie przetwarzania brzegowego firmy Microsoft: strefa brzegowa platformy Azure.'
services: vnf-manager
author: cherylmc
ms.service: vnf-manager
ms.topic: article
ms.date: 01/13/2021
ms.author: cherylmc
ms.openlocfilehash: 04555303d5128db6c183d27a0c5fcb69063fdc28
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185414"
---
# <a name="about-azure-edge-zone-preview"></a>Informacje o strefie Edge platformy Azure w wersji zapoznawczej

Strefa brzegowa platformy Azure to rodzina ofert od Microsoft Azure, która umożliwia użytkownikom przetwarzanie danych blisko użytkownika. Maszyny wirtualne, kontenery i inne wybrane usługi platformy Azure można wdrażać w strefach brzegowych, aby spełnić wymagania dotyczące małych opóźnień i wysokiej przepływności aplikacji.

Typowe scenariusze przypadków użycia dla stref brzegowych obejmują:

- Polecenie i kontrola w czasie rzeczywistym.
- Analiza w czasie rzeczywistym i inferencing za pośrednictwem sztucznej analizy i uczenia maszynowego.
- Wizja maszyn.
- Zdalne renderowanie w przypadku scenariuszy o rzeczywistości mieszanej i infrastruktury VDI.
- Gry dla wielu graczy.
- Przesyłanie strumieniowe multimediów i dostarczanie zawartości.
- Nadzór i zabezpieczenia.

Istnieją trzy typy stref brzegowych platformy Azure:

- Strefy usługi Azure Edge Zone
- Strefy brzegowe platformy Azure z przewoźnikiem
- Prywatne strefy brzegowe platformy Azure

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Strefy usługi Azure Edge Zone

![Strefy usługi Azure Edge Zone](./media/edge-zones-overview/edge-zones.png "Strefy usługi Azure Edge Zone")

Strefy na platformie Azure są małymi rozszerzeniami systemu Azure umieszczonymi w centrach ludności, które są daleko od regionów świadczenia usługi Azure. Strefy usługi Azure Edge obsługują maszyny wirtualne, kontenery i wybrany zestaw usług platformy Azure, dzięki którym można uruchamiać aplikacje korzystające z opóźnienia i intensywność przepływności w pobliżu użytkowników końcowych. Strefy usługi Azure Edge są częścią sieci globalnej firmy Microsoft. Zapewniają bezpieczną, niezawodną łączność o wysokiej przepustowości między aplikacjami, które działają w strefie brzegowej blisko użytkownika. Strefy usługi Azure Edge są własnością firmy Microsoft i są przez nią obsługiwane. Aby zarządzać usługami i wdrażać je w strefach brzegowych, można użyć tego samego zestawu narzędzi platformy Azure i tego samego portalu.

Typowe przypadki użycia obejmują:

- Gier i przesyłania strumieniowego gier.
- Przesyłanie strumieniowe multimediów i dostarczanie zawartości.
- Analiza w czasie rzeczywistym i inferencing za pośrednictwem sztucznej analizy i uczenia maszynowego.
- Renderowanie dla rzeczywistości mieszanej.

Strefy usługi Azure Edge będą dostępne w następujących obszarach Metro:

- Nowy Jork, NY
- Los Angeles, CA
- Miami, FL

Aby uzyskać więcej informacji [, skontaktuj się z zespołem strefy brzegowej](https://aka.ms/EdgeZones) .

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Strefy brzegowe platformy Azure z przewoźnikiem

![Strefy brzegowe z przewoźnikiem](./media/edge-zones-overview/edge-carrier.png "Strefy brzegowe z przewoźnikiem")

Strefy usługi Azure Edge z przewoźnikiem to małe, rozbudowane rozszerzenia platformy Azure, które są umieszczane w centrach danych na urządzeniach przenośnych operatorów. Strefa brzegowa platformy Azure z infrastrukturą operatora ma jeden przeskok z sieci 5G operatora komórkowego. To rozmieszczenie oferuje czas oczekiwania krótszy niż 10 milisekund na aplikacje z urządzeń przenośnych.

Strefy usługi Azure Edge z przewoźnikiem są wdrażane w centrach danych operatorów mobilnych i połączone z siecią globalną firmy Microsoft. Zapewniają bezpieczną, niezawodną łączność o wysokiej przepustowości między aplikacjami, które działają blisko użytkownika. Deweloperzy mogą używać tego samego zestawu znanych narzędzi do kompilowania i wdrażania usług w strefach brzegowych.

Typowe przypadki użycia obejmują:

- Gier i przesyłania strumieniowego gier.
- Przesyłanie strumieniowe multimediów i dostarczanie zawartości.
- Analiza w czasie rzeczywistym i inferencing za pośrednictwem sztucznej analizy i uczenia maszynowego.
- Renderowanie dla rzeczywistości mieszanej.
- Połączone aplikacje mobilne.
- Tele — medycyna.

Strefy brzegowe będą oferowane w ramach partnerstwa z następującymi operatorami:

- W&T (Atlanta, Dallas i Los Angeles)

Niezależni dostawcy oprogramowania pracującego nad zoptymalizowanymi i skalowalnymi aplikacjami połączonymi z sieciami 5Gymi mogą teraz używać nowej lokalizacji w wersji zapoznawczej&usługi Los Zarejestruj się, aby uzyskać dostęp do programu wczesnego zastosowania, aby korzystać z bezpiecznej łączności o wysokiej przepustowości.

Aby uzyskać więcej informacji [, skontaktuj się z zespołem strefy brzegowej](https://aka.ms/EdgeZones) .

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Prywatne strefy brzegowe platformy Azure

![Prywatne strefy brzegowe](./media/edge-zones-overview/private-edge.png "Prywatne strefy brzegowe")

Prywatne strefy brzegowe platformy Azure to małe rozszerzenia platformy Azure, które są umieszczane lokalnie. Strefa usługi Azure Private Edge jest oparta na [Azure Stackej platformie brzegowej](https://azure.microsoft.com/products/azure-stack/edge/) . Zapewnia ona dostęp do małych opóźnień do usług obliczeniowych i magazynowych wdrożonych lokalnie. Prywatne strefy brzegowe umożliwiają również wdrażanie aplikacji od niezależnych dostawców oprogramowania i zwirtualizowanych funkcji sieciowych (VNFs) jako [aplikacji zarządzanych przez platformę Azure](https://azure.microsoft.com/services/managed-applications/) wraz z maszynami wirtualnymi i kontenerami lokalnymi. VNFs te mogą obejmować rdzenie pakietów mobilnych, routery, zapory i urządzenia SD-WAN. Prywatna strefa brzegowa platformy Azure obejmuje rozwiązanie aranżacji w chmurze, które pozwala zarządzać cyklami życia VNFs i aplikacji z Azure Portal.

Strefa usługi Azure Private Edge umożliwia tworzenie i wdrażanie aplikacji lokalnych przy użyciu tych samych znanych narzędzi, które są używane do kompilowania i wdrażania aplikacji na platformie Azure.

Umożliwia także:

- Uruchom prywatne sieci mobilne (prywatna LTE, 5G prywatny).
- Zaimplementuj funkcje zabezpieczeń, takie jak zapory.
- Zwiększaj możliwości sieci lokalnych w wielu gałęziach i na platformie Azure przy użyciu urządzeń SD-WAN na tych samych urządzeniach strefy prywatnej krawędzi i zarządzaj nimi z poziomu platformy Azure.

Typowe przypadki użycia obejmują:

- Polecenie i kontrola w czasie rzeczywistym.
- Analiza i inferencing w czasie rzeczywistym dzięki sztucznej analizie i uczeniu maszynowym.
- Wizja maszyn.
- Zdalne renderowanie w przypadku scenariuszy o rzeczywistości mieszanej i infrastruktury VDI.
- Nadzór i zabezpieczenia.

Mamy rozbudowany ekosystem dostawców VNF, niezależnych dostawcy oprogramowania i partnerów MSP, aby umożliwić kompleksowe rozwiązania korzystające z prywatnych stref brzegowych. Aby uzyskać więcej informacji [, skontaktuj się z zespołem prywatnej strefy brzegowej](https://aka.ms/EdgeZonesPartner) .

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>Prywatni partnerzy strefy brzegowej

![Prywatni partnerzy strefy brzegowej](./media/edge-zones-overview/partners.png "Partnerzy prywatnych stref brzegowych")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Zwirtualizowane funkcje sieciowe (VNFs)

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Zwirtualizowane rozwijające się rdzeń pakietu (vEPC) dla sieci mobilnych

- [Potwierdzone sieci](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druid oprogramowanie](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Przełącznik](https://www.metaswitch.com/)
- [Chmura cyfrowa automatyzacji firmy Nokia](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Przenośne partnerzy radiowi

- [Celona](https://www.celona.io/azure-edge)
- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>Dostawcy SD-WAN

- [128 Technology](https://www.128technology.com/)
- [Znaleźliśmy](https://netfoundry.io/)
- [Nuage sieci z firmy Nokia](https://www.nuagenetworks.net/)
- [Sieci odwracające](https://www.versa-networks.com/)
- [VMware SD-WAN według Velocloud](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>Dostawcy routera

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Dostawcy zapory

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>Dostawcy rozwiązań zarządzanych: operatorzy mobilni i globalne Integratory systemu (GSIs)

| GSIs i operatory | Operatory mobilne |
| --- | --- |
| Amdocs                       | Etisalat             |
| Wieża (Tower)               | NTT Communications   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| Federacyjna sieć bezprzewodowa           | SK Telecom           |
| Infosys                      | Telefonica           |
| Mahindra Tech                | Telstra              |
|                              | Vodafone             |

Aby uzyskać informacje na temat sposobu, w jaki stał się partnerem [, skontaktuj się z zespołem strefy prywatnej Edge](https://aka.ms/EdgeZonesPartner) .

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>Rozwiązania prywatnych stref brzegowych

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Prywatna sieć mobilna w strefach prywatnych krawędzi

![Prywatna sieć mobilna w strefach prywatnych krawędzi](./media/edge-zones-overview/mobile-networks.png "Prywatna sieć mobilna w strefach prywatnych krawędzi")

Teraz można wdrożyć prywatną sieć mobilną w strefach prywatnych krawędzi. Prywatne sieci mobilne umożliwiają niezwykle małe opóźnienia, wysoką pojemność i niezawodną i bezpieczną sieć bezprzewodową wymaganą przez aplikacje o znaczeniu krytycznym.

Prywatne sieci komórkowe mogą umożliwiać scenariusze, takie jak:
- Polecenie i kontrola zautomatyzowanych pojazdów z przewodnikiem (AGVs) w magazynach.
- Komunikacja między robotami w czasie rzeczywistym.
- Aplikacje o zwiększonej rzeczywistości i wirtualnej rzeczywistości.

Zwirtualizowana funkcja sieciowa vEPC () jest mózgiem prywatnej sieci mobilnej. Teraz można wdrożyć vEPC w prywatnych strefach brzegowych. Aby uzyskać listę partnerów vEPC dostępnych w strefach prywatnych krawędzi, zobacz [VEPC niezależnych dostawców oprogramowania](#vEPC).

Wdrożenie prywatnego rozwiązania sieci mobilnej w strefach prywatnych krawędzi wymaga innych składników, takich jak punkty dostępu mobilnego, karty SIM i inne VNFs, takie jak routery. Dostęp do licencjonowanego lub nielicencjonowanego spektrum ma kluczowe znaczenie dla konfigurowania prywatnej sieci mobilnej. Może być potrzebna pomoc w zakresie planowania Federacji, fizycznego układu, instalacji i pomocy technicznej. Aby uzyskać listę partnerów, zobacz [Mobile Radio Partners](#mobile-radio).

Firma Microsoft zapewnia ekosystem partnera, który może pomóc we wszystkich aspektach tego procesu. Partnerzy mogą pomóc w planowaniu sieci, zakupieniu wymaganych urządzeń, konfigurowaniu sprzętu i zarządzaniu konfiguracją z platformy Azure. Zestaw sprawdzonych partnerów, którzy ściśle integrują się z firmą Microsoft, zapewniają, że Twoje rozwiązanie będzie niezawodne i łatwe w użyciu. Możesz skupić się na podstawowych scenariuszach i zależeć od firmy Microsoft i jej partnerów, aby pomóc w zasobie.

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN w strefach prywatnych krawędzi

![SD-WAN w strefach prywatnych krawędzi](./media/edge-zones-overview/sd-wan.png "SD-WAN w strefach prywatnych krawędzi")

SD-WAN umożliwia tworzenie sieci rozległych (WAN) klasy korporacyjnej z następującymi korzyściami:

- Zwiększona przepustowość
- Dostęp do chmury o wysokiej wydajności
- Wstawianie usługi
- Niezawodność
- Zarządzanie zasadami
- Rozległa widoczność sieci

SD-WAN zapewnia bezproblemową łączność z oddziałami firmy, która jest podłączona z nadmiarowych kontrolerów centralnych przy niższych kosztach użytkowania.
Usługa SD-WAN w strefach prywatnych krawędzi umożliwia przejście z modelu skoncentrowanego na CAPEX do modelu oprogramowanie jako usługa (SaaS) w celu ograniczenia budżetów IT. Możesz użyć własnego wyboru partnerów SD-WAN, Orchestrator lub Controller, aby włączyć nowe usługi i natychmiast propagować je w całej sieci.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, skontaktuj się z następującymi zespołami:

* [Zespół strefy brzegowej](https://aka.ms/EdgeZones)
* [Prywatny zespół strefy brzegowej, aby zostać partnerem](https://aka.ms/EdgeZonesPartner)
