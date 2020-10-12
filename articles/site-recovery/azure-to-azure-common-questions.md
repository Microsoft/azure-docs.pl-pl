---
title: Często zadawane pytania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure przy użyciu programu Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7bc8427a51a9931ca82155232569767f12a8e266
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87534026"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Często zadawane pytania: odzyskiwanie po awarii z platformy Azure do platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure w innym regionie świadczenia usługi Azure w przypadku korzystania z [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>W jaki sposób Site Recovery cena?

Zapoznaj się z [Azure Site Recovery cennikami maszyn wirtualnych](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Jak działa bezpłatna warstwa Azure Site Recovery?

Każde wystąpienie chronione za pomocą Azure Site Recovery jest bezpłatne przez pierwsze 31 dni ochrony. Po upływie tego okresu ochrona każdego wystąpienia ma na [Azure Site Recovery ceny za usługę Azure Virtual Machines](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy w ciągu pierwszych 31 dni będą naliczane inne opłaty za korzystanie z platformy Azure?

Tak. Mimo że Azure Site Recovery jest bezpłatny w ciągu pierwszych 31 dni chronionego wystąpienia, mogą zostać naliczone opłaty za usługę Azure Storage, transakcje magazynu i transfery danych. Odzyskana maszyna wirtualna może również naliczać opłaty za usługę Azure COMPUTE. Zapoznaj się ze szczegółowymi informacjami na temat cen [Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Jakie są najlepsze rozwiązania dotyczące odzyskiwania po awarii w usłudze Azure Virtual Machines?

1. [Informacje o architekturze platformy Azure do platformy Azure](azure-to-azure-architecture.md)
1. [Przejrzyj obsługiwane i nieobsługiwane konfiguracje](azure-to-azure-support-matrix.md)
1. [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-how-to-enable-replication.md)
1. [Wykonywanie testu przejścia w tryb failover](azure-to-azure-tutorial-dr-drill.md)
1. [Przełączenie w tryb failover i powrót po awarii do regionu podstawowego](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Jak jest zapewniona pojemność w regionie docelowym?

Plan zespołu Site Recovery i zarządzanie pojemnością platformy Azure w celu zapewnienia wystarczającej pojemności infrastruktury. Po uruchomieniu trybu failover zespoły mogą także zapewnić, że wystąpienia maszyn wirtualnych chronione przez Site Recovery zostaną wdrożone w regionie docelowym.

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Czy można replikować maszyny wirtualne z włączoną funkcją szyfrowania dysków Azure?

Tak. Site Recovery obsługuje odzyskiwanie po awarii maszyn wirtualnych z włączonymi Azure Disk Encryption. Po włączeniu replikacji platforma Azure kopiuje wszystkie wymagane klucze szyfrowania dysków i wpisy tajne z regionu źródłowego do regionu docelowego w kontekście użytkownika. Jeśli nie masz odpowiednich uprawnień, administrator zabezpieczeń może skopiować klucze i wpisy tajne za pomocą skryptu.

- Site Recovery obsługuje Azure Disk Encryption maszyn wirtualnych platformy Azure z systemem Windows.
- Site Recovery obsługuje Azure Disk Encryption wersji 0,1, która zawiera schemat, który wymaga Azure Active Directory (Azure AD). Site Recovery obsługuje również wersję 1,1, która nie wymaga usługi Azure AD. [Dowiedz się więcej o schemacie rozszerzeń dla usługi Azure Disk Encryption](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - W przypadku Azure Disk Encryption w wersji 1,1 musisz użyć maszyn wirtualnych z systemem Windows z dyskami zarządzanymi.
  - [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md) na temat włączania replikacji szyfrowanych maszyn wirtualnych.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Czy mogę wybrać konto usługi Automation z innej grupy zasobów?

Ta funkcja nie jest obecnie obsługiwana przez portal, ale można wybrać konto usługi Automation z innej grupy zasobów za pośrednictwem programu PowerShell.

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>Czy po określeniu konta usługi Automation, które znajduje się w innej grupie zasobów niż magazyn, czy mogę usunąć element Runbook, jeśli nie istnieje żaden inny magazyn?

Utworzony niestandardowy element Runbook jest narzędziem i można go bezpiecznie usunąć, jeśli taka sama nie jest wymagana.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Czy można replikować maszyny wirtualne do innej subskrypcji?

Tak. maszyny wirtualne platformy Azure można replikować do innej subskrypcji w ramach tej samej dzierżawy usługi Azure AD.

Skonfiguruj odzyskiwanie po awarii [w różnych subskrypcjach](https://azure.microsoft.com/blog/cross-subscription-dr) , wybierając kolejną subskrypcję w momencie replikacji.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Czy można replikować przypięte do strefy maszyny wirtualne platformy Azure do innego regionu?

Tak, można [replikować przypięte strefy maszyny wirtualne](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do innego regionu.

### <a name="can-i-replicate-vms-in-a-region-that-has-zones-from-non-zone-to-zonal-configuration"></a>Czy można replikować maszyny wirtualne w regionie, w którym znajdują się strefy od strefy nienależącej do warstwy?

Nie, to nie jest obecnie obsługiwane. Aby obejść ten sposób, można replikować maszynę wirtualną przy użyciu funkcji ASR do konfiguracji stref w innym regionie, a następnie wyłączyć replikację. Następnie ponownie Włącz replikację z tego regionu do oryginalnego regionu i wybierz strefę konfiguracji dla trybu failover.

### <a name="can-i-exclude-disks"></a>Czy można wykluczać dyski?

Tak, możesz wykluczyć dyski w czasie ochrony przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [jak wykluczać dyski z replikacji](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Czy mogę dodać nowe dyski do replikowanych maszyn wirtualnych i włączyć dla nich replikację?

Tak, dodanie nowych dysków do replikowanych maszyn wirtualnych i włączenie dla nich replikacji jest obsługiwane w przypadku maszyn wirtualnych platformy Azure z dyskami zarządzanymi. Po dodaniu nowego dysku do maszyny wirtualnej platformy Azure, na której włączono replikację, kondycja replikacji maszyny wirtualnej wyświetla ostrzeżenie. To ostrzeżenie oznacza, że co najmniej jeden dysk w maszynie wirtualnej jest dostępny do ochrony. Można włączyć replikację dla dodanych dysków.

- Jeśli włączysz ochronę dla dodanych dysków, ostrzeżenie zniknie po replikacji początkowej.
- Jeśli nie włączysz replikacji dla dysku, możesz odrzucić ostrzeżenie.
- W przypadku przełączenia w tryb failover maszyny wirtualnej, która ma dodany dysk i włączono replikację, istnieją punkty replikacji. Punkty replikacji będą zawierać dyski dostępne do odzyskania.

Załóżmy na przykład, że maszyna wirtualna ma jeden dysk i zostanie dodany nowy. Może istnieć punkt replikacji, który został utworzony przed dodaniem dysku. Ten punkt replikacji pokazuje, że składa się z "1 z 2 dysków".

Site Recovery nie obsługuje "gorąca usuwanie" dysku z zreplikowanej maszyny wirtualnej. W przypadku usunięcia dysku maszyny wirtualnej należy wyłączyć i ponownie włączyć replikację maszyny wirtualnej.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często mogę przeprowadzić replikację do platformy Azure?

Replikacja jest ciągła w przypadku replikowania maszyn wirtualnych platformy Azure do innego regionu platformy Azure. Aby uzyskać więcej informacji, zobacz [Architektura replikacji z platformy Azure do platformy Azure](./azure-to-azure-architecture.md#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Czy można replikować maszyny wirtualne w obrębie regionu? Potrzebuję tej funkcji do migrowania maszyn wirtualnych.

Nie można użyć rozwiązania odzyskiwania dysku z platformy Azure na platformę Azure do replikowania maszyn wirtualnych w regionie.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Czy można replikować wystąpienia maszyn wirtualnych do dowolnego regionu platformy Azure?

Za pomocą Site Recovery można replikować i odzyskiwać maszyny wirtualne między dowolnymi dwoma regionami w tym samym klastrze geograficznym. Klastry geograficzne są definiowane z uwzględnieniem opóźnień danych i suwerenności. Aby uzyskać więcej informacji, zobacz [Macierz obsługi Site Recovery regionie](./azure-to-azure-support-matrix.md#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Czy Site Recovery wymaga połączenia z Internetem?

Nie, Site Recovery nie wymaga łączności z Internetem. Jednak wymaga dostępu do Site Recovery adresów URL i zakresów adresów IP, jak wspomniano w temacie [Obsługa sieci w przypadku odzyskiwania po awarii maszyny wirtualnej platformy Azure](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Czy można replikować aplikację, która ma osobną grupę zasobów dla różnych warstw?

Tak, można replikować aplikację i zachować konfigurację odzyskiwania po awarii w osobnej grupie zasobów.

Jeśli na przykład aplikacja ma aplikację, bazę danych i sieć Web każdej warstwy, należy wybrać [Kreatora replikacji](./azure-to-azure-how-to-enable-replication.md#enable-replication) trzy razy, aby chronić wszystkie warstwy. Site Recovery będą replikować te trzy warstwy do trzech różnych grup zasobów.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Czy mogę przenieść konta magazynu między grupami zasobów?

Nie, ten scenariusz jest nieobsługiwany. Jeśli jednak przypadkowo przeniesiesz konta magazynu do innej grupy zasobów i usuniesz pierwotną grupę zasobów, możesz utworzyć nową grupę zasobów o takiej samej nazwie jak stara Grupa zasobów, a następnie przenieść konto magazynu do tej grupy zasobów.

## <a name="replication-policy"></a>Zasady replikacji

### <a name="what-is-a-replication-policy"></a>Co to są zasady replikacji?

Zasady replikacji określają ustawienia dla historii przechowywania punktów odzyskiwania. Zasady definiują również częstotliwość migawek spójnych z aplikacjami. Domyślnie Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi:

- 24 godziny dla historii przechowywania punktów odzyskiwania.
- 4 godziny dla częstotliwości migawek spójnych z aplikacjami.

[Dowiedz się więcej o ustawieniach replikacji](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie awarii?

Punkt odzyskiwania spójny na poziomie awarii zawiera dane na dysku, tak jak w przypadku ściągania przewodu z serwera podczas tworzenia migawki. Punkt odzyskiwania spójny na poziomie awarii nie obejmuje niczego, co było w pamięci podczas tworzenia migawki.

Obecnie większość aplikacji może odzyskać z migawek spójnych na poziomie awarii. Punkt odzyskiwania spójny na poziomie awarii jest zwykle wystarczający dla systemów operacyjnych i aplikacji, takich jak serwery plików, serwery DHCP i serwery wydruku.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaka jest częstotliwość generowania punktów odzyskiwania spójnych na poziomie awarii?

Site Recovery tworzy punkt odzyskiwania spójny na poziomie awarii co 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie aplikacji?

Punkty odzyskiwania spójne z aplikacją są tworzone na podstawie migawek spójnych z aplikacjami. Punkty odzyskiwania spójne z aplikacją przechwytują te same dane co migawki spójne z awarią, a także przechwytują dane w pamięci i wszystkie transakcje w procesie.

Ze względu na dodatkową zawartość migawki spójne z aplikacjami są najbardziej wykorzystywane i są najdłuższe. Zalecamy używanie punktów odzyskiwania spójnych na poziomie aplikacji dla systemów operacyjnych i aplikacji baz danych, takich jak SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaki jest wpływ punktów odzyskiwania spójnych na poziomie aplikacji na wydajność aplikacji?

Punkty odzyskiwania spójne z aplikacją przechwytują wszystkie dane w pamięci i w procesie. Ponieważ punkty odzyskiwania przechwytują te dane, wymagają one struktury, takiej jak Usługa kopiowania woluminów w tle w systemie Windows w celu przełączenia aplikacji w stan spoczynku. Jeśli proces przechwytywania jest częsty, może mieć wpływ na wydajność, gdy obciążenie jest już zajęte. Nie zalecamy używania niskich częstotliwości dla punktów odzyskiwania spójnych z aplikacjami dla obciążeń niezwiązanych z bazą danych. Nawet w przypadku obciążenia bazy danych wystarczy 1 godzinę.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaka jest minimalna częstotliwość generowania punktów odzyskiwania spójnych z aplikacjami?

Site Recovery można utworzyć punkt odzyskiwania spójny dla aplikacji o minimalnej częstotliwości wynoszącej 1 godzinę.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak są generowane i zapisywane punkty odzyskiwania?

Aby zrozumieć, jak Site Recovery generuje punkty odzyskiwania, zobacz przykład zasad replikacji. Te zasady replikacji mają punkt odzyskiwania z 24-godzinnym okresem przechowywania i migawką częstotliwości zgodną z aplikacją (1 godzina).

Site Recovery tworzy punkt odzyskiwania spójny na poziomie awarii co 5 minut. Tej częstotliwości nie można zmienić. W ciągu ostatniej godziny można wybrać jeden z 12 punktów spójnych z awarią i 1 punkt spójności aplikacji. W miarę upływu czasu program Site Recovery czyści wszystkie punkty odzyskiwania poza ostatnią godziną i zapisuje tylko 1 punkt odzyskiwania na godzinę.

Poniższy zrzut ekranu ilustruje przykład. Zrzut ekranu:

- W ciągu ostatniej godziny istnieją punkty odzyskiwania z częstotliwością wynoszącą 5 minut.
- W ciągu ostatniej godziny Site Recovery zachowuje tylko 1 punkt odzyskiwania.

   ![Lista wygenerowanych punktów odzyskiwania](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko z powrotem mogę odzyskać?

Najstarszym punktem odzyskiwania, którego można użyć, jest 72 godzin.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mam zasady replikacji przez 24 godziny. Co się stanie, jeśli problem uniemożliwia Site Recovery wygenerowanie punktów odzyskiwania przez ponad 24 godziny? Czy moje poprzednie punkty odzyskiwania zostaną utracone?

Nie, Site Recovery będzie przechowywać wszystkie poprzednie punkty odzyskiwania. W zależności od okna przechowywania punktów odzyskiwania Site Recovery zastępuje najstarszy punkt tylko wtedy, gdy generuje nowe punkty. Z powodu problemu Site Recovery nie może wygenerować żadnych nowych punktów odzyskiwania. Dopóki nie pojawią się nowe punkty odzyskiwania, wszystkie stare punkty pozostaną po przejściu do okna przechowywania.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Jak zmienić zasady replikacji po włączeniu replikacji na maszynie wirtualnej?

Przejdź do **magazynu Site Recovery**  >  **Site Recovery**  >  **zasady replikacji**infrastruktury. Wybierz zasady, które chcesz edytować, i Zapisz zmiany. Każda zmiana zostanie również zastosowana do wszystkich istniejących replikacji.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Czy wszystkie punkty odzyskiwania są kompletną kopią maszyny wirtualnej czy różnicą?

Pierwszy wygenerowany punkt odzyskiwania ma kompletną kopię. Wszystkie kolejne punkty odzyskiwania mają zmiany różnicowe.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Czy zwiększenie okresu przechowywania punktów odzyskiwania zwiększa koszt magazynu?

Tak, jeśli okres przechowywania zostanie zwiększony z 24 godzin do 72 godzin, Site Recovery będzie zapisywać punkty odzyskiwania dla dodatkowych 48 godzin. Dodatkowy czas będzie powodować naliczanie opłat za magazyn. Na przykład pojedynczy punkt odzyskiwania może mieć zmiany różnicowe o wartości 10 GB i koszt za GB wynoszący $0,16 miesięcznie. Dodatkowe opłaty byłyby $1,60 × 48 miesięcznie.

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Czy mogę włączyć replikację z spójnością aplikacji na serwerach z systemem Linux?

Tak. Azure Site Recovery dla systemu operacyjnego Linux obsługuje niestandardowe skrypty aplikacji na potrzeby spójności aplikacji. Skrypt niestandardowy z opcjami pre i post będzie używany przez agenta mobilności Azure Site Recovery podczas spójności aplikacji. [Dowiedz się więcej](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>Spójność między MASZYNami wirtualnymi

### <a name="what-is-multi-vm-consistency"></a>Co to jest spójność między MASZYNami wirtualnymi?

Spójność wielu maszyn wirtualnych gwarantuje, że punkt odzyskiwania będzie spójny dla wszystkich zreplikowanych maszyn.

Site Recovery zapewnia opcję **spójności obejmującą wiele maszyn wirtualnych** , która tworzy grupę replikacji wszystkich maszyn.

Gdy maszyny wirtualne zostaną przełączone w tryb failover, będą mieć udostępnione punkty odzyskiwania spójne pod kątem awarii i aplikacje.

Zapoznaj się z samouczkiem, aby [włączyć spójność między maszynami](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm)wirtualnymi.

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Czy można przełączyć pojedynczą maszynę wirtualną do trybu failover w grupie replikacji spójnej z wielomaszynową kontrolą?

Po wybraniu opcji **spójności dotyczącej wiele maszyn** wirtualnych oznacza to, że aplikacja ma zależność na wszystkich maszynach, które znajdują się w grupie. Tryb failover pojedynczej maszyny wirtualnej jest niedozwolony.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Ile maszyn wirtualnych można replikować jako część grupy replikacji spójności z wieloma MASZYNami wirtualnymi?

Można replikować 16 maszyn wirtualnych razem w grupie replikacji.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Kiedy należy włączyć spójność wiele maszyn wirtualnych?

Ponieważ spójność wielu maszyn wirtualnych jest intensywnym PROCESORem, włączenie jej może wpłynąć na wydajność obciążeń. Spójność wielu maszyn wirtualnych należy stosować tylko wtedy, gdy na maszynach są uruchomione te same obciążenia i wymagana jest spójność między wieloma maszynami. Na przykład jeśli masz dwa SQL Server wystąpienia i dwa serwery sieci Web w aplikacji, należy mieć tylko spójność dla SQL Server wystąpień.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Czy można dodać już replikację maszyny wirtualnej do grupy replikacji?
Podczas włączania replikacji można dodać maszynę wirtualną do nowej grupy replikacji. Podczas włączania replikacji można także dodać maszynę wirtualną do istniejącej grupy replikacji. Nie można jednak dodać już replikowanej maszyny wirtualnej do nowej grupy replikacji lub istniejącej grupy replikacji.
 
## <a name="failover"></a>Tryb failover


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Jak jest zapewniana pojemność w regionie docelowym maszyn wirtualnych platformy Azure?

Plan zespołu Site Recovery i zarządzanie pojemnością platformy Azure w celu zapewnienia wystarczającej pojemności infrastruktury. Po uruchomieniu trybu failover zespoły mogą także zapewnić, że wystąpienia maszyn wirtualnych chronione przez Site Recovery zostaną wdrożone w regionie docelowym.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

Tryb failover nie jest automatyczny. Można uruchomić tryb failover za pomocą jednego kliknięcia w portalu lub można użyć [programu PowerShell](azure-to-azure-powershell.md) do wyzwolenia trybu failover.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Czy można zachować publiczny adres IP po przejściu w tryb failover?

Nie można zachować publicznego adresu IP aplikacji produkcyjnej po przejściu do trybu failover.

Podczas przełączania obciążenia w ramach procesu przełączania do trybu failover należy przypisać do obciążenia zasób publicznego adresu IP platformy Azure. Zasób publicznego adresu IP platformy Azure musi być dostępny w regionie docelowym. Możesz ręcznie przypisać zasób publicznego adresu IP platformy Azure lub zautomatyzować go przy użyciu planu odzyskiwania. Dowiedz się, jak [skonfigurować publiczne adresy IP po](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)przejściu do trybu failover.

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Czy można zachować prywatny adres IP podczas pracy w trybie failover?

Tak, możesz zachować prywatny adres IP. Domyślnie po włączeniu odzyskiwania po awarii dla maszyn wirtualnych platformy Azure Site Recovery tworzy zasoby docelowe na podstawie ustawień zasobów źródłowych. W przypadku usługi Azure Virtual Machines skonfigurowanych ze statycznymi adresami IP Site Recovery próbuje zainicjować ten sam adres IP dla docelowej maszyny wirtualnej, jeśli nie jest używany.
Informacje na temat [zachowywania adresów IP podczas pracy w trybie failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Dlaczego po przejściu w tryb failover serwer przypisał nowy adres IP?

Site Recovery próbuje podać adres IP w momencie przejścia w tryb failover. Jeśli dla tego adresu jest przydana inna maszyna wirtualna, Site Recovery ustawia następny dostępny adres IP jako element docelowy.

Dowiedz się więcej o [konfigurowaniu mapowania sieci i adresowania IP dla sieci wirtualnych](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Co to są najnowsze punkty odzyskiwania **(najniższy cel RPO)** ?

**Najnowsza opcja (najniższy cel punktu odzyskiwania)** najpierw przetwarza wszystkie dane, które zostały wysłane do Site Recovery. Gdy usługa przetwarza dane, tworzy punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem do trybu failover na maszynie wirtualnej. Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO). Maszyna wirtualna utworzona po przejściu w tryb failover ma wszystkie dane zreplikowane do Site Recovery z momentu wyzwolenia trybu failover.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Czy **najnowsze punkty odzyskiwania (najniższych RPO)** mają wpływ na RTO pracy awaryjnej?

Tak. Site Recovery przetwarza wszystkie oczekujące dane przed przełączeniem w tryb failover, dlatego ta opcja ma wyższy cel czasu odzyskiwania (RTO) w porównaniu z innymi opcjami.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Co oznacza **Najnowsza przetworzona** opcja w punktach odzyskiwania?

**Najnowsza przetworzona** opcja przejdzie w tryb failover wszystkich maszyn wirtualnych w planie do najnowszego punktu odzyskiwania, który Site Recovery przetworzony. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny wirtualnej. Ta opcja zapewnia niską RTO, ponieważ nie ma czasu poświęcanego na przetwarzanie nieprzetworzonych danych.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Co się stanie, jeśli w regionie podstawowym wystąpił nieoczekiwany przestój?

Po awarii można wyzwolić przejście w tryb failover. Site Recovery nie wymaga łączności z regionu podstawowego w celu przełączenia w tryb failover.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Co to jest RTO maszyny wirtualnej w trybie failover?

Site Recovery ma umowę [SLA RTO o wartości 2 godz](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Jednak w ciągu kilku minut Site Recovery przechodzić w tryb failover maszyn wirtualnych. Możesz obliczyć RTO, przechodząc do zadań trybu failover, co pokazuje czas trwania maszyny wirtualnej. W przypadku planu odzyskiwania RTO zapoznaj się z następną sekcją.

## <a name="recovery-plans"></a>Plany odzyskiwania

### <a name="what-is-a-recovery-plan"></a>Co to jest plan odzyskiwania?

Plan odzyskiwania w Site Recovery organizuje odzyskiwanie maszyn wirtualnych w trybie failover. Ułatwia to odzyskiwanie spójnie, powtarzalne i zautomatyzowane. Plan odzyskiwania dotyczy następujących wymagań:

- Definiowanie grupy maszyn wirtualnych, które są łączone w tryb failover
- Definiowanie zależności między maszynami wirtualnymi w celu dokładnego przetworzenia aplikacji
- Automatyzowanie odzyskiwania wraz z niestandardowymi akcjami ręcznymi w celu uzyskania zadań innych niż tryb failover maszyn wirtualnych

Dowiedz się więcej [o tworzeniu planów odzyskiwania](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Jak odbywa się sekwencjonowanie w planie odzyskiwania?

W planie odzyskiwania można utworzyć wiele grup, aby osiągnąć sekwencjonowanie. Każda grupa działa w trybie failover jednocześnie. Maszyny wirtualne, które są częścią tej samej grupy, są przełączane do trybu failover razem z inną grupą. Aby dowiedzieć się, jak modelować aplikację przy użyciu planu odzyskiwania, zobacz [Informacje o planach odzyskiwania](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak mogę znaleźć RTO planu odzyskiwania?

Aby sprawdzić RTO planu odzyskiwania, wykonaj test pracy w trybie failover dla planu odzyskiwania i przejdź do **Site Recovery zadań**.
W poniższym przykładzie zapoznaj się z **SAPTestRecoveryPlan**zadania. Zadanie zajęło 8 minut i 59 sekund na przełączenie w tryb failover wszystkich maszyn wirtualnych i wykonanie określonych akcji.

![Lista zadań Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Czy mogę dodać elementy Runbook usługi Automation do planu odzyskiwania?

Tak, możesz zintegrować Azure Automation elementów Runbook z planem odzyskiwania. Dowiedz się więcej o [dodawaniu Azure Automation elementów Runbook](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ochrona i powrót po awarii

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Po przełączeniu w tryb failover z regionu podstawowego do regionu odzyskiwania po awarii. Czy maszyny wirtualne w regionie DR są chronione automatycznie?

Nie. Po przełączeniu maszyn wirtualnych platformy Azure w [tryb failover](./azure-to-azure-tutorial-failover-failback.md) z jednego regionu do innego, maszyny wirtualne są uruchamiane w regionie odzyskiwania w stanie niechronionym. Aby zakończyć przywracanie maszyn wirtualnych do regionu podstawowego, należy ponownie [włączyć ochronę](./azure-to-azure-how-to-reprotect.md) maszyn wirtualnych w regionie pomocniczym.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Czy w czasie ponownej ochrony program Site Recovery replikowania pełnych danych z regionu pomocniczego do regionu podstawowego?

Zależy to od sytuacji. Jeśli istnieje maszyna wirtualna regionu źródłowego, zostaną zsynchronizowane tylko zmiany między dyskiem źródłowym a dyskiem docelowym. Site Recovery oblicza różnic, porównując dyski, a następnie przesyła dane. Ten proces zazwyczaj trwa kilka godzin. Aby uzyskać więcej informacji na temat tego, co się dzieje podczas ponownej ochrony, zobacz Ponowne [Włączanie ochrony wystąpień maszyn wirtualnych platformy Azure w trybie failover w regionie podstawowym](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Ile czasu zajmuje powrót po awarii?

Po przeprowadzeniu ochrony powrót po awarii pobiera ten sam czas pracy awaryjnej z regionu podstawowego do regionu pomocniczego.

## <a name="capacity"></a><a name="capacity"></a>Pojemność

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Jak jest zapewniana pojemność w regionie docelowym maszyn wirtualnych platformy Azure?

Plan zespołu Site Recovery i zarządzanie pojemnością platformy Azure w celu zapewnienia wystarczającej pojemności infrastruktury. Po uruchomieniu trybu failover zespoły mogą także zapewnić, że wystąpienia maszyn wirtualnych chronione przez Site Recovery zostaną wdrożone w regionie docelowym.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Czy Site Recovery współpracuje z wystąpieniami zarezerwowanymi?

Tak. [zarezerwowanych maszyn wirtualnych platformy Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/) można zakupić w regionie odzyskiwania po awarii, a Site Recovery będzie używać ich w ramach operacji przejścia w tryb failover. Nie jest wymagana dodatkowa konfiguracja.

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
