---
title: Informacje o strefach usługi Azure Edge — wersja zapoznawcza
description: Dowiedz się więcej o ofertach edge computing firmy Microsoft.
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 04/02/2020
ms.author: ganesr
ms.openlocfilehash: aaa849633591bfd34a9fca026c820ec2f9137844
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410834"
---
# <a name="about-azure-edge-zones---preview"></a>Informacje o strefach usługi Azure Edge — wersja zapoznawcza

Usługa Azure Edge Zones to rodzina ofert platformy Microsoft Azure, która umożliwia przetwarzanie danych blisko użytkownika. Maszyny wirtualne, kontenery i inne wybrane usługi platformy Azure można wdrożyć w strefach brzegowych, aby sprostać niskim opóźnieniom i wysokim wymaganiom przepływności aplikacji.

Typowe scenariusze przypadków użycia dla stref brzegowych obejmują:

- Sterowanie i sterowanie w czasie rzeczywistym w robotyce
- Analiza w czasie rzeczywistym i wnioskowanie ze sztuczną inteligencją i uczeniem maszynowym
- Wizja maszyny
- Zdalne renderowanie dla rzeczywistości mieszanej i scenariuszy VDI
- Wciągająca gra dla wielu graczy
- Przesyłanie strumieniowe multimediów i dostarczanie zawartości
- Nadzór i bezpieczeństwo

Strefy usługi Azure Edge są dostępne w trzech oddzielnych ofertach:

- Strefy usługi Azure Edge
- Strefy usługi Azure Edge z usługą Carrier
- Prywatne strefy brzegowe platformy Azure

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Strefy usługi Azure Edge

![Strefy krawędzi](./media/edge-zones-overview/edge-zones.png "Strefy brzegowe")

Strefy usługi Azure Edge to rozszerzenia platformy Azure o niewielkich rozmiarach umieszczone w centrach populacji, które znajdują się daleko od regionów platformy Azure. Strefy usługi Azure Edge obsługują maszyny wirtualne, kontenery i wybrany zestaw usług platformy Azure, które umożliwiają uruchamianie aplikacji wrażliwych na opóźnienia i dużej przepustowości w pobliżu użytkowników końcowych. Strefy usługi Azure Edge są częścią globalnej sieci firmy Microsoft i oferują bezpieczną, niezawodną łączność o wysokiej przepustowości między aplikacjami działającymi w strefie brzegowej w pobliżu użytkownika a pełnym zestawem usług platformy Azure uruchomionych w regionach platformy Azure. Strefy usługi Azure Edge są własnością firmy Microsoft i są przez nią zarządzane i umożliwiają korzystanie z tego samego zestawu narzędzi platformy Azure i portalu do zarządzania i wdrażania usług w strefach brzegowych.

Typowe przypadki użycia obejmują:

- Gry i strumieniowanie gier
- Przesyłanie strumieniowe multimediów i dostarczanie zawartości
- Analiza w czasie rzeczywistym i wnioskowanie przy użyciu sztucznej inteligencji i uczenia maszynowego
- Renderowanie dla rzeczywistości mieszanej

Strefa usługi Azure Edge będzie dostępna w następujących stacjach metra:

- Nowy Jork, NOWY JORK
- Los Angeles, Kalifornia
- Miami, FL

