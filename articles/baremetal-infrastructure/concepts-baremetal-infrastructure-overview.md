---
title: Przegląd infrastruktury BareMetal na platformie Azure
description: Omówienie infrastruktury BareMetal na platformie Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 1/4/2021
ms.openlocfilehash: 94cee52eccd8cc9e9631b47bdf84892d763d86d3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776317"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Co to jest BareMetal Infrastructure w wersji zapoznawczej na platformie Azure?

Infrastruktura Azure BareMetal zapewnia bezpieczne rozwiązanie do migrowania niestandardowych obciążeń przedsiębiorstwa. Wystąpienia BareMetal to nieudostępniony sprzęt hosta/serwera przypisany do użytkownika. Odblokowuje przenoszenie rozwiązania Premium z wyspecjalizowanymi obciążeniami wymagającymi certyfikowanych sprzętu, licencjonowania i umów pomocy technicznej. Platforma Azure obsługuje monitorowanie infrastruktury i jej konserwację, a monitorowanie i monitorowanie aplikacji w systemie operacyjnym gościa jest objęte własnością.

Infrastruktura BareMetal zapewnia ścieżkę do modernizacji infrastruktury, przy jednoczesnym zachowaniu istniejących inwestycji i architektury. Dzięki infrastrukturze BareMetal można przenosić wyspecjalizowane obciążenia na platformę Azure, umożliwiając dostęp i integrację z usługami platformy Azure przy małym opóźnieniu.

## <a name="sku-availability-in-azure-regions"></a>Dostępność jednostki SKU w regionach platformy Azure
Infrastruktura BareMetal dla obciążeń wyspecjalizowanych i ogólnego przeznaczenia jest dostępna, począwszy od czterech regionów na podstawie poprawek 4,2 (rev 4,2):
- West Europe
- Europa Północna
- Wschodnie stany USA 2
- South Central US

>[!NOTE]
>**Rev 4,2** to najnowsza BareMetal infrastruktura z wykorzystaniem istniejącej architektury rev 4.  Rev 4 zapewnia bliższą bliskość hostom maszyn wirtualnych platformy Azure. W przypadku opóźnień sieci między maszynami wirtualnymi platformy Azure i jednostkami wystąpienia BareMetal wdrożonymi w znacznikach lub wierszach z 4.  Możesz uzyskać dostęp do wystąpień BareMetal i zarządzać nimi za pomocą Azure Portal. 

## <a name="support"></a>Pomoc techniczna
Infrastruktura BareMetal jest zgodna z normami ISO 27001, ISO 27017, SOC 1 i SOC 2.  Używa ona również modelu "BYOL" (przyjęcie): systemu operacyjnego, wyspecjalizowanego obciążenia i aplikacji innych firm.  

Po otrzymaniu dostępu głównego i pełnej kontroli przyjmujesz odpowiedzialność za:
- Projektowanie i wdrażanie rozwiązań do tworzenia kopii zapasowych i odzyskiwania, wysokiej dostępności i odzyskiwania po awarii
- Licencjonowanie, zabezpieczenia i pomoc techniczna dla systemów operacyjnych i oprogramowania innych firm

Firma Microsoft jest odpowiedzialna za:
- Udostępnianie sprzętu dla wyspecjalizowanych obciążeń 
- Inicjowanie obsługi systemu operacyjnego

:::image type="content" source="media/baremetal-support-model.png" alt-text="Model obsługi infrastruktury BareMetal" border="false":::

## <a name="compute"></a>Compute
Infrastruktura BareMetal oferuje wiele jednostek SKU dla wyspecjalizowanych obciążeń. Dostępne jednostki SKU są dostępne w zakresie od mniejszego systemu dwugniazdowego do systemu 24-gniazdowego. Użyj jednostek SKU specyficznych dla obciążenia dla wyspecjalizowanego obciążenia.

Sama sygnatura wystąpienia BareMetal łączy następujące składniki:

- **Obliczanie:** Serwery oparte na innej generacji procesorów Intel Xeon, które zapewniają niezbędną możliwość obliczeniową i są certyfikowane dla wyspecjalizowanego obciążenia.

- **Sieć:** Ujednolicona sieć szkieletowa o dużej szybkości, która łączy się ze składnikami, magazynu i sieci LAN.

- **Magazyn:** Infrastruktura dostępna za pomocą ujednoliconej sieci szkieletowej.

W ramach infrastruktury wielu dzierżawców sygnatury BareMetal klienci są wdrażani w izolowanych dzierżawcach. Podczas wdrażania dzierżawy należy nazwać subskrypcję platformy Azure w ramach rejestracji na platformie Azure. Ta subskrypcja platformy Azure jest rozliczana z BareMetal wystąpieniami.

>[!NOTE]
>Klient wdrożony w wystąpieniu BareMetal zostaje odizolowany do dzierżawy. Dzierżawa jest odizolowana od sieci, magazynu i warstwy obliczeniowej od innych dzierżawców. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżawców nie są ze sobą widoczne ani nie komunikują się ze sobą w wystąpieniach BareMetal.

## <a name="os"></a>System operacyjny
Podczas aprowizacji wystąpienia BareMetal można wybrać system operacyjny, który ma zostać zainstalowany na maszynach. 

>[!NOTE]
>Należy pamiętać, że infrastruktura BareMetal jest modelem BYOL.

Dostępne wersje systemu operacyjnego Linux to:
- Red Hat Enterprise Linux (RHEL) 7,6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 Z DODATKIEM SP3
   - SLES 12 SP4
   - SLES 12 Z DODATKIEM SP5
   - SLES 15 SP1

## <a name="storage"></a>Storage
Wystąpienia BareMetal oparte na konkretnym typie jednostki SKU są dostępne ze wstępnie zdefiniowanym magazynem NFS dla określonego typu obciążenia. Po udostępnieniu BareMetal można udostępnić więcej przestrzeni dyskowej na podstawie szacowanego wzrostu przez przesłanie żądania pomocy technicznej. Wszystkie magazyny są dostarczane z dyskiem flash z poprawkami 4,2 z obsługą NFSv3 i NFSv4. Zostanie udostępniona nowsza wersja 4,5 interfejsu SSD. Aby uzyskać więcej informacji na temat określania rozmiarów magazynu, zobacz sekcję [Typ obciążenia BareMetal](../virtual-machines/workloads/sap/get-started.md) .

>[!NOTE]
>Magazyn używany do BareMetal jest zgodny z wymaganiami dotyczącymi [publikacji FIPS (Federal Information Processing Standard) 140-2](/microsoft-365/compliance/offering-fips-140-2) . Dane są bezpiecznie przechowywane na dyskach.

## <a name="networking"></a>Sieć
Architektura usług sieciowych platformy Azure to kluczowy składnik dla pomyślnego wdrożenia wyspecjalizowanych obciążeń w wystąpieniach BareMetal. Prawdopodobnie nie wszystkie systemy IT znajdują się już na platformie Azure. Platforma Azure oferuje technologię sieciową, dzięki której platforma Azure będzie wyglądać jak wirtualne centrum danych do lokalnych wdrożeń oprogramowania. Funkcje sieci platformy Azure wymagane przez wystąpienia BareMetal są następujące:

- Sieci wirtualne platformy Azure są połączone z obwodem usługi ExpressRoute, który łączy się z zasobami sieci lokalnej.
- Obwód usługi ExpressRoute, który łączy się z siecią lokalną z platformą Azure, powinien mieć przepustowość minimalną wynoszącą 1 GB/s lub wyższą.
- Rozszerzona usługa Active Directory i system DNS na platformie Azure lub w pełni działa na platformie Azure.

Korzystanie z programu ExpressRoute umożliwia rozbudowanie sieci lokalnej do chmury firmy Microsoft przez połączenie prywatne z pomocą dostawcy łączności. Możesz włączyć usługę **ExpressRoute Premium** , aby zwiększyć łączność w granicach geopolitycznych lub użyć **lokalnych ExpressRoute** do transferowania danych ekonomicznych między lokalizacją znajdującą się w wybranym regionie platformy Azure.

Wystąpienia BareMetal są obsługiwane w zakresie adresów IP serwera sieci wirtualnej platformy Azure.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Diagram infrastruktury usługi Azure BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

Pokazana architektura jest podzielona na trzy sekcje:
- **Po lewej:** pokazuje lokalną infrastrukturę klienta, która uruchamia różne aplikacje, łącząc się za pomocą partnera lub lokalnego routera brzegowego, takiego jak Equinix. Aby uzyskać więcej informacji, zobacz temat [dostawcy i lokalizacje połączeń: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Wyśrodkuj:** pokazuje [ExpressRoute](../expressroute/expressroute-introduction.md) zainicjowany przy użyciu subskrypcji platformy Azure, która oferuje łączność z usługą Azure Edge Network.
- **Prawo:** pokazuje usługę Azure IaaS, a w tym przypadku używa maszyn wirtualnych do hostowania aplikacji, które są obsługiwane w ramach sieci wirtualnej platformy Azure.
- **U dołu:** pokazuje, że za pomocą bramy ExpressRoute włączono usługę [ExpressRoute FastPath](../expressroute/about-fastpath.md) dla łączności BareMetal z małymi opóźnieniami.   
   >[!TIP]
   >W celu zapewnienia obsługi tej bramy ExpressRoute powinna być UltraPerformance.  Aby uzyskać więcej informacji, zobacz [Informacje o bramach sieci wirtualnej ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest nauczenie się, jak identyfikować jednostki wystąpienia BareMetal i współdziałać z nimi za pomocą Azure Portal.

> [!div class="nextstepaction"]
> [Zarządzanie wystąpieniami BareMetal za pomocą witryny Azure Portal](connect-baremetal-infrastructure.md)