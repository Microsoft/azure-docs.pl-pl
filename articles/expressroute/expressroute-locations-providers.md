---
title: 'Lokalizacje i dostawcy połączeń: Azure ExpressRoute | Microsoft Docs'
description: Ten artykuł zawiera szczegółowe omówienie lokalizacji, w których są oferowane usługi, i sposobu łączenia z regionami świadczenia usługi Azure. Informacje są posortowane według lokalizacji.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: cherylmc
ms.openlocfilehash: 3b45c20e2d3e8829913c9e566516f85866ee80d4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390316"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Lokalizacje według dostawcy](expressroute-locations.md)
> * [Dostawcy według lokalizacji](expressroute-locations-providers.md)


Tabele w tym artykule zawierają informacje na temat zasięgu geograficznego i lokalizacji usługi ExpressRoute, dostawców łączności usługi ExpressRoute i integratorów systemów usługi ExpressRoute (SI).

> [!Note]
> Regiony platformy Azure i lokalizacje usługi ExpressRoute to dwie różne koncepcje, zrozumienie różnicy między nimi ma kluczowe znaczenie dla eksplorowania łączności sieci hybrydowej platformy Azure. 
>
>

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure
Regiony platformy Azure to globalne centra danych, w których znajdują się zasoby obliczeniowe, sieciowe i magazynowe platformy Azure. Podczas tworzenia zasobu platformy Azure klient musi wybrać lokalizację zasobu. Lokalizacja zasobu określa, które centrum danych platformy Azure (lub strefa dostępności) jest tworzony w zasobie.

## <a name="expressroute-locations"></a>Lokalizacje usługi ExpressRoute
Lokalizacje usługi ExpressRoute (czasami nazywane lokalizacjami komunikacji równorzędnej lub meet-me-locations) to obiekty kolokacji, w których znajdują się urządzenia microsoft enterprise edge (MSEE). Lokalizacje usługi ExpressRoute są punktem wejścia do sieci firmy Microsoft i są dystrybuowane globalnie, zapewniając klientom możliwość nawiązania połączenia z siecią firmy Microsoft na całym świecie. Te lokalizacje są miejscem, w którym partnerzy usługi ExpressRoute i klienci direct usługi ExpressRoute wystawiają połączenia krzyżowe z siecią firmy Microsoft. Ogólnie rzecz biorąc lokalizacja usługi ExpressRoute nie musi być zgodna z regionem platformy Azure. Na przykład klient może utworzyć obwód usługi ExpressRoute z lokalizacją zasobów *wschodnich stanów USA*w lokalizacji komunikacji równorzędnej w *Seattle.*

