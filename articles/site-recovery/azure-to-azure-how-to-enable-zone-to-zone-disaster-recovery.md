---
title: Włączanie odzyskiwania po awarii ze strefy do strefy dla usługi Azure Virtual Machines
description: W tym artykule opisano, kiedy i jak używać odzyskiwania po awarii strefy do strefy dla maszyn wirtualnych platformy Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713412"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Włączanie odzyskiwania po awarii maszyny wirtualnej platformy Azure między strefami dostępności

W tym artykule opisano sposób replikowania, trybu failover i powrotu po awarii maszyn wirtualnych platformy Azure z jednej strefy dostępności do innej w tym samym regionie świadczenia usługi Azure.

>[!NOTE]
>
>- Obsługa odzyskiwania po awarii między strefami jest obecnie ograniczona do następujących regionów: Azja Południowo-Wschodnia, Japonia Wschodnia, Australia Wschodnia, JIO Indie Zachodnie, Południowe Zjednoczone Królestwo, Europa Zachodnia, Europa Północna, Środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2 i Zachodnie stany USA 2.  
>- Site Recovery nie przenosi ani nie przechowuje danych klienta poza region, w którym są wdrażane, gdy klient korzysta z odzyskiwania po awarii ze strefy do strefy. Klienci mogą wybrać magazyn usługi Recovery Services z innego regionu, jeśli tak. Magazyn usługi Recovery Services zawiera metadane, ale nie zawiera rzeczywistych danych klienta.

Site Recovery usługa przyczynia się do strategii ciągłości działania i odzyskiwania po awarii, utrzymując działanie aplikacji biznesowych podczas planowanych i nieplanowanych awarii. Zalecaną opcją odzyskiwania po awarii jest utrzymanie działania aplikacji w przypadku awarii regionalnych.

Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa ma co najmniej jedno centrum danych. 

Jeśli chcesz przenieść maszyny wirtualne do strefy dostępności w innym regionie, [zapoznaj się z tym artykułem.](../resource-mover/move-region-availability-zone.md)

## <a name="using-availability-zones-for-disaster-recovery"></a>Używanie Strefy dostępności odzyskiwania po awarii 

Zazwyczaj Strefy dostępności są używane do wdrażania maszyn wirtualnych w konfiguracji wysokiej dostępności. Mogą one być zbyt blisko siebie, aby służyć jako rozwiązanie odzyskiwania po awarii w przypadku klęski żywiołowej.

Jednak w niektórych scenariuszach można Strefy dostępności do odzyskiwania po awarii:

- Wielu klientów, którzy mieli strategię odzyskiwania po awarii w chmurze podczas hostowania aplikacji w środowisku lokalnym, czasami chce naśladować tę strategię po migracji aplikacji na platformę Azure. Ci klienci potwierdzają fakt, że strategia odzyskiwania po awarii może nie działać w przypadku awarii fizycznej na dużą skalę i zaakceptować to ryzyko. W przypadku takich klientów odzyskiwanie po awarii typu strefa-strefa może być używane jako opcja odzyskiwania po awarii.

- Wielu innych klientów ma skomplikowaną infrastrukturę sieciową i nie chce jej odtworzyć w regionie pomocniczym ze względu na powiązane koszty i złożoność. Odzyskiwanie po awarii między strefami zmniejsza złożoność, ponieważ wykorzystuje nadmiarowe koncepcje dotyczące sieci Strefy dostępności znacznie prostszą konfigurację. Tacy klienci preferują prostotę i mogą również używać Strefy dostępności odzyskiwania po awarii.

- W niektórych regionach, które nie mają sparowanego regionu w ramach tej samej jurysdykcji prawnej (na przykład Azja Południowo-Wschodnia), odzyskiwanie po awarii między strefami może służyć jako de facto rozwiązanie odzyskiwania po awarii, ponieważ pomaga zapewnić zgodność z przepisami, ponieważ twoje aplikacje i dane nie przejdą przez granice krajowe. 

- Odzyskiwanie po awarii między strefami implikuje replikację danych na krótsze odległości w porównaniu z odzyskiwaniem po awarii z platformy Azure do platformy Azure, w związku z tym może wystąpić mniejsze opóźnienie i w związku z tym niższy poziom celu odzyskiwania.

Chociaż są to silne zalety, istnieje możliwość, że odzyskiwanie po awarii typu strefa-strefa może nie spełniać wymagań dotyczących odporności w przypadku klęski żywiołowej w całym regionie.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Sieć odzyskiwania po awarii między strefami

Jak wspomniano powyżej, odzyskiwanie po awarii między strefami zmniejsza złożoność, ponieważ wykorzystuje nadmiarowe koncepcje dotyczące sieci Strefy dostępności znacznie prostszą konfigurację. Zachowanie składników sieciowych w scenariuszu odzyskiwania po awarii między strefami zostało opisane poniżej: 

- Virtual Network: W przypadku rzeczywistych trybu failover można użyć tej samej sieci wirtualnej co sieć źródłową. Użyj innej sieci wirtualnej niż źródłową sieć wirtualną do testowania pracy w trybie failover.

- Podsieć: obsługiwana jest trybu failover w tej samej podsieci.

- Prywatny adres IP: jeśli używasz statycznych adresów IP, możesz użyć tych samych adresów IP w strefie docelowej, jeśli zdecydujesz się je skonfigurować w taki sposób.

- Przyspieszona sieć: podobnie jak w przypadku odzyskiwania po awarii na platformie Azure, możesz włączyć przyspieszoną sieć, jeśli jest to możliwe w przypadku, gdy jest to możliwe w ramach sku maszyny wirtualnej.

- Publiczny adres IP: wcześniej utworzony standardowy publiczny adres IP w tym samym regionie można dołączyć do docelowej maszyny wirtualnej. Podstawowe publiczne adresy IP nie obsługują scenariuszy związanych ze strefą dostępności.

- Równoważenie obciążenia: Standardowy zasób równoważenia obciążenia jest zasobem regionalnym, dlatego docelową maszynę wirtualną można dołączyć do puli zaplecza tego samego równoważenia obciążenia. Nowy równoważenie obciążenia nie jest wymagany.

- Sieciowa grupa zabezpieczeń: możesz użyć tych samych sieciowych grup zabezpieczeń, które są stosowane do źródłowej maszyny wirtualnej.

## <a name="pre-requisites"></a>Wymagania wstępne

Przed wdrożeniem odzyskiwania po awarii strefy w strefie dla maszyn wirtualnych należy upewnić się, że inne funkcje włączone na maszynie wirtualnej są współdziałające ze strefą do odzyskiwania po awarii strefy.

|Cecha  | Instrukcja obsługi  |
|---------|---------|
|Klasyczne maszyny wirtualne   |     Nieobsługiwane    |
|Maszyny wirtualne ARM    |    Obsługiwane    |
|Azure Disk Encryption v1 (podwójne przejście, z Azure Active Directory (Azure AD))     |     Obsługiwane   |
|Azure Disk Encryption w wersji 2 (pojedynczy przebieg, bez usługi Azure AD)    |    Obsługiwane    |
|Dyski niezarządzane    |    Nieobsługiwane    |
|Dyski zarządzane    |    Obsługiwane    |
|Klucze zarządzane przez klienta    |    Obsługiwane    |
|Grupy umieszczania w pobliżu    |    Obsługiwane    |
|Współdziałanie kopii zapasowych    |    Obsługiwane są kopie zapasowe i przywracanie na poziomie plików. Tworzenie i przywracanie kopii zapasowej na poziomie dysku i maszyny wirtualnej, ale nie jest obsługiwane.    |
|Dodawanie/usuwanie na gorąco    |    Dyski można dodać po włączeniu replikacji między strefami. Usuwanie dysków po włączeniu replikacji między strefami nie jest obsługiwane.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Konfigurowanie odzyskiwania Site Recovery strefowego na strefowe odzyskiwanie po awarii

### <a name="log-in"></a>Logowanie się

Zaloguj się w witrynie Azure Portal.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Włączanie replikacji dla strefowej maszyny wirtualnej platformy Azure

