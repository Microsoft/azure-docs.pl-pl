---
title: SAP HANA dostępność w ramach jednego regionu świadczenia usługi Azure | Microsoft Docs
description: Opisuje operacje SAP HANA na natywnych maszynach wirtualnych platformy Azure w jednym regionie świadczenia usługi Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c706ba6847334648fade1e8983e00433d3fa618
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978207"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA dostępność w ramach jednego regionu świadczenia usługi Azure
W tym artykule opisano kilka scenariuszy dostępności w ramach jednego regionu świadczenia usługi Azure. Platforma Azure ma wiele regionów, rozmieszczonych na całym świecie. Aby zapoznać się z listą regionów świadczenia usługi Azure, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/). Do wdrażania SAP HANA na maszynach wirtualnych w ramach jednego regionu świadczenia usługi Azure firma Microsoft oferuje wdrożenie pojedynczej maszyny wirtualnej z wystąpieniem platformy HANA. Aby zwiększyć dostępność, można wdrożyć dwie maszyny wirtualne z dwoma wystąpieniami HANA w ramach [zestawu dostępności platformy Azure](../../windows/tutorial-availability-sets.md) korzystającego z replikacji systemu Hana w celu zapewnienia dostępności. 

Obecnie platforma Azure oferuje [strefy dostępności platformy Azure](../../../availability-zones/az-overview.md). W tym artykule nie opisano szczegółowo Strefy dostępności. Obejmuje to również ogólną dyskusję na temat używania zestawów dostępności, a Strefy dostępności.

Regiony platformy Azure, w których Strefy dostępności są oferowane, mają wiele centrów danych. Centra danych są niezależne w dostawie źródła, chłodzenia i sieci elektrycznej. Powodem oferowania różnych stref w ramach jednego regionu świadczenia usługi Azure jest wdrożenie aplikacji na dwóch lub trzech oferowanych Strefy dostępności. Wdrożenie między strefami, problemy związane z obsługą i siecią wpływające na tylko jedną infrastrukturę strefy dostępności platformy Azure, wdrożenie aplikacji w regionie świadczenia usługi Azure jest nadal funkcjonalne. Może dojść do mniejszej pojemności. Na przykład maszyny wirtualne w jednej strefie mogą zostać utracone, ale maszyny wirtualne w pozostałych dwóch strefach nadal będą działać. 
 
Zestaw dostępności platformy Azure to logiczna funkcja grupowania, która pomaga zapewnić, że zasoby maszyn wirtualnych znajdujące się w zestawie dostępności są odizolowane od siebie, gdy zostaną wdrożone w centrum danych platformy Azure. Maszyny wirtualne platformy Azure umieszczone w zestawie dostępności korzystają z wielu serwerów fizycznych, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. W niektórych dokumentacji platformy Azure ta konfiguracja jest określana jako umieszczenie w różnych [domenach aktualizacji i błędów](../../manage-availability.md). Te miejsca zwykle znajdują się w centrum danych platformy Azure. Przy założeniu, że problemy ze źródłem mocy i sieci wpłyną na wdrażane centrum danych, będzie to miało wpływ na wszystkie możliwości w jednym regionie świadczenia usługi Azure.

Rozmieszczenie centrów danych, które reprezentują Strefy dostępności platformy Azure, stanowi kompromis między zapewnieniem akceptowalnego opóźnienia sieci między usługami wdrożonymi w różnych strefach i odległości między centrami danych. Naturalny awarią idealnie nie wpływa na zasilanie, dostarczenie sieci i infrastrukturę dla wszystkich Strefy dostępności w tym regionie. Jednakże, podobnie jak ogromne Natural awarią, Strefy dostępności może nie zapewniać dostępności w obrębie jednego regionu. Pomyśl o huragan Maria, który trafił na wyspa Portoryko 20 września 2017. Huragan zasadniczo spowodowało niemal 100 procent niedostępności na wyspach o szerokości 90-milowej.

## <a name="single-vm-scenario"></a>Scenariusz z jedną maszyną wirtualną

W scenariuszu z jedną maszyną wirtualną tworzysz maszynę wirtualną platformy Azure dla wystąpienia SAP HANA. Usługa Azure Premium Storage umożliwia hostowanie dysku systemu operacyjnego i wszystkich dysków z danymi. Umowa SLA do czasu działania platformy Azure wynosi 99,9%, a umowy SLA innych składników platformy Azure jest wystarczająca, aby można było zrealizować umowy SLA dostępności dla klientów. W tym scenariuszu nie ma potrzeby korzystania z zestawu dostępności platformy Azure dla maszyn wirtualnych z uruchomioną warstwą DBMS. W tym scenariuszu polegasz na dwóch różnych funkcjach:

- Ponowne uruchamianie maszyny wirtualnej platformy Azure (nazywane również naprawianiem usługi platformy Azure)
- SAP HANA ponowne uruchomienie

