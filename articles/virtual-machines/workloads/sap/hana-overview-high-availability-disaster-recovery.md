---
title: Wysoka dostępność i odzyskiwanie po awarii dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Zapewnienie wysokiej dostępności i planowanie odzyskiwania po awarii SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e1fd4d53a642052c55bbe5d31bf80133ae89d35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675457"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA — duże wystąpienia wysokiej dostępności i odzyskiwania po awarii na platformie Azure 

>[!IMPORTANT]
>Ta dokumentacja nie zastępuje dokumentacji dotyczącej administracji SAP HANA ani informacji o oprogramowaniu SAP. Należy się spodziewać, że czytelnik ma solidne zrozumienie i doświadczenie w SAP HANA administrowania i działaniami, szczególnie w przypadku tematów dotyczących tworzenia kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii.

Ważne jest, aby wykonać kroki i procesy wykonywane w środowisku oraz z wersjami i wydaniami platformy HANA. Niektóre procesy opisane w tej dokumentacji zostały uproszczone w celu lepszego zrozumienia i nie są przeznaczone do użycia jako szczegółowe kroki operacji ostatecznej handbooks. Jeśli chcesz utworzyć handbooks operacji dla konfiguracji, musisz przetestować i wykonać procesy oraz udokumentować procesy związane z określonymi konfiguracjami. 


Wysoka dostępność i odzyskiwanie po awarii (DR) to kluczowe aspekty uruchamiania SAP HANA o kluczowym znaczeniu na serwerze Azure (duże wystąpienia). Ważne jest, aby współpracować z oprogramowaniem SAP, integratorem systemów lub firmą Microsoft w celu poprawnego architekta i wdrożenia odpowiednich strategii wysokiej dostępności i odzyskiwania po awarii. Ważne jest również, aby wziąć pod uwagę cel punktu odzyskiwania (RPO) i cel czasu odzyskiwania, które są specyficzne dla danego środowiska.

Firma Microsoft obsługuje pewne SAP HANA funkcje wysokiej dostępności z dużymi wystąpieniami platformy HANA. Te funkcje obejmują:

- **Replikacja magazynu**: system magazynu może replikować wszystkie dane do innej sygnatury dużego wystąpienia Hana w innym regionie świadczenia usługi Azure. SAP HANA działa niezależnie od tej metody. Ta funkcja jest domyślnym mechanizmem odzyskiwania po awarii oferowanym dla dużych wystąpień platformy HANA.
- **Replikacja systemu Hana**: [replikacja wszystkich danych w SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do oddzielnego systemu SAP HANA. Cel czasu odzyskiwania jest zminimalizowany przez replikację danych w regularnych odstępach czasu. SAP HANA obsługuje asynchroniczne, synchroniczne tryby w pamięci i synchroniczne. Tryb synchroniczny jest używany tylko dla systemów SAP HANA, które znajdują się w tym samym centrum danych lub mniej niż 100 km od siebie. W bieżącym projekcie sygnatur dużego wystąpienia usługi HANA replikacja systemu HANA może być używana w celu zapewnienia wysokiej dostępności tylko w obrębie jednego regionu. Replikacja systemu HANA wymaga niezależnego serwera proxy lub składnika routingu do konfiguracji odzyskiwania po awarii w innym regionie świadczenia usługi Azure. 
- **Funkcja autoprzełączania do trybu failover hosta**: lokalne rozwiązanie do odzyskiwania błędów dla SAP HANA, które jest alternatywą dla replikacji systemu Hana. Jeśli węzeł główny stanie się niedostępny, należy skonfigurować co najmniej jedną SAP HANA wstrzymania w trybie skalowania w poziomie i SAP HANA automatycznie przechodzić w tryb failover do węzła w stanie wstrzymania.

SAP HANA na platformie Azure (duże wystąpienia) są oferowane w dwóch regionach świadczenia usługi Azure w czterech obszarach geopolitycznych (USA, Australia, Europa i Japonia). Dwa regiony w obszarze geopolitycznym, które obsługują sygnatury dużych wystąpień usługi HANA, są połączone z oddzielnymi dedykowanymi obwodami sieciowymi. Są one używane do replikowania migawek magazynu w celu zapewnienia metod odzyskiwania po awarii. Replikacja nie jest ustanawiana domyślnie, ale jest skonfigurowana dla klientów, którzy zamawiają funkcje odzyskiwania po awarii. Replikacja magazynu zależy od użycia migawek magazynu dla dużych wystąpień platformy HANA. Nie jest możliwe wybranie regionu platformy Azure jako regionu DR, który znajduje się w innym obszarze geopolitycznym. 

W poniższej tabeli przedstawiono obecnie obsługiwane metody wysokiej dostępności i odzyskiwania po awarii:

| Scenariusz obsługiwany w dużych wystąpieniach HANA | Opcja wysokiej dostępności | Opcja odzyskiwania po awarii | Komentarze |
| --- | --- | --- | --- |
| Jeden węzeł | Niedostępne. | Dedykowana Konfiguracja odzyskiwania po awarii.<br /> Konfiguracja Multipurpose DR. | |
| Automatyczne przejście w tryb failover hosta: skalowanie w poziomie (z wstrzymaniem lub bez)<br /> włącznie z 1 + 1 | Możliwe w przypadku wstrzymania roli aktywnej.<br /> HANA kontroluje przełącznik roli. | Dedykowana Konfiguracja odzyskiwania po awarii.<br /> Konfiguracja Multipurpose DR.<br /> Synchronizacja DR przy użyciu replikacji magazynu. | Zestawy woluminów HANA są dołączone do wszystkich węzłów.<br /> Lokacja DR musi mieć taką samą liczbę węzłów. |
| Replikacja systemu HANA | Możliwe z instalacją podstawową lub pomocniczą.<br /> Pomocnicze przeniesienie do roli głównej w przypadku pracy awaryjnej.<br /> Replikacja systemu HANA i tryb failover kontroli systemu operacyjnego. | Dedykowana Konfiguracja odzyskiwania po awarii.<br /> Konfiguracja Multipurpose DR.<br /> Synchronizacja DR przy użyciu replikacji magazynu.<br /> Odzyskiwanie po awarii przy użyciu funkcji replikacji systemu HANA nie jest jeszcze możliwe bez składników innych firm. | Osobny zestaw woluminów dysku są dołączane do każdego węzła.<br /> Tylko woluminy repliki pomocniczej w lokacji produkcyjnej są replikowane do lokalizacji odzyskiwania po awarii.<br /> W witrynie DR jest wymagany jeden zestaw woluminów. | 

Dedykowana Konfiguracja odzyskiwania po awarii polega na tym, że jednostka usługi HANA duże wystąpienie w lokacji odzyskiwania po awarii nie jest używana do uruchamiania jakichkolwiek innych obciążeń lub systemów nieprodukcyjnych. Jednostka jest pasywna i jest wdrażana tylko wtedy, gdy jest wykonywana awaria trybu failover. Chociaż nie jest to preferowany wybór dla wielu klientów.

Zapoznaj się z artykułami [obsługiwane scenariusze](hana-supported-scenario.md) , aby poznać układ magazynu i szczegóły sieci Ethernet dla architektury.

> [!NOTE]
> [SAP HANA wdrożenia MCOD](https://launchpad.support.sap.com/#/notes/1681092) (wiele wystąpień Hana w jednej jednostce) jako scenariusze nakładki współpracują z metodami ha i dr wymienionymi w tabeli. Wyjątkiem jest użycie replikacji systemu HANA z automatycznym klastrem trybu failover w oparciu o Pacemaker. Taki przypadek obsługuje tylko jedno wystąpienie HANA na jednostkę. W przypadku wdrożeń programu [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) tylko metody ha i dr oparte na magazynie nie będą działały, jeśli wdrożono więcej niż jedną dzierżawę. Po wdrożeniu jednej dzierżawy wszystkie wymienione metody są prawidłowe.  

W przypadku konfiguracji wieloznacznej jednostki HANA w witrynie odzyskiwania po awarii jest uruchamiane środowisko o dużym wystąpieniu. W razie awarii Zamknij system nieprodukcyjny, zainstaluj zestawy woluminów zreplikowane w magazynie (dodatkowe), a następnie uruchom wystąpienie produkcyjne platformy HANA. Większość klientów korzystających z funkcji odzyskiwania po awarii dużego wystąpienia HANA korzysta z tej konfiguracji. 


Więcej informacji na temat SAP HANA wysokiej dostępności można znaleźć w następujących artykułach SAP: 

- [Oficjalny dokument SAP HANA o wysokiej dostępności](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Przewodnik administrowania SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Film wideo SAP HANA Academy na SAP HANA replikacji systemu](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Uwaga dotycząca pomocy technicznej SAP #1999880 — często zadawane pytania dotyczące replikacji systemu SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [Uwaga dotycząca pomocy technicznej SAP #2165547 — SAP HANA wykonywania kopii zapasowych i przywracania w środowisku SAP HANAego replikacji systemu](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Uwaga dotycząca pomocy technicznej SAP #1984882 — używanie replikacji systemu SAP HANA na potrzeby wymiany sprzętu z minimalnym/zerowym czasem przestoju](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Zagadnienia dotyczące sieci w przypadku odzyskiwania po awarii z dużymi wystąpieniami HANA

Aby skorzystać z funkcji odzyskiwania po awarii w dużych wystąpieniach platformy HANA, należy zaprojektować łączność sieciową z dwoma regionami świadczenia usługi Azure. Musisz mieć połączenie obwodu usługi Azure ExpressRoute z lokalnego obszaru platformy Azure i innego połączenia obwodowego z regionu odzyskiwania po awarii. Ta miara obejmuje sytuację, w której występuje problem w regionie świadczenia usługi Azure, w tym lokalizację Microsoft Enterprise Edge router (MSEE).

W ramach drugiej miary można połączyć wszystkie sieci wirtualne platformy Azure, które łączą się z SAP HANA na platformie Azure (duże wystąpienia) w jednym regionie, do obwodu usługi ExpressRoute, który łączy duże wystąpienia HANA w innym regionie. Korzystając z tego *połączenia krzyżowego*, usługi działające w sieci wirtualnej platformy Azure w regionie 1 mogą łączyć się z jednostkami dużej instancji Hana w regionie 2 i odwrotnie. Ta miara dotyczy przypadku, w którym tylko jedna z lokalizacji MSEE, które łączą się z lokalizacją lokalną z platformą Azure, przejdzie w tryb offline.

Na poniższej ilustracji przedstawiono odporną konfigurację dla przypadków odzyskiwania po awarii:

![Optymalna Konfiguracja odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Inne wymagania dotyczące replikacji magazynu duże wystąpienia HANA na potrzeby odzyskiwania po awarii

Oprócz powyższych wymagań dotyczących konfiguracji odzyskiwania po awarii z dużymi wystąpieniami HANA należy:

- Zamów SAP HANA na platformie Azure (duże wystąpienia) jednostki SKU o takim samym rozmiarze jak jednostki SKU produkcyjne i wdrażaj je w regionie odzyskiwania po awarii. W bieżących wdrożeniach klienta te wystąpienia są używane do uruchamiania nieprodukcyjnych wystąpień platformy HANA. Te konfiguracje są nazywane *konfiguracjami MULTIPURPOSE Dr*.   
- Zamów dodatkową przestrzeń dyskową w witrynie odzyskiwania po awarii dla każdego SAP HANA na platformie Azure (duże wystąpienia), które mają zostać odzyskane w tej lokacji. Zakup dodatkowego magazynu pozwala przydzielić woluminy magazynu. Możesz przydzielić woluminy docelowe replikacji magazynu z regionu produkcji usługi Azure do odzyskiwania po awarii w regionie świadczenia usługi Azure.
- W przypadku, gdy HSR Instalatora na serwerze podstawowym i skonfigurowania replikacji na podstawie magazynu w lokacji odzyskiwania po awarii, należy zakupić dodatkowy magazyn w lokacji odzyskiwania po awarii, aby dane węzłów podstawowych i pomocniczych zostały zreplikowane do lokacji odzyskiwania po awarii.

  **Następne kroki**
- Odwołaj się [do kopii zapasowych i przywracania](hana-backup-restore.md).













