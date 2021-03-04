---
title: Włącz odzyskiwanie strefy do strefy odzyskiwania po awarii dla platformy Azure Virtual Machines
description: W tym artykule opisano, kiedy i jak używać strefy do odzyskiwania po awarii strefy dla maszyn wirtualnych platformy Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: beba8e1d8126818f142e4873d551ed077af869d2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035241"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Włączanie odzyskiwania po awarii maszyny wirtualnej platformy Azure między strefami dostępności

W tym artykule opisano sposób replikowania maszyn wirtualnych platformy Azure w trybie failover i powrotu po awarii z jednej strefy dostępności do innej w ramach tego samego regionu świadczenia usługi Azure.

>[!NOTE]
>
>- Obsługa odzyskiwania po awarii strefy do strefy jest obecnie ograniczona do następujących regionów: Azja Południowo-Wschodnia, Japonia Wschodnia, Australia Wschodnia, Południowe Zjednoczone Królestwo, Europa Zachodnia, Europa Północna, środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2 i zachodnie stany USA 2.  
>- Site Recovery nie przenosi ani nie zapisuje danych klienta poza regionem, w którym jest wdrażany, gdy klient korzysta z strefy do odzyskiwania po awarii strefy. Klienci mogą wybrać magazyn Recovery Services z innego regionu, jeśli tak się wybierzesz. Magazyn Recovery Services zawiera metadane, ale nie dane rzeczywiste klienta.

Usługa Site Recovery przyczynia się do zachowania ciągłości działania i odzyskiwania po awarii, dzięki czemu aplikacje biznesowe są wykonywane w trakcie planowanych i nieplanowanych przestojów. Jest to zalecana opcja odzyskiwania po awarii, aby zapewnić, że aplikacje będą działać w przypadku wystąpienia regionalnego.

Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa ma co najmniej jedno centrum danych. 

Jeśli chcesz przenieść maszyny wirtualne do strefy dostępności w innym regionie, [zapoznaj się z tym artykułem](../resource-mover/move-region-availability-zone.md).

## <a name="using-availability-zones-for-disaster-recovery"></a>Korzystanie z Strefy dostępności na potrzeby odzyskiwania po awarii 

Zwykle Strefy dostępności są używane do wdrażania maszyn wirtualnych w konfiguracji o wysokiej dostępności. Mogą być zbyt blisko siebie, aby służyć jako rozwiązanie odzyskiwania po awarii w przypadku klęski żywiołowej.

Jednak w niektórych scenariuszach Strefy dostępności można wykorzystać na potrzeby odzyskiwania po awarii:

- Wielu klientów, którzy mieli strategię odzyskiwania po awarii w trybie Metro, podczas gdy aplikacje hostingowe lokalnie są czasami wyglądały na naśladowanie tej strategii po migracji aplikacji na platformę Azure. Ci klienci potwierdzają fakt, że strategia odzyskiwania po awarii jest niedostępna w przypadku awarii na dużą skalę i zaakceptowanie tego ryzyka. W przypadku takich klientów można użyć odzyskiwania po awarii strefy do strefy jako opcji odzyskiwania po awarii.

- Wielu innych klientów ma skomplikowaną infrastrukturę sieciową i nie chce jej ponownie utworzyć w regionie pomocniczym ze względu na koszt i złożoność. Odzyskiwanie po awarii strefy do strefy zmniejsza złożoność, ponieważ wykorzystuje nadmiarowe koncepcje sieci w Strefy dostępności, co znacznie upraszcza konfigurację. Ci klienci wolą prostotę i mogą również używać Strefy dostępności do odzyskiwania po awarii.

- W niektórych regionach, które nie mają sparowanego regionu w ramach tej samej jurysdykcji prawnej (na przykład Południowo-Wschodnia), odzyskiwanie po awarii strefy do strefy może obsłużyć jako niefaktyczne rozwiązanie do odzyskiwania po awarii, ponieważ pomaga zapewnić zgodność z przepisami, ponieważ aplikacje i dane nie przechodzą między granicami krajowymi. 

