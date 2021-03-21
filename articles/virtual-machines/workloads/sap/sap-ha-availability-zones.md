---
title: Konfiguracje obciążeń SAP z Strefy dostępności platformy Azure | Microsoft Docs
description: Architektura i scenariusze wysokiej dostępności dla oprogramowania SAP NetWeaver przy użyciu Strefy dostępności platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671627"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfiguracje obciążenia SAP ze strefami dostępności platformy Azure
Oprócz wdrożenia różnych warstw architektury SAP w zestawach dostępności platformy Azure, bardziej ostatnio wprowadzone [strefy dostępności platformy Azure](../../../availability-zones/az-overview.md) można również użyć do wdrożeń obciążeń SAP. Strefa dostępności platformy Azure jest definiowana jako: "unikatowe lokalizacje fizyczne w regionie. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależną moc, chłodzenie i sieć. Strefy dostępności platformy Azure nie są dostępne we wszystkich regionach. W przypadku regionów platformy Azure, które zapewniają Strefy dostępności, sprawdź [mapę regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Ta mapa pokazuje, które regiony zapewniają lub są ogłaszane w celu zapewnienia Strefy dostępności. 

W przypadku typowej architektury SAP NetWeaver lub S/4HANA należy chronić trzy różne warstwy:

- Warstwa aplikacji SAP, która może być jedną na kilka dziesiątek maszyn wirtualnych. Chcesz zminimalizować prawdopodobieństwo wdrożenia maszyn wirtualnych na tym samym serwerze hosta. Te maszyny wirtualne należy również zapewnić akceptowalną bliskość warstwy DBMS, aby zachować opóźnienie sieci w akceptowalnym oknie
- Warstwa SAP ASCS/SCS, która reprezentuje single point of failure w architekturze SAP NetWeaver i S/4HANA. Zwykle są używane dwie maszyny wirtualne, które mają być używane przez platformę trybu failover. W związku z tym te maszyny wirtualne powinny być przydzieloną w różnych awariach infrastruktury i domenach aktualizacji
- Warstwa SAP DBMS również, która reprezentuje single point of failure. W zwykłych przypadkach składa się z dwóch maszyn wirtualnych, które są objęte strukturą trybu failover. W związku z tym te maszyny wirtualne powinny być przydzieloną w różnych awariach infrastruktury i domenach aktualizacji. Wyjątki są SAP HANA wdrożenia skalowalne w poziomie, w których można używać więcej niż dwóch maszyn wirtualnych

Główne różnice między wdrażaniem krytycznych maszyn wirtualnych za pomocą zestawów dostępności lub Strefy dostępności są następujące:

- Wdrożenie z zestawem dostępności obejmuje tworzenie maszyn wirtualnych w ramach zestawu w jednej strefie lub centrum danych (niezależnie od tego, co dotyczy danego regionu). W związku z tym wdrażanie za pomocą zestawu dostępności nie jest chronione przez problemy dotyczące mocy, chłodzenia lub sieci, które mają wpływ na dataceter strefy jako całość. Na stronie, maszyny wirtualne są wyrównane do aktualizacji i domen błędów w tej strefie lub centrum danych. W odniesieniu do warstwy SAP ASCS lub DBMS, w której chronimy dwie maszyny wirtualne w zestawie dostępności, wyrównanie z domeną błędów i aktualizacji zapobiega tym, że obie maszyny wirtualne kończą się na tym samym sprzęcie hosta 
- Wdrożenie maszyn wirtualnych za pośrednictwem Strefy dostępności platformy Azure i wybranie różnych stref (maksymalnie trzy możliwe do wykonania) powoduje wdrożenie maszyn wirtualnych w różnych lokalizacjach fizycznych i dodanie dodatkowej ochrony przed problemami z mocą, chłodzeniem lub siecią, które mają wpływ na datacetery strefy jako całość. Jednak podczas wdrażania więcej niż jednej maszyny wirtualnej tej samej rodziny maszyn wirtualnych w tej samej strefie dostępności nie ma żadnej ochrony z tych maszyn wirtualnych kończących się na tym samym hoście. W związku z tym wdrażanie za pośrednictwem Strefy dostępności jest idealnym rozwiązaniem dla warstwy SAP ASCS i DBMS, gdzie zwykle jest to dwie maszyny wirtualne. W przypadku warstwy aplikacji SAP, która może być drastycznie więcej niż dwie maszyny wirtualne, może być konieczne powrót do innego modelu wdrażania (zobacz nowszy).