Będziesz mieć dostęp do usług Azure we wszystkich regionach regionu geopolitycznego, jeśli połączysz się przynajmniej z jedną lokalizacją usługi ExpressRoute w tym regionie. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Regiony platformy Azure do lokalizacji usługi ExpressRoute w regionie geopolitycznym
Poniższa tabela zawiera mapę regionów świadczenia usługi Azure dla lokalizacji usługi ExpressRoute w regionie geopolitycznym.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Australia — instytucje rządowe** | Australia Środkowa, Australia Środkowa 2 |Canberra, Canberra2 |
| **Europa** | Francja Środkowa, Francja Południowa, Niemcy Północne, Niemcy Środkowo-Zachodnie, Europa Północna, Norwegia Wschodnia, Norwegia Zachodnia, Szwajcaria Północ, Szwajcaria Zachód, Wielka Brytania Zachodnia, Wielka Brytania Południowa, Europa Zachodnia |Amsterdam, Amsterdam2, Kopenhaga, Dublin, Frankfurt, Genewa, Londyn, Londyn2, Marsylia, Mediolan, Monachium, Newport (Walia), Oslo, Paryż, Stavanger, Sztokholm, Zurych, Monachium |
| **Ameryka Północna** | Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Zachodnio-środkowe stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nowy Jork, San Antonio, Seattle, Dolina Krzemowa, Dolina Krzemowa2, Waszyngton, Waszyngton DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Azji** | Azja Wschodnia, Azja Południowo-Wschodnia | Bangkok, Hongkong, Hongkong2, Dżakarta, Kuala Lumpur, Singapur, Singapur2, Tajpej |
| **Indie** | Indie Zachodnie, Indie Środkowe, Indie Południowe |Chennai, Chennai2, Mumbaj, Mumbaj2 |
| **Japonia** | Japońska Zachodnia, Japonia Wschodnia |Osaka, Tokio, Tokio2 |
| **Oceania** | Australia Południowo-Wschodnia, Australia Wschodnia |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Korea Południowa** | Korea Środkowa, Korea Południowa |Busan, Seul|
| **Zjednoczone Emiraty Arabskie** | Zjednoczone Emiraty ZeA Central, Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Ameryki | Dubaj, Dubaj2 |
| **Republika Południowej Afryki** | Republika Południowej Afryki Zachód, Republika Południowej Afryki |Kapsztad, Johannesburg |
| **Ameryka Południowa** | Brazylia Południowa |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiony platformy Azure i granice geopolityczne chmur krajowych
W poniższej tabeli zamieszczono informacje o regionach i granicach geopolitycznych chmur krajowych.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Chmura administracji USA** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia, US DoD (region środkowy), US DoD (region wschodni)  |Atlanta, Chicago, Dallas, Nowy Jork, Phoenix, San Antonio, Seattle, Dolina Krzemowa, Waszyngton |
| **Chiny Wschodnie** |Chiny Wschodnie, Chiny Wschodnie 2 |Szanghaj, Szanghaj 2 |
| **Chiny Północne** |Chiny Północne, Chiny Północne 2 |Pekin, Pekin 2 |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Dostawcy połączenia usługi ExpressRoute

W poniższej tabeli przedstawiono lokalizacje połączeń i dostawców usług dla każdej lokalizacji. Jeśli chcesz wyświetlić dostawców usług i lokalizacje, w których świadczą usługi, zobacz [Lokalizacje według dostawcy usług](expressroute-locations.md).

* **Lokalne regiony platformy Azure** to te, do których może uzyskać dostęp [usługa ExpressRoute Local](expressroute-faqs.md) w każdej lokalizacji komunikacji równorzędnej. **n/a** oznacza, że usługa ExpressRoute Local nie jest dostępna w tej lokalizacji komunikacji równorzędnej.