- Odzyskiwanie po awarii strefy do strefy oznacza replikację danych na krótszych odległość w porównaniu z platformą Azure z odzyskiwaniem po awarii platformy Azure. w związku z tym można zobaczyć małe opóźnienia i w związku z tym niższy cel punktu odzyskiwania.

Chociaż są one silnymi zaletami, istnieje możliwość, że odzyskanie awaryjne strefy może mieć niewielkie wymagania dotyczące odporności w przypadku klęski żywiołowej całego regionu.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Obsługa sieci na potrzeby odzyskiwania po awarii strefy do strefy

Jak wspomniano powyżej, funkcja odzyskiwania po awarii strefy do strefy zmniejsza złożoność, ponieważ wykorzystuje nadmiarowe koncepcje sieci w Strefy dostępności, co znacznie upraszcza konfigurację. Zachowanie składników sieciowych w scenariuszu odzyskiwania po awarii strefy w strefie jest opisane poniżej: 

- Virtual Network: możesz użyć tej samej sieci wirtualnej co Sieć źródłowa w przypadku rzeczywistych przełączeń w tryb failover. Użyj innej sieci wirtualnej do źródłowej sieci wirtualnej na potrzeby testu pracy w trybie failover.

- Podsieć: przełączenie w tryb failover do tej samej podsieci jest obsługiwane.

- Prywatny adres IP: Jeśli są używane statyczne adresy IP, możesz użyć tych samych IP w strefie docelowej, jeśli zdecydujesz się je skonfigurować w taki sposób.

- Przyspieszona sieć: podobna do platformy Azure do odzyskiwania po awarii platformy Azure, możesz włączyć przyspieszoną sieć, jeśli jednostka SKU maszyny wirtualnej ją obsługuje.

- Publiczny adres IP: możesz dołączyć wcześniej utworzony standardowy publiczny adres IP w tym samym regionie do docelowej maszyny wirtualnej. Podstawowe publiczne adresy IP nie obsługują scenariuszy związanych ze strefami dostępności.

- Moduł równoważenia obciążenia: standardowy moduł równoważenia obciążenia jest zasobem regionalnym, w związku z czym docelowa maszyna wirtualna może zostać dołączona do puli zaplecza tego samego modułu równoważenia obciążenia. Nowy moduł równoważenia obciążenia nie jest wymagany.

- Sieciowa Grupa zabezpieczeń: możesz użyć tych samych sieciowych grup zabezpieczeń, które zostały zastosowane do źródłowej maszyny wirtualnej.

## <a name="pre-requisites"></a>Wymagania wstępne

Przed wdrożeniem strefy do odzyskiwania po awarii strefy dla maszyn wirtualnych należy upewnić się, że inne funkcje włączone na maszynie wirtualnej współdziałają ze strefą odzyskiwania po awarii strefy.

|Cecha  | Instrukcja obsługi  |
|---------|---------|
|Klasyczne maszyny wirtualne   |     Nieobsługiwane    |
|Maszyny wirtualne ARM    |    Obsługiwane    |
|Azure Disk Encryption V1 (podwójny przebieg, z Azure Active Directory (Azure AD))     |     Obsługiwane   |
|Azure Disk Encryption v2 (Single Pass, bez usługi Azure AD)    |    Obsługiwane    |
|Dyski niezarządzane    |    Nieobsługiwane    |
|Dyski zarządzane    |    Obsługiwane    |
|Klucze zarządzane przez klienta    |    Obsługiwane    |
|Grupy umieszczania w pobliżu    |    Obsługiwane    |
|Współdziałanie kopii zapasowej    |    Tworzenie kopii zapasowych i przywracanie na poziomie plików są obsługiwane. Kopia zapasowa i przywracanie na poziomie dysku i maszyny wirtualnej nie są obsługiwane.    |
|Dodaj/Usuń na gorąco    |    Dyski można dodać po włączeniu replikacji strefy do strefy. Usunięcie dysków po włączeniu replikacji strefy do strefy nie jest obsługiwane.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Konfigurowanie strefy Site Recovery na potrzeby odzyskiwania po awarii strefy

### <a name="log-in"></a>Logowanie się

Zaloguj się w witrynie Azure Portal.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Włącz replikację dla strefowej maszyny wirtualnej platformy Azure

