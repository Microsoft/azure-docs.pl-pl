---
title: Integrowanie odzyskiwania po awarii maszyny wirtualnej platformy Azure ExpressRoute Azure z usługą Azure Site Recovery
description: Opisuje sposób konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu usług Azure Site Recovery i Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88654382"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrowanie ExpressRoute z odzyskiwaniem po awarii dla maszyn wirtualnych platformy Azure


W tym artykule opisano sposób integrowania usługi Azure ExpressRoute z usługą [Azure Site Recovery](site-recovery-overview.md)podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure.

Site Recovery umożliwia odzyskiwanie po awarii maszyn wirtualnych platformy Azure przez replikowanie danych maszyny wirtualnej platformy Azure na platformę Azure.

- Jeśli maszyny wirtualne platformy Azure używają [dysków zarządzanych przez platformę Azure](../virtual-machines/managed-disks-overview.md), dane maszyny wirtualnej są replikowane do zreplikowanego dysku zarządzanego w regionie pomocniczym.
- Jeśli maszyny wirtualne platformy Azure nie używają dysków zarządzanych, dane maszyny wirtualnej są replikowane do konta usługi Azure Storage.
- Punkty końcowe replikacji są publiczne, ale ruch związany z replikacją dla maszyn wirtualnych platformy Azure nie przekracza Internetu.

Usługa ExpressRoute umożliwia rozszerzonie sieci lokalnych do chmury Microsoft Azure przez połączenie prywatne przez dostawcę połączenia. Jeśli skonfigurowano ExpressRoute, integruje się z Site Recovery w następujący sposób:

- **Podczas replikacji między regionami platformy Azure**: ruch związany z replikacją na potrzeby odzyskiwania po awarii maszyny wirtualnej platformy Azure jest tylko na platformie Azure i ExpressRoute nie jest wymagany ani używany do replikacji. Jednak w przypadku łączenia się z lokacji lokalnej z maszynami wirtualnymi platformy Azure w podstawowej lokacji platformy Azure istnieje wiele problemów, które należy wziąć pod uwagę podczas konfigurowania odzyskiwania po awarii dla tych maszyn wirtualnych platformy Azure.
- **Przejście w tryb failover między regionami platformy Azure**: gdy wystąpi awaria, maszyny wirtualne platformy Azure są przełączane do pracy awaryjnej z regionu podstawowego do pomocniczego. Po przejściu w tryb failover do regionu pomocniczego należy wykonać kilka czynności w celu uzyskania dostępu do maszyn wirtualnych platformy Azure w regionie pomocniczym przy użyciu ExpressRoute.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem upewnij się, że rozumiesz następujące pojęcia:

- [Obwody](../expressroute/expressroute-circuit-peerings.md) usługi ExpressRoute
- ExpressRoute [domeny routingu](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- [Lokalizacje](../expressroute/expressroute-locations.md)ExpressRoute.
- [Architektura replikacji](azure-to-azure-architecture.md) maszyny wirtualnej platformy Azure
- Jak [skonfigurować replikację](azure-to-azure-tutorial-enable-replication.md) maszyn wirtualnych platformy Azure.
- Jak przełączać maszyny wirtualne platformy Azure [w tryb failover](azure-to-azure-tutorial-failover-failback.md) .


## <a name="general-recommendations"></a>Ogólne zalecenia

Aby uzyskać najlepsze rozwiązania i zapewnić wydajne cele czasu odzyskiwania (RTO) na potrzeby odzyskiwania po awarii, zalecamy wykonanie następujących czynności podczas konfigurowania Site Recovery integracji z usługą ExpressRoute:

- Udostępnij składniki sieciowe przed przełączeniem w tryb failover do regionu pomocniczego:
    - Po włączeniu replikacji dla maszyn wirtualnych platformy Azure Site Recovery może automatycznie wdrażać zasoby sieciowe, takie jak sieci, podsieci i bramy w docelowym regionie platformy Azure, na podstawie ustawień sieci źródłowej.
    - Site Recovery nie może automatycznie skonfigurować zasobów sieciowych, takich jak bramy sieci wirtualnej.
    - Zalecamy udostępnienie tych dodatkowych zasobów sieciowych przed przełączeniem w tryb failover. To wdrożenie wiąże się z małym przestojem i może mieć wpływ na ogólny czas odzyskiwania, jeśli nie zostało to zrobione podczas planowania wdrożenia.
- Uruchom przechodzenie do zwykłego odzyskiwania po awarii:
    - Ta próba pozwala zweryfikować strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Pomaga uniknąć problemów z konfiguracją ostatnich minut, które mogą mieć negatywny wpływ na RTO.
    - Po uruchomieniu testowej pracy w trybie failover w celu przechodzenia do szczegółów zalecamy użycie oddzielnej sieci maszyn wirtualnych platformy Azure, a nie domyślnej sieci skonfigurowanej po włączeniu replikacji.
- W przypadku pojedynczego obwodu ExpressRoute Użyj różnych przestrzeni adresów IP.
    - Zalecamy użycie innej przestrzeni adresów IP dla docelowej sieci wirtualnej. Pozwala to uniknąć problemów podczas ustanawiania połączeń w regionie awarii.
    - Jeśli nie możesz użyć oddzielnej przestrzeni adresowej, upewnij się, że test pracy w trybie failover odzyskiwania po awarii jest uruchamiany w oddzielnym sieci testowej z różnymi adresami IP. Nie można połączyć dwóch sieci wirtualnych z nakładaną przestrzenią adresów IP do tego samego obwodu ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikowanie maszyn wirtualnych platformy Azure przy użyciu ExpressRoute


Aby skonfigurować replikację maszyn wirtualnych platformy Azure w lokacji głównej, a następnie nawiązać połączenie z tymi maszynami wirtualnymi za pośrednictwem witryny ExpressRoute, należy wykonać następujące czynności:

1. [Włącz replikację](azure-to-azure-tutorial-enable-replication.md) dla każdej maszyny wirtualnej platformy Azure.
2. Opcjonalnie Zezwól Site Recovery na Konfigurowanie sieci:
    - Podczas konfigurowania i włączania replikacji program Site Recovery konfiguruje sieci, podsieci i podsieci bramy w docelowym regionie platformy Azure, aby dopasować je do tych w regionie źródłowym. Site Recovery również są mapowane między źródłową i docelową siecią wirtualną.
    - Jeśli nie chcesz, aby Site Recovery automatycznie, przed włączeniem replikacji Utwórz zasoby sieciowe po stronie docelowej.
3. Utwórz inne elementy sieci:
    - Site Recovery nie tworzy tabel tras, bram sieci wirtualnych, połączeń bramy sieci wirtualnej, wirtualnej komunikacji równorzędnej ani innych zasobów sieciowych i połączeń w regionie pomocniczym.
    - Należy utworzyć te dodatkowe elementy sieciowe w regionie pomocniczym, w dowolnym czasie przed uruchomieniem trybu failover z regionu podstawowego.
    - Aby skonfigurować i połączyć te zasoby sieciowe, można użyć [planów odzyskiwania](site-recovery-create-recovery-plans.md) i skryptów automatyzacji.
1. Jeśli masz wdrożone sieciowe urządzenie wirtualne (urządzenie WUS) w celu kontrolowania przepływu ruchu sieciowego, pamiętaj, że:
    - Domyślna trasa systemowa platformy Azure dla replikacji maszyny wirtualnej platformy Azure to 0.0.0.0/0.
    - Zazwyczaj wdrożenia urządzenie WUS definiują trasy domyślnej (0.0.0.0/0), które wymuszają ruch wychodzący z Internetu do przepływu przez urządzenie WUS. Trasa domyślna jest używana, gdy nie można znaleźć innej konfiguracji określonej trasy.
    - W takim przypadku urządzenie WUS może być przeciążony, jeśli cały ruch związany z replikacją przechodzi przez urządzenie WUS.
    - To samo ograniczenie ma zastosowanie również w przypadku używania tras domyślnych do routingu wszystkich ruchu maszyn wirtualnych platformy Azure do wdrożeń lokalnych.
    - W tym scenariuszu zalecamy [utworzenie punktu końcowego usługi sieciowej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w sieci wirtualnej dla usługi Microsoft. Storage, dzięki czemu ruch związany z replikacją nie opuszcza granicy platformy Azure.

## <a name="replication-example"></a>Przykład replikacji

Zazwyczaj wdrożenia korporacyjne mają podział obciążeń na wiele sieci wirtualnychów platformy Azure z centralnym koncentratorem łączności dla łączności zewnętrznej z Internetem i lokacjami lokalnymi. Topologia gwiazdy jest zwykle używana razem z ExpressRoute.

![Lokalne na platformę Azure z usługą ExpressRoute przed przejściem w tryb failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Aplikacje są wdrażane w regionie Azja Wschodnia platformy Azure.
- **Sieci wirtualnych szprych**. Aplikacje są wdrażane w dwóch szprychach sieci wirtualnych:
    - **VNet1 źródłowa**: 10.1.0.0/24.
    - **VNet2 źródłowa**: 10.2.0.0/24.
    - Każda sieć wirtualna szprych jest połączona z **koncentratorem** sieci wirtualnej.
- **Sieć wirtualna centrum**. Istnieje **Sieć** wirtualna koncentratora sieci wirtualnej z koncentratorem: 10.10.10.0/24.
  - Ta sieć wirtualna centrum działa jako strażnik.
  - Cała komunikacja między podsieciami odbywa się za pośrednictwem tego centrum.
    - **Podsieci sieci wirtualnej centrum**. Sieć wirtualna centrum ma dwie podsieci:
    - **Podsieć urządzenie WUS**: 10.10.10.0/25. Ta podsieć zawiera urządzenie WUS (10.10.10.10).
    - **Podsieć bramy**: 10.10.10.128/25. Ta podsieć zawiera bramę ExpressRoute, która jest połączona z połączeniem ExpressRoute, które jest kierowane do lokacji lokalnej za pośrednictwem domeny routingu prywatnej komunikacji równorzędnej.
- Lokalne centrum danych ma połączenie obwodu usługi ExpressRoute za pomocą krawędzi partnera w Hongkongu.
- Wszystkie Routing są kontrolowane za pomocą tabel usługi Azure Route (UDR).
- Cały ruch wychodzący między sieci wirtualnych lub lokalnym centrum danych jest kierowany przez urządzenie WUS.

### <a name="hub-and-spoke-peering-settings"></a>Ustawienia koncentratora i komunikacji równorzędnej

#### <a name="spoke-to-hub"></a>Szprycha do piasty

**Kierunek** | **Ustawienie** | **Stan**
--- | --- | ---
Szprycha do piasty | Zezwalaj na adres sieci wirtualnej | Enabled (Włączony)
Szprycha do piasty | Zezwalaj na ruch przesłany dalej | Enabled (Włączony)
Szprycha do piasty | Zezwalaj na tranzyt bramy | Disabled
Szprycha do piasty | Użyj usuwania bram | Enabled (Włączony)

 ![Konfiguracja komunikacji równorzędnej z usługą Hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Piasta do szprychy

**Kierunek** | **Ustawienie** | **Stan**
--- | --- | ---
Piasta do szprychy | Zezwalaj na adres sieci wirtualnej | Enabled (Włączony)
Piasta do szprychy | Zezwalaj na ruch przesłany dalej | Enabled (Włączony)
Piasta do szprychy | Zezwalaj na tranzyt bramy | Enabled (Włączony)
Piasta do szprychy | Użyj usuwania bram | Disabled

 ![Konfiguracja komunikacji równorzędnej między centrami](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Przykładowe kroki

W naszym przykładzie po włączeniu replikacji dla maszyn wirtualnych platformy Azure w sieci źródłowej należy wykonać następujące czynności:

1. Należy [włączyć replikację](azure-to-azure-tutorial-enable-replication.md) dla maszyny wirtualnej.
2. Site Recovery utworzy replikę repliki, podsieci i podsieci bramy w regionie docelowym.
3. Site Recovery tworzy mapowania między sieciami źródłowymi i sieciami docelowymi repliki, które tworzy.
4. Możesz ręcznie tworzyć bramy sieci wirtualnej, połączenia bramy sieci wirtualnej, komunikację równorzędną sieci wirtualnych lub inne zasoby lub połączenia sieciowe.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Praca awaryjna maszyn wirtualnych platformy Azure w przypadku korzystania z usługi ExpressRoute

Po niepowodzeniu maszyn wirtualnych platformy Azure w docelowym regionie platformy Azure przy użyciu Site Recovery można uzyskać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering)ExpressRoute.

- Musisz połączyć ExpressRoute z docelową siecią wirtualną z nowym połączeniem. Istniejące połączenie ExpressRoute nie jest automatycznie przenoszone.
- Sposób skonfigurowania połączenia usługi ExpressRoute z docelową siecią wirtualną zależy od topologii ExpressRoute.


### <a name="access-with-two-circuits"></a>Dostęp z dwoma obwodymi

#### <a name="two-circuits-with-two-peering-locations"></a>Dwa obwody z dwoma lokalizacjami komunikacji równorzędnej

Ta konfiguracja pomaga chronić obwody usługi ExpressRoute przed awarią regionalną. Jeśli główna lokalizacja komunikacji równorzędnej ulegnie awarii, połączenia mogą być kontynuowane z innej lokalizacji.

- Obwód połączony ze środowiskiem produkcyjnym jest zwykle podstawowym. Obwód pomocniczy ma zwykle niższą przepustowość, którą można zwiększyć w przypadku wystąpienia awarii.
- Po przejściu w tryb failover można nawiązać połączenia z pomocniczego obwodu usługi ExpressRoute do docelowej sieci wirtualnej. Alternatywnie można skonfigurować i przygotować połączenia w przypadku awarii, aby skrócić czas odzyskiwania.
- W przypadku jednoczesnych połączeń z sieci wirtualnych głównymi i docelowymi upewnij się, że Routing lokalny używa obwodu pomocniczego i połączenia po przejściu do trybu failover.
- Sieci wirtualnych źródłowy i docelowy mogą odbierać nowe adresy IP lub zachować te same wartości po przejściu w tryb failover. W obu przypadkach połączenia pomocnicze mogą zostać ustanowione przed przełączeniem w tryb failover.


#### <a name="two-circuits-with-single-peering-location"></a>Dwa obwody z jedną lokalizacją komunikacji równorzędnej

Ta konfiguracja pomaga chronić przed awarią podstawowego obwodu usługi ExpressRoute, ale nie w przypadku przełączenia pojedynczej lokalizacji komunikacji równorzędnej ExpressRoute, co wpływa na oba obwody.

- Możesz mieć jednoczesne połączenia z lokalnego centrum danych do źródłowej sieci wirtualnej z obwodem podstawowym oraz do docelowej sieci wirtualnej z obwodem pomocniczym.
- W przypadku jednoczesnych połączeń z serwerem podstawowym i docelowym upewnij się, że Routing lokalny używa obwodu pomocniczego i połączenia po przejściu do trybu failover.
-   Nie można połączyć obu obwodów z tą samą siecią wirtualną, gdy w tej samej lokalizacji komunikacji równorzędnej są tworzone obwody.

### <a name="access-with-a-single-circuit"></a>Dostęp za pomocą pojedynczego obwodu

W tej konfiguracji istnieje tylko jeden obwód ExpressRoute. Chociaż obwód ma nadmiarowe połączenie na wypadek awarii, jeden obwód trasy nie zapewni odporności, jeśli region komunikacji równorzędnej ulegnie awarii. Należy pamiętać, że:

- Maszyny wirtualne platformy Azure można replikować do dowolnego regionu świadczenia usługi Azure w [tej samej lokalizacji geograficznej](azure-to-azure-support-matrix.md#region-support). Jeśli docelowy region platformy Azure nie znajduje się w tej samej lokalizacji co źródło, musisz włączyć usługę ExpressRoute Premium, jeśli używasz pojedynczego obwodu usługi ExpressRoute. Dowiedz się więcej o [lokalizacjach ExpressRoute](../expressroute/expressroute-locations.md) i [cenach ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Nie można nawiązać połączenia ze źródłem i docelowa sieci wirtualnych jednocześnie do obwodu, jeśli ta sama przestrzeń adresów IP jest używana w regionie docelowym. W tym scenariuszu:    
    -  Odłącz połączenie po stronie źródłowej, a następnie nawiąż połączenie po stronie docelowej. Ta zmiana połączenia może być inicjowana przez skrypt jako część planu odzyskiwania Site Recovery. Należy pamiętać, że:
        - W przypadku awarii regionalnej, jeśli region podstawowy jest niedostępny, operacja rozłączenia może zakończyć się niepowodzeniem. Może to mieć wpływ na tworzenie połączenia z regionem docelowym.
        - Jeśli połączenie zostało utworzone w regionie docelowym, a region podstawowy odzyskuje później, może wystąpić porzucanie pakietów, jeśli dwa jednoczesne połączenia próbują nawiązać połączenie z tą samą przestrzenią adresową.
        - Aby tego uniknąć, należy natychmiast przerwać połączenie podstawowe.
        - Po powrocie po awarii maszyny wirtualnej do regionu podstawowego można ponownie nawiązać połączenie podstawowe po rozłączeniu połączenia pomocniczego.
-   Jeśli w docelowej sieci wirtualnej są używane różne przestrzenie adresowe, możesz jednocześnie nawiązać połączenie ze źródłem i docelową sieci wirtualnych z tego samego obwodu usługi ExpressRoute.


## <a name="failover-example"></a>Przykład pracy awaryjnej

W naszym przykładzie używamy następującej topologii:

- Dwa różne obwody usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej.
- Zachowaj prywatne adresy IP dla maszyn wirtualnych platformy Azure po przejściu do trybu failover.
- Docelowy region odzyskiwania to Azja Południowo-Wschodnia platformy Azure.
- Pomocnicze połączenie obwodu usługi ExpressRoute jest nawiązywane za pomocą krawędzi partnera w Singapurze.

W przypadku prostej topologii korzystającej z pojedynczego obwodu usługi ExpressRoute z tym samym adresem IP po przejściu do trybu failover [zapoznaj się z tym artykułem](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Przykładowe kroki
Aby zautomatyzować odzyskiwanie w tym przykładzie, należy wykonać następujące czynności:

1. Postępuj zgodnie z instrukcjami, aby skonfigurować replikację.
2. Przełączenie [maszyn wirtualnych platformy Azure](azure-to-azure-tutorial-failover-failback.md)w tryb failover z dodatkowymi krokami w trakcie lub po zakończeniu pracy w trybie awaryjnym.

    a. Utwórz bramę usługi Azure ExpressRoute w sieci wirtualnej centrum regionów docelowych. Jest to potrzebne do połączenia sieci wirtualnej koncentratora docelowego z obwodem ExpressRoute.

    b. Utwórz połączenie z docelowej sieci wirtualnej z koncentratorem do docelowego obwodu usługi ExpressRoute.

    c. Skonfiguruj komunikację równorzędną wirtualnych między koncentratorem a sieciami wirtualnymi w regionie docelowym. Właściwości komunikacji równorzędnej w regionie docelowym będą takie same, jak w regionie źródłowym.

    d. Skonfiguruj UDR w sieci wirtualnej centrum i dwa szprychy sieci wirtualnych.

    - Właściwości UDR po stronie docelowej są takie same jak te, które znajdują się po stronie źródłowej przy użyciu tych samych adresów IP.
    - W przypadku różnych docelowych adresów IP UDR należy odpowiednio zmodyfikować.


Powyższe kroki można wykonać przy użyciu skryptu w ramach [planu odzyskiwania](site-recovery-create-recovery-plans.md). W zależności od wymagań dotyczących łączności i czasu odzyskiwania aplikacji powyższe kroki można także wykonać przed rozpoczęciem pracy w trybie failover.

#### <a name="after-recovery"></a>Po odzyskiwaniu

Po odzyskiwaniu maszyn wirtualnych i zakończeniu łączności środowisko odzyskiwania jest w następujący sposób.

![Lokalne na platformę Azure z usługą ExpressRoute po przejściu w tryb failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o korzystaniu z [planów odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.