Funkcja autostartu maszyny wirtualnej platformy Azure lub naprawianie usługi jest funkcją platformy Azure, która działa na dwóch poziomach:

- Host serwera platformy Azure sprawdza kondycję maszyny wirtualnej hostowanej na hoście serwera.
- Kontroler sieci szkieletowej platformy Azure monitoruje kondycję i dostępność hosta serwera.

Funkcja sprawdzania kondycji monitoruje kondycję każdej maszyny wirtualnej hostowanej na hoście serwera platformy Azure. Jeśli maszyna wirtualna przestanie działać w stanie nieprawidłowym, ponowne uruchomienie maszyny wirtualnej może zostać zainicjowane przez agenta hosta platformy Azure, który sprawdza kondycję maszyny wirtualnej. Kontroler sieci szkieletowej sprawdza kondycję hosta, sprawdzając wiele różnych parametrów, które mogą wskazywać na problemy z sprzętem hosta. Sprawdza również dostępność hosta za pośrednictwem sieci. Wskazanie problemów z hostem może prowadzić do następujących zdarzeń:

- Jeśli host sygnalizuje zły stan kondycji, zostanie wyzwolone ponowne uruchomienie hosta i ponowne uruchomienie maszyn wirtualnych uruchomionych na hoście.
- Jeśli host nie jest w dobrej kondycji po pomyślnym ponownym uruchomieniu, zainicjowano ponowne wdrożenie maszyn wirtualnych, które znajdowały się początkowo w węźle w dobrej kondycji na serwerze hosta z kondycją. W takim przypadku oryginalny host jest oznaczony jako nie w dobrej kondycji. Nie będzie on używany do dalszych wdrożeń, dopóki nie zostanie wyczyszczone lub zastąpione.
- Jeśli host w złej kondycji ma problemy podczas ponownego uruchamiania, zostanie wyzwolone natychmiastowe ponowne uruchomienie maszyn wirtualnych na hoście w dobrej kondycji. 

Dzięki monitorowaniu hosta i maszyn wirtualnych na platformie Azure maszyny wirtualne platformy Azure, które napotykają problemy z hostem, są automatycznie uruchamiane ponownie na dobrym hoście platformy Azure. 

>[!IMPORTANT]
>Naprawianie usługi platformy Azure nie spowoduje ponownego uruchomienia maszyn wirtualnych z systemem Linux, w których system operacyjny gościa jest w stanie awaryjnego jądra. Domyślne ustawienia powszechnie używanych wydań systemu Linux nie automatycznie ponownie uruchamiają maszyn wirtualnych ani serwera, na którym jądro systemu Linux jest w stanie awaryjnego. Zamiast tego domyślne przewidywane jest zachowanie systemu operacyjnego w stanie awaryjnego jądra, aby można było dołączać debuger jądra do analizy. System Azure jest uznawany za takie zachowanie, ponieważ nie powoduje automatycznego ponownego uruchomienia maszyny wirtualnej w systemie operacyjnym gościa w takim stanie. Przyjęto, że takie wystąpienia są bardzo rzadko. Można zastąpić domyślne zachowanie, aby umożliwić ponowne uruchomienie maszyny wirtualnej. Aby zmienić zachowanie domyślne, Włącz parametr "jądro. awaryjnego" w/etc/sysctl.conf. Czas ustawiony dla tego parametru jest w sekundach. Typowe zalecane wartości oczekują na 20-30 sekund przed wyzwoleniem ponownego uruchomienia za pomocą tego parametru. Zobacz też <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

Druga funkcja, z której korzystasz w tym scenariuszu, to fakt, że usługa HANA uruchamiana w ponownie uruchomionej maszynie wirtualnej jest uruchamiana automatycznie po ponownym uruchomieniu maszyny wirtualnej. Usługę Hana można skonfigurować do [samoobsługowego ponownego uruchomienia](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) za pomocą usług licznika alarmowego różnych usług platformy Hana.

