---
title: Co to jest infrastruktura BareMetal na platformie Azure?
description: Zawiera omówienie infrastruktury BareMetal na platformie Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c0fd250a63ce93d3f8b62dfe76fe753c928801ce
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536892"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Co to jest infrastruktura BareMetal na platformie Azure?

Microsoft Azure oferuje infrastrukturę chmury z szeroką gamą zintegrowanych usług w chmurze, które spełniają Twoje potrzeby biznesowe. Jednak w niektórych przypadkach może być konieczne uruchomienie usług na serwerach bez warstwy wirtualizacji. Może być konieczne uzyskanie dostępu do konta głównego i kontrola nad systemem operacyjnym. Aby zaspokoić taką potrzebę, platforma Azure oferuje infrastrukturę BareMetal dla kilku aplikacji o wysokiej wartości i znaczeniu krytycznym.

Infrastruktura BareMetal składa się z dedykowanych wystąpień Systemu BareMetal (wystąpień obliczeniowych), magazynu o wysokiej wydajności i odpowiedniego dla aplikacji (NFS, ISCSI i Fiber Channel), a także zestawu wirtualnych sieci LAN (V LAN) specyficznych dla funkcji (V LAN) w izolowanym środowisku. Magazyn może być współużytowany w wystąpieniach systemu BareMetal w celu włączenia funkcji, takich jak klastry skalowania w poziomie lub tworzenia par o wysokiej dostępności za pomocą stonith.
 
To środowisko ma również specjalne sieci V LAN, do których można uzyskać dostęp, jeśli używasz maszyn wirtualnych w co najmniej jednej sieci wirtualnej platformy Azure w ramach subskrypcji platformy Azure. Całe środowisko jest reprezentowane jako grupa zasobów w subskrypcji platformy Azure.

Infrastruktura BareMetal jest oferowana w ponad 30 jednostkach SKU, od serwerów z 2 gniazdami do serwerów z 24 gniazdami i pamięci od 1,5 TB do 24 TB. Z pamięcią oktanową jest również dostępny duży zestaw jednostki SKU. Platforma Azure oferuje największy zakres wystąpień bez systemu metalowego w chmurze w hiperskali.

## <a name="why-baremetal-infrastructure"></a>Dlaczego warto wybrać infrastrukturę BareMetal?  

Niektóre centralne obciążenia w przedsiębiorstwie są oparte na technologiach, które nie są przeznaczone do uruchamiania w typowym środowisku chmury zwirtualizowanej. Wymagają one specjalnej architektury, certyfikowanego sprzętu lub bardzo dużych rozmiarów. Mimo że te technologie mają najbardziej zaawansowane funkcje ochrony danych i ciągłości działalności biznesowej, te funkcje nie są wbudowane dla chmury zwirtualizowanej. Są one bardziej wrażliwe na opóźnienia, hałaśliwe sąsiady i wymagają o wiele większej kontroli nad zarządzaniem zmianami i działaniami konserwacyjnymi.

Infrastruktura BareMetal jest budowaną, certyfikowaną i przetestowaną pod kątem wybranego zestawu takich aplikacji. Platforma Azure była pierwszą ofertą takich rozwiązań i od tego czasu jest liderem w zakresie największego portfolio i najbardziej zaawansowanych systemów.

Infrastruktura BareMetal oferuje następujące korzyści: 

- Wystąpienia dedykowane
- Certyfikowany sprzęt dla wyspecjalizowanych obciążeń
    - SAP (zobacz [Sap Note #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (zobacz [Oracle document ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Od zeru (bez wirtualizacji obliczeń)
- Małe opóźnienie między maszynami wirtualnych aplikacji hostowanych na platformie Azure a wystąpieniami aplikacji BareMetal (0,35 ms)
- Wszystkie dyski FLASH SSD i NVMe
    - Do 1 PB/dzierżawa 
    - IOPS do 1,2 miliona/dzierżawa 
    - Przepustowość sieci 40/100 GB
    - Dostępne za pośrednictwem systemu plików NFS, iSCSI i FC
- Nadmiarowe zasilanie, zasilacze, karty sieciowe, tors, porty, sieci WAN, magazyn i zarządzanie
- Hot spares for replacement on a failure (without the need for refiguring) (Zapasy zapasowe do wymiany w przypadku awarii (bez konieczności ponownej konfiguracji)
- Okna obsługi koordynowanej przez klienta
- Migawki, archiwizacja, dublowanie i klonowanie z zastosowaniem aplikacji


## <a name="baremetal-benefits"></a>Korzyści z firmy BareMetal  

Infrastruktura BareMetal jest przeznaczona dla obciążeń o krytycznym znaczeniu, które wymagają certyfikacji do uruchamiania aplikacji dla przedsiębiorstw. Wystąpienia programu BareMetal są przeznaczone tylko dla Ciebie i będziesz mieć pełny dostęp (dostęp główny) do systemu operacyjnego. Instalacja systemu operacyjnego i aplikacji jest zarządzana zgodnie z potrzebami. Ze względów bezpieczeństwa wystąpienia są aprowowane w ramach usługi Azure Virtual Network (VNet) bez łączności z Internetem. Tylko usługi działające na maszynach wirtualnych i innych usługach platformy Azure w tej samej sieci warstwy 2 mogą komunikować się z wystąpieniami systemu BareMetal.  

Infrastruktura BareMetal oferuje następujące korzyści: 

- Certyfikowany sprzęt dla wyspecjalizowanych obciążeń
- SAP (zapoznaj się z [informacjami o #1928533](https://launchpad.support.sap.com/#/notes/1928533)SAP )
- Oracle (zobacz [Oracle document ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Nienadzorowane wystąpienie programu BareMetal, własność pojedynczej dzierżawy
- Małe opóźnienie między maszynami wirtualnych aplikacji hostowanej na platformie Azure a wystąpieniami aplikacji BareMetal (0,35 ms)
- Obsługa wszystkich dysków FLASH SSD i NVMe
- Baza danych do 1 PB/dzierżawa 
- IOPS do 1,2 miliona/dzierżawa 
- Przepustowość sieci 50 GB 

## <a name="sku-availability-in-azure-regions"></a>Dostępność SKU w regionach świadczenia usługi Azure

Infrastruktura BareMetal oferuje wiele jednostki SKU certyfikowanych dla wyspecjalizowanych obciążeń. Użyj jednostki SKU specyficznych dla obciążenia, aby spełnić twoje potrzeby.

- Duże wystąpienia — od systemów z dwoma gniazdami do systemów z czterema gniazdami.  
- Bardzo duże wystąpienia — od systemów z czterema gniazdami do dwudziestu gniazd. 

Infrastruktura BareMetal dla wyspecjalizowanych obciążeń jest dostępna w następujących regionach świadczenia usługi Azure:
- West Europe
- Europa Północna
- Obsługa stref Zachodnio-środkowych (Niemcy)
- Obsługa stref *Wschodnie usa 2
- Obsługa stref *Wschodnie usa
- Obsługa *stref Zachodnie stany USA
- Obsługa *stref Zachodnie stany USA 2
- South Central US

>[!NOTE]
>**Obsługa stref** dotyczy stref dostępności w regionie, w którym wystąpienia systemu BareMetal można wdrażać w różnych strefach w celu zapewnienia wysokiej odporności i dostępności. Ta funkcja umożliwia obsługę skalowania aktywne-aktywne w wielu lokacjach.

## <a name="managing-baremetal-instances-in-azure"></a>Zarządzanie wystąpieniami programu BareMetal na platformie Azure 

W zależności od potrzeb topologie aplikacji infrastruktury BareMetal mogą być złożone. Można wdrożyć wiele wystąpień w co najmniej jednej lokalizacji, z magazynem udostępnionym lub dedykowanym oraz wyspecjalizowanymi połączeniami sieci LAN i WAN. Dlatego w przypadku infrastruktury BareMetal platforma Azure oferuje skonsultatywne przechwytywanie tych informacji przez csa/GBB w terenie w portalu aprowizowania. 

Do czasu aprowizowania infrastruktury BareMetal system operacyjny, sieci, woluminy magazynu, rozmieszczenia w strefach i regionach oraz połączenia sieci WAN między lokalizacjami są już wstępnie skonfigurowane. Możesz zarejestrować swoje licencje systemu operacyjnego (BYOL), skonfigurować system operacyjny i zainstalować warstwę aplikacji.

Wszystkie zasoby bareMetal oraz ich stan i atrybuty będą dostępne w Azure Portal. Możesz również obsługiwać wystąpienia i otwierać żądania obsługi oraz bilety pomocy technicznej z tego wystąpienia. 

## <a name="operational-model"></a>Model operacyjny
Infrastruktura BareMetal jest zgodna ze standardami ISO 27001, ISO 27017, SOC 1 i SOC 2. Używa również modelu bring-your-own-license (BYOL): systemu operacyjnego, wyspecjalizowanego obciążenia i aplikacji innych firm.  

Natychmiast po otrzymaniu dostępu do konta głównego i pełnej kontroli przejmujesz odpowiedzialność za:
- Projektowanie i wdrażanie rozwiązań do tworzenia kopii zapasowych i odzyskiwania, wysokiej dostępności i odzyskiwania po awarii.
- Licencjonowanie, zabezpieczenia i pomoc techniczna dla systemu operacyjnego i oprogramowania innych firm.

Firma Microsoft jest odpowiedzialna za:
- Zapewnianie sprzętu dla wyspecjalizowanych obciążeń. 
- Aprowizowanie systemu operacyjnego.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagram modelu obsługi infrastruktury BareMetal." border="false":::

## <a name="baremetal-instance-stamp"></a>Sygnatura wystąpienia bareMetal

Sygnatura wystąpienia systemu BareMetal łączy następujące składniki:

- **Przetwarzanie:** Serwery oparte na generacji procesorów Intel Xeon, które zapewniają niezbędne możliwości obliczeniowe i są certyfikowane dla wyspecjalizowanego obciążenia.

- **Sieć:** Ujednolicona sieć szkieletowa o dużej szybkości łączy składniki obliczeniowe, magazynu i sieci LAN.

- **Magazyn:** Infrastruktura dostępna za pośrednictwem ujednoliconej sieci szkieletowej.

W ramach infrastruktury wielodostępnej sygnatury BareMetal klienci są wdrażani w dzierżawach izolowanych. Podczas wdrażania dzierżawy należy nazwać subskrypcję platformy Azure w ramach rejestracji platformy Azure. Ta subskrypcja platformy Azure to ta, która jest rozliczana za wystąpienia systemu BareMetal.

>[!NOTE]
>Klient wdrażający wystąpienie systemu BareMetal jest izolowany w dzierżawie. Dzierżawa jest izolowana w warstwie sieci, magazynu i zasobów obliczeniowych od innych dzierżaw. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżaw nie widzą siebie nawzajem ani nie komunikują się ze sobą w swoich wystąpieniach baremetalowych.

## <a name="operating-system"></a>System operacyjny
Podczas aprowizowania wystąpienia programu BareMetal można wybrać system operacyjny, który chcesz zainstalować na maszynach. 

>[!NOTE]
>Pamiętaj, że baremetalowa infrastruktura jest modelem BYOL.

Dostępne wersje systemu operacyjnego Linux to:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage
Infrastruktura BareMetal zapewnia wysoce nadmiarowy magazyn NFS i magazyn Fiber Channel. Infrastruktura oferuje głęboką integrację dla obciążeń przedsiębiorstwa, takich jak SAP, SQL i inne. Zapewnia również spójne z aplikacją funkcje ochrony danych i zarządzania danymi. Narzędzia do samoobsługowego zarządzania oferują wydajne w przestrzeni możliwości tworzenia migawek, klonowania i replikacji szczegółowej wraz z pojedynczym okienkiem monitorowania. Infrastruktura ta zapewnia zerowe możliwości celu punktu punktu sprzedaży i celu punktu dostępu na potrzeby dostępności danych i ciągłości działania. 

Infrastruktura magazynu oferuje:
- Maksymalnie 4 x 100 GB łączy.
- Do 32 GB łączy światłowodowych.
- Wszystkie dyski FLASH SSD i NVMe.
- Bardzo małe opóźnienia i wysoka przepływność.
- Skaluje do 4 PB nieprzetworzonych magazynów. 
- Do 11 milionów IOPS.

Obsługiwane są następujące protokoły dostępu do danych: 
- iSCSI 
- NFS (wersja 3 lub 4) 
- Fiber Channel 
- NvMe przez FC  

## <a name="networking"></a>Sieć
Architektura usług sieciowych platformy Azure jest kluczowym składnikiem pomyślnego wdrażania wyspecjalizowanych obciążeń w wystąpieniach systemu BareMetal. Prawdopodobnie nie wszystkie systemy IT znajdują się już na platformie Azure. Platforma Azure oferuje technologię sieci, aby platforma Azure wyglądała jak wirtualne centrum danych we wdrożeniach oprogramowania lokalnego. Funkcje sieciowe platformy Azure wymagane dla wystąpień programu BareMetal obejmują:

- Sieci wirtualne platformy Azure połączone Azure ExpressRoute obwodem sieciowym, który łączy się z zasobami sieci lokalnej.
- Obwód usługi ExpressRoute, który łączy się lokalnie z platformą Azure, powinien mieć minimalną przepustowość co najmniej 1 Gb/s.
- Rozszerzona usługa Active Directory i system DNS na platformie Azure lub całkowicie uruchomiona na platformie Azure.

Usługa ExpressRoute umożliwia rozszerzenie sieci lokalnej na chmurę firmy Microsoft za pośrednictwem połączenia prywatnego z pomocą dostawcy połączenia. Usługa **ExpressRoute Local** umożliwia ekonomiczne przesyłanie danych między lokalizacją lokalną a regionem świadczenia usługi Azure. Aby rozszerzyć łączność między granicami geopolitycznymi, możesz włączyć **usługę ExpressRoute Premium.** 

Wystąpienia programu BareMetal są aprowowane w zakresie adresów IP serwera sieci wirtualnej platformy Azure.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Diagram architektury diagramu infrastruktury Azure BareMetal." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

Pokazana architektura jest podzielona na trzy sekcje:
- **Po lewej stronie:** Przedstawia lokalną infrastrukturę klienta, która uruchamia różne aplikacje, łącząc się za pośrednictwem partnera lub lokalnego routera brzegowego, takiego jak Equinix. Aby uzyskać więcej informacji, zobacz [Connectivity providers and locations: Azure ExpressRoute](../expressroute/expressroute-locations.md)(Dostawcy i lokalizacje łączności: Azure ExpressRoute ).
- **Środek:** Pokazuje [usługę ExpressRoute](../expressroute/expressroute-introduction.md) aprowizowana przy użyciu subskrypcji platformy Azure oferującą łączność z siecią brzegową platformy Azure.
- **Po prawej stronie:** Pokazuje usługę Azure IaaS, a w tym przypadku użycie maszyn wirtualnych do hostów aplikacji, które są aprowowane w sieci wirtualnej platformy Azure.
- **Na dole:** Pokazuje użycie bramy usługi ExpressRoute włączonej z [usługą ExpressRoute FastPath dla](../expressroute/about-fastpath.md) łączności BareMetal, oferując małe opóźnienia.   
   >[!TIP]
   >Aby to obsługiwać, brama usługi ExpressRoute powinna mieć domyślną ultrawydajność. Aby uzyskać więcej informacji, zobacz About ExpressRoute virtual network gateways (Informacje o bramach sieci [wirtualnej usługi ExpressRoute).](../expressroute/expressroute-about-virtual-network-gateways.md)

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest nauczenia się, jak identyfikować wystąpienia baremetalowe i wchodzić z nimi w interakcje za pośrednictwem Azure Portal.

> [!div class="nextstepaction"]
> [Zarządzanie wystąpieniami aplikacji BareMetal za pośrednictwem Azure Portal](connect-baremetal-infrastructure.md)
