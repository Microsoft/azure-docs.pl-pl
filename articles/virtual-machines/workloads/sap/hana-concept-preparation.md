---
title: Zasady odzyskiwania po awarii i przygotowanie do SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Zasady odzyskiwania po awarii i przygotowanie do SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: babd7c1dcae9d83af1f6c41e756b663d92d6d486
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677127"
---
# <a name="disaster-recovery-principles"></a>Zasady odzyskiwania po awarii

Duże wystąpienia HANA oferują funkcjonalność odzyskiwania po awarii między sygnaturami dużych wystąpień usługi HANA w różnych regionach platformy Azure. Na przykład w przypadku wdrażania jednostek usługi HANA o dużych wystąpieniach w regionie zachodnie stany USA platformy Azure można użyć jednostek z dużymi wystąpieniami HANA w regionie Wschodnie stany USA jako jednostki odzyskiwania po awarii. Jak wspomniano wcześniej, odzyskiwanie po awarii nie jest konfigurowane automatycznie, ponieważ wymaga uregulowania innej jednostki dużego wystąpienia HANA w regionie DR. Konfiguracja odzyskiwania po awarii działa w przypadku skalowania w górę oraz ustawień skalowania w poziomie. 

W scenariuszach wdrożonych do tej pory klienci używają jednostki w regionie DR do uruchamiania systemów nieprodukcyjnych, które używają zainstalowanego wystąpienia platformy HANA. Jednostka dużej instancji HANA musi być tą samą jednostką SKU jak jednostka SKU używana do celów produkcyjnych. Na poniższej ilustracji przedstawiono, jak Konfiguracja dysku między jednostką serwera w regionie produkcyjnym platformy Azure a regionem odzyskiwania po awarii wygląda następująco:

