---
title: Rozwiązanie VMware firmy Azure według CloudSimple — chmury prywatne
description: Dowiedz się więcej o CloudSimple koncepcji i korzyściach, w tym o całkowitej ciągłości działania programu VMware, zgodności z istniejącymi narzędziami, umiejętnościami i procesami.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140721"
---
# <a name="cloudsimple-private-cloud-overview"></a>Omówienie chmury prywatnej CloudSimple

CloudSimple przekształca i rozszerza obciążenia oprogramowania VMware do chmur publicznych w ciągu kilku minut. Za pomocą usługi CloudSimple można lokalnie wdrożyć oprogramowanie VMware w infrastrukturze platformy Azure bez systemu operacyjnego. Wdrożenie jest przechowywane w lokalizacjach platformy Azure i w pełni integruje się z pozostałą częścią chmury platformy Azure.

Rozwiązanie CloudSimple zapewnia pełną ciągłość działania programu VMware. To rozwiązanie zapewnia korzyści z chmury publicznej:

* Elastyczność
* Innowacyjność
* Wydajność

Dzięki CloudSimple można korzystać z modelu zużycia w chmurze, który obniża całkowity koszt posiadania. Oferuje on również Inicjowanie obsługi administracyjnej na żądanie, płatność zgodnie z oczekiwaniami i optymalizację wydajności.

CloudSimple jest w pełni zgodny z:

* Istniejące narzędzia
* Umiejętności
* Procesy

Ta zgodność umożliwia zespołom zarządzanie obciążeniami w chmurze platformy Azure bez zakłócania tego typu zasad:

* Sieć
* Zabezpieczenia  
* Ochrona danych  
* Inspekcja

CloudSimple zarządza infrastrukturą i wszystkimi niezbędnymi usługami sieci i zarządzania. Usługa CloudSimple umożliwia zespołowi skoncentrowanie się na:

* Wartość biznesowa
* Aprowizacja aplikacji
* Ciągłość działalności biznesowej
* Pomoc techniczna
* Wymuszanie zasad

## <a name="private-cloud-environment-overview"></a>Omówienie środowiska chmury prywatnej

Chmura prywatna to izolowany stos oprogramowania VMware, który obsługuje:

* Hosty ESXi
* vCenter
* Sieci vSAN
* NSX

Chmurami prywatnymi zarządza się za pomocą portalu CloudSimple. Mają własny serwer vCenter we własnej domenie zarządzania.

Stos działa na:

* Węzły dedykowane
* Węzły sprzętu izolowanych od zera

Użytkownicy wykorzystują stos za pomocą natywnych narzędzi VMware, w tym:

* vCenter
* NSX Manager

Można wdrażać dedykowane węzły w lokalizacjach platformy Azure. Następnie możesz zarządzać nimi za pomocą platformy Azure i usługi CloudSimple. Chmura prywatna składa się z co najmniej jednego klastra vSphere, a każdy klaster zawiera od 3 do 16 węzłów.

Chmurę prywatną można utworzyć przy użyciu zakupionych węzłów płatność zgodnie z rzeczywistym użyciem lub zarezerwowanych, dedykowanych węzłów.

Chmurę prywatną można podłączyć do środowiska lokalnego i sieci platformy Azure przy użyciu następujących połączeń:

* Bezpieczeństwo
* Prywatna sieć VPN
* Azure ExpressRoute

Środowisko chmury prywatnej zostało zaprojektowane w celu wyeliminowania pojedynczych punktów awarii:

* Klastry ESXi są skonfigurowane z wysoką dostępnością vSphere i mają rozmiar co najmniej jeden węzeł zapasowy na potrzeby odporności.
* Sieci vSAN zapewnia nadmiarowy magazyn podstawowy. Sieci vSAN wymaga co najmniej trzech węzłów w celu zapewnienia ochrony przed pojedynczym awarią. Można skonfigurować sieci vSAN w celu zapewnienia większej odporności dla większych klastrów.
* Aby zapewnić ochronę przed awariami magazynu, można skonfigurować maszyny wirtualne programu vCenter, PSC i NSX Manager przy użyciu zasad magazynu RAID-10. vSphere HA chroni przed awariami węzłów i sieci.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenariusze wdrażania chmury prywatnej