Ten scenariusz dla jednej maszyny wirtualnej można ulepszyć, dodając do konfiguracji SAP HANA węzeł zimnej pracy w trybie failover. W dokumentacji SAP HANA ta konfiguracja jest nazywana automatycznie przełączeniem [do trybu failover](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Taka konfiguracja może mieć sens w przypadku sytuacji, w której serwer jest ograniczony, i można ustawić węzeł jednego serwera jako węzeł automatycznej pracy awaryjnej hosta dla zestawu hostów produkcyjnych. Jednak na platformie Azure, w której podstawowa infrastruktura platformy Azure zapewnia dobry serwer docelowy dla pomyślnego ponownego uruchomienia maszyny wirtualnej, nie ma sensu wdrażania SAP HANA przełączenia w tryb failover hosta. Ze względu na Naprawianie usługi platformy Azure nie ma architektury referencyjnej, która przewiduje węzeł w stanie wstrzymania dla automatycznie przełączenia w tryb failover. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Specjalny przypadek SAP HANA konfiguracji skalowalnych w poziomie na platformie Azure
Wysoka dostępność SAP HANA skalowalnych w poziomie konfiguracji polega na Naprawianie usługi maszyn wirtualnych platformy Azure i ponownym uruchomieniu wystąpienia SAP HANA, gdy maszyna wirtualna jest uruchomiona ponownie. Architektury wysokiej dostępności oparte na replikacji systemu HANA są wprowadzane w późniejszym czasie. 


## <a name="availability-scenarios-for-two-different-vms"></a>Scenariusze dostępności dla dwóch różnych maszyn wirtualnych

Jeśli używasz dwóch maszyn wirtualnych platformy Azure w ramach zestawu dostępności platformy Azure, możesz zwiększyć czas działania między tymi dwiema maszynami wirtualnymi, jeśli są one umieszczone w zestawie dostępności platformy Azure w ramach jednego regionu świadczenia usługi Azure. Podstawowa konfiguracja na platformie Azure będzie wyglądać następująco:

![Diagram dwóch maszyn wirtualnych ze wszystkimi warstwami](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Aby zilustrować różne scenariusze dostępności, niektóre warstwy na diagramie są pomijane. Na diagramie są wyświetlane tylko warstwy, które przedstawiają maszyny wirtualne, hosty, zestawy dostępności i regiony platformy Azure. Wystąpienia Virtual Network platformy Azure, grupy zasobów i subskrypcje nie odgrywają roli w scenariuszach opisanych w tej sekcji.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikowanie kopii zapasowych na drugą maszynę wirtualną

Jedną z najbardziej podstawoweych konfiguracji jest użycie kopii zapasowych. W szczególności mogą istnieć kopie zapasowe dziennika transakcji wysłane z jednej maszyny wirtualnej do innej maszyny wirtualnej platformy Azure. Możesz wybrać typ usługi Azure Storage. W tej konfiguracji użytkownik jest odpowiedzialny za wykonywanie skryptów kopii zaplanowanych kopii zapasowych, które są wykonywane na pierwszej maszynie wirtualnej na drugiej maszynie wirtualnej. Jeśli musisz użyć drugiego wystąpienia maszyn wirtualnych, musisz przywrócić pełne, przyrostowe/różnicowe kopie zapasowe dziennika transakcji do wymaganego momentu. 

Architektura wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacją magazynu](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Ten Instalator nie jest dobrze dostosowany do osiągnięcia doskonałego celu punktu odzyskiwania (RPO) i celu czasu odzyskiwania (RTO). W szczególności RTO się z powodu konieczności pełnego przywrócenia kompletnej bazy danych przy użyciu skopiowanych kopii zapasowych. Ta konfiguracja jest jednak przydatna do odzyskiwania z niezamierzonych usunięć danych w głównych wystąpieniach. Za pomocą tej konfiguracji można w dowolnym momencie przywrócić do określonego punktu w czasie, wyodrębnić dane i zaimportować usunięte dane do wystąpienia głównego. W związku z tym warto używać metody kopiowania kopii zapasowych w połączeniu z innymi funkcjami wysokiej dostępności. 

Podczas kopiowania kopii zapasowych może być możliwe użycie mniejszej maszyny wirtualnej niż główna maszyna wirtualna, na której działa wystąpienie SAP HANA. Pamiętaj, że możesz dołączyć mniejszą liczbę wirtualnych dysków twardych do mniejszych maszyn wirtualnych. Aby uzyskać informacje na temat limitów poszczególnych typów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikacja systemu SAP HANA bez automatycznej pracy awaryjnej

Scenariusze opisane w tej sekcji wykorzystują replikację systemu SAP HANA. Aby uzyskać dokumentację oprogramowania SAP, zobacz [Replikacja systemu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scenariusze bez automatycznej pracy awaryjnej nie są wspólne dla konfiguracji w ramach jednego regionu świadczenia usługi Azure. Konfiguracja bez automatycznej pracy awaryjnej, chociaż uniknięcie instalacji Pacemaker wymaga ręcznego monitorowania i przełączania do trybu failover. Ponieważ jest to potrzebne, większość klientów opiera się na naprawianiu usługi platformy Azure. Istnieje kilka przypadków, w których ta konfiguracja może pomóc w scenariuszach niepowodzeń. Lub w niektórych przypadkach klient może chcieć zwiększyć wydajność.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA replikację systemu bez przełączenia do trybu failover i bez wstępnego ładowania danych

W tym scenariuszu używasz replikacji systemu SAP HANA do przenoszenia danych w sposób synchroniczny, aby osiągnąć cel punktu odzyskiwania równy 0. Z drugiej strony masz wystarczająco dużo RTO, że nie potrzebujesz przełączenia w tryb failover lub wstępnego ładowania danych do pamięci podręcznej wystąpienia HANA. W takim przypadku możliwe jest osiągnięcie dalszej gospodarki konfiguracją przez podejmowanie następujących działań:

- Uruchom inne wystąpienie SAP HANA na drugiej maszynie wirtualnej. W przypadku wystąpienia SAP HANA na drugiej maszynie wirtualnej większość pamięci jest większa. W przypadku przejścia w tryb failover do drugiej maszyny wirtualnej należy zamknąć uruchomione wystąpienie SAP HANA, które ma dane w pełni załadowane na drugiej maszynie wirtualnej, tak aby zreplikowane dane mogły zostać załadowane do pamięci podręcznej wystąpienia obiektu HANA w drugiej maszynie wirtualnej.
- Użyj mniejszego rozmiaru maszyny wirtualnej na drugiej maszynie wirtualnej. Jeśli nastąpi przejście w tryb failover, masz dodatkowy krok przed ręcznym trybem failover. W tym kroku zostanie wprowadzona zmiana rozmiaru maszyny wirtualnej na rozmiar źródłowej maszyny wirtualnej. 
 
Scenariusz wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacją magazynu](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Nawet jeśli nie korzystasz z wstępnego ładowania danych w celu replikacji systemu HANA, potrzebujesz co najmniej 64 GB pamięci. Potrzeba również wystarczającej ilości pamięci oprócz 64 GB, aby zachować dane magazynu wierszy w pamięci wystąpienia docelowego.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA replikację systemu bez przełączenia do trybu failover i wstępnego ładowania danych

W tym scenariuszu dane, które są replikowane do wystąpienia HANA w drugiej maszynie wirtualnej, są wstępnie załadowane. Eliminuje to dwie korzyści wynikające z braku wstępnego ładowania danych. W takim przypadku nie można uruchomić innego systemu SAP HANA na drugiej maszynie wirtualnej. Nie można również użyć mniejszego rozmiaru maszyny wirtualnej. W związku z tym klienci rzadko implementują ten scenariusz.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikacja systemu SAP HANA z automatycznym trybem failover

W przypadku standardowej i najbardziej typowej konfiguracji dostępności w ramach jednego regionu świadczenia usługi Azure na dwóch maszynach wirtualnych platformy Azure z systemem SLES Linux jest zdefiniowany klaster trybu failover. Klaster SLES Linux jest oparty na platformie [Pacemaker](./high-availability-guide-suse-pacemaker.md) , w połączeniu z urządzeniem [STONITH](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) . 

Z perspektywy SAP HANA jest synchronizowany używany tryb replikacji i zostanie skonfigurowana automatyczna praca awaryjna. Na drugiej maszynie wirtualnej wystąpienie SAP HANA działa jako węzeł rezerwy aktywnej. Węzeł gotowości odbiera synchroniczny strumień rekordów zmian z wystąpienia podstawowego SAP HANA. W miarę jak transakcje są zatwierdzane przez aplikację w węźle podstawowym HANA, podstawowy węzeł HANA oczekuje na potwierdzenie zatwierdzenia aplikacji do momentu potwierdzenia przez węzeł pomocniczy SAP HANA, że otrzymał rekord zatwierdzania. SAP HANA oferuje dwa tryby replikacji synchronicznej. Aby uzyskać szczegółowe informacje i opis różnic między tymi dwoma trybami replikacji synchronicznej, zapoznaj się z tematem [tryby replikacji artykułu SAP na potrzeby replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Ogólna konfiguracja wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacją magazynu i trybem failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Możesz wybrać to rozwiązanie, ponieważ umożliwia osiągnięcie celu punktu odzyskiwania = 0 i niskiej RTO. Skonfiguruj SAP HANA łączności klienta, aby klienci SAP HANA używali wirtualnego adresu IP do nawiązywania połączenia z konfiguracją replikacji systemu HANA. Taka konfiguracja eliminuje konieczność ponownego skonfigurowania aplikacji w przypadku przełączenia w tryb failover do węzła pomocniczego. W tym scenariuszu jednostki SKU maszyny wirtualnej platformy Azure dla podstawowych i pomocniczych maszyn wirtualnych muszą być takie same.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki krok po kroku dotyczące konfigurowania tych konfiguracji na platformie Azure, zobacz:

- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Wysoka dostępność dla SAP HANA przy użyciu replikacji systemowej](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Aby uzyskać więcej informacji na temat dostępności SAP HANA w regionach platformy Azure, zobacz:

- [Dostępność SAP HANA w różnych regionach świadczenia usługi Azure](./sap-hana-availability-across-regions.md)