Twoja motywacja do wdrożenia w Strefy dostępności platformy Azure powinna polegać na zapewnieniu awarii pojedynczej krytycznej maszyny wirtualnej lub możliwości skrócenia przestojów związanych z stosowaniem poprawek do oprogramowania w ramach krytycznych problemów związanych z infrastrukturą, które mogą mieć wpływ na dostępność jednego lub wielu centrów danych platformy Azure. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Zagadnienia dotyczące wdrażania między Strefy dostępności


Korzystając z Strefy dostępności, należy wziąć pod uwagę następujące kwestie:

- Nie ma żadnych gwarancji dotyczących odległości między różnymi Strefy dostępności w regionie świadczenia usługi Azure.
- Strefy dostępności nie są idealnym rozwiązaniem odzyskiwania po awarii. Naturalne katastrofy mogą spowodować rozległe szkody w regionach świata, w tym duże szkody związane z infrastrukturą elektryczną. Odległości między różnymi strefami mogą nie być wystarczająco duże, aby stworzyć odpowiednie rozwiązanie DR.
- Opóźnienie sieci w Strefy dostępności nie jest takie samo we wszystkich regionach świadczenia usługi Azure. W niektórych przypadkach można wdrożyć i uruchomić warstwę aplikacji SAP w różnych strefach, ponieważ opóźnienie sieci z jednej strefy do aktywnej maszyny wirtualnej systemu DBMS jest dopuszczalne. Jednak w niektórych regionach świadczenia usługi Azure opóźnienie między aktywną maszyną wirtualną DBMS a wystąpieniem aplikacji SAP w przypadku wdrożenia w różnych strefach może nie być akceptowalne dla procesów firmy SAP Business. W takich przypadkach architektura wdrożenia musi być inna, z aktywną/aktywną architekturą dla aplikacji lub aktywną/pasywną architekturą, w której opóźnienie sieci między strefami jest zbyt wysokie.
- Podczas decydowania, gdzie należy używać Strefy dostępności, należy oprzeć swoją decyzję o opóźnieniu sieci między strefami. Opóźnienie sieci odgrywa ważną rolę w dwóch obszarach:
    - Opóźnienie między dwoma wystąpieniami systemu DBMS, które muszą mieć replikację synchroniczną. Im wyższe opóźnienie sieci, tym bardziej prawdopodobnie będzie to miało wpływ na skalowalność obciążenia.
    - Różnica w opóźnieniu sieci między maszyną wirtualną z uruchomionym wystąpieniem okna dialogowego SAP a aktywnym wystąpieniem DBMS i podobną maszyną wirtualną w innej strefie. W miarę wzrostu tej różnicy wpływ na czas działania procesów biznesowych i zadań wsadowych rośnie również, zależnie od tego, czy są one uruchamiane w strefie w systemie DBMS, czy w innej strefie.

Podczas wdrażania maszyn wirtualnych platformy Azure w ramach Strefy dostępności i ustanowienia rozwiązań trybu failover w tym samym regionie świadczenia usługi Azure obowiązują pewne ograniczenia:

- Podczas wdrażania programu do Strefy dostępności platformy Azure należy użyć [usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) . 
- Mapowanie wyliczeń stref na strefy fizyczne zostało rozwiązane w oparciu o subskrypcję platformy Azure. Jeśli używasz różnych subskrypcji do wdrażania systemów SAP, musisz zdefiniować idealne strefy dla każdej subskrypcji.
- Zestawów dostępności platformy Azure nie można wdrażać w ramach strefy dostępności platformy Azure, chyba że zostanie użyta [Grupa umieszczania usługi Azure zbliżeniowe](../../co-location.md). Sposób wdrażania warstwy systemu SAP DBMS i centralnych usług w różnych strefach i w tym samym czasie wdrażają warstwę aplikacji SAP przy użyciu zestawów dostępności i nadal zbliżają się bliskość maszyn wirtualnych, które są opisane w artykule [usługi zbliżeniowe platformy Azure, aby zapewnić optymalne opóźnienie sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md). Jeśli nie korzystasz z grup umieszczania bliskości platformy Azure, musisz wybrać jedną lub drugą jako strukturę wdrażania dla maszyn wirtualnych.
- Nie można użyć [podstawowego Load Balancer platformy Azure](../../../load-balancer/load-balancer-overview.md) do tworzenia rozwiązań klastra trybu failover opartych na systemie Windows Server Failover Clustering lub Linux Pacemaker. Zamiast tego należy użyć [jednostki SKU usługa Load Balancer w warstwie Standardowa platformy Azure](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Idealna kombinacja Strefy dostępności
Jeśli chcesz wdrożyć system SAP NetWeaver lub S/4HANA w różnych strefach, istnieją dwie architektury zasad, które można wdrożyć:

- Aktywne/aktywne: para maszyn wirtualnych z systemem ASCS/SCS i para maszyn wirtualnych z uruchomioną warstwą DBMS są dystrybuowane w dwóch strefach. Liczba maszyn wirtualnych z uruchomioną warstwą aplikacji SAP jest wdrażana w liczbie parzystych w tych samych dwóch strefach. Jeśli maszyna wirtualna systemu DBMS lub ASCS/SCS jest przełączana w tryb failover, niektóre otwarte i aktywne transakcje mogą zostać wycofane. Ale użytkownicy są zarejestrowani. W rzeczywistości nie ma znaczenia, w których strefach działa aktywna maszyna wirtualna DBMS i wystąpienia aplikacji. Ta architektura jest preferowaną architekturą do wdrożenia w różnych strefach.
- Aktywne/pasywne: para maszyn wirtualnych z systemem ASCS/SCS i para maszyn wirtualnych z uruchomioną warstwą DBMS są dystrybuowane w dwóch strefach. Liczba maszyn wirtualnych z uruchomioną warstwą aplikacji SAP jest wdrażana w jednej z Strefy dostępności. Warstwa aplikacji jest uruchamiana w tej samej strefie co aktywne wystąpienie ASCS/SCS i DBMS. Ta architektura wdrażania jest używana, jeśli opóźnienie sieci między różnymi strefami jest zbyt wysokie, aby można było uruchomić warstwę aplikacji rozłożoną w obrębie stref. Zamiast tego warstwa aplikacji SAP musi działać w tej samej strefie co aktywne wystąpienie ASCS/SCS i/lub DBMS. Jeśli maszyna wirtualna ASCS/SCS lub DBMS zostanie przełączona w tryb failover do strefy pomocniczej, może wystąpić wyższy czas opóźnienia sieci i zmniejszenie przepływności. I wymagane jest powrót po awarii poprzedniej maszyny wirtualnej w trybie failover, tak szybko, jak to możliwe, aby wrócić do poprzednich poziomów przepływności. Jeśli wystąpi awaria strefy, warstwa aplikacji musi zostać przełączona w tryb failover na strefę pomocniczą. Działanie, które użytkownicy środowiska są gotowe do zamknięcia systemu. W niektórych regionach świadczenia usługi Azure ta architektura jest jedyną wykonalną architekturą, która ma być używana Strefy dostępności. Jeśli nie możesz zaakceptować potencjalnego wpływu maszyn wirtualnych ASCS/SCS lub DBMS do trybu failover z strefą pomocniczą, być może lepszym rozwiązaniem jest powiększanie wdrożenia zestawu dostępności


Przed podjęciem decyzji o sposobie używania Strefy dostępności należy określić:

- Opóźnienie sieci między trzema strefami w regionie świadczenia usługi Azure. Znajomość opóźnień sieci między strefami w regionie polega na wybraniu stref z najmniejszym opóźnieniem sieci w ruchu sieciowym między strefami.
- Różnica między opóźnieniami między MASZYNami wirtualnymi w ramach jednej z tych stref, wybór i opóźnienie sieci w dwóch strefach wyboru.
- Określenie, czy typy maszyn wirtualnych, które należy wdrożyć, są dostępne w dwóch wybranych strefach. W przypadku niektórych maszyn wirtualnych, w szczególności maszyn wirtualnych serii M, mogą wystąpić sytuacje, w których niektóre jednostki SKU są dostępne tylko w dwóch z tych trzech stref.

## <a name="network-latency-between-and-within-zones"></a>Opóźnienie sieci między i w obrębie stref
Aby określić opóźnienie między różnymi strefami, należy wykonać następujące:

- Wdróż jednostkę SKU maszyny wirtualnej, której chcesz użyć dla wystąpienia systemu DBMS we wszystkich trzech strefach. Upewnij się, że [usługa Azure przyspieszone sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona po przemierzeniu tej miary.
- Po znalezieniu dwóch stref o najmniejszej opóźnieniu sieci należy wdrożyć inne trzy maszyny wirtualne jednostki SKU maszyny wirtualnej, która ma być używana jako maszyna wirtualna warstwy aplikacji dla trzech Strefy dostępności. Zmierz opóźnienie sieci w odniesieniu do dwóch maszyn wirtualnych systemu DBMS w dwóch wybranych strefach DBMS. 
- Użyj **`niping`** jako narzędzia pomiarowego. To narzędzie z oprogramowania SAP jest opisane w uwagi dotyczące pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Skup się na poleceniach udokumentowanych dla pomiarów opóźnienia. Ponieważ **polecenie ping** nie działa przez przyspieszone ścieżki kodu sieciowego platformy Azure, nie zalecamy jej używania.

Nie musisz wykonywać tych testów ręcznie. Można znaleźć [test opóźnienia strefy dostępności](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) procedury programu PowerShell, który automatyzuje testy opóźnienia opisane poniżej. 

W oparciu o pomiary i dostępność jednostek SKU maszyn wirtualnych w Strefy dostępności należy podjąć pewne decyzje:

- Zdefiniuj idealne strefy dla warstwy DBMS.
- Określ, czy chcesz rozpowszechnić aktywną warstwę aplikacji SAP w jednej, dwóch lub wszystkich trzech strefach, w zależności od różnic między strefami w strefie.
- Ustal, czy chcesz wdrożyć aktywną/pasywną konfigurację lub aktywną/aktywną konfigurację z punktu widzenia aplikacji. (Te konfiguracje są wyjaśnione w dalszej części tego artykułu).

W podejmowaniu tych decyzji należy również wziąć pod uwagę zalecenia dotyczące opóźnień sieci SAP, zgodnie z opisem w temacie SAP uwagi [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Wprowadzone pomiary i decyzje są prawidłowe dla subskrypcji platformy Azure, która została użyta podczas przeprowadzonych pomiarów. Jeśli używasz innej subskrypcji platformy Azure, musisz powtórzyć pomiary. Mapowanie wyliczonych stref może być inne dla innej subskrypcji platformy Azure.


> [!IMPORTANT]
> Oczekuje się, że pomiary opisane wcześniej będą zapewniały różne wyniki w każdym regionie świadczenia usługi Azure, który obsługuje [strefy dostępności](https://azure.microsoft.com/global-infrastructure/geographies/). Nawet jeśli wymagania dotyczące opóźnień sieci są takie same, może być konieczne przyjęcie różnych strategii wdrażania w różnych regionach świadczenia usługi Azure, ponieważ opóźnienie sieci między strefami może być różne. W niektórych regionach świadczenia usługi Azure opóźnienie sieci między trzema różnymi strefami może być bardzo zróżnicowane. W innych regionach opóźnienie sieci między trzema różnymi strefami może być bardziej jednorodne. To, że zawsze opóźnienie sieci należy do przedziału od 1 do 2 milisekund. Nie można uogólnionie opóźniać sieci między Strefy dostępności w regionach platformy Azure.

## <a name="activeactive-deployment"></a>Wdrożenie aktywne/aktywne
Ta architektura wdrażania jest nazywana aktywnym/aktywnym ze względu na wdrożenie aktywnych serwerów aplikacji SAP w dwóch lub trzech strefach. Wystąpienie usług SAP Central, które używa replikacji z kolejki zostanie wdrożone między dwoma strefami. Ta sama wartość dotyczy warstwy DBMS, która zostanie wdrożona w tych samych strefach co usługa SAP Central. Biorąc pod uwagę tę konfigurację, należy znaleźć dwie Strefy dostępności w Twoim regionie, które oferują opóźnienia sieci między strefami, które są akceptowalne dla obciążenia i synchronicznej replikacji systemu DBMS. Należy również upewnić się, że różnica między opóźnieniami sieci w wybranych strefach i opóźnieniu sieci między strefami nie jest zbyt duża. 

Istotą architektury SAP jest to, o ile nie zostanie on skonfigurowany inaczej, użytkownicy i zadania wsadowe mogą być wykonywane w różnych wystąpieniach aplikacji. Efektem ubocznym tego faktu z aktywnym/aktywnym wdrożeniem jest to, że zadania wsadowe mogą być wykonywane przez dowolne wystąpienia aplikacji SAP niezależnie od tego, czy są uruchamiane w tej samej strefie z aktywnym systemem DBMS, czy nie. Jeśli różnica w opóźnieniu sieci między strefami różnic jest mała w porównaniu z opóźnieniami sieci w strefie, różnica w czasie wykonywania zadań wsadowych może nie być znacząca. Jednak im większa różnica opóźnienia sieci w obrębie strefy w porównaniu z ruchem sieciowym strefy to, a czas wykonywania zadań wsadowych może mieć wpływ na więcej, jeśli zadanie zostało wykonane w strefie, w której wystąpienie systemu DBMS nie jest aktywne. Jest to klient, który chce określić akceptowalne różnice w czasie wykonywania. I z tym, co jest dopuszczalnym opóźnieniem sieci dla ruchu między strefami.

Regiony platformy Azure, w których takie wdrożenie aktywne/aktywne powinno być możliwe bez dużych różnic w czasie wykonywania i przepływności w ramach warstwy aplikacji wdrożonej na różnych Strefy dostępności, na przykład:

- Zachodnie stany USA 2 (wszystkie trzy strefy)
- East stany USA 2 (wszystkie trzy strefy)
- Środkowe stany USA (wszystkie trzy strefy)
- Europa Północna (wszystkie trzy strefy)
- Europa Zachodnia (dwie z trzech stref)
- Wschodnie stany USA (dwie z trzech stref)
- Południowo-środkowe stany USA (dwie z trzech stref)
- Południowe Zjednoczone Królestwo (dwie z trzech stref)

Regiony platformy Azure, w których nie jest zalecana architektura wdrażania SAP w różnych strefach, są następujące:

- Francja Środkowa 
- Północna Republika Południowej Afryki
- Kanada Środkowa
- Japonia Wschodnia

W zależności od tego, co zgadzasz się na różnice w czasie wykonywania, nie można zakwalifikować się również do innych regionów.


Uproszczony schemat aktywnego/aktywnego wdrożenia w dwóch strefach może wyglądać następująco:

![Wdrożenie strefy aktywne/aktywne](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

W tej konfiguracji są stosowane następujące zagadnienia:

- Nie przy użyciu [grupy umieszczania usługi Azure zbliżeniowe](../../co-location.md), strefy dostępności platformy Azure jako domeny błędów i aktualizacji dla wszystkich maszyn wirtualnych, ponieważ zestawy dostępności nie mogą zostać wdrożone w strefy dostępności platformy Azure.
- Jeśli chcesz połączyć wdrożenia stref dla warstwy DBMS i usług centralnych, ale chcesz korzystać z zestawów dostępności platformy Azure dla warstwy aplikacji, musisz użyć grup bliskości platformy Azure, zgodnie z opisem w artykule [grupy umieszczenia bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md).
- W przypadku modułów równoważenia obciążenia klastrów pracy awaryjnej usług SAP Central i warstwy DBMS należy użyć [standardowej jednostki SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Podstawowa Load Balancer nie będzie działała między strefami.
- Sieć wirtualna platformy Azure wdrożona w celu hostowania systemu SAP wraz z jego podsieciami jest rozciągana między strefami. Dla każdej strefy nie są potrzebne oddzielne sieci wirtualne.
- W przypadku wszystkich wdrażanych maszyn wirtualnych należy użyć [usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane dla wdrożeń strefowych.
- Usługi Azure Premium Storage, [SSD w warstwie Ultra Storage](../../disks-types.md#ultra-disk)i ANF nie obsługują żadnego typu replikacji magazynu w różnych strefach. Aplikacja (system DBMS lub SAP Central Services) musi replikować ważne dane.
- Ta sama wartość dotyczy udostępnionego katalogu sapmnt, który jest dyskiem współdzielonym (Windows), udziałem CIFS (Windows) lub udziałem NFS (Linux). Należy użyć technologii, która replikuje te udostępnione dyski lub udziały między strefami. Te technologie są obsługiwane:
  - W przypadku systemu Windows rozwiązanie klastra korzystające z usługi oprogramowanie SIOS DataKeeper, zgodnie z opisem w [klastrze, wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - W przypadku systemu SUSE Linux udział NFS, który jest skompilowany jako opisany w [wysokiej dostępności dla systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    Obecnie rozwiązanie, które korzysta z serwera plików Microsoft Scale-Out, zgodnie z opisem w temacie [Przygotowanie infrastruktury platformy Azure do obsługi oprogramowania SAP wysokiej dostępności przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nie jest obsługiwane w różnych strefach.
- Trzecia Strefa jest używana do hostowania urządzenia SBD, jeśli tworzysz klaster systemu [SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) i korzystasz z urządzeń SBD, a nie z Agenta ogrodzenia platformy Azure. Lub w przypadku dodatkowych wystąpień aplikacji.
- Aby osiągnąć spójność czasu wykonywania dla krytycznych procesów firmy, możesz spróbować skierować określone zadania usługi Batch i użytkowników do wystąpień aplikacji znajdujących się w strefie z aktywnym wystąpieniem DBMS przy użyciu grup serwera SAP Batch, grup logowania SAP lub grup RFC. Jednak w przypadku trybu failover strefy należy ręcznie przenieść te grupy do wystąpień uruchomionych na maszynach wirtualnych, które znajdują się w strefie z maszyną wirtualną usługi Active DB.  
- Możesz chcieć wdrożyć nieaktywne wystąpienia okna dialogowego w każdej z tych stref. 

> [!IMPORTANT]
> W tym scenariuszu aktywny/aktywny dodatkowe opłaty za przepustowość są ogłaszane przez firmę Microsoft z 04/01/2020 w dniu. Sprawdź [szczegóły cennika dotyczącego przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/). Transfer danych między warstwą aplikacji SAP i warstwą SAP DBMS jest dość intensywnie. W związku z tym scenariusz aktywny/aktywny może przyczynić się do ponoszenia kosztów. Kontynuuj sprawdzanie tego artykułu, aby uzyskać dokładne koszty 


## <a name="activepassive-deployment"></a>Wdrożenie aktywne/pasywne
Jeśli nie możesz znaleźć akceptowalnej różnicy między opóźnieniem sieci w jednej strefie a opóźnieniem ruchu sieciowego między strefami, możesz wdrożyć architekturę, która ma aktywny/pasywny znak z punktu widzenia warstwy aplikacji SAP. Należy zdefiniować *aktywną* strefę, czyli strefę, w której wdrażana jest kompletna warstwa aplikacji, oraz miejsce, w którym próbujesz uruchomić zarówno aktywny system DBMS, jak i wystąpienie usług SAP Central Services. W przypadku takiej konfiguracji należy upewnić się, że nie masz skrajnych różnic czasu wykonywania, w zależności od tego, czy zadanie jest uruchamiane w strefie z aktywnym wystąpieniem systemu DBMS, czy nie, w transakcjach roboczych i zadaniach wsadowych.

Regiony platformy Azure, w których może być preferowany typ architektury wdrożenia w różnych strefach, są następujące:

- Southeast Asia
- Australia Wschodnia
- Brazylia Południowa
- Niemcy Środkowo-Zachodnie
- Północna Republika Południowej Afryki
- Francja Środkowa 
- Kanada Środkowa
- Japonia Wschodnia


Podstawowy układ architektury wygląda następująco:

![Wdrożenie strefy aktywne/pasywne](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

W tej konfiguracji są stosowane następujące zagadnienia:

- Zestawów dostępności nie można wdrożyć w Strefy dostępności platformy Azure. Aby wyrównać tę wartość, możesz użyć grup umieszczania sąsiedztwa platformy Azure, jak opisano w temacie [grupy umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md).
- W przypadku korzystania z tej architektury należy uważnie monitorować stan i próbować utrzymywać aktywne wystąpienia usług DBMS i SAP Central w tej samej strefie co wdrożona warstwa aplikacji. W przypadku przejścia w tryb failover usługi SAP Central lub systemu DBMS, należy się upewnić, że można ręcznie wrócić do strefy z użyciem warstwy aplikacji SAP wdrożonej jak najszybciej.
- W przypadku modułów równoważenia obciążenia klastrów pracy awaryjnej usług SAP Central i warstwy DBMS należy użyć [standardowej jednostki SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Podstawowa Load Balancer nie będzie działała między strefami.
- Sieć wirtualna platformy Azure wdrożona w celu hostowania systemu SAP wraz z jego podsieciami jest rozciągana między strefami. Dla każdej strefy nie są potrzebne oddzielne sieci wirtualne.
- W przypadku wszystkich wdrażanych maszyn wirtualnych należy użyć [usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane dla wdrożeń strefowych.
- Usługi Azure Premium Storage, [SSD w warstwie Ultra Storage](../../disks-types.md#ultra-disk)i ANF nie obsługują żadnego typu replikacji magazynu w różnych strefach. Aplikacja (system DBMS lub SAP Central Services) musi replikować ważne dane.
- Ta sama wartość dotyczy udostępnionego katalogu sapmnt, który jest dyskiem współdzielonym (Windows), udziałem CIFS (Windows) lub udziałem NFS (Linux). Należy użyć technologii, która replikuje te udostępnione dyski lub udziały między strefami. Te technologie są obsługiwane:
    - W przypadku systemu Windows rozwiązanie klastra korzystające z usługi oprogramowanie SIOS DataKeeper, zgodnie z opisem w [klastrze, wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - W przypadku systemu SUSE Linux udział NFS, który jest skompilowany jako opisany w [wysokiej dostępności dla systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  Obecnie rozwiązanie, które korzysta z serwera plików Microsoft Scale-Out, zgodnie z opisem w temacie [Przygotowanie infrastruktury platformy Azure do obsługi oprogramowania SAP wysokiej dostępności przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nie jest obsługiwane w różnych strefach.
- Trzecia Strefa jest używana do hostowania urządzenia SBD, jeśli tworzysz klaster systemu [SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) i korzystasz z urządzeń SBD, a nie z Agenta ogrodzenia platformy Azure. Lub w przypadku dodatkowych wystąpień aplikacji.
- Należy wdrożyć uśpione maszyny wirtualne w strefie pasywnej (z punktu widzenia systemu DBMS), aby umożliwić uruchamianie zasobów aplikacji w przypadku awarii strefy.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) nie jest obecnie w stanie replikować aktywnych maszyn wirtualnych do nieaktywnych maszyn wirtualnych między strefami. 
- Należy zainwestować w automatyzację, która umożliwia automatyczne uruchamianie warstwy aplikacji SAP w drugiej strefie, jeśli wystąpi awaria strefy.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Połączona Konfiguracja wysokiej dostępności i odzyskiwania po awarii
Firma Microsoft nie udostępnia żadnych informacji o odległościach geograficznych między obiektami, które obsługują różne Strefy dostępności platformy Azure w regionie świadczenia usługi Azure. Mimo że niektórzy klienci używają stref dla połączonej konfiguracji HA i DR, która niesie obietnice zwiększenia cel punktu odzyskiwania (RPO) o wartości zero. Punkt odzyskiwania równy zero oznacza, że nie należy tracić żadnych zakontraktowanych transakcji bazy danych nawet w przypadku odzyskiwania po awarii. 

> [!NOTE]
> Zalecamy użycie konfiguracji podobnej do tej w pewnych okolicznościach. Można na przykład użyć go, gdy dane nie mogą opuścić regionu platformy Azure ze względów bezpieczeństwa lub zgodności. 

Oto przykład tego, jak taka konfiguracja może wyglądać w następujący sposób:

![Połączenie odzyskiwania po awarii o wysokiej dostępności w strefach](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

W tej konfiguracji są stosowane następujące zagadnienia:

- Przyjęto założenie, że istnieje znacząca odległość między obiektami, które obsługują strefę dostępności, lub że wymuszone pozostawanie w obrębie pewnego regionu świadczenia usługi Azure. Zestawów dostępności nie można wdrożyć w Strefy dostępności platformy Azure. Aby wyrównać tę wartość, możesz użyć grup umieszczania sąsiedztwa platformy Azure, jak opisano w temacie [grupy umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md).
- W przypadku korzystania z tej architektury należy uważnie monitorować stan i próbować utrzymywać aktywne wystąpienia usług DBMS i SAP Central w tej samej strefie co wdrożona warstwa aplikacji. W przypadku przejścia w tryb failover usługi SAP Central lub systemu DBMS, należy się upewnić, że można ręcznie wrócić do strefy z użyciem warstwy aplikacji SAP wdrożonej jak najszybciej.
- Na maszynach wirtualnych, na których działają aktywne wystąpienia aplikacji usługi pytań i odpowiedzi, powinny być wstępnie zainstalowane wystąpienia aplikacji produkcyjnej.
- W przypadku niepowodzenia w strefach Zamknij wystąpienia aplikacji do kontroli jakości i zamiast tego Uruchom wystąpienia produkcyjne. Aby to zrobić, należy użyć nazw wirtualnych dla wystąpień aplikacji.
- W przypadku modułów równoważenia obciążenia klastrów pracy awaryjnej usług SAP Central i warstwy DBMS należy użyć [standardowej jednostki SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Podstawowa Load Balancer nie będzie działała między strefami.
- Sieć wirtualna platformy Azure wdrożona w celu hostowania systemu SAP wraz z jego podsieciami jest rozciągana między strefami. Dla każdej strefy nie są potrzebne oddzielne sieci wirtualne.
- W przypadku wszystkich wdrażanych maszyn wirtualnych należy użyć [usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane dla wdrożeń strefowych.
- Usługi Azure Premium Storage i [SSD w warstwie Ultra Storage](../../disks-types.md#ultra-disk) nie obsługują żadnego typu replikacji magazynu w różnych strefach. Aplikacja (system DBMS lub SAP Central Services) musi replikować ważne dane.
- Ta sama wartość dotyczy udostępnionego katalogu sapmnt, który jest dyskiem współdzielonym (Windows), udziałem CIFS (Windows) lub udziałem NFS (Linux). Należy użyć technologii, która replikuje te udostępnione dyski lub udziały między strefami. Te technologie są obsługiwane:
    - W przypadku systemu Windows rozwiązanie klastra korzystające z usługi oprogramowanie SIOS DataKeeper, zgodnie z opisem w [klastrze, wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - W przypadku systemu SUSE Linux udział NFS, który jest skompilowany jako opisany w [wysokiej dostępności dla systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Obecnie rozwiązanie, które korzysta z serwera plików Microsoft Scale-Out, zgodnie z opisem w temacie [Przygotowanie infrastruktury platformy Azure do obsługi oprogramowania SAP wysokiej dostępności przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nie jest obsługiwane w różnych strefach.
- Trzecia Strefa jest używana do hostowania urządzenia SBD, jeśli tworzysz klaster systemu [SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) i korzystasz z urządzeń SBD, a nie z Agenta ogrodzenia platformy Azure. 





## <a name="next-steps"></a>Następne kroki
Oto kilka następnych kroków do wdrożenia między Strefy dostępności platformy Azure:

- [Klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Przygotowanie infrastruktury platformy Azure pod kątem wysokiej dostępności SAP przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień oprogramowania SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)