* **Strefa** odnosi się do [cen](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Globalna komercyjna platforma Azure
| **Lokalizacja** | **Adres** | **Strefy** | **Lokalne regiony platformy Azure** | **Er Bezpośredni** | **Usługodawców** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa Zachodnia | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa Zachodnia | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | Nie dotyczy | Nie dotyczy | Equinix, Megaport |
| **Auckland** | [Grupa Vocus NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | Nie dotyczy | 10g | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Bangkok** | [Ais](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | Nie dotyczy | 10g | Ais |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Korea Południowa | Nie dotyczy | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia Środkowa | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia Środkowa 2| 10G, 100G | CDC |
| **Kapsztad** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Republika Południowej Afryki Zachód | 10g | BCX, Rozwiązania internetowe - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Indie Południowe | 10g | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Indie Południowe | Nie dotyczy | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Północno-środkowe stany USA | 10G, 100G | Aryaka Networks, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Kopenhaga** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | Nie dotyczy | 10g | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | Nie dotyczy | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Zachodnio-środkowe stany USA | Nie dotyczy | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Zjednoczone Emiraty Północne | Nie dotyczy | Etisalat Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone |
| **Dubaj2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Zjednoczone Emiraty Północne | Nie dotyczy | du datamena, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa Północna | 10G, 100G | Ogierek, eir, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Niemcy Zachodnio-Środkowe | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Genewa** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Szwajcaria Zachód | 10G, 100G | Equinix, Megaport |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Azja Wschodnia | Nie dotyczy | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hongkong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Nie dotyczy | 10g | |
| **Jakarta** | Telkom Indonezja | 4 | Nie dotyczy | 10g | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Republika Południowej Afryki Północ | 10g | British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara CELUJE](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | Nie dotyczy | Nie dotyczy | TIME dotCom |
| **Las Vegas** | [Przełącznik LV](https://www.switch.com/las-vegas) | 1 | Nie dotyczy | Nie dotyczy | CenturyLink Cloud Connect, Megaport |
| **Londyn** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Południowe Zjednoczone Królestwo | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Londyn2** | [Telehouse Północ Dwa](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Południowe Zjednoczone Królestwo | 10G, 100G | CenturyLink Cloud Connect, Colt, IX Reach, Equinix, Megaport, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | Nie dotyczy | Nie dotyczy | CoreSite, Equinix, Megaport, Sieci Neutrona, NTT, Transtelco, Zayo |
| **Marsylia** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Francja Południowa | Nie dotyczy | DE-CIX, GEANT, Interxion, Sieć Jaguar, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australia Południowo-Wschodnia | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | Nie dotyczy | 10g | C3ntro, Equinix, Megaport, Sieci Neutrona |
| **Mediolan** | [IRIDEOS ( IRIDEOS )](https://irideos.it/en/data-centers/) | 1 | Nie dotyczy | 10g | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | Nie dotyczy | 10G, 100G | Bell Kanada, Cologix, Megaport, Telus, Zayo |
| **Mumbaj** | Tata Communications | 2 | Indie Zachodnie | Nie dotyczy | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbaj2** | Airtel | 2 | Indie Zachodnie | Nie dotyczy | Airtel, Sify, Vodafone Idea |
| **Monachium** | [EdgeConneX (Wyjęcie krawędzi)](https://www.edgeconnex.com/locations/europe/) | 1 | Nie dotyczy | 10G, 100G | |
| **Nowy Jork** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | Nie dotyczy | Nie dotyczy | CenturyLink Cloud Connect, Colt, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Walia)** | [Dane nowej generacji](https://www.nextgenerationdata.co.uk) | 1 | Zachodnie Zjednoczone Królestwo | Nie dotyczy | British Telecom, Colt, Poziom 3 Communications, Dane nowej generacji |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japonia Zachodnia | 10G, 100G | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norwegia Wschodnia | 10G, 100G | Megaport, Telenor, Telia Carrier |
| **Paryż** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Francja Środkowa | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NastępnyDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | Nie dotyczy | 10g | Megaport, NextDC |
| **Miasto Quebec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kanada Wschodnia | Nie dotyczy | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Południowo-środkowe stany USA | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazylia Południowa | Nie dotyczy | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Zachodnie stany USA 2 | 10G, 100G | Aryaka Networks, Equinix, Poziom 3 Communications, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Korea Środkowa | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Dolina Krzemowa** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Zachodnie stany USA | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Dolina Krzemowa2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Zachodnie stany USA | 10G, 100G | Ogierek, Coresite | 
| **Singapur** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Azja Południowo-Wschodnia | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | [Przełącznik globalny Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Azja Południowo-Wschodnia | 10G, 100G | Chiny Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel |
| **Stavanger** | [Zielona Góra DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norwegia Zachodnia | 10G, 100G | |
| **Sztokholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | Nie dotyczy | 10g | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australia Wschodnia | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Okręg wyborczy Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australia Wschodnia | 10G, 100G | Megaport, NextDC |
| **Tajpej** | Chief Telecom | 2 | Nie dotyczy | 10g | Główny telekomunikacja, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japonia Wschodnia | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokio2** | [W TOKIO](https://www.attokyo.com/) | 2 | Japonia Wschodnia | 10G, 100G | W TOKIO |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Kanada Środkowa | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Cologix | 10G, 100G | |
| **Waszyngton** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Wschodnie stany USA, wschodnie stany USA 2 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Waszyngton 2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Wschodnie stany USA, wschodnie stany USA 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **Zurych** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Szwajcaria Północna | 10G, 100G | Intercloud, Interxion, Megaport, Swisscom |

 **+** oznacza, że wkrótce

### <a name="national-cloud-environments"></a>Krajowe środowiska chmury

Chmury krajowe platformy Azure są odizolowane od siebie i od globalnej komercyjnej platformy Azure. Usługa ExpressRoute dla jednej chmury platformy Azure nie może połączyć się z regionami platformy Azure w innych regionach.

### <a name="us-government-cloud"></a>Chmura administracji USA
| **Lokalizacja** | **Adres** | **Lokalne regiony platformy Azure**| **Er Bezpośredni** | **Usługodawców** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | Nie dotyczy | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Nie dotyczy | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | Nie dotyczy | 10G, 100G | Equinix, Megaport, Verizon |
| **Nowy Jork** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | Nie dotyczy | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov Arizona | Nie dotyczy | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov Teksas | Nie dotyczy | CenturyLink Cloud Connect, Megaport |
| **Dolina Krzemowa** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | Nie dotyczy | 10G, 100G | AT&T, Equinix, Poziom 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Nie dotyczy | Nie dotyczy | Equinix, Megaport |
| **Waszyngton** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | STANY ZJEDNOCZONE DoD East, Stany Zjednoczone Gov Virginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Chiny
| **Lokalizacja** | **Usługodawców** |
| --- | --- |
| **Pekin** |China Telecom |
| **Pekin 2** | China Telecom, China Unicom, GDS |
| **Szanghaj** |China Telecom |
| **Szanghaj 2** | Chiny Telecom, GDS |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach)

### <a name="germany"></a>Niemcy
| **Lokalizacja** | **Usługodawców** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Łączność za pośrednictwem dostawców usług Exchange
Jeśli dostawca połączenia nie został wymieniony w poprzednich sekcjach, możesz i tak utworzyć połączenie.

* Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy jest on połączony z dowolną wymianą z tabeli powyżej. Użyj poniższych linków, aby zebrać więcej informacji o usługach oferowanych przez dostawców wymiany. Kilku dostawców połączenia jest już połączonych z wymianami sieci Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/)
  * [NextDC (W yszłym)](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Poproś dostawcę połączenia o rozszerzenie sieci o wybraną lokalizację komunikacji równorzędnej.
  * Dopilnuj, by dostawca połączenia rozszerzył łączność w sposób wysoko dostępny, aby nie wystąpiły żadne punkty awarii.
* Zamów obwód usługi ExpressRoute z wymianą jako dostawcą połączenia, aby połączyć się z firmą Microsoft.
  * Wykonaj kroki opisane w artykule [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Tworzenie obwodu usługi ExpressRoute), aby skonfigurować łączność.

## <a name="connectivity-through-satellite-operators"></a>Łączność za pośrednictwem operatorów satelitarnych
Jeśli jesteś zdalny i nie masz łączności światłowodowej lub chcesz zapoznać się z innymi opcjami łączności, możesz sprawdzić następujących operatorów satelitarnych. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Łączność za pośrednictwem dodatkowych usługodawców
| **Lokalizacja** | **Exchange** | **Dostawcy połączeń** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Poziom 3 Komunikacja | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, TelekomDeutsch land GmbH, Telia |
| **Atlanta** | Equinix| Zamek Koronny
| **Kapsztad** | Teraco | MTN |
| **Chicago** | Equinix| Zamek Koronny, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **SRA Hongkong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londyn** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Zamek Koronny, Spectrum Enterprise, Transtelco |
| **Madryt** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **Nowy Jork** |Equinix, Megaport | Altice Business, Zamek Koronny, Spectrum Enterprise, Webair |
| **Paryż** | Equinix | Proximus |
| **Miasto Quebec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Dolina Krzemowa** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Waszyngton** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telekomunikacja Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Integratorzy systemu ExpressRoute
Włączanie prywatnej łączności do własnych potrzeb może być wyzwaniem w zależności od skali sieci. Możesz pracować z dowolnymi integratorami systemu wymienionymi w poniższej tabeli, którzy ułatwiają dołączanie do usługi ExpressRoute.

| **Kontynent** | **Integratorzy systemów** |
| --- | --- |
| **Azji** |Avanade Inc., OneAs1a |
| **Australia** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Ameryka Północna** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Ameryka Południowa** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