Aby uzyskać więcej [informacji, skontaktuj się z zespołem Edge Zones.](https://aka.ms/EdgeZones)

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Strefy usługi Azure Edge z usługą Carrier

![Strefy krawędzi z nośnikiem](./media/edge-zones-overview/edge-carrier.png "Strefy krawędzi z nośnikiem")

Strefy usługi Azure Edge z operatorem to rozszerzenia platformy Azure o niewielkich rozmiarach umieszczone w centrach danych operatorów telefonii komórkowej w centrach populacji. Strefa usługi Azure Edge z infrastrukturą operatora jest umieszczana o jeden przeskok z sieci 5G operatora komórkowego, oferując opóźnienie poniżej 10 milisekund do aplikacji z urządzeń przenośnych. Usługa Azure Edge Zones z operatorem jest wdrażana w centrach danych operatorów komórkowych i jest połączona z globalną siecią firmy Microsoft. Oferują one bezpieczną, niezawodną łączność o wysokiej przepustowości między aplikacjami działającymi blisko użytkownika a pełnym zestawem usług platformy Azure uruchomionych w regionach platformy Azure. Deweloperzy mogą używać tego samego zestawu znanych narzędzi do tworzenia i wdrażania usług w strefach brzegowych.

Typowe przypadki użycia obejmują:

- Gry i strumieniowanie gier
- Przesyłanie strumieniowe multimediów i dostarczanie zawartości
- Analiza w czasie rzeczywistym i wnioskowanie przy użyciu sztucznej inteligencji i uczenia maszynowego
- Renderowanie dla rzeczywistości mieszanej
- Połączone samochody
- Telemedycyna

Strefy brzegowe będą oferowane we współpracy z następującymi operatorami:

- AT&T (Atlanta, Dallas i Los Angeles)

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Prywatne strefy brzegowe platformy Azure

![Prywatne strefy brzegowe](./media/edge-zones-overview/private-edge.png "Prywatne strefy brzegowe")

Strefy usługi Azure Private Edge to rozszerzenia platformy Azure o niewielkich rozmiarach umieszczone lokalnie. Strefy usługi Azure Private Edge są oparte na platformie [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) i umożliwiają dostęp do usług obliczeniowych i magazynowania o małych opóźnieniach wdrożonych lokalnie. Strefy prywatnej edge umożliwiają również wdrażanie zwirtualizowanych funkcji sieciowych (VNF), takich jak rdzenie pakietów mobilnych, routery, zapory i urządzenia SD-WAN oraz aplikacje z usługi ISVS jako [aplikacje zarządzane przez platformę Azure](https://azure.microsoft.com/services/managed-applications/) obok siebie wraz z maszynami wirtualnymi i kontenerami lokalnymi. Usługi Azure Private Edge Zones są wyposażone w natywne rozwiązanie aranżacji dla chmury, które umożliwia zarządzanie cyklem życia zwirtualizowanych funkcji sieciowych (VNF) i aplikacji z witryny Azure portal.

Strefy usługi Azure Private Edge umożliwia tworzenie i wdrażanie aplikacji lokalnie przy użyciu tych samych znanych narzędzi używanych do tworzenia i wdrażania aplikacji na platformie Azure. Można również uruchamiać prywatne sieci komórkowe (prywatne LTE, prywatne 5G), funkcje zabezpieczeń, takie jak zapory, i rozszerzać sieci lokalne na wiele gałęzi i platformy Azure przy użyciu urządzeń SD-WAN na tych samych urządzeniach prywatnej strefy brzegowej i zarządzać nimi z platformy Azure.

Typowe przypadki użycia obejmują:

- Sterowanie i sterowanie w czasie rzeczywistym w robotyce
- Analiza w czasie rzeczywistym i wnioskowanie ze sztuczną inteligencją i uczeniem maszynowym
- Wizja maszyny
- Zdalne renderowanie dla rzeczywistości mieszanej i scenariuszy VDI
- Nadzór i bezpieczeństwo

Mamy bogaty ekosystem dostawców VNF, dostawców oprogramowania i partnerów MSP, aby umożliwić kompleksowe rozwiązania przy użyciu prywatnych stref brzegowych. Aby uzyskać więcej [informacji, skontaktuj się z zespołem Private Edge Zones.](https://aka.ms/EdgeZonesPartner)

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>Prywatne strefy brzegowe - Partnerzy

![Partnerzy prywatnej strefy brzegowej](./media/edge-zones-overview/partners.png "Partnerzy prywatnej strefy brzegowej")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Zwirtualizowane funkcje sieciowe (VNF)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Zwirtualizowany Ewoluowany Rdzeń Pakietów (vEPC) dla sieci komórkowych

- [Potwierdzone sieci](https://www.affirmednetworks.com/)
- [Oprogramowanie Druid](https://www.druidsoftware.com/)
- [Expeto ( Expeto )](https://www.expeto.io/)
- [Mavenir (Mavenir)](https://mavenir.com/)
- [Metaprzełącznik](https://www.metaswitch.com/)
- [Nokia Digital Automation Cloud](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Mobilni partnerzy radiowi

- [Commscope Ruckus](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>Dostawcy SD-WAN

- [Fundacja NetFoundry](https://netfoundry.io/)
- [NuageNetworks od Nokia](https://www.nuagenetworks.net/)
- [VMware SD-WAN firmy Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>Dostawcy routerów

- [Arista](https://www.arista.com/)

[Skontaktuj się z zespołem Private Edge Zones,](https://aka.ms/EdgeZonesPartner) aby uzyskać więcej informacji na temat zostania partnerem.

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Dostawcy zapory

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>Dostawcy rozwiązań zarządzanych - operatorzy komórkowi i globalni integratorzy systemów

| Globalne si i operatorów | Operatorzy komórkowi |
| --- | --- |
| Amdocs ( amdocs )                       | Etisalat ( Etisalat )             |
| Wieża Amerykańska               | NTT Communications   |
| Link stulecia                 | Proximus             |
| Expeto ( Expeto )                       | Rogers               |
| Łączność bezprzewodowa federacyjne           | SK Telekomunikacja           |
| Infosys                      | Telefonica           |
| Tech Mahindra                | Telstra              |
|        *                     | Vodafone             |

[Skontaktuj się z zespołem Private Edge Zones,](https://aka.ms/EdgeZonesPartner) aby uzyskać więcej informacji na temat zostania partnerem.

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>Prywatne strefy brzegowe - Rozwiązania

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Prywatna sieć komórkowa w prywatnych strefach brzegowych

![Prywatna sieć komórkowa w prywatnych strefach brzegowych](./media/edge-zones-overview/mobile-networks.png "Prywatna sieć komórkowa w prywatnych strefach brzegowych")

Teraz można wdrożyć prywatną sieć komórkową w prywatnych strefach brzegowych. Prywatne sieci komórkowe umożliwiają bardzo małe opóźnienia, dużą pojemność oraz niezawodną i bezpieczną sieć bezprzewodową, która jest wymagana w krytycznych aplikacjach biznesowych. Prywatne sieci komórkowe mogą włączać scenariusze, takie jak dowodzenie i kontrola zautomatyzowanych pojazdów kierowanych (AGV) w magazynie, komunikacja w czasie rzeczywistym między robotami w inteligentnej fabryce i rozszerzone aplikacje oparte na rzeczywistości użytkowej oraz aplikacje brzegowe rzeczywistości wirtualnej.

Zwirtualizowane ewoluowały pakiet core (vEPC) funkcja sieci tworzy mózgi prywatnej sieci komórkowej. Teraz można wdrożyć vEPC w prywatnych strefach brzegowych. Aby uzyskać listę partnerów vEPC, którzy są dostępni w prywatnych strefach brzegowych, zobacz [isv vEPC](#vEPC).

Wdrożenie rozwiązania prywatnej sieci komórkowej w strefach prywatnej krawędzi wymaga innych składników, takich jak mobilne punkty dostępu, karty SIM i inne sieci wirtualne, takie jak routery. Dostęp do licencjonowanego lub nielicencjonowanego widma ma kluczowe znaczenie dla utworzenia prywatnej sieci komórkowej. Ponadto może być potrzebna pomoc dotycząca planowania rf, układu fizycznego, instalacji i obsługi technicznej. Aby uzyskać listę partnerów, zobacz [Mobilni partnerzy radiowi](#mobile-radio).

Firma Microsoft udostępnia ekosystem partnerów, który może pomóc we wszystkich aspektach tego procesu — od planowania sieci, zakupu wymaganych urządzeń, konfigurowania sprzętu, po zarządzanie konfiguracją z platformy Azure. Dzięki zestawowi zweryfikowanych partnerów, którzy są ściśle zintegrowani z firmą Microsoft, możesz mieć pewność, że rozwiązanie będzie niezawodne i łatwe w użyciu. Możesz skupić się na podstawowych scenariuszach, jednocześnie opierając się na firmie Microsoft i jej partnerach, aby pomóc w pozostałych.

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN w prywatnych strefach brzegowych

![SD-WAN w prywatnych strefach brzegowych](./media/edge-zones-overview/sd-wan.png "SD-WAN w prywatnych strefach brzegowych")
 
Technologia SD-WAN jako technologia umożliwia tworzenie sieci rozległych (WAN) klasy korporacyjnej ze zwiększoną przepustowością, wysokowydajnym dostępem do chmury, wstawianiem usług, niezawodnością, zarządzaniem zasadami i ekstensywną widocznością sieci. SD-WAN zapewnia bezproblemową łączność w oddziale firmy zaaranżowanej z nadmiarowych kontrolerów centralnych, przy niższych kosztach posiadania.
SD-WAN w prywatnych strefach brzegowych pozwala odejść od modelu zorientowanego na capex, do modelu Software-as-a-service (SaaS), aby zmniejszyć budżety IT. Możesz użyć wybranego koordynatora lub kontrolera partnerów SD-WAN, aby włączyć nowe usługi i natychmiast propagować je w całej sieci.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, skontaktuj się z następującymi zespołami:

* [Zespół Edge Zones](https://aka.ms/EdgeZones)
* [Zespół Private Edge Zones - zostać partnerem](https://aka.ms/EdgeZonesPartner)