1. W menu Azure Portal wybierz pozycję Maszyny wirtualne lub Wyszukaj i wybierz pozycję Maszyny wirtualne na dowolnej stronie. Wybierz maszynę wirtualną, którą chcesz replikować. W przypadku odzyskiwania po awarii strefy do strefy ta maszyna wirtualna musi już znajdować się w strefie dostępności.

2. W obszarze Operacja wybierz pozycję Odzyskiwanie po awarii.

3. Jak pokazano poniżej, na karcie podstawy wybierz opcję "tak", aby "odzyskiwanie awaryjne między Strefy dostępności?".

    ![Strona Ustawienia podstawowe](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Jeśli akceptujesz wszystkie wartości domyślne, kliknij przycisk "Przejrzyj i uruchom replikację", a następnie pozycję "Rozpocznij replikację".

5. Jeśli chcesz wprowadzić zmiany w ustawieniach replikacji, kliknij przycisk Dalej: Ustawienia zaawansowane.

6. Zmień ustawienia w zależności od ustawień domyślnych wszędzie tam, gdzie jest to konieczne. W przypadku użytkowników platformy Azure do odzyskiwania po awarii na platformie Azure ta strona może wydawać się znana. Więcej informacji o opcjach przedstawionych w tym bloku można znaleźć [tutaj](./azure-to-azure-tutorial-enable-replication.md)

    ![Strona ustawień zaawansowanych](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Kliknij pozycję "dalej: recenzja + Uruchom replikację", a następnie polecenie "Uruchom replikację".

## <a name="faqs"></a>Często zadawane pytania

**1. w jaki sposób ceny są wykonywane w przypadku odzyskiwania po awarii strefy do strefy?**
Ceny odzyskiwania po awarii strefy do strefy są takie same jak ceny odzyskiwania po awarii platformy Azure. Więcej szczegółów można znaleźć na stronie cennika [tutaj](https://azure.microsoft.com/pricing/details/site-recovery/) i [tutaj](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/). Należy zauważyć, że opłaty za ruch wychodzący wyświetlane w strefie do odzyskiwania po awarii strefy byłyby mniejsze niż region do odzyskania po awarii.

**2. Jaka jest umowa SLA dotycząca RTO i celu punktu odzyskiwania?**
Umowa SLA RTO jest taka sama jak w przypadku Site Recovery ogólnej. Firma Microsoft zastrzega sobie RTO przez maksymalnie 2 godziny. Brak zdefiniowanej umowy SLA na potrzeby celu punktu odzyskiwania.

**3. czy pojemność jest gwarantowana w strefie pomocniczej?**
Plan zespołu Site Recovery i zarządzanie pojemnością platformy Azure w celu zapewnienia wystarczającej pojemności infrastruktury. Po uruchomieniu trybu failover zespoły mogą także zapewnić, że wystąpienia maszyn wirtualnych chronione przez program Site Recovery zostaną wdrożone w strefie docelowej.

**4. które systemy operacyjne są obsługiwane?**
Odzyskiwanie po awarii strefy do strefy obsługuje te same systemy operacyjne co platforma Azure do odzyskiwania po awarii platformy Azure. Zapoznaj się z macierzą pomocy technicznej [tutaj](./azure-to-azure-support-matrix.md).

**5. czy źródłowa i docelowa Grupa zasobów jest taka sama?**
Nie, należy przełączyć się w tryb failover do innej grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Kroki, które należy wykonać w celu przeprowadzenia przechodzenia do szczegółów odzyskiwania po awarii, przełączenia w tryb failover, ponownego włączenia ochrony i powrotu po awarii, są takie same jak w przypadku scenariusza odzyskiwania po awarii platformy Azure.

Aby przeprowadzić drążenie odzyskiwania po awarii, wykonaj kroki opisane [tutaj](./azure-to-azure-tutorial-dr-drill.md).

Aby przeprowadzić tryb failover i ponownie włączyć ochronę maszyn wirtualnych w strefie pomocniczej, wykonaj kroki opisane [tutaj](./azure-to-azure-tutorial-failover-failback.md).

Aby przeprowadzić powrót po awarii do strefy podstawowej, wykonaj kroki opisane [tutaj](./azure-to-azure-tutorial-failback.md).
