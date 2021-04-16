---
title: 'Dostawcy połączeń i lokalizacje: Azure ExpressRoute | Microsoft Docs'
description: Ten artykuł zawiera szczegółowe omówienie lokalizacji, w których są oferowane usługi, i sposobu łączenia z regionami świadczenia usługi Azure. Informacje posortowano według dostawców połączeń.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 741fd7e4554ffe07de08e96233c440210eefa2fa
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498411"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>Partnerzy łączności i lokalizacje komunikacji równorzędnej usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Lokalizacje według dostawcy](expressroute-locations.md)
> * [Dostawcy według lokalizacji](expressroute-locations-providers.md)


Tabele w tym artykule zawierają informacje na temat geograficznego zasięgu i lokalizacji usługi ExpressRoute, dostawców łączności usługi ExpressRoute i integratorów systemu usługi ExpressRoute (SI).

> [!Note]
> Regiony platformy Azure i lokalizacje usługi ExpressRoute to dwie odrębne i różne koncepcje. Zrozumienie różnic między nimi ma kluczowe znaczenie dla eksplorowania łączności sieci hybrydowej platformy Azure. 
>
>

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure
Regiony platformy Azure to globalne centra danych, w których znajdują się zasoby obliczeniowe, sieciowe i magazynowe platformy Azure. Podczas tworzenia zasobu platformy Azure klient musi wybrać lokalizację zasobu. Lokalizacja zasobu określa, w którym centrum danych platformy Azure (lub strefie dostępności) zostanie utworzony zasób.

## <a name="expressroute-locations"></a>Lokalizacje usługi ExpressRoute
Lokalizacje usługi ExpressRoute (czasami określane jako lokalizacje komunikacji równorzędnej lub lokalizacje spotkania) są obiektami kolokowania, w których znajdują się urządzenia Microsoft Enterprise Edge (MSEE). Lokalizacje usługi ExpressRoute są punktem wejścia do sieci firmy Microsoft i są dystrybuowane globalnie, zapewniając klientom możliwość łączenia się z siecią firmy Microsoft na całym świecie. W tych lokalizacjach partnerzy i klienci ExpressRoute Direct ExpressRoute wystawiają połączenia krzyżowe z siecią firmy Microsoft. Ogólnie rzecz biorąc, lokalizacja usługi ExpressRoute nie musi odpowiadać regionowi świadczenia usługi Azure. Na przykład klient może utworzyć obwód usługi ExpressRoute z lokalizacją zasobu *Wschodnie* usa w lokalizacji komunikacji równorzędnej *Seattle.*

Będziesz mieć dostęp do usług Azure we wszystkich regionach regionu geopolitycznego, jeśli połączysz się przynajmniej z jedną lokalizacją usługi ExpressRoute w tym regionie.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Regiony świadczenia usługi Azure i lokalizacje usługi ExpressRoute w regionach geopolitycznych.
Poniższa tabela zawiera mapę regionów świadczenia usługi Azure dla lokalizacji usługi ExpressRoute w regionie geopolitycznym.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Australia — instytucje rządowe** |Australia Środkowa, Australia Środkowa 2 |Canberra, Canberra2 |
| **Europa** | Francja Środkowa, Francja Południowa, Niemcy Północne, Niemcy Zachodnio-środkowe, Europa Północna, Bliski Wschód, Azja Zachodnia, Holandia Północna, Holandia Zachodnia, Zachodnie Zjednoczone Królestwo, Południowe Zjednoczone Królestwo, Europa Zachodnia |Amsterdam, Amsterdam2, Portor, Dublin, Amsterdam2, Londyn, Londyn2, Londyn2, Londyn, Marsylia, Port, Newport (Walia), Dolnośląski, Paryż, Stavanger, Dojrzałość, Londyn |
| **Ameryka Północna** |Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Zachodnio-środkowe stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneaczna, Montreal, Nowy Jork, Phoenix, Miasto Quebec, Queretaro (Meksyk), Clipcy, San Antonio, Seattle, Dolina Krzemowa, Dolina Krzemowa2, Toronto, Toronto2, Seattle, Waszyngton, Waszyngton 2 |
| **Azja** | Azja Wschodnia, Azja Południowo-Wschodnia | Portier, Hongkong, Hongkong2, Dżaarta, Kuala Lumpur, Singapur, Singapur2, Londyn |
| **Indie** | Indie Zachodnie, Indie Środkowe, Indie Południowe |Chennai, Chennai2, Mumbaj, Mumbaj2 |
| **Japonia** | Japońska Zachodnia, Japonia Wschodnia |Osaka, Tokio, Tokio2 |
| **Oceania** | Australia Południowo-Wschodnia, Australia Wschodnia |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Korea Południowa** | Korea Środkowa, Korea Południowa |Busan, Seul|
| **Zjednoczone Emiraty Arabskie** | Środkowe Zjednoczone Emiraty Zjednoczone, Północne Zjednoczone Emiraty Zjednoczone | Dobowe, Zabłocie2 |
| **Republika Południowej Afryki** | Zachodnia Republika Południowej Afryki, Północna Republika Południowej Afryki |Kapsztad, Johannesburg |
| **Ameryka Południowa** | Brazylia Południowa |Dorota, Rio de Paulo, Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiony i granice geopolityczne chmur krajowych
W poniższej tabeli zamieszczono informacje o regionach i granicach geopolitycznych chmur krajowych.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Chmura administracji USA** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia, US DoD (region środkowy), US DoD (region wschodni)  |Atlanta,Chicago, Dallas, Nowy Jork, Phoenix, San Antonio, Seattle, Dolina Krzemowa, Waszyngton |
| **Chiny Wschodnie** |Chiny Wschodnie, Chiny Wschodnie 2 |Szanghaj, Szanghaj 2 |
| **Chiny Północne** |Chiny Północne, Chiny Północne 2 |Pekin, Pekin 2 |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Dostawcy połączenia usługi ExpressRoute