Poniżej przedstawiono przykładowe przypadki użycia wdrożenia chmury prywatnej.

### <a name="data-center-retirement-or-migration"></a>Wycofanie lub migracja centrum danych

* Uzyskaj dodatkową pojemność po osiągnięciu limitów istniejących centrów danych lub odświeżania sprzętu.
* Dodaj wymaganą pojemność w chmurze i Usuń zarządzaniem mu towarzysząę zarządzania odświeżeniami sprzętowymi.
* Zmniejszenie ryzyka i kosztów migracji w chmurze w porównaniu do czasochłonnych operacji konwersji lub ponownej architektury.
* Przyspiesz migracje w chmurze przy użyciu znanych narzędzi i umiejętności programu VMware. W chmurze korzystaj z usług platformy Azure w celu modernizacji aplikacji w Twoim tempie.

### <a name="expand-on-demand"></a>Rozwiń na żądanie

* Rozwiń chmurę, aby zaspokoić nieprzewidziane potrzeby, takie jak nowe środowiska programistyczne lub sezonowe zmiany pojemności.
* Utwórz nową pojemność na żądanie i Zachowaj ją tylko tak długo, jak to konieczne.
* Ogranicz inwestycje z góry, przyspiesz szybkość aprowizacji i zmniejsz złożoność dzięki tej samej architekturze i zasadom zarówno w środowisku lokalnym, jak i w chmurze.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Odzyskiwanie po awarii i pulpity wirtualne w chmurze platformy Azure

* Ustanów zdalny dostęp do danych, aplikacji i pulpitów w chmurze platformy Azure. Połączenia o wysokiej przepustowości umożliwiają szybkie przekazywanie i pobieranie danych w celu odzyskania zdarzeń. Sieci o małym opóźnieniu zapewniają krótszy czas odpowiedzi, który użytkownicy oczekują od aplikacji klasycznej.

* Replikuj wszystkie zasady i sieci w chmurze przy użyciu portalu CloudSimple i znanych narzędzi VMware. Replikacja zmniejsza nakład pracy i ryzyko tworzenia i zarządzania implementacjami odzyskiwania po awarii i infrastruktury VDI.

### <a name="high-performance-applications-and-databases"></a>Aplikacje i bazy danych o wysokiej wydajności

* Uruchamiaj najbardziej wymagające obciążenia przy użyciu architektury ze zbyt zbieżną architekturą zapewnianą przez CloudSimple.
* Uruchamiaj oprogramowanie Oracle, Microsoft SQL Server, systemy oprogramowania pośredniczącego i bazy danych bez SQL o wysokiej wydajności.
* Poznaj chmurę jako własne centrum danych z szybkimi połączeniami sieciowymi o szybkości 25 GB/s. Połączenia o dużej szybkości umożliwiają uruchamianie aplikacji hybrydowych obejmujących lokalne, VMware na platformie Azure i prywatne obciążenia platformy Azure bez naruszania wydajności.

### <a name="true-hybrid"></a>Prawdziwe hybrydowe

* Ujednolicenie DevOps w ramach usług VMware i Azure.
* Optymalizuj administrację VMware dla usług i rozwiązań platformy Azure, które można stosować do wszystkich obciążeń.
* Uzyskaj dostęp do usług w chmurze publicznej bez konieczności rozszerzania centrum danych ani tworzenia architektury aplikacji.
* Scentralizowanie tożsamości, zasad kontroli dostępu, rejestrowania i monitorowania aplikacji VMware na platformie Azure.

## <a name="limits"></a>Limity

W poniższej tabeli wymieniono limity węzłów zasobów chmury prywatnej.

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](create-private-cloud.md)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
