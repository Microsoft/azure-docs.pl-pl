---
title: Omówienie infrastruktury BareMetal na platformie Azure
description: Zawiera omówienie infrastruktury BareMetal na platformie Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/08/2021
ms.openlocfilehash: 7a4998a096a5c5d9e793c34d5046dce59262a2ae
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257572"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Co to jest infrastruktura BareMetal na platformie Azure?

Microsoft Azure oferuje infrastrukturę chmurową obejmującą szeroką gamę zintegrowanych usług w chmurze, które spełniają wymagania biznesowe. W niektórych przypadkach może być konieczne uruchomienie usług na serwerach bez systemu operacyjnego, które nie korzystają z warstwy wirtualizacji. Może być potrzebny dostęp do katalogu głównego i kontrola nad systemem operacyjnym (OS). Aby spełnić takie wymagania, platforma Azure oferuje infrastrukturę BareMetal dla kilku aplikacji o wysokiej wartości i o znaczeniu strategicznym.

Infrastruktura BareMetal składa się z dedykowanych wystąpień BareMetal (wystąpień obliczeniowych), magazynu o wysokiej wydajności i odpowiednich aplikacji (NFS, ISCSI i Fiber Channel) oraz zestawu wirtualnych sieci LAN specyficznych dla funkcji (VLAN) w środowisku izolowanym. Magazyn może być współużytkowany przez wystąpienia BareMetal, aby umożliwić korzystanie z takich funkcji, jak w przypadku klastrów skalowalnych w poziomie lub tworzenia par wysokiej dostępności z STONITH.
 
To środowisko ma również specjalne sieci VLAN, do których można uzyskać dostęp, jeśli są uruchomione maszyny wirtualne w co najmniej jednej sieci wirtualnej platformy Azure (sieci wirtualnych) w ramach subskrypcji platformy Azure. Całe środowisko jest reprezentowane jako Grupa zasobów w ramach subskrypcji platformy Azure.

Infrastruktura BareMetal jest oferowana w ponad 30 jednostkach SKU z 2-gniazdowych do 24-gniazdowych serwerów i pamięci z zakresu od 1,5 TB do 24 usług TBs. Duży zestaw jednostek SKU jest również dostępny z pamięcią Octane. Platforma Azure oferuje największy zakres wystąpień systemu operacyjnego w chmurze w skali.

## <a name="why-baremetal-infrastructure"></a>Dlaczego warto BareMetal infrastrukturę?  

Niektóre centralne obciążenia w przedsiębiorstwie składają się z technologii, które nie są przeznaczone do uruchamiania w typowym ustawieniu chmury zwirtualizowanej. Wymagają one specjalnej architektury, certyfikowanego sprzętu lub bardzo dużych rozmiarów. Chociaż te technologie mają najbardziej zaawansowane funkcje ochrony danych i zapewniania ciągłości działalności biznesowej, te funkcje nie są kompilowane dla chmury zwirtualizowanej. Są one bardziej poufne dla opóźnień, sąsiadów z zakłóceniami i wymagają większej kontroli nad działaniami w zakresie zarządzania zmianami i konserwacji.

Infrastruktura BareMetal jest skompilowana, certyfikowana i przetestowana pod kątem wybranych aplikacji. Platforma Azure była pierwszą ofertą, aby zaoferować takie rozwiązania, a ponieważ jest to klient z największą ilością portfolio i większością zaawansowanych systemów.

Infrastruktura BareMetal oferuje następujące korzyści: 

- Wystąpienia dedykowane
- Certyfikowany sprzęt dla wyspecjalizowanych obciążeń
    - SAP (zapoznaj się z [uwagami sap #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (zapoznaj się z [identyfikatorem dokumentu oracle #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Bez systemu operacyjnego (bez wirtualizacji obliczeniowej)
- Małe opóźnienia między maszynami wirtualnymi aplikacji hostowanymi na platformie Azure a wystąpieniami BareMetal (0,35 MS)
- Wszystkie dyski SSD Flash i interfejsu NVMe
    - Do 1 PB/dzierżawy 
    - Liczba operacji we/wy do 1,2 mln/dzierżawców 
    - 40/100 GB przepustowości sieci
    - Dostępne za pośrednictwem systemu plików NFS, ISCSI i FC
- Nadmiarowe zasilanie, zasilacze, karty sieciowe, ne, porty, sieci WAN, magazyn i zarządzanie
- Gorąca zapasy do zastąpienia w przypadku awarii (bez konieczności ponownego konfigurowania)
- Okna obsługi skoordynowane przez klienta
- Migawki, archiwum, dublowanie i klonowanie obsługujące aplikacje


## <a name="sku-availability-in-azure-regions"></a>Dostępność jednostki SKU w regionach platformy Azure

Infrastruktura BareMetal oferuje wiele jednostek SKU certyfikowanych dla wyspecjalizowanych obciążeń. Użyj jednostek SKU specyficznych dla obciążenia, aby zaspokoić Twoje potrzeby.

- Duże wystąpienia — od dwóch gniazd do systemów z czterema gniazdami.  
- Bardzo duże wystąpienia — od czterech gniazd do dwudziestu gniazd. 

Infrastruktura BareMetal dla wyspecjalizowanych obciążeń jest dostępna w następujących regionach świadczenia usługi Azure:
- West Europe
- Europa Północna
- Obsługa Niemcy Środkowo-Zachodnie * stref
- Wschodnie stany USA 2 * Obsługa stref
- Obsługa stref Wschodnie stany USA *
- Obsługa stref zachodnich USA *
- Obsługa stref zachodnich USA 2 *
- South Central US

>[!NOTE]
>**Obsługa stref** dotyczy stref dostępności w regionie, w którym można wdrażać wystąpienia BareMetal w różnych strefach w celu zapewnienia wysokiej odporności i dostępności. Ta funkcja umożliwia obsługę skalowania w wielu lokacjach aktywne-aktywne.

## <a name="managing-baremetal-instances-in-azure"></a>Zarządzanie wystąpieniami BareMetal na platformie Azure 

W zależności od potrzeb topologii aplikacji infrastruktury BareMetal mogą być złożone. Można wdrożyć wiele wystąpień w co najmniej jednej lokalizacji z magazynem udostępnionym lub dedykowanym oraz wyspecjalizowanymi połączeniami sieci LAN i WAN. W związku z tym w przypadku infrastruktury BareMetal platforma Azure oferuje przechwycenie konsultacji przez CSA/GBB w polu w portalu aprowizacji. 

W czasie, gdy infrastruktura BareMetal jest obsługiwana, system operacyjny, sieci, woluminy magazynowe, lokalizacje w strefach i regionach oraz połączenia WAN między lokalizacjami są już wstępnie skonfigurowane. Ustawiono opcję rejestrowania licencji systemu operacyjnego (BYOL), skonfigurowania systemu operacyjnego i zainstalowania warstwy aplikacji.

W Azure Portal będzie można zobaczyć wszystkie zasoby infrastruktury BareMetal oraz ich stan i atrybuty. Możesz również obsługiwać wystąpienia i otwierać żądania obsługi oraz obsługiwać bilety. 

## <a name="operational-model"></a>Model operacyjny
Infrastruktura BareMetal jest zgodna z normami ISO 27001, ISO 27017, SOC 1 i SOC 2. Używa ona również modelu "BYOL" (przyjęcie): systemu operacyjnego, wyspecjalizowanego obciążenia i aplikacji innych firm.  

Po otrzymaniu dostępu głównego i pełnej kontroli przyjmujesz odpowiedzialność za:
- Projektowanie i wdrażanie rozwiązań do tworzenia kopii zapasowych i odzyskiwania, wysokiej dostępności i odzyskiwania po awarii.
- Licencjonowanie, zabezpieczenia i pomoc techniczna dla systemu operacyjnego i oprogramowania innych firm.

Firma Microsoft jest odpowiedzialna za:
- Udostępnianie sprzętu dla wyspecjalizowanych obciążeń. 
- Inicjowanie obsługi systemu operacyjnego.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagram modelu obsługi infrastruktury BareMetal." border="false":::

## <a name="baremetal-instance-stamp"></a>Sygnatura wystąpienia BareMetal

Sama sygnatura wystąpienia BareMetal łączy następujące składniki:

- **Obliczanie:** Serwery oparte na generacji procesorów Intel Xeon, które zapewniają niezbędną możliwość obliczeniową i są certyfikowane dla wyspecjalizowanego obciążenia.

- **Sieć:** Ujednolicona sieć szkieletowa o dużej szybkości, która łączy się ze składnikami, magazynu i sieci LAN.

- **Magazyn:** Infrastruktura dostępna za pomocą ujednoliconej sieci szkieletowej.

W ramach infrastruktury wielu dzierżawców sygnatury BareMetal klienci są wdrażani w izolowanych dzierżawcach. Podczas wdrażania dzierżawy należy nazwać subskrypcję platformy Azure w ramach rejestracji na platformie Azure. Ta subskrypcja platformy Azure jest rozliczana za wystąpienia usługi BareMetal.

>[!NOTE]
>Klient wdraża wystąpienie BareMetal jest izolowany do dzierżawy. Dzierżawa jest odizolowana od sieci, magazynu i warstwy obliczeniowej od innych dzierżawców. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżawców nie mogą być nawzajem widoczne ani komunikować się ze sobą w wystąpieniach BareMetal.

## <a name="operating-system"></a>System operacyjny
Podczas aprowizacji wystąpienia BareMetal można wybrać system operacyjny, który ma zostać zainstalowany na maszynach. 

>[!NOTE]
>Należy pamiętać, że infrastruktura BareMetal jest modelem BYOL.

Dostępne wersje systemu operacyjnego Linux to:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage
Infrastruktura BareMetal zapewnia wysoce nadmiarowy magazyn NFS i magazyn Fiber Channel. Infrastruktura oferuje ścisłą integrację dla obciążeń przedsiębiorstwa, takich jak SAP, SQL i innych. Zapewnia również ochronę danych spójną na poziomie aplikacji i funkcje zarządzania danymi. Narzędzia do samoobsługowego zarządzania umożliwiają tworzenie migawek, klonowanie i precyzyjne replikacje w celu zapewnienia optymalnego miejsca. Infrastruktura umożliwia zero możliwości RPO i RTO na potrzeby dostępności danych i zapewnienia ciągłości działania. 

Infrastruktura magazynu oferuje następujące funkcje:
- Maksymalnie 4 x 100 GB linków.
- Do 32 GB linków Fiber Channel.
- Wszystkie dyski SSD i interfejsu NVMe programu Flash.
- Niezwykle małe opóźnienia i Wysoka przepływność.
- Skaluje do 4 PB magazynu pierwotnego. 
- Do 11 000 000 operacji we/wy na sekundę.

Obsługiwane są następujące protokoły dostępu do danych: 
- iSCSI 
- System plików NFS (V3 lub v4) 
- Kanał Fiber 
- Interfejsu NVMe przez FC  

## <a name="networking"></a>Sieć
Architektura usług sieciowych platformy Azure to kluczowy składnik dla pomyślnego wdrożenia wyspecjalizowanych obciążeń w wystąpieniach BareMetal. Prawdopodobnie nie wszystkie systemy IT znajdują się już na platformie Azure. Platforma Azure oferuje technologię sieciową, dzięki której platforma Azure będzie wyglądać jak wirtualne centrum danych do lokalnych wdrożeń oprogramowania. Funkcje sieci platformy Azure wymagane przez wystąpienia BareMetal obejmują:

- Sieci wirtualne platformy Azure połączone z obwodem usługi Azure ExpressRoute, który łączy się z zasobami sieci lokalnej.
- Obwód usługi ExpressRoute, który łączy się lokalnie z platformą Azure, powinien mieć przepustowość minimalną wynoszącą 1 GB/s lub wyższą.
- Rozszerzona Active Directory i system DNS na platformie Azure lub w pełni działa na platformie Azure.

Usługa ExpressRoute umożliwia rozbudowanie sieci lokalnej do chmury firmy Microsoft przez połączenie prywatne z pomocą dostawcy łączności. **ExpressRoute Local** można używać do oszczędnego transferu danych między lokalizacją lokalną i wybranym regionem platformy Azure. Aby zwiększyć łączność w granicach geopolitycznych, można włączyć usługę **ExpressRoute Premium**. 

Wystąpienia BareMetal są obsługiwane w zakresie adresów IP serwera sieci wirtualnej platformy Azure.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Diagram architektoniczny diagramu infrastruktury usługi Azure BareMetal." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

Pokazana architektura jest podzielona na trzy sekcje:
- Z **lewej:** Pokazuje infrastrukturę lokalną klienta, która uruchamia różne aplikacje, łącząc się za pomocą partnera lub lokalnego routera brzegowego, takiego jak Equinix. Aby uzyskać więcej informacji, zobacz temat [dostawcy i lokalizacje połączeń: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Wyśrodkuj:** Pokazuje [ExpressRoute](../expressroute/expressroute-introduction.md) zainicjowany przy użyciu subskrypcji platformy Azure, która oferuje łączność z usługą Azure Edge Network.
- **Prawo:** Pokazuje usługę Azure IaaS, a w tym przypadku używa maszyn wirtualnych do hostowania aplikacji, które są obsługiwane w ramach sieci wirtualnej platformy Azure.
- **Dół:** Pokazuje korzystanie z bramy ExpressRoute z włączoną obsługą [ExpressRoute FastPath](../expressroute/about-fastpath.md) dla łączności BareMetal oferującej małe opóźnienia.   
   >[!TIP]
   >W celu zapewnienia obsługi tej bramy ExpressRoute powinna być UltraPerformance. Aby uzyskać więcej informacji, zobacz [Informacje o bramach sieci wirtualnej ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest nauczenie się, jak identyfikować wystąpienia BareMetal i korzystać z nich za pomocą Azure Portal.

> [!div class="nextstepaction"]
> [Zarządzanie wystąpieniami BareMetal za pomocą Azure Portal](connect-baremetal-infrastructure.md)