W poniższej tabeli przedstawiono lokalizacje według dostawcy usług. Jeśli chcesz wyświetlić dostępnych dostawców według lokalizacji, zobacz [Dostawcy usług według lokalizacji](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Globalna komercyjna platforma Azure

| **Dostawca usług** | **Microsoft Azure** | **Microsoft 365**  | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Obsługiwane | Obsługiwane | Chennai2, Mumbaj2 |
| **[Ais](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Obsługiwane | Obsługiwane | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, SIEM Hongkong, Sao Paulo, Seattle, Dolina Krzemowa, Singapur, Tokio, Waszyngton |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Dolina Krzemowa, Londyn, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **[AT TOKIO](https://www.attokyo.com/connectivity/azure.html)** | Obsługiwane | Obsługiwane | Osaka, Tokio2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Obsługiwane | Obsługiwane | Tokio |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Obsługiwane |Obsługiwane |Montreal, Toronto, miasto Quebec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Chicago, Dolina Krzemowa, Dolina Krzemowa, Hongkong SAR, Dolina Krzemowa, Dolina Krzemowa, Sydney, Tokio, Londyn2, Newport (Walia), Paryż, Sao Paulo, Singapur, Sydney, Tokio, Waszyngton |
| **[Bsnl](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html)** |Obsługiwane |Obsługiwane |Madras, Bombaj |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Obsługiwane |Obsługiwane |Miami |
| **CDC** | Obsługiwane | Obsługiwane | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |Amsterdam2, Chicago, Dublin, Hongkong, Las Vegas, Londyn2, Nowy Jork, Paryż, San Antonio, Dolina Krzemowa, Tokio, Toronto, Waszyngton, Waszyngton 2 |
| **[Chief Telecom](https://www.chief.com.tw/)** |Obsługiwane |Obsługiwane |Hongkong, Hongkong |
| **China Mobile International** |Obsługiwane |Obsługiwane | Hongkong, Hongkong2, Singapur |
| **China Telecom Global** |Obsługiwane |Obsługiwane |Hongkong, Hongkong2 |
| **China Unicom Global** |Obsługiwane |Obsługiwane | Hongkong, Singapur2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Obsługiwane |Obsługiwane |Tajpej |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Obsługiwane |Obsługiwane |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Minneaczna, Montreal, Toronto, Toronto, Waszyngton |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Seattle, Chicago, Dublin, Dolina Krzemowa, Hongkong, Londyn, Londyn2, Londyn, Newport, Nowy Jork, Osaka, Paryż, Dolina Krzemowa2, Singapur2, Tokio, Waszyngton, Dolina Krzemowa2 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Obsługiwane |Obsługiwane |Chicago, Denver, Los Angeles, Nowy Jork, Dolina Krzemowa, Dolina Krzemowa2, Waszyngton, Waszyngton 2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Obsługiwane |Obsługiwane |Amsterdam2, Gdynia 2, Gdynia, Marsylia, Mumbaj, Przemysł, Nowy Jork |
| **[Devoli](https://devoli.com/expressroute)** | Obsługiwane |Obsługiwane | Auckland, Melbourne, Sydney |
| **du datamena** |Obsługiwane |Obsługiwane | Port2 |
| **eir** |Obsługiwane |Obsługiwane |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Obsługiwane |Obsługiwane |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Atlanta, Seattle, Dolina Krzemowa, Portota, Canberra2, Chicago, Dallas, Dallas2, Dublin, Seattle, Hongkong SAR, Londyn, Londyn2, Los Angeles, Los Angeles2, Melbourne, Miami, Dallas, Nowy Jork, Osaka, Paryż, Rio de Seattle, Sao Paulo, Seattle, Seoul, Dolina Krzemowa, Singapur, Singapur, Sydney, Tokio, Toronto, Waszyngton, Waszyngton |
| **Etisalat UAE** |Obsługiwane |Obsługiwane |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Dublin, Amsterdam, Londyn |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Obsługiwane |Obsługiwane |Tajpej|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Obsługiwane |Obsługiwane |Mediolan|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Obsługiwane |Obsługiwane |Montreal|
| **[GÉANT](https://www.geant.org/Networks)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Dublin, Dublin, Marsylia |
| **[GlobalConnect]()** | Obsługiwane |Obsługiwane | Zdjęty, Stavanger | 
| **Gtt** |Obsługiwane |Obsługiwane |Londyn2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Obsługiwane| Obsługiwane | Madras, Bombaj |
| **[iAdvantage](https://www.scx.sunevision.com/)** | Obsługiwane | Obsługiwane | Hongkong 2 |
| **Intelsat** | Obsługiwane | Obsługiwane | Waszyngton 2 |
| **[InterCloud](https://www.intercloud.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dolina Krzemowa, Hongkong, Londyn, Nowy Jork, Paryż, Singapur, Waszyngton, Dolina Krzemowa |
| **[Internet 2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Dolina Krzemowa, Waszyngton |
| **[Internet Initiative Japan Inc. — IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Internet Solutions — Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg, Londyn |
| **[Interxion (Międzysieć)](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Paryż, Dublin, Londyn, Londyn, Marsylia, Paryż, Paryż |
| **[IRIDEOS](https://irideos.it/)** |Obsługiwane |Obsługiwane |Mediolan |
| **Iron Mountain** | Obsługiwane |Obsługiwane |Waszyngton |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Obsługiwane |Obsługiwane | Amsterdam, Londyn2, Dolina Krzemowa, Toronto, Waszyngton |
| **Sieć Zduniowa** |Obsługiwane |Obsługiwane |Marsylia, Paryż |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Obsługiwane |Obsługiwane |Londyn, Newport (Walia) |
| **[KINX](https://www.kinx.net/service/cloudhub/ms-expressroute/?lang=en)** |Obsługiwane |Obsługiwane |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Obsługiwane |Obsługiwane |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Obsługiwane | Obsługiwane | Amsterdam |
| **[Kt](https://cloud.kt.com/)** | Obsługiwane | Obsługiwane | Seul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Londyn, Newport (Walia), Sao Paulo, Seattle, Dolina Krzemowa, Singapur, Waszyngton |
| **LG CNS** |Obsługiwane |Obsługiwane |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[Megaport (Megaport)](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane |Obsługiwane |Amsterdam, Atlanta, Auckland, Chennai, Chicago, Dallas, Denver, Dolina Krzemowa, Chicago2, Dublin, Seattle, Hongkong, Hongkong2, Las Vegas, Londyn, Londyn2, Los Angeles, Melbourne, Miami, Minneaczna, Montreal, Nowy Jork, Osaka, Paryż, Perth, Quebec City, San Antonio, Seattle, Singapur2, Singapur2, Stavanger, Stavanger, Seattle, Sydney2, Tokio, Tokio2 Toronto, Stan, Waszyngton, Waszyngton 2, Dc2, Dc2 |
| **[Mtn](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Obsługiwane |Obsługiwane |Londyn |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Obsługiwane |Obsługiwane |Dallas, Los Angeles, Miami, Sao Paulo, Waszyngton |
| **[Dane nowej generacji](https://vantage-dc-cardiff.co.uk/)** |Obsługiwane |Obsługiwane |Newport (Walia) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Obsługiwane |Obsługiwane |Melbourne, Perth, Sydney, Sydney2 |
| **[Nos (Brak)](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Obsługiwane |Obsługiwane |Amsterdam2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Obsługiwane |Obsługiwane |Amsterdam, SIEM Hongkong, Londyn, Los Angeles, Osaka, Singapur, Sydney, Tokio, Waszyngton |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Obsługiwane |Obsługiwane |Tokio |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |Obsługiwane |Obsługiwane |Amsterdam2, Paryż |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Obsługiwane |Obsługiwane |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Obsługiwane |Obsługiwane |Marsylia |
| **[Optus](https://www.optus.com.au/enterprise/)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Dolina Krzemowa, Dolina Krzemowa, Dolina Krzemowa, Singapur, Sydney, Tokio, Waszyngton |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Obsługiwane | Obsługiwane | Port2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Las Vegas, Dolina Krzemowa, Waszyngton |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Obsługiwane |Obsługiwane |Chicago, Hongkong, Hongkong2, Londyn, Singapur2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | Obsługiwane | Obsługiwane | Auckland |
| **[Retelit](https://www.retelit.it/EN/Home.aspx)** | Obsługiwane | Obsługiwane | Mediolan | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Obsługiwane |Obsługiwane |Seul |
| **[Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Obsługiwane |Obsługiwane | Londyn2, Waszyngton |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Obsługiwane |Obsługiwane |Chennai, Mumbaj2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Obsługiwane |Obsługiwane |Hongkong2, Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Obsługiwane |Obsługiwane |Londyn2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Obsługiwane |Obsługiwane |Auckland, Sydney |
| **[Przebieg](https://business.sprint.com/solutions/cloud-networking/)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Obsługiwane | Obsługiwane | Podczas pracy z uniwersytetem w 1997 |
| **[Tata Communications](https://www.tatacommunications.com/solutions/network/cloud-ready-networks/)** |Obsługiwane |Obsługiwane |Amsterdam, Chennai, SIEM Hongkong, Londyn, Mumbaj, Sao Paulo, Dolina Krzemowa, Singapur, Waszyngton |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Obsługiwane |Obsługiwane |Amsterdam, Sao Paulo |
| **[Telehouse — KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Obsługiwane |Obsługiwane |Londyn, Londyn2, Singapur2 |
| **Telenor** |Obsługiwane |Obsługiwane |Amsterdam, Londyn, Dosyć |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Obsługiwane | Obsługiwane |Amsterdam, Chicago, Dallas, Dolina Krzemowa, Hongkong, Londyn, Londyn, Paryż, Dolina Krzemowa, Waszyngton |
| **[Telin](https://www.telin.net/product/data-connectivity/telin-cloud-exchange)** | Obsługiwane | Obsługiwane |Jakarta |
| **Telmex Uninet**| Obsługiwane | Obsługiwane | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Obsługiwane |Obsługiwane |Melbourne, Singapur, Sydney |
| **[Telus](https://www.telus.com)** |Obsługiwane |Obsługiwane |Montreal, Seattle, Miasto Quebec, Toronto, Seattle |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Obsługiwane | Obsługiwane | Kuala Lumpur |
| **[Tokai Communications](https://www.tokai-com.co.jp/en/)** | Obsługiwane | Obsługiwane | Osaka, Tokio 2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Obsługiwane |Obsługiwane |Dallas, Queretaro (Meksyk)|
| **T-Systems** |Obsługiwane |Obsługiwane |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[Uih](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Obsługiwane | Obsługiwane | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, SIEM Hongkong, Londyn, Mumbaj, Dolina Krzemowa, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Obsługiwane | Obsługiwane | Waszyngton 2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Obsługiwane | Obsługiwane | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Obsługiwane |Obsługiwane |Amsterdam2, Londyn, Singapur |
| **[Vodafone Idea](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Obsługiwane | Obsługiwane | Mumbaj2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Denver, Londyn, Los Angeles, Montreal, Nowy Jork, Paryż, Seattle, Toronto, Waszyngton, Waszyngton 2 |

 **+** oznacza, że wkrótce

### <a name="national-cloud-environment"></a>Krajowe środowisko chmury

Chmury krajowe platformy Azure są odizolowane od siebie i od globalnej komercyjnej platformy Azure. Usługa ExpressRoute dla jednej chmury platformy Azure nie może połączyć się z regionami świadczenia usługi Azure w innych. 

### <a name="us-government-cloud"></a>Chmura administracji USA

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Chicago, Phoenix, Dolina Krzemowa, Waszyngton |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |Nowy Jork, Phoenix, San Antonio, Waszyngton |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Atlanta, Chicago, Dallas, Nowy Jork, Seattle, Dolina Krzemowa, Waszyngton |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane | Obsługiwane | Chicago, Dallas, San Antonio, Seattle, Waszyngton |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Nowy Jork, Dolina Krzemowa, Waszyngton |

### <a name="china"></a>Chiny

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **China Telecom** |Obsługiwane |Nieobsługiwane |W 2018 r. Było to 2018, 2018, 2018, |
| **China Unicom** | Obsługiwane | Nieobsługiwane | Pekin 2, Szanghaj 2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Obsługiwane |Nieobsługiwane |Pekin 2, Szanghaj 2 |

Aby dowiedzieć się więcej, zobacz [ExpressRoute in China (ExpressRoute in China ).](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Niemcy

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Obsługiwane |Nieobsługiwane |Berlin |
| **Międzysieć** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane  | Nieobsługiwane | Berlin |
| **T-Systems** |Obsługiwane |Nieobsługiwane |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Łączność za pośrednictwem dostawców programu Exchange

Jeśli dostawca połączenia nie został wymieniony w poprzednich sekcjach, możesz i tak utworzyć połączenie.

* Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy jest on połączony z dowolną wymianą z tabeli powyżej. Użyj poniższych linków, aby zebrać więcej informacji o usługach oferowanych przez dostawców wymiany. Kilku dostawców połączenia jest już połączonych z wymianami sieci Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion (Międzysieć)](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Poproś dostawcę połączenia o rozszerzenie sieci o wybraną lokalizację komunikacji równorzędnej.
  * Dopilnuj, by dostawca połączenia rozszerzył łączność w sposób wysoko dostępny, aby nie wystąpiły żadne punkty awarii.
* Zamów obwód usługi ExpressRoute z wymianą jako dostawcą połączenia, aby połączyć się z firmą Microsoft.
  * Wykonaj kroki opisane w artykule [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Tworzenie obwodu usługi ExpressRoute), aby skonfigurować łączność.

## <a name="connectivity-through-satellite-operators"></a>Łączność za pośrednictwem operatorów satelitarnych
Jeśli jesteś zdalny i nie masz łączności światłowodowej lub chcesz poznać inne opcje łączności, możesz sprawdzić następujące operatory satelitarne. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Łączność za pośrednictwem dodatkowych dostawców usług

| **Dostawca połączenia** | **Exchange** | **Lokalizacje** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nowy Jork, Waszyngton |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[BeanfieldLiniąpołączanie](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londyn |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londyn, Singapur, Waszyngton |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/)** | Equinix | SRA Hongkong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | Londyn |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Dolina Krzemowa, Waszyngton |
| **[W tym celu](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, Nowy Jork, Waszyngton |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | Londyn, Singapur, Waszyngton |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londyn |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Miasto Quebec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Waszyngton |
| **[Gulf Bridge International](https://gbiinc.com/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londyn, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Wb.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |SRA Hongkong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Waszyngton |
| **[Mtn](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Kapsztad, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londyn |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[Oncore Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londyn, Paryż |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | Frankfurt |  
| **[Ponowne próby (RETN)](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **[Tata Teleservices](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect)** | Tata Communications | Madras, Bombaj |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, Nowy Jork, Dolina Krzemowa | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londyn | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nowy Jork |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Dolina Krzemowa, Waszyngton |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Dolina Krzemowa, Dolina Krzemowa 2|
| **Zain** |Equinix |Londyn|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madryt |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Łączność za pośrednictwem dostawców centrów danych

| **Dostawca** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Bank danych](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[Centra danych qts](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Strumieniowe centra danych]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Łączność za pośrednictwem krajowych sieci badawczych i edukacyjnych (NREN)

| **Dostawca**|
| --- |
| **AARNET**| 
| **DeIC za pośrednictwem GÉANT**|
| **GARR za pośrednictwem GÉANT**|
| **GÉANT**|
| **HEAnet za pośrednictwem GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS za pośrednictwem GÉANT**|
| **SINET**|
| **Surfnet za pośrednictwem GÉANT**|

* Jeśli Twój dostawca połączenia nie został tutaj wymieniony, być może połączenia są nawiązywane za pośrednictwem partnerów wymiany z usługą ExpressRoute wymienionych powyżej.

## <a name="expressroute-system-integrators"></a>Integratorzy systemu ExpressRoute
Włączanie prywatnej łączności do własnych potrzeb może być wyzwaniem w zależności od skali sieci. Możesz pracować z dowolnymi integratorami systemu wymienionymi w poniższej tabeli, którzy ułatwiają dołączanie do usługi ExpressRoute.

| **Integrator systemu** | **Kontynent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Azja, Europa, Ameryka Północna, Ameryka Południowa |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Azja
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Ameryka Północna |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Ameryka Północna |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Ameryka Północna |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australia |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Niemcy) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[Nowy podpis](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Azja |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Ameryka Północna |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Ameryka Północna |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Ameryka Południowa |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [ExpressRoute FAQ (ExpressRoute — często zadawane pytania).](expressroute-faqs.md)
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
