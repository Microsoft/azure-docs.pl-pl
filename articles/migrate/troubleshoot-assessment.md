---
title: Rozwiązywanie problemów dotyczących oceny i wizualizacji zależności w programie Azure Migrate
description: Uzyskaj pomoc dotyczącą oceny i wizualizacji zależności w Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863602"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Rozwiązywanie problemów z wizualizacją zależności/oceny

Ten artykuł pomaga w rozwiązywaniu problemów z oceną i wizualizacją zależności przy użyciu [Azure Migrate: odnajdywania i oceny](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemy z gotowością do oceny

Rozwiąż problemy z gotowością do oceny w następujący sposób:

**Problem** | **Wprowadzanie poprawek**
--- | ---
Nieobsługiwany typ rozruchu | Platforma Azure nie obsługuje maszyn wirtualnych z typem rozruchu EFI. Przed uruchomieniem migracji zalecamy przekonwertowanie typu rozruchu na system BIOS. <br/><br/>Do obsługi migracji takich maszyn wirtualnych można użyć migracji serwera Azure Migrate. Spowoduje to przekonwertowanie typu rozruchowego maszyny wirtualnej na system BIOS podczas migracji.
Warunkowo obsługiwany system operacyjny Windows | System operacyjny przeszedłł datę końca okresu obsłudze i potrzebuje niestandardowej umowy pomocy technicznej (CSA) w celu uzyskania [pomocy technicznej na platformie Azure](/troubleshoot/azure/virtual-machines/server-software-support). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie. Przejrzyj informacje na temat [przygotowywania serwerów z systemem Windows Server 2003](prepare-windows-server-2003-migration.md) do migracji na platformę Azure.
Nieobsługiwany system operacyjny Windows | Platforma Azure obsługuje tylko [wybrane wersje systemu operacyjnego Windows](/troubleshoot/azure/virtual-machines/server-software-support). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie serwera.
Warunkowo potwierdzony system operacyjny Linux | Platforma Azure poświadcza tylko [wybrane wersje systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie serwera. Więcej informacji można również znaleźć [tutaj](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) .
Niepotwierdzony system operacyjny Linux | Serwer może zaczynać się na platformie Azure, ale system Azure nie zapewnia obsługi systemu operacyjnego. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie do [zatwierdzonej wersji systemu Linux](../virtual-machines/linux/endorsed-distros.md) .
Nieznany system operacyjny | System operacyjny maszyny wirtualnej został określony jako "inny" w vCenter Server. To zachowanie uniemożliwia Azure Migrate weryfikacji gotowości maszyny wirtualnej platformy Azure. Przed przeprowadzeniem migracji serwera upewnij się, że system operacyjny jest [obsługiwany](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) przez platformę Azure.
Nieobsługiwana wersja bitowa | Maszyny wirtualne z 32-bitowymi systemami operacyjnymi mogą przeprowadzić rozruch na platformie Azure, ale zalecamy uaktualnienie do wersji 64-bitowej przed migracją na platformę Azure.
Wymaga subskrypcji Microsoft Visual Studio | Na serwerze działa system operacyjny klienta systemu Windows, który jest obsługiwany tylko przez subskrypcję programu Visual Studio.
Nie znaleziono maszyny wirtualnej wymaganej wydajności magazynu | Wydajność magazynu (operacje wejścia/wyjścia na sekundę [IOPS] i przepływność) wymagane dla serwera przekraczają obsługę maszyny wirtualnej platformy Azure. Przed migracją Zmniejsz wymagania dotyczące magazynu dla serwera.
Nie znaleziono maszyny wirtualnej dla wymaganej wydajności sieci | Wydajność sieci (WE/wychodzącej) wymagana przez serwer przekracza obsługę maszyny wirtualnej platformy Azure. Zmniejsz wymagania dotyczące sieci dla serwera.
Nie znaleziono maszyny wirtualnej w określonej lokalizacji | Użyj innej lokalizacji docelowej przed migracją.
Co najmniej jeden niewłaściwy dysk | Co najmniej jeden dysk dołączony do maszyny wirtualnej nie spełnia wymagań platformy Azure. Z<br/><br/> Azure Migrate: Funkcja odnajdywania i oceny nie obsługuje obecnie dysków SSD w warstwie Ultra i ocenia dyski w oparciu o limity dysków dla dysków zarządzanych w warstwie Premium (32 TB).<br/><br/> Dla każdego dysku podłączonego do maszyny wirtualnej upewnij się, że rozmiar dysku to < 64 TB (obsługiwane przez dyski SSD w warstwie Ultra).<br/><br/> Jeśli tak nie jest, zmniejsz rozmiar dysku przed przeprowadzeniem migracji na platformę Azure lub Użyj wielu dysków [na platformie Azure, aby uzyskać](../virtual-machines/premium-storage-performance.md#disk-striping) wyższe limity magazynu. Należy upewnić się, że wydajność (IOPS i przepustowość) wymagana przez poszczególne dyski są obsługiwane przez [maszyny wirtualne zarządzane](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)przez platformę Azure.
Co najmniej jedna nieodpowiednia karta sieciowa. | Usuń nieużywane karty sieciowe z serwera przed migracją.
Liczba dysków przekracza limit | Usuń nieużywane dyski z serwera przed migracją.
Rozmiar dysku przekracza limit | Azure Migrate: Funkcja odnajdywania i oceny nie obsługuje obecnie dysków SSD w warstwie Ultra i ocenia dyski w oparciu o limity dysku Premium (32 TB).<br/><br/> Platforma Azure obsługuje jednak dyski o rozmiarze do 64 TB (obsługiwane przez dyski SSD w warstwie Ultra). Zmniejsz liczbę dysków do mniej niż 64 TB przed migracją lub Użyj wielu [dysków na platformie Azure, aby uzyskać](../virtual-machines/premium-storage-performance.md#disk-striping) wyższe limity magazynu.
Dysk niedostępny w określonej lokalizacji | Przed przeprowadzeniem migracji upewnij się, że dysk znajduje się w lokalizacji docelowej.
Dysk niedostępny dla określonej nadmiarowości | Dysk powinien używać typu magazynu nadmiarowości zdefiniowanego w ustawieniach oceny (domyślnie LRS).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny wirtualnej z wymaganymi rdzeniami i pamięcią | Platforma Azure nie może znaleźć odpowiedniego typu maszyny wirtualnej. Przed migracją Zmniejsz ilość pamięci i liczbę rdzeni serwera lokalnego.
Nie można określić przydatności maszyny wirtualnej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednego dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednej karty sieciowej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono rozmiaru maszyny wirtualnej dla zarezerwowanego wystąpienia waluty oferty | Serwer został oznaczony jako nieodpowiedni, ponieważ nie znaleziono rozmiaru maszyny wirtualnej dla wybranej kombinacji RI, oferta i waluta. Edytuj właściwości oceny, aby wybrać prawidłowe kombinacje i ponownie obliczyć ocenę. 
Protokół internetowy gotowości warunkowej | Dotyczy wyłącznie ocen systemu Azure VMware (Automatyczna synchronizacja). Automatyczna synchronizacja nie obsługuje współczynnika adresów internetowych IPv6. Skontaktuj się z zespołem automatycznej synchronizacji w celu uzyskania wskazówek dotyczących korygowania, jeśli serwer został wykryty przy użyciu protokołu IPv6.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Sugerowane narzędzie do migracji w ramach oceny automatycznej synchronizacji opartej na imporcie oznaczono jako nieznane

W przypadku serwerów zaimportowanych za pośrednictwem pliku CSV domyślne narzędzie do migracji w ramach programu i oceny automatycznej synchronizacji jest nieznane. Mimo że w przypadku serwerów w środowisku VMware zaleca się korzystanie z rozwiązania VMware hybryd Cloud Extension (HCX). [Dowiedz się więcej](../azure-vmware/tutorial-deploy-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Maszyny wirtualne z systemem Linux są gotowe do oceny maszyn wirtualnych platformy Azure

W przypadku maszyn wirtualnych VMware i funkcji Hyper-V funkcja oceny maszyn wirtualnych platformy Azure oznacza maszyny wirtualne z systemem Linux jako "warunkowo gotowe" ze względu na znaną przerwę. 

- Przerwy uniemożliwiają wykrycie pomocniczej wersji systemu operacyjnego Linux zainstalowanej na lokalnych maszynach wirtualnych.
- Na przykład w przypadku RHEL 6,10 obecnie Ocena maszyny wirtualnej platformy Azure wykrywa tylko RHEL 6 jako wersję systemu operacyjnego. Wynika to z faktu, że vCenter Server AR hosta funkcji Hyper-V nie zapewnia wersji jądra dla systemów operacyjnych maszyn wirtualnych z systemem Linux.
-  Ponieważ platforma Azure poświadcza tylko określone wersje systemu Linux, maszyny wirtualne z systemem Linux są obecnie oznaczone jako gotowe do oceny maszyny wirtualnej platformy Azure.
- Możesz określić, czy system operacyjny Linux uruchomiony na lokalnej maszynie wirtualnej jest zatwierdzony na platformie Azure, przeglądając [Pomoc techniczną platformy Azure](../virtual-machines/linux/endorsed-distros.md)w systemie Linux.
-  Po zweryfikowaniu poświadczonej dystrybucji można zignorować to ostrzeżenie.

Tę lukę można rozwiązać przez włączenie [odnajdowania aplikacji](./how-to-discover-applications.md) na maszynach wirtualnych VMware. Ocena maszyny wirtualnej platformy Azure używa systemu operacyjnego wykrytego na maszynie wirtualnej przy użyciu podanych poświadczeń gościa. Te dane systemu operacyjnego identyfikują odpowiednie informacje o systemie operacyjnym w przypadku maszyn wirtualnych z systemami Windows i Linux.

## <a name="operating-system-version-not-available"></a>Wersja systemu operacyjnego jest niedostępna

W przypadku serwerów fizycznych informacje o pomocniczej wersji systemu operacyjnego powinny być dostępne. Jeśli nie jest dostępny, skontaktuj się z pomoc techniczna firmy Microsoft. W przypadku serwerów w środowisku VMware Azure Migrate używa informacji o systemie operacyjnym określonych dla maszyny wirtualnej w vCenter Server. Jednak vCenter Server nie zapewnia wersji pomocniczej dla systemów operacyjnych. Aby odnaleźć wersję pomocniczą, należy skonfigurować [Odnajdowanie aplikacji](./how-to-discover-applications.md). W przypadku maszyn wirtualnych funkcji Hyper-V odnajdowanie wersji pomocniczej systemu operacyjnego nie jest obsługiwane. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Jednostki SKU platformy Azure większe niż lokalne w ramach oceny maszyny wirtualnej platformy Azure

Ocena maszyn wirtualnych platformy Azure może zalecać jednostki SKU maszyny wirtualnej platformy Azure z większą liczbą rdzeni i pamięci niż bieżąca alokacja lokalna na podstawie typu oceny:

- Zalecenie dotyczące jednostki SKU maszyny wirtualnej zależy od właściwości oceny.
- Ma to wpływ na typ oceny wykonywanej w ocenie maszyny wirtualnej platformy Azure: *oparta na wydajności* lub *w środowisku lokalnym*.
- W przypadku ocen opartych na wydajności Ocena maszyn wirtualnych platformy Azure traktuje dane użycia lokalnych maszyn wirtualnych (procesora CPU, pamięci, dysku i wykorzystania sieci) w celu określenia odpowiedniej docelowej jednostki SKU maszyny wirtualnej dla lokalnych maszyn wirtualnych. Dodaje również współczynnik komfortu podczas określania efektywnego wykorzystania.
- W przypadku lokalnego określania wielkości dane wydajności nie są brane pod uwagę, a docelowa jednostka SKU jest zalecana na podstawie przydziału lokalnego.

Aby pokazać, jak to może mieć wpływ na zalecenia, przyjrzyjmy się przykładowi:

Mamy lokalną maszynę wirtualną z czterema rdzeniami i 8 GB pamięci, z użyciem procesora CPU 50% i 50% wykorzystania pamięci, a określonym czynnikiem komfortu wynoszącym 1,3.

-  Jeśli ocena jest przeprowadzana **lokalnie**, zaleca się używanie jednostki SKU maszyny wirtualnej platformy Azure z czterema rdzeniami i 8 GB pamięci.
- Jeśli ocena jest oparta na wydajności, w oparciu o efektywne wykorzystanie procesora CPU i pamięci (50% z 4 rdzeni * 1,3 = 2,6 rdzenie i 50% 8 GB pamięci * 1,3 = 5,3 GB pamięci), zalecana jest najtańsza jednostka SKU maszyny wirtualnej czterech rdzeni (najbliższa obsługiwana liczba rdzeni) i osiem GB pamięci (najbliższy obsługiwany rozmiar pamięci).
- [Dowiedz się więcej](concepts-assessment-calculation.md#types-of-assessments) o ustalaniu wielkości ocen.

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Dlaczego zalecane jednostki SKU dysku platformy Azure są większe niż lokalne w ramach oceny maszyny wirtualnej platformy Azure?

Ocena maszyn wirtualnych platformy Azure może być zalecana dla większego dysku na podstawie typu oceny.
- Rozmiary dysku są zależne od dwóch właściwości oceny: Określanie rozmiarów kryteriów i typów magazynów.
- Jeśli kryteria ustalania wielkości są **oparte na wydajności**, a typ magazynu jest ustawiony na wartość **Automatyczne**, operacje we/wy i przepływność dysku są brane pod uwagę podczas identyfikowania docelowego typu dysku (HDD w warstwie Standardowa, SSD w warstwie Standardowa lub Premium). Zalecana jest jednostka SKU dysku z typu dysku, a zalecenie uwzględnia wymagania dotyczące rozmiaru dysku lokalnego.
- Jeśli kryterium ustalania rozmiaru jest **oparte na wydajności**, a typ magazynu to **Premium**, zalecana jest jednostka SKU dysku Premium na platformie Azure na podstawie wymagań operacji we/wy na sekundę, przepływności i rozmiaru dysku lokalnego. Ta sama logika jest używana do przeprowadzania ustalania rozmiarów dysków, gdy kryteria ustalania wielkości są **jako lokalne** , a typ magazynu to **HDD w warstwie Standardowa**, **SSD w warstwie Standardowa** lub **Premium**.

Przykładowo, jeśli masz dysk lokalny z 32 GB pamięci, ale zagregowane liczby operacji we/wy odczytu i zapisu dla dysku to 800 IOPS, funkcja oceny maszyn wirtualnych platformy Azure zaleca dysk w warstwie Premium (z powodu wyższych wymagań IOPS), a następnie zaleca użycie jednostki SKU dysku, która może obsługiwać wymagane operacje we/wy i rozmiar. Najbliższym dopasowaniem w tym przykładzie byłaby jednostka P15 (256 GB, 1100 operacji we/wy na sekundę). Mimo że rozmiar wymagany przez dysk lokalny to 32 GB, Ocena maszyn wirtualnych platformy Azure zaleca większy dysk z powodu dużego wymagania IOPS dysku lokalnego.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Dlaczego brakuje danych dotyczących wydajności dla niektórych/wszystkich maszyn wirtualnych w moim raporcie oceny?

W przypadku oceny „Na podstawie wydajności” eksport raportu oceny zawiera ciąg „PercentageOfCoresUtilizedMissing” lub „PercentageOfMemoryUtilizedMissing”, gdy urządzenie usługi Azure Migrate nie może zebrać danych wydajności lokalnych maszyn wirtualnych. Sprawdź następujące kwestie:

- Czy maszyny wirtualne są włączone przez czas trwania, dla którego tworzysz ocenę
- Jeśli brakuje tylko liczników pamięci i próbujesz ocenić maszyny wirtualne funkcji Hyper-V, sprawdź, czy dla tych maszyn wirtualnych jest włączona pamięć dynamiczna. Występuje obecnie znany problem, który jest spowodowany tym, że urządzenie usługi Azure Migrate nie może zebrać informacji o wykorzystaniu pamięci dla takich maszyn wirtualnych.
- Jeśli brakuje wszystkich liczników wydajności, upewnij się, że zostały spełnione wymagania dotyczące dostępu do portów dla oceny. Dowiedz się więcej o wymaganiach dostępu do portów dla oprogramowania [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [funkcji Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) i oceny [fizycznej](./migrate-support-matrix-physical.md#port-access) .
Uwaga: Jeśli brakuje któregoś z liczników wydajności Azure Migrate, odnajdywanie i ocenianie powraca do przyznanych rdzeni/pamięci lokalnie i zaleca odpowiednio rozmiar maszyny wirtualnej.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Dlaczego brakuje danych dotyczących wydajności dla niektórych/wszystkich serwerów w raporcie na maszynie wirtualnej platformy Azure i/lub w ramach oceny automatycznej synchronizacji?

W przypadku oceny "wydajność na podstawie wydajności" Eksport raportu oceny ma wartość "PercentageOfCoresUtilizedMissing" lub "PercentageOfMemoryUtilizedMissing", gdy urządzenie Azure Migrate nie może zbierać danych wydajności dla serwerów lokalnych. Sprawdź następujące kwestie:

- Jeśli serwery są włączone na czas trwania, dla którego tworzysz ocenę
- Jeśli brakuje tylko liczników pamięci i próbujesz ocenić serwery w środowisku funkcji Hyper-V. W tym scenariuszu należy włączyć pamięć dynamiczną na serwerach i ponownie obliczyć ocenę, aby odzwierciedlić najnowsze zmiany. Urządzenie może zbierać wartości wykorzystania pamięci dla serwerów w środowisku funkcji Hyper-V tylko wtedy, gdy na serwerze jest włączona pamięć dynamiczna.

- Jeśli brakuje wszystkich liczników wydajności, upewnij się, że połączenia wychodzące na portach 443 (HTTPS) są dozwolone.

    > [!Note]
    > Jeśli brakuje któregokolwiek z liczników wydajności, Azure Migrate: odnajdywanie i ocenianie powraca do przyznanych rdzeni/pamięci lokalnie i zalecane jest odpowiednie dopasowanie rozmiaru maszyny wirtualnej.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Dlaczego brakuje danych dotyczących wydajności dla niektórych/wszystkich wystąpień lub baz danych SQL w ramach oceny usługi Azure SQL?

Aby zapewnić zbieranie danych dotyczących wydajności, należy sprawdzić:

- Jeśli serwery SQL są włączone na czas trwania tworzenia oceny
- Jeśli stan połączenia agenta programu SQL Server w Azure Migrate to "Connected" i sprawdź ostatni puls 
- Jeśli Azure Migrate stan połączenia dla wszystkich wystąpień SQL ma wartość "Połączono" w bloku odnalezionego wystąpienia programu SQL
- Jeśli brakuje wszystkich liczników wydajności, upewnij się, że połączenia wychodzące na portach 443 (HTTPS) są dozwolone

Jeśli brakuje któregokolwiek z liczników wydajności, usługa Azure SQL Assessment zaleca najmniejszą konfigurację usługi Azure SQL dla tego wystąpienia/bazy danych.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Dlaczego ocena ufności mojej oceny jest niska?

Ocena ufności dla ocen „Na podstawie wydajności” jest obliczana w oparciu o wartość procentową [dostępnych punktów danych](./concepts-assessment-calculation.md#ratings) potrzebnych do obliczenia oceny. Poniżej przedstawiono przyczyny, z powodu których ocena ufności może być niska:

- Nie profilujesz swojego środowiska przez czas trwania, dla którego tworzysz ocenę. Jeśli na przykład tworzysz ocenę z czasem trwania wydajności ustawionym na jeden tydzień, musisz poczekać co najmniej tydzień po uruchomieniu odnajdywania, aby zebrać wszystkie punkty danych. Jeśli nie można czekać na czas trwania, należy zmienić czas trwania wydajności na mniejszy okres i **ponownie obliczyć** ocenę.
 
- Ocena nie jest w stanie zbierać danych wydajności dla niektórych lub wszystkich serwerów w okresie oceny. W celu uzyskania oceny o wysokiej pewności upewnij się, że: 
    - Serwery są zasilane na czas trwania oceny
    - Połączenia wychodzące na portach 443 są dozwolone
    - W przypadku pamięci dynamicznej na serwerach funkcji Hyper-V jest włączona 
    - Stan połączenia agentów w Azure Migrate to "Połączono" i sprawdź ostatni puls
    - W przypadku usługi Azure SQL Assessment Azure Migrate stan połączenia dla wszystkich wystąpień SQL ma wartość "Połączono" w bloku odnalezionego wystąpienia programu SQL

    Użyj opcji **Oblicz ponownie**, aby uwzględnić najnowsze zmiany w ocenie ufności.

- W przypadku maszyn wirtualnych platformy Azure i ocen dotyczących automatycznej wersji programu po rozpoczęciu odnajdywania zostały utworzone kilka serwerów. Na przykład jeśli tworzysz ocenę dla historii wydajności dla ostatniego miesiąca, ale kilka serwerów zostało utworzonych w środowisku tylko tydzień temu. W takim przypadku dane wydajności dla nowych serwerów nie będą dostępne przez cały czas, a Ocena zaufania byłaby niska. [Dowiedz się więcej](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- W przypadku ocen usługi Azure SQL utworzono kilka wystąpień lub baz danych SQL po rozpoczęciu odnajdywania. Na przykład jeśli tworzysz ocenę dla historii wydajności dla ostatniego miesiąca, ale kilka wystąpień SQL lub baz danych zostało utworzonych w środowisku tylko tydzień temu. W takim przypadku dane wydajności dla nowych serwerów nie będą dostępne przez cały czas, a Ocena zaufania byłaby niska. [Dowiedz się więcej](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Czy licencja systemu operacyjnego jest uwzględniona w ocenie maszyny wirtualnej platformy Azure?

Ocena maszyn wirtualnych platformy Azure uwzględnia obecnie koszt licencji systemu operacyjnego dla serwerów z systemem Windows. Koszty licencji dla serwerów z systemem Linux nie są obecnie brane pod uwagę.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Jak zmienia się rozmiar na podstawie wydajności w ramach oceny maszyny wirtualnej platformy Azure?

Ocena maszyn wirtualnych platformy Azure stale zbiera dane wydajności serwerów lokalnych i używa ich do zaleceń jednostki SKU maszyny wirtualnej i jednostki SKU dysku na platformie Azure. [Dowiedz się, jak](concepts-assessment-calculation.md#calculate-sizing-performance-based) zbierane są dane oparte na wydajności.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Dlaczego moja Ocena pokazuje ostrzeżenie, że zostało utworzone przy użyciu nieprawidłowej kombinacji wystąpień zarezerwowanych, czasu działania maszyny wirtualnej i rabatu (%)?
Po wybraniu opcji "zarezerwowane wystąpienia" rabat (%) i nie mają zastosowania właściwości "czas pracy maszyny wirtualnej". Po utworzeniu oceny z nieprawidłową kombinacją tych właściwości przyciski Edytuj i Oblicz ponownie są wyłączone. Utwórz nową ocenę. [Dowiedz się więcej](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Nie widzę danych o wydajności niektórych kart sieciowych na serwerach fizycznych

Taka sytuacja może wystąpić, jeśli na serwerze fizycznym jest włączona Wirtualizacja funkcji Hyper-V. Na tych serwerach z powodu przerwy w działaniu produktu Azure Migrate obecnie są odnajdywane fizyczne i wirtualne karty sieciowe. Przepływność sieci jest przechwytywana tylko na odnalezionych wirtualnych kartach sieciowych.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>Zalecana jednostka SKU maszyny wirtualnej platformy Azure dla mojego serwera fizycznego jest zbyt duży

Taka sytuacja może wystąpić, jeśli na serwerze fizycznym jest włączona Wirtualizacja funkcji Hyper-V. Na tych serwerach Azure Migrate obecnie są odnajdywane fizyczne i wirtualne karty sieciowe. Tym samym, nie. wykryte karty sieciowe są większe niż rzeczywiste. Ponieważ Ocena maszyn wirtualnych platformy Azure wybiera maszynę wirtualną platformy Azure, która może obsługiwać wymaganą liczbę kart sieciowych, może to spowodować, że maszyna wirtualna jest powiększona. [Dowiedz się więcej](./concepts-assessment-calculation.md#calculating-sizing) o wpływie nie. kart sieciowych w przypadku zmiany rozmiarów. Jest to przerwy w działaniu produktu, które zostaną rozkierowane do przodu.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Kategoria gotowości "niegotowa" dla mojego serwera fizycznego

Kategoria gotowość może zostać niepoprawnie oznaczona jako "niegotowa" w przypadku serwera fizycznego z włączoną wirtualizacją funkcji Hyper-V. Na tych serwerach z powodu przerwy w działaniu produktu Azure Migrate obecnie są odnajdywane zarówno karty fizyczne, jak i wirtualne. Tym samym, nie. wykryte karty sieciowe są większe niż rzeczywiste. W przypadku ocen opartych na środowisku lokalnym i wydajności usługa Azure VM Assessment wybiera maszynę wirtualną platformy Azure, która może obsługiwać wymaganą liczbę kart sieciowych. Jeśli liczba wykrytych kart sieciowych jest większa niż 32, wartość maksymalna to. Kart sieciowych obsługiwanych na maszynach wirtualnych platformy Azure, serwer zostanie oznaczony jako "niegotowy".  [Dowiedz się więcej](./concepts-assessment-calculation.md#calculating-sizing) o wpływie nie. Kart sieciowych na wymiarze.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Liczba odnalezionych kart sieciowych wyższych niż rzeczywista dla serwerów fizycznych

Taka sytuacja może wystąpić, jeśli na serwerze fizycznym jest włączona Wirtualizacja funkcji Hyper-V. Na tych serwerach Azure Migrate obecnie są odnajdywane zarówno fizyczne, jak i wirtualne karty sieciowe. Tym samym, nie. wykryte karty sieciowe są większe niż rzeczywiste.

## <a name="dependency-visualization-in-azure-government"></a>Wizualizacja zależności w Azure Government

Analiza zależności oparta na agentach nie jest obsługiwana w Azure Government. Użyj analizy zależności bez agenta.


## <a name="dependencies-dont-show-after-agent-install"></a>Zależności nie są wyświetlane po zainstalowaniu agenta

Po zainstalowaniu agentów wizualizacji zależności na lokalnych maszynach wirtualnych Azure Migrate zwykle trwa 15-30 minut, aby wyświetlić zależności w portalu. Jeśli zaczekasz dłużej niż 30 minut, upewnij się, że Microsoft Monitoring Agent (MMA) może nawiązać połączenie z obszarem roboczym Log Analytics.

Dla maszyn wirtualnych z systemem Windows:
1. W panelu sterowania uruchom MMA.
2. We **właściwościach Microsoft Monitoring Agent**  >  **Azure log Analytics (OMS)** upewnij się, że **stan** obszaru roboczego to zielony.
3. Jeśli stan nie jest zielony, spróbuj usunąć obszar roboczy i dodać go ponownie do MMA.

    ![Stan MMA](./media/troubleshoot-assessment/mma-properties.png)

W przypadku maszyn wirtualnych z systemem Linux upewnij się, że polecenia instalacji programu MMA i agenta zależności powiodły się. [Tutaj](../azure-monitor/vm/service-map.md#post-installation-issues)znajdziesz więcej wskazówek dotyczących rozwiązywania problemów.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- **Agent MMS**: Zapoznaj się z obsługiwanymi systemami operacyjnymi [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)i [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) .
- **Agent zależności**: obsługiwane systemy operacyjne [Windows i Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) .

## <a name="visualize-dependencies-for--1-hour"></a>Wizualizuj zależności dla > 1 godziny

Dzięki analizie zależności bez wykorzystania agentów można wizualizować zależności lub eksportować je na mapie przez okres do 30 dni.

Analiza zależności oparta na agentach, chociaż Azure Migrate pozwala na powrót do określonej daty w ostatnim miesiącu, maksymalny czas trwania wizualizacji zależności wynosi godzinę. Można na przykład użyć funkcji czas trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można je wyświetlić tylko dla jednego okresu. Można jednak użyć dzienników Azure Monitor, aby [wykonać zapytanie o dane zależności](./how-to-create-group-machine-dependencies.md) przez dłuższy czas.

## <a name="visualized-dependencies-for--10-servers"></a>Wizualne zależności dla serwerów > 10

W Azure Migrate, z analizą zależności opartą na agentach, można [wizualizować zależności dla grup](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) z maksymalnie 10 maszynami wirtualnymi. W przypadku większych grup zalecamy dzielenie maszyn wirtualnych na mniejsze grupy w celu wizualizacji zależności.


## <a name="servers-show-install-agent"></a>Serwery pokazują "Zainstaluj agenta"

Po przeprowadzeniu migracji serwerów z funkcją wizualizacji zależności na platformie Azure serwery mogą wyświetlać akcję "Zainstaluj agenta" zamiast "Wyświetl zależności" z powodu następujących zachowań:

- Po przeprowadzeniu migracji na platformę Azure serwery lokalne są wyłączone i równoważne maszyny wirtualne są na platformie Azure. Te serwery uzyskują inny adres MAC.
- Serwery mogą również mieć inny adres IP w zależności od tego, czy został zachowany lokalny adres IP.
- Jeśli adresy MAC i IP różnią się od lokalnych, Azure Migrate nie kojarzy serwerów lokalnych z dowolnymi Service Map danymi zależności. W takim przypadku zostanie wyświetlona opcja instalacji agenta zamiast wyświetlania zależności.
- Po przeprowadzeniu migracji testowej na platformę Azure lokalne serwery pozostają włączone zgodnie z oczekiwaniami. Równoważne serwery na platformie Azure uzyskują różne adresy MAC i mogą uzyskiwać różne adresów IP. Jeśli nie zablokujesz wychodzącego ruchu dziennika Azure Monitor z tych serwerów, Azure Migrate nie będą kojarzyć serwerów lokalnych z dowolnymi Service Map danymi zależności i w ten sposób będzie wyświetlana opcja instalacji agentów, a nie do wyświetlania zależności.

## <a name="dependencies-export-csv-shows-unknown-process"></a>Plik CSV składnika eksport zależności zawiera "nieznany proces"
W analizie zależności bez wykorzystania agentów nazwy procesów są przechwytywane na podstawie najlepszego wysiłku. W niektórych scenariuszach, chociaż nazwy serwera źródłowego i docelowego oraz port docelowy są przechwytywane, nie jest możliwe ustalenie nazw procesów na obu końcach zależności. W takich przypadkach proces jest oznaczony jako "nieznany proces".

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>Podczas próby skonfigurowania obszaru roboczego w programie Azure Migrate obszar roboczy Log Analytics nie jest wyświetlany.
Usługa Azure Migrate obecnie obsługuje tworzenie obszaru roboczego pakietu OMS w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia. Jeśli obszar roboczy jest tworzony poza Azure Migrate w innym regionie, nie można go skojarzyć z projektem.


## <a name="capture-network-traffic"></a>Przechwyć ruch sieciowy

Zbierz dzienniki ruchu sieciowego w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Naciśnij klawisz F12, aby rozpocząć Narzędzia deweloperskie. W razie konieczności Wyczyść ustawienie  **Wyczyść wpisy przy nawigacji** .
3. Wybierz kartę **Sieć** i Rozpocznij przechwytywanie ruchu sieciowego:
   - W programie Chrome wybierz opcję **Zachowaj dziennik**. Nagrywanie powinno być uruchamiane automatycznie. Czerwony okrąg wskazuje na to, że ruch jest przechwytywany. Jeśli czerwony okrąg nie jest wyświetlany, wybierz czarny okrąg, aby rozpocząć.
   - W programie Microsoft Edge i Internet Explorer nagrywanie powinno być uruchamiane automatycznie. Jeśli tak nie jest, wybierz zielony przycisk odtwarzania.
4. Spróbuj odtworzyć błąd.
5. Po napotkaniu błędu podczas rejestrowania, zatrzymywania rejestrowania i zapisywania kopii zapisanego działania:
   - W programie Chrome kliknij prawym przyciskiem myszy i wybierz pozycję **Zapisz jako Har z zawartością**. Ta akcja kompresuje i eksportuje dzienniki jako plik. HAR.
   - W przeglądarce Microsoft Edge lub Internet Explorer wybierz opcję **Eksportuj ruch przechwycony** . Ta akcja kompresuje i eksportuje dziennik.
6. Wybierz kartę **konsola** , aby sprawdzić, czy występują ostrzeżenia lub błędy. Aby zapisać dziennik konsoli:
   - W programie Chrome kliknij prawym przyciskiem myszy w dowolnym miejscu w dzienniku konsoli. Wybierz pozycję **Zapisz jako**, aby wyeksportować i zip log.
   - W przeglądarce Microsoft Edge lub Internet Explorer, kliknij prawym przyciskiem myszy błędy i wybierz polecenie **Kopiuj wszystko**.
7. Zamknij Narzędzia deweloperskie.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Gdzie są wykryte dane systemu operacyjnego z mojej oceny?

- W przypadku maszyn wirtualnych VMware domyślnie są to dane systemu operacyjnego udostępniane przez program vCenter. 
   - W przypadku maszyn wirtualnych VMware Linux, jeśli Odnajdowanie aplikacji jest włączone, szczegóły systemu operacyjnego są pobierane z maszyny wirtualnej gościa. Aby sprawdzić szczegóły systemu operacyjnego w ocenie, przejdź do widoku odnalezione serwery i wskaźnik myszy nad wartością w kolumnie "system operacyjny". W wyskakującym tekście zobaczysz, czy widoczne dane systemu operacyjnego są zbierane z serwera vCenter Server, czy z maszyny wirtualnej gościa przy użyciu poświadczeń maszyny wirtualnej. 
   - W przypadku maszyn wirtualnych z systemem Windows szczegóły systemu operacyjnego są zawsze pobierane z vCenter Server.
- W przypadku maszyn wirtualnych funkcji Hyper-V dane systemu operacyjnego są zbierane z hosta funkcji Hyper-V
- W przypadku serwerów fizycznych jest on pobierany z serwera.

## <a name="next-steps"></a>Następne kroki

[Utwórz](how-to-create-assessment.md) lub [Dostosuj](how-to-modify-assessment.md) ocenę.