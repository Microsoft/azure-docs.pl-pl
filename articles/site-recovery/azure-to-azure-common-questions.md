---
title: Często zadawane pytania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure przy użyciu programu Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397969"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Często zadawane pytania: odzyskiwanie po awarii z platformy Azure do platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure do innego regionu platformy Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .

## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>W jaki sposób Site Recovery cena?

Dowiedz się więcej o [kosztach](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) odzyskiwania po awarii maszyny wirtualnej platformy Azure.

### <a name="how-does-the-free-tier-work"></a>Jak działa bezpłatna warstwa?

Każde wystąpienie chronione za pomocą Site Recovery jest bezpłatne przez pierwsze 31 dni ochrony. Po upływie tego okresu ochrona każdego wystąpienia ma według stawek podsumowania w [szczegółach cennika](https://azure.microsoft.com/pricing/details/site-recovery/). Koszty można oszacować przy użyciu [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=site-recovery).

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Czy opłaty za korzystanie z platformy Azure są naliczane w ciągu pierwszych 31 dni?

Tak. Mimo że Azure Site Recovery jest bezpłatny w ciągu pierwszych 31 dni chronionego wystąpienia, mogą zostać naliczone opłaty za usługę Azure Storage, transakcje magazynu i transfery danych. Odzyskana maszyna wirtualna może również naliczać opłaty za usługę Azure COMPUTE. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Jak mogę rozpocząć pracę z odzyskiwaniem po awarii maszyny wirtualnej platformy Azure?

1. [Zapoznaj](azure-to-azure-architecture.md) się z architekturą odzyskiwania po awarii maszyny wirtualnej platformy Azure.
2. [Przejrzyj](azure-to-azure-support-matrix.md) wymagania dotyczące pomocy technicznej.
3. [Skonfiguruj](azure-to-azure-how-to-enable-replication.md) odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure.
4. [Uruchom drążenie odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) z testowym trybem failover.
5. [Uruchom pełną pracę w trybie failover](azure-to-azure-tutorial-failover-failback.md) w regionie pomocniczym platformy Azure.
6. [Powrót po awarii](azure-to-azure-tutorial-failback.md) z regionu pomocniczego do regionu podstawowego.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Jak zapewnić pojemność w regionie docelowym?

Zespół Site Recovery i zespół zarządzania pojemnością platformy Azure planują wystarczającą pojemność infrastruktury. Po uruchomieniu trybu failover zespoły mogą również upewnić się, że wystąpienia maszyn wirtualnych chronione przez Site Recovery są wdrażane w regionie docelowym.

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Czy można replikować maszyny wirtualne z szyfrowaniem dysków?

Tak. Site Recovery obsługuje odzyskiwanie po awarii maszyn wirtualnych z włączoną funkcją Azure Disk Encryption (ADE). Po włączeniu replikacji platforma Azure kopiuje wszystkie wymagane klucze szyfrowania dysków i wpisy tajne z regionu źródłowego do regionu docelowego w kontekście użytkownika. Jeśli nie masz wymaganych uprawnień, administrator zabezpieczeń może skopiować klucze i wpisy tajne za pomocą skryptu.

- Site Recovery obsługuje ADE dla maszyn wirtualnych platformy Azure z systemem Windows.
- Site Recovery obsługuje:
    - ADE wersja 0,1, która zawiera schemat, który wymaga Azure Active Directory (Azure AD).
    - ADE w wersji 1,1, która nie wymaga usługi Azure AD. W przypadku wersji 1,1 maszyny wirtualne z systemem Windows Azure muszą mieć dyski zarządzane.
    - [Dowiedz się więcej](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). o schematach rozszerzeń.

[Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md) na temat włączania replikacji szyfrowanych maszyn wirtualnych.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Czy mogę wybrać konto usługi Automation z innej grupy zasobów?

Gdy zezwolisz Site Recovery na zarządzanie aktualizacjami dla rozszerzenia usługi mobilności uruchomionego na replikowanych maszynach wirtualnych platformy Azure, zostanie wdrożony globalny element Runbook (używany przez usługi platformy Azure) za pośrednictwem konta usługi Azure Automation. Możesz użyć konta usługi Automation, które Site Recovery utworzyć, lub wybrać użycie istniejącego konta usługi Automation. 

Obecnie w portalu można wybrać tylko konto usługi Automation w tej samej grupie zasobów co magazyn. Możesz wybrać konto usługi Automation z innej grupy zasobów przy użyciu programu PowerShell. [Dowiedz się więcej](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Czy w przypadku korzystania z konta usługi Automation klienta, które nie znajduje się w grupie zasobów magazynu, można usunąć domyślny element Runbook?

Tak, możesz je usunąć, jeśli nie jest to potrzebne. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>Czy można replikować maszyny wirtualne do innej subskrypcji?

Tak. maszyny wirtualne platformy Azure można replikować do dowolnej subskrypcji w ramach tej samej dzierżawy usługi Azure AD. Po włączeniu odzyskiwania po awarii dla maszyn wirtualnych domyślnie pokazana subskrypcja dotyczy źródłowej maszyny wirtualnej. Można modyfikować subskrypcję docelową oraz inne ustawienia (takie jak grupa zasobów i Sieć wirtualna), które są automatycznie wypełniane z wybranej subskrypcji.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Czy można replikować maszyny wirtualne w strefie dostępności do innego regionu?

Tak. maszyny wirtualne można replikować w strefach dostępności do innego regionu platformy Azure. Docelową maszynę wirtualną można wdrożyć jako pojedyncze wystąpienie, w zestawie dostępności lub w strefie dostępności, jeśli jest ona obsługiwana w regionie docelowym. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Czy można replikować maszyny wirtualne niebędące strefami do strefy w tym samym regionie? 

Nie jest to obsługiwane w portalu. Do tego celu można użyć interfejsu API REST/PowerShell.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Czy można replikować maszyny wirtualne do innej strefy w tym samym regionie?

Obsługa tej usługi jest ograniczona do kilku regionów. [Dowiedz się więcej](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Czy można wykluczać dyski z replikacji?

Tak, podczas konfigurowania replikacji można wykluczyć dyski przy użyciu programu PowerShell. [Dowiedz się więcej](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Czy można replikować nowe dyski do replikowanych maszyn wirtualnych?

W przypadku zreplikowanych maszyn wirtualnych z dyskami zarządzanymi można dodać nowe dyski i włączyć dla nich replikację. Po dodaniu nowego dysku replikowana maszyna wirtualna pokazuje komunikat ostrzegawczy informujący o tym, że co najmniej jeden dysk na maszynie wirtualnej jest dostępny do ochrony. 

- Po włączeniu replikacji dla dodanych dysków ostrzeżenie zniknie po replikacji początkowej.
- Jeśli nie chcesz włączać replikacji dla dysku, możesz odrzucić ostrzeżenie.
- Jeśli maszyna wirtualna zostanie przełączona w tryb failover przy użyciu dodanych dysków, punkty replikacji pokazują dyski dostępne do odzyskania. Na przykład po dodaniu drugiego dysku do maszyny wirtualnej z jednym dyskiem punkt replikacji utworzony przed dodaniem zostanie wyświetlony jako "1 z 2 dysków".

Site Recovery nie obsługuje "gorąca usuwanie" dysków z zreplikowanej maszyny wirtualnej. W przypadku usunięcia dysku maszyny wirtualnej należy wyłączyć i ponownie włączyć replikację maszyny wirtualnej.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często mogę przeprowadzić replikację do platformy Azure?

Replikacja jest ciągła w przypadku replikowania maszyn wirtualnych platformy Azure do innego regionu platformy Azure. [Dowiedz się więcej](./azure-to-azure-architecture.md#replication-process) o tym, jak działa replikacja.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Czy można replikować maszyny wirtualne w obrębie regionu? 

Nie można użyć Site Recovery do replikowania dysków w regionie.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Czy można replikować wystąpienia maszyn wirtualnych do dowolnego regionu platformy Azure?

Maszyny wirtualne można replikować i odzyskiwać między dwoma regionami w tym samym klastrze geograficznym. Klastry geograficzne są definiowane z uwzględnieniem opóźnień danych i suwerenności. [Dowiedz się więcej](./azure-to-azure-support-matrix.md#region-support) o obsłudze regionów.

### <a name="does-site-recovery-need-internet-connectivity"></a>Czy Site Recovery wymaga łączności z Internetem?

Nie, ale maszyny wirtualne muszą mieć dostęp do Site Recovery adresów URL i zakresów adresów IP. [Dowiedz się więcej](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Czy można replikować aplikacje między grupami zasobów?

Tak, można replikować aplikację i zachować konfigurację odzyskiwania po awarii w oddzielnej grupie zasobów.

Jeśli na przykład aplikacje mają trzy warstwy (Application/Database/Web) w różnych grupach zasobów, należy włączyć replikację trzy razy, aby chronić wszystkie warstwy. Site Recovery replikuje trzy warstwy do trzech różnych grup zasobów.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Czy mogę przenieść konta magazynu między grupami zasobów?

Nie, to nie jest obsługiwane. W przypadku przypadkowego przeniesienia kont magazynu do innej grupy zasobów i usunięcia oryginalnej grupy zasobów można utworzyć nową grupę zasobów o takiej samej nazwie jak stara Grupa zasobów, a następnie przenieść konto magazynu do tej grupy zasobów.

## <a name="replication-policy"></a>Zasady replikacji

### <a name="what-is-a-replication-policy"></a>Co to są zasady replikacji?

Zasady replikacji definiują historię przechowywania punktów odzyskiwania oraz częstotliwość migawek spójnych z aplikacjami.  Site Recovery tworzy domyślne zasady replikacji w następujący sposób:

- Przechowuj punkty odzyskiwania przez 24 godziny.
- Wykonaj migawki spójne z aplikacjami co cztery godziny.

[Dowiedz się więcej](azure-to-azure-how-to-enable-replication.md#customize-target-resources) o ustawieniach replikacji.

### <a name="whats-a-crash-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie awarii?

Punkt odzyskiwania spójny na poziomie awarii zawiera dane na dysku, tak jak w przypadku ściągania przewodu z serwera podczas tworzenia migawki. Nie obejmuje to niczego, co było w pamięci podczas tworzenia migawki.

Obecnie większość aplikacji może odzyskać z migawek spójnych na poziomie awarii. Punkt odzyskiwania spójny na poziomie awarii jest zwykle wystarczający dla systemów operacyjnych innych niż bazy danych, a także aplikacji, takich jak serwery plików, serwery DHCP i serwery wydruku.

Site Recovery automatycznie tworzy punkt odzyskiwania spójny na poziomie awarii co pięć minut.

### <a name="whats-an-application-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie aplikacji?

Punkty odzyskiwania spójne z aplikacjami są tworzone na podstawie migawek spójnych na poziomie aplikacji. Przechwytują one te same dane co w przypadku migawek spójnych na poziomie awarii oraz dodatkowo przechwytują dane w pamięci oraz wszystkie transakcje w procesie.

Ze względu na dodatkową zawartość, migawki spójne z aplikacjami są najbardziej wykorzystywane i nie mogą być dłuższe. Zalecamy używanie punktów odzyskiwania spójnych na poziomie aplikacji dla systemów operacyjnych baz danych i aplikacji, takich jak SQL Server. W przypadku systemu Windows migawki spójne z aplikacjami używają Usługa kopiowania woluminów w tle (VSS).

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Czy punkty odzyskiwania spójne z aplikacjami mają wpływ na wydajność?

 Punkty odzyskiwania spójne z aplikacją przechwytują wszystkie dane w pamięci i procesu, jeśli są przechwytywane często, mogą mieć wpływ na wydajność, gdy obciążenie jest już zajęte. Nie zaleca się przechwycenia zbyt często dla obciążeń niezwiązanych z bazą danych. Nawet w przypadku obciążeń związanych z bazami danych jedna godzina powinna być wystarczająca.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Jaka jest minimalna częstotliwość generowania punktów odzyskiwania spójnych na poziomie aplikacji?

Site Recovery można utworzyć punkty odzyskiwania spójne z aplikacjami o minimalnej częstotliwości jednej godziny.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Czy mogę włączyć replikację spójną na poziomie aplikacji dla maszyn wirtualnych z systemem Linux?

Tak. Agent mobilności dla systemu Linux obsługuje niestandardowe skrypty dla spójności aplikacji. Agent jest używany w skrypcie niestandardowym z opcjami pre i post. [Dowiedz się więcej](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak są generowane i zapisywane punkty odzyskiwania?

Aby zrozumieć, jak Site Recovery generuje punkty odzyskiwania, użyjmy przykładu. 

- Zasady replikacji przechowują punkty odzyskiwania przez 24 godziny i pobierają migawki częstotliwości spójnej na poziomie aplikacji co godzinę.
- Site Recovery tworzy punkt odzyskiwania spójny na poziomie awarii co pięć minut. Tej częstotliwości nie można zmienić.
- Site Recovery oczyszcza punkty odzyskiwania po godzinie, zapisując jeden punkt na godzinę.

Tak więc w ciągu ostatniej godziny można wybrać jeden z 12 punktów spójnych pod kątem awarii i drugi punkt spójny z aplikacją, jak pokazano na ilustracji.

   ![Lista wygenerowanych punktów odzyskiwania](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko z powrotem mogę odzyskać?

Najstarszym punktem odzyskiwania, którego można użyć, jest 72 godzin.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Co się stanie, jeśli Site Recovery nie może wygenerować punktów odzyskiwania przez więcej niż 24 godziny? 

Jeśli masz zasady replikacji przez 24 godziny, a Site Recovery nie mogą generować punktów odzyskiwania przez więcej niż 24 godziny, Twoje stare punkty odzyskiwania pozostaną. Site Recovery zastępuje tylko najstarszy punkt, jeśli generuje nowe punkty. Dopóki nie pojawią się nowe punkty odzyskiwania, wszystkie stare punkty pozostają po przejściu do okna przechowywania.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Czy mogę zmienić zasady replikacji po włączeniu replikacji?

Tak. W > magazynu **Site Recovery**  >  **zasady replikacji** infrastruktury wybierz i Edytuj zasady. Zmiany dotyczą już istniejących zasad.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Czy wszystkie punkty odzyskiwania są kompletną kopią maszyny wirtualnej?

Pierwszy wygenerowany punkt odzyskiwania ma kompletną kopię. Kolejne punkty odzyskiwania mają zmiany różnicowe.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Zwiększenie kosztów przechowywania punktów odzyskiwania zwiększa koszty magazynowania?

Tak. Na przykład w przypadku zwiększenia okresu przechowywania z 24 godzin do 72, Site Recovery zapisuje punkty odzyskiwania przez dodatkowe 48 godzin. Dodanego czasu wiążą się z uwzględnieniem zmian w magazynie. Przykładowo, jeśli pojedynczy punkt odzyskiwania ma zmiany różnicowe o wartości 10 GB, a koszt za GB wynoszący $0,16 miesięcznie, opłaty dodatkowe byłyby $1,60 × 48 miesięcznie.

## <a name="multi-vm-consistency"></a>Spójność między MASZYNami wirtualnymi

### <a name="what-is-multi-vm-consistency"></a>Co to jest spójność między MASZYNami wirtualnymi?

Spójność wielu maszyn wirtualnych zapewnia spójność punktów odzyskiwania między replikowanymi maszynami wirtualnymi.

- Po włączeniu spójności między MASZYNami wirtualnymi Site Recovery tworzy grupę replikacji wszystkich maszyn z włączoną opcją. 
- Po przełączeniu w tryb failover maszyny w grupie replikacji mają udostępnione punkty odzyskiwania spójne pod kątem awarii i aplikacje.

[Informacje](azure-to-azure-tutorial-enable-replication.md#enable-replication) o sposobie włączania spójności między maszynami wirtualnymi.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Czy można przełączyć pojedynczą maszynę wirtualną do trybu failover w grupie replikacji?

Nie. Włączenie spójności między MASZYNami wirtualnymi oznacza, że aplikacja ma zależność od wszystkich maszyn wirtualnych w grupie replikacji, a pojedyncza praca w trybie failover jest niedozwolona.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Ile maszyn wirtualnych można replikować razem w grupie?

Można replikować 16 maszyn wirtualnych razem w grupie replikacji.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Kiedy należy włączyć spójność wiele maszyn wirtualnych?

Spójność wielu maszyn wirtualnych jest intensywnym PROCESORem, a włączenie jej może wpłynąć na wydajność obciążeń. Włącz opcję tylko wtedy, gdy maszyny wirtualne korzystają z tego samego obciążenia, i potrzebujesz spójności na wielu maszynach. Na przykład jeśli masz dwa wystąpienia SQL Server i dwa serwery sieci Web w aplikacji, Włącz tylko spójność dla tego wystąpienia SQL Server.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Czy mogę dodać replikację maszyny wirtualnej do grupy replikacji?

Po włączeniu replikacji dla maszyny wirtualnej można ją dodać do nowej grupy replikacji lub do istniejącej grupy. Nie można dodać maszyny wirtualnej, która jest już replikowana do grupy. 
 
## <a name="failover"></a>Tryb failover

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Jak zapewnić pojemność w regionie docelowym?

Zespół Site Recovery i zespół ds. zarządzania pojemnością platformy Azure planują wystarczającą pojemność infrastruktury. Po rozpoczęciu pracy w trybie failover zespoły są również pomocne w zapewnieniu, że wystąpienia maszyn wirtualnych chronione przez Site Recovery mogą być wdrażane w regionie docelowym.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

Tryb failover nie jest automatyczny. Możesz uruchomić tryb failover za pomocą jednego kliknięcia w portalu lub użyć  [programu PowerShell](azure-to-azure-powershell.md) do wyzwolenia trybu failover.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Czy można zachować publiczny adres IP po przejściu do trybu failover?

Nie można zachować publicznego adresu IP dla aplikacji produkcyjnej po przejściu do trybu failover.

Podczas przełączania obciążenia w ramach procesu przełączania do trybu failover należy przypisać do niego zasób publicznego adresu IP platformy Azure. Zasób musi być dostępny w regionie docelowym. Zasób publicznego adresu IP platformy Azure można przypisać ręcznie lub zautomatyzować przy użyciu planu odzyskiwania. [Dowiedz się](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) , jak skonfigurować publiczne adresy IP po przejściu do trybu failover.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Czy można zachować prywatny adres IP po przejściu do trybu failover?

Tak. Domyślnie po włączeniu odzyskiwania po awarii dla maszyn wirtualnych platformy Azure Site Recovery tworzy zasoby docelowe na podstawie ustawień zasobów źródłowych. W przypadku maszyn wirtualnych platformy Azure skonfigurowanych ze statycznymi adresami IP Site Recovery próbuje zainicjować ten sam adres IP dla docelowej maszyny wirtualnej, jeśli nie jest używany.
[Dowiedz się więcej o](site-recovery-retain-ip-azure-vm-failover.md) przechowywaniu adresów IP po przejściu do trybu failover.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Dlaczego po przejściu w tryb failover do maszyny wirtualnej przypisano nowy adres IP?

Site Recovery próbuje podać adres IP w momencie przejścia w tryb failover. Jeśli inna maszyna wirtualna używa tego adresu, Site Recovery ustawia następny dostępny adres IP jako element docelowy.

[Dowiedz się więcej o](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) konfigurowaniu mapowania sieci i adresowania IP dla sieci wirtualnych.

### <a name="whats-the-latest-recovery-point"></a>Co to jest *najnowszy* punkt odzyskiwania?

Najnowszy punkt odzyskiwania *(najniższy cel RPO)* wykonuje następujące czynności:

1. Najpierw przetwarza wszystkie dane, które zostały wysłane do Site Recovery.
2. Gdy usługa przetwarza dane, tworzy punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem do trybu failover na maszynie wirtualnej. Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO).
3. Maszyna wirtualna utworzona po przejściu w tryb failover ma wszystkie dane zreplikowane do Site Recovery z momentu wyzwolenia trybu failover.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>Czy *najnowsze* punkty odzyskiwania wpływają na RTO pracy awaryjnej?

Tak. Site Recovery przetwarza wszystkie oczekujące dane przed przełączeniem w tryb failover, dlatego ta opcja ma wyższy cel czasu odzyskiwania (RTO) niż inne opcje.

### <a name="whats-the-latest-processed-recovery-option"></a>Co to jest *Najnowsza przetworzona* opcja odzyskiwania?

*Najnowsza przetworzona* opcja wykonuje następujące czynności:

1. Przejdzie ona do trybu failover wszystkich maszyn wirtualnych do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Ta opcja zapewnia niską RTO, ponieważ nie ma czasu poświęcanego na przetwarzanie nieprzetworzonych danych.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Co w przypadku nieoczekiwanej awarii w regionie podstawowym?

Można uruchomić tryb failover. Site Recovery nie wymaga łączności z regionu podstawowego w celu przełączenia w tryb failover.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Co to jest RTO maszyny wirtualnej w trybie failover?

Site Recovery ma umowę SLA RTO na [dwie godziny](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). W większości przypadków Site Recovery przejdzie w tryb failover maszyn wirtualnych w ciągu kilku minut. Aby obliczyć RTO, przejrzyj zadanie trybu failover, w którym jest wyświetlany czas potrzebny do przeprowadzenia maszyny wirtualnej. 

## <a name="recovery-plans"></a>Plany odzyskiwania

### <a name="whats-a-recovery-plan"></a>Co to jest plan odzyskiwania?

[Plan odzyskiwania](site-recovery-create-recovery-plans.md) w Site Recovery organizować tryb failover i odzyskiwanie maszyn wirtualnych. Ułatwia to odzyskiwanie spójnie, powtarzalne i zautomatyzowane. Wykonuje następujące czynności:

- Definiuje grupę maszyn wirtualnych, które są łączone w tryb failover
- Definiuje zależności między maszynami wirtualnymi, aby aplikacja była dokładnie dostępna.
- Automatyzuje odzyskiwanie przy użyciu opcji niestandardowych akcji ręcznych dla zadań innych niż tryb failover maszyny wirtualnej. 


### <a name="how-does-sequencing-work"></a>Jak działa sekwencjonowanie?

W planie odzyskiwania można utworzyć wiele grup maszyn wirtualnych na potrzeby sekwencjonowania. Grupy są przełączane pojedynczo, dzięki czemu maszyny wirtualne będące częścią tej samej grupy są przełączane w tryb failover. [Dowiedz się więcej](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak mogę znaleźć RTO planu odzyskiwania?

Aby sprawdzić RTO planu odzyskiwania, wykonaj test pracy w trybie failover dla planu odzyskiwania. W obszarze **zadania Site Recovery** Sprawdź czas trwania testu pracy w trybie failover. Na przykładowym zrzucie ekranu zadanie testowego trybu failover **SAPTestRecoveryPlan** zajęło 8 minut i 59 sekund.

![Wyświetl listę zadań pokazujących czas trwania testowej pracy w trybie failover dla RTO](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Czy mogę dodać elementy Runbook usługi Automation do planów odzyskiwania?

Tak. [Dowiedz się więcej](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ochrona i powrót po awarii

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Czy po przejściu w tryb failover maszyny wirtualne w regionie pomocniczym są chronione automatycznie? 

Nie. Po przełączeniu w tryb failover maszyn wirtualnych z jednego regionu do innego, maszyny wirtualne są uruchamiane w docelowym regionie odzyskiwania po awarii w stanie niechronionym. Aby ponownie [chronić](./azure-to-azure-how-to-reprotect.md) maszyny wirtualne w regionie pomocniczym, należy włączyć replikację do regionu podstawowego.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Czy podczas ponownego włączania ochrony wszystkie dane zreplikowane z regionu pomocniczego mają być podstawowe? 

Jest to zależne od. Jeśli istnieje maszyna wirtualna regionu źródłowego, zostaną zsynchronizowane tylko zmiany między dyskiem źródłowym a dyskiem docelowym. Site Recovery porównuje dyski z różnymi możliwościami, a następnie przesyła dane. Ten proces zazwyczaj trwa kilka godzin. [Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Jak długo trwa powrót po awarii?

Po przeprowadzeniu ochrony powrót po awarii pobiera ten sam czas pracy awaryjnej z regionu podstawowego do regionu pomocniczego.

## <a name="capacity"></a><a name="capacity"></a>Pojemność

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Jak zapewnić pojemność w regionie docelowym?

Plan zespołu Site Recovery i zarządzanie pojemnością platformy Azure w celu zapewnienia wystarczającej pojemności infrastruktury. Po rozpoczęciu pracy w trybie failover zespoły są również pomocne w zapewnieniu, że wystąpienia maszyn wirtualnych chronione przez Site Recovery mogą być wdrażane w regionie docelowym.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Czy Site Recovery współpracuje z wystąpieniami zarezerwowanymi?

Tak, możesz zakupić [zastrzeżone maszyny wirtualne platformy Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/) w regionie odzyskiwania po awarii, a w przypadku korzystania z nich Site Recovery operacji przejścia w tryb failover. Nie jest wymagana dodatkowa konfiguracja.

## <a name="security"></a>Zabezpieczenia

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?

Nie, Site Recovery nie przechwytuje replikowanych danych i nie zawiera żadnych informacji o działaniach na maszynach wirtualnych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.

Site Recovery to ISO 27001:2013, 27018, HIPAA i DPA certyfikowane. Usługa jest SOC2 i FedRAMP oceny JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?

Tak, obsługiwane są zarówno szyfrowanie podczas przesyłania, jak i [szyfrowanie na platformie Azure](../storage/common/storage-service-encryption.md) .

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj wymagania dotyczące pomocy technicznej platformy Azure na platformie Azure](azure-to-azure-support-matrix.md).
- [Skonfiguruj replikację z platformy Azure na platformę Azure](azure-to-azure-tutorial-enable-replication.md).
- Jeśli masz pytania po zapoznaniu się z tym artykułem, Opublikuj je na [stronie pytań firmy Microsoft dotyczącym usługi Azure Recovery Services&](/answers/topics/azure-site-recovery.html).