![Konfiguracja instalacji DR z punktu widzenia dysku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak pokazano na tej grafice przeglądu, należy zastanowić się nad drugim zestawem woluminów dysku. Woluminy dysku docelowego są takie same jak woluminy produkcyjne dla wystąpienia produkcyjnego w jednostkach odzyskiwania po awarii. Te woluminy dysków są skojarzone z jednostką serwera dużego wystąpienia HANA w lokacji odzyskiwania po awarii. Następujące woluminy są replikowane z regionu produkcji do lokacji DR:

- /hana/data
- /hana/logbackups 
- /Hana/Shared (łącznie z/usr/SAP)

Wolumin/Hana/log nie jest replikowany, ponieważ dziennik transakcji SAP HANA nie jest wymagany w sposób, w jaki wykonywane jest przywracanie z tych woluminów. 

Podstawą oferowanej funkcji odzyskiwania po awarii jest funkcja replikacji magazynu oferowana przez infrastrukturę dużych wystąpień platformy HANA. Funkcja, która jest używana na stronie magazynu, nie jest stałym strumieniem zmian, które są replikowane w sposób asynchroniczny w miarę dokonywania zmian w woluminie magazynu. Zamiast tego jest mechanizmem, który polega na tym, że migawki tych woluminów są tworzone w regularnych odstępach czasu. Różnica między już replikowaną migawką a nową migawką, która nie została jeszcze zreplikowana, jest następnie przekazywana do lokacji odzyskiwania po awarii do woluminów na dysku docelowym.  Te migawki są przechowywane na woluminach, a w przypadku trybu failover odzyskiwania po awarii należy przywrócić te woluminy.  

Pierwszy transfer kompletnych danych woluminu powinien być wcześniejszy niż różnica między migawkami. W związku z tym woluminy w lokacji DR zawierają co najmniej jedną migawkę woluminu wykonywaną w lokacji produkcyjnej. Na koniec można użyć tego systemu, aby przejść do wcześniejszego stanu w celu odzyskania utraconych danych bez wycofywania systemu produkcyjnego.

Jeśli istnieje wdrożenie MCOD z wieloma niezależnymi wystąpieniami SAP HANA w jednej jednostce o dużej instancji HANA, oczekuje się, że wszystkie wystąpienia SAP HANA będą pobierać magazyn replikowany na stronie DR.

W przypadku korzystania z replikacji systemu HANA jako funkcji wysokiej dostępności w lokacji produkcyjnej i używania replikacji opartej na magazynie dla lokacji DR są replikowane woluminy obu węzłów z lokacji głównej do wystąpienia usługi DR. Należy zakupić dodatkowy magazyn (ten sam rozmiar jak węzeł podstawowy) w lokacji DR, aby uwzględnić replikację zarówno z podstawowego, jak i pomocniczego do odzyskiwania po awarii. 



>[!NOTE]
>Funkcja replikacji magazynu dużych wystąpień platformy HANA to dublowanie i replikowanie migawek magazynu. Jeśli nie wykonasz migawek magazynu wprowadzonych w sekcji Tworzenie kopii zapasowych i przywracanie tego artykułu, nie można przeprowadzić replikacji do lokacji odzyskiwania po awarii. Wykonanie migawki magazynu jest wymaganiem wstępnym do replikacji magazynu do lokacji odzyskiwania po awarii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Przygotowanie scenariusza odzyskiwania po awarii
W tym scenariuszu w środowisku produkcyjnym platformy Azure jest uruchomiony system produkcyjny w dużych wystąpieniach HANA. W przypadku kroków, które należy wykonać, Załóżmy, że identyfikator SID tego systemu HANA to "PRD" i że masz system nieprodukcyjny działający w dużych wystąpieniach HANA w regionie platformy Azure. Załóżmy, że jego identyfikator SID to "TST". Na poniższej ilustracji przedstawiono tę konfigurację:

![Rozpoczęcie instalacji programu DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Jeśli wystąpienie serwera nie zostało jeszcze uporządkowane z dodatkowym zestawem woluminów magazynu, SAP HANA w usłudze Azure Service Management dołącza dodatkowy zestaw woluminów jako element docelowy dla repliki produkcyjnej do jednostki usługi HANA o dużym wystąpieniu, w której jest uruchamiane wystąpienie TST HANA. W tym celu należy podać identyfikator SID wystąpienia produkcyjnego platformy HANA. Po SAP HANA w usłudze Azure Service Management potwierdzi załącznik tych woluminów, należy zainstalować te woluminy w jednostce dużego wystąpienia HANA.

![Następny krok instalatora DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Następnym krokiem jest zainstalowanie drugiego wystąpienia SAP HANA w jednostce usługi HANA duże wystąpienie w regionie platformy Azure, w którym jest uruchamiane wystąpienie TST HANA. Nowo zainstalowane wystąpienie SAP HANA musi mieć ten sam identyfikator SID. Utworzony użytkownicy muszą mieć ten sam identyfikator UID i grupy, które ma wystąpienie produkcyjne. Aby uzyskać szczegółowe informacje, przeczytaj temat [wykonywanie kopii zapasowej i przywracanie](hana-backup-restore.md) . Jeśli instalacja zakończyła się pomyślnie, musisz:

- Wykonaj krok 2 przygotowania migawki magazynu opisanego w temacie [Tworzenie kopii zapasowych i przywracanie](hana-backup-restore.md).
- Utwórz klucz publiczny dla jednostki usługi DR o dużej instancji platformy HANA, jeśli jeszcze tego nie zrobiono. Zobacz Krok 3 przygotowania migawki magazynu opisanego w temacie [Tworzenie kopii zapasowych i przywracanie](hana-backup-restore.md).
- Zachowaj *HANABackupCustomerDetails.txt* przy użyciu nowego wystąpienia platformy Hana i sprawdź, czy połączenie z magazynem działa prawidłowo.  
- Zatrzymaj nowo zainstalowane wystąpienie SAP HANA w jednostce usługi HANA duże wystąpienie w regionie odzyskiwania po awarii.
- Odinstaluj te woluminy PRD i skontaktuj się SAP HANA w usłudze Azure Service Management. Woluminy nie mogą pozostać zainstalowane w jednostce, ponieważ nie mogą być dostępne podczas działania jako cel replikacji magazynu.  

![Diagram przedstawiający relację replikacji między woluminami PRD w regionie produkcyjnym platformy Azure i woluminami PRD w regionie odzyskiwania po awarii.](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Zespół operacyjny ustanawia relację replikacji między woluminami PRD w regionie produkcyjnym platformy Azure i woluminami PRD w regionie odzyskiwania po awarii.

>[!IMPORTANT]
>Wolumin/Hana/log nie jest replikowany, ponieważ nie jest konieczne przywrócenie zreplikowanej bazy danych SAP HANA do stanu spójnego w lokacji odzyskiwania po awarii.

Następnie skonfiguruj lub Dostosuj harmonogram tworzenia kopii zapasowych migawek magazynu, aby uzyskać dostęp do RTO i punktu odzyskiwania w przypadku awarii. Aby zminimalizować cel punktu odzyskiwania, należy ustawić następujące Interwały replikacji w usłudze w dużym wystąpieniu usługi HANA:
- Dla woluminów objętych połączoną migawką ( **Snapshot Type)** Ustaw replikację co 15 minut do odpowiadającego miejsca docelowego woluminu magazynu w lokacji odzyskiwania po awarii.
- W przypadku woluminu kopii zapasowej dziennika transakcji ( **dzienniki** typu migawek), ustawione na replikację co 3 minuty do odpowiadającego miejsca docelowego woluminu magazynu w lokacji odzyskiwania po awarii.

Aby zminimalizować cel punktu odzyskiwania, skonfiguruj następujące elementy:
- Wykonaj migawkę magazynu typu **Hana** (zobacz "krok 7: wykonywanie migawek") co 30 minut do 1 godziny.
- Wykonaj kopie zapasowe dziennika transakcji SAP HANA co 5 minut.
- Wykonaj **dzienniki** typ migawki magazyn co 5-15 minut. W tym okresie można osiągnąć cel punktu odzyskiwania około 15-25 minut.

W przypadku tej konfiguracji sekwencja kopii zapasowych dziennika transakcji, migawek magazynu i replikacji woluminu kopii zapasowej dziennika transakcji HANA oraz/Hana/Data oraz/Hana/Shared (zawiera/usr/SAP) może wyglądać jak dane wyświetlane na tej ilustracji:

 ![Relacja między migawką kopii zapasowej dziennika transakcji i duplikatem przyciągania na osi czasu](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Aby osiągnąć jeszcze lepszy cel punktu odzyskiwania po awarii, można skopiować kopie zapasowe dziennika transakcji HANA z SAP HANA na platformie Azure (duże wystąpienia) do innych regionów świadczenia usługi Azure. Aby osiągnąć dalsze zmniejszenie punktu odzyskiwania, wykonaj następujące czynności:

1. Utwórz kopię zapasową dziennika transakcji HANA tak często, jak to możliwe, aby/Hana/logbackups.
1. Użyj rsync, aby skopiować kopie zapasowe dziennika transakcji do maszyn wirtualnych platformy Azure hostowanych przez udziały NFS. Maszyny wirtualne znajdują się w sieciach wirtualnych platformy Azure w regionie produkcyjnym platformy Azure i w regionach odzyskiwania po awarii. Musisz połączyć sieci wirtualne platformy Azure z obwodem łączącym duże wystąpienia produkcji HANA z platformą Azure. Zapoznaj się z grafiką w sekcji [uwagi dotyczące sieci dotyczącej odzyskiwania po awarii przy użyciu dużych wystąpień platformy Hana](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) . 
1. Przechowuj kopie zapasowe dziennika transakcji w regionie na maszynie wirtualnej podłączonej do magazynu wyeksportowanego systemu plików NFS.
1. W przypadku trybu failover w przypadku awarii należy uzupełnić kopie zapasowe dziennika transakcji, które znajdują się na woluminie/Hana/logbackups z ostatnio przetworzonymi kopiami zapasowymi dziennika transakcji w udziale NFS w lokacji odzyskiwania po awarii. 
1. Rozpocznij tworzenie kopii zapasowej dziennika transakcji, aby przywrócić najnowszą kopię zapasową, która może zostać zapisana w regionie odzyskiwania po awarii.

Podczas operacji dużego wystąpienia HANA należy potwierdzić konfigurację relacji replikacji i rozpocząć tworzenie kopii zapasowych migawek magazynu wykonywania, replikacja danych rozpoczyna się.

![Krok instalacji DR przed ustanowieniem replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

W miarę postępów replikacji migawki na woluminach PRD w regionach platformy Azure odzyskiwania po awarii nie są przywracane. Są one przechowywane. Jeśli woluminy są zainstalowane w takim stanie, reprezentują stan, w którym zostały odinstalowane te woluminy po zainstalowaniu wystąpienia SAP HANA PRD w jednostce serwerowej w regionie usługi odzyskiwania po awarii. Reprezentują one również kopie zapasowe magazynu, które nie zostały jeszcze przywrócone.

W przypadku przejścia w tryb failover można również przywrócić starszą migawkę magazynu zamiast najnowszej migawki magazynu.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [procedurą trybu failover odzyskiwania po awarii](hana-failover-procedure.md).