1. W menu Azure Portal wybierz pozycję Maszyny wirtualne lub wyszukaj i wybierz pozycję Maszyny wirtualne na dowolnej stronie. Wybierz maszynę wirtualną, którą chcesz replikować. W przypadku odzyskiwania po awarii "strefa-strefa" ta maszyna wirtualna musi już znajdować się w strefie dostępności.

2. W obszarze Operacja wybierz pozycję Odzyskiwanie po awarii.

3. Jak pokazano poniżej, na karcie Podstawy wybierz pozycję "Tak" dla opcji "Odzyskiwanie po awarii między Strefy dostępności?".

    ![Strona Ustawienia podstawowe](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Jeśli zaakceptujesz wszystkie wartości domyślne, kliknij pozycję "Przejrzyj i rozpocznij replikację", a następnie pozycję "Rozpocznij replikację".

5. Jeśli chcesz wprowadzić zmiany w ustawieniach replikacji, kliknij pozycję "Dalej: Ustawienia zaawansowane".

6. Zmień ustawienia poza domyślnymi wszędzie tam, gdzie jest to odpowiednie. Dla użytkowników odzyskiwania po awarii Azure–Azure ta strona może wydawać się znajoma. Więcej szczegółów na temat opcji przedstawionych w tym bloku można znaleźć [tutaj](./azure-to-azure-tutorial-enable-replication.md)

    ![Strona Ustawienia zaawansowane](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Kliknij pozycję "Dalej: Przegląd + Rozpocznij replikację", a następnie pozycję "Rozpocznij replikację".

## <a name="faqs"></a>Często zadawane pytania

**1. Jak działają ceny odzyskiwania po awarii ze strefy do strefy?**
Cennik odzyskiwania po awarii ze stref do strefy jest identyczny z cenami odzyskiwania po awarii Azure–Azure. Więcej szczegółów można znaleźć na stronie cennika [tutaj](https://azure.microsoft.com/pricing/details/site-recovery/) i [tutaj.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Należy pamiętać, że opłaty za ruch wychodzący, które będą naliczane w przypadku odzyskiwania po awarii ze strefy do strefy, będą niższe niż w przypadku odzyskiwania po awarii region-region.

**2. Jaka jest umowa SLA dla celu RTO i celu celu czasowego?**
Umowa SLA celu czasowego jest taka sama jak w przypadku Site Recovery ogólnego. Mamy obietnicę czasu RTO do 2 godzin. Nie ma zdefiniowanej umowy SLA dla celu.

**3. Czy pojemność jest gwarantowana w strefie pomocniczej?**
Zespół Site Recovery i zespół ds. zarządzania pojemnością platformy Azure planują wystarczającą pojemność infrastruktury. Po rozpoczęciu pracy w trybie failover zespoły pomagają również zapewnić, że wystąpienia maszyn wirtualnych chronione przez Site Recovery zostaną wdrożone w strefie docelowej.

**4. Które systemy operacyjne są obsługiwane?**
Odzyskiwanie po awarii z strefy do strefy obsługuje te same systemy operacyjne co odzyskiwanie po awarii z platformy Azure do platformy Azure. Zapoznaj się z macierzą obsługi [tutaj.](./azure-to-azure-support-matrix.md)

**5. Czy źródłowe i docelowe grupy zasobów mogą być takie same?**
Nie, należy przesieć w tryb fail over do innej grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Kroki, które należy wykonać, aby uruchomić przechodzenie do szczegółów odzyskiwania po awarii, przechodzenie w tryb fail over, ponowną ochronę i powrót po awarii, są takie same jak w scenariuszu z platformy Azure do usługi Azure Disaster Recovery.

Aby przeprowadzić przechodzenie do szczegółów odzyskiwania po awarii, wykonaj kroki opisane [tutaj.](./azure-to-azure-tutorial-dr-drill.md)

Aby wykonać trybu failover i ponownie wchłonić maszyny wirtualne w strefie pomocniczej, wykonaj kroki opisane [tutaj.](./azure-to-azure-tutorial-failover-failback.md)

Aby awarii do strefy podstawowej, wykonaj kroki opisane [tutaj.](./azure-to-azure-tutorial-failback.md)
