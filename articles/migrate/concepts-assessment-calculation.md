---
title: Oceny w ocenie serwera migracji platformy Azure
description: Dowiedz się więcej o ocenach w ocenie serwera migracji platformy Azure
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d1f32eea0ec6a8a4877fd1dc134344cfe68dcaba
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537767"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Oceny w usłudze Azure Migrate:Ocena serwera

Ten artykuł zawiera omówienie ocen w narzędziu [oceny migracji:serwer usłudze Azure.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Narzędzie Oceny serwera może oceniać lokalne maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper V i serwery fizyczne pod kątem migracji na platformę Azure.

## <a name="whats-an-assessment"></a>Co to jest ocena?

Ocena za pomocą narzędzia Ocena serwera mierzy gotowość i szacuje wpływ migracji serwerów lokalnych na platformę Azure.

> [!NOTE]
> W usłudze Azure dla instytucji rządowych przejrzyj obsługiwane lokalizacje oceny [docelowej.](migrate-support-matrix.md#supported-geographies-azure-government) Należy zauważyć, że zalecenia dotyczące rozmiaru maszyny Wirtualnej w ocenach będą używać serii maszyn wirtualnych specjalnie dla regionów chmury rządowej. [Dowiedz się więcej](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) o typach maszyn wirtualnych.

## <a name="types-of-assessments"></a>Rodzaje ocen

Oceny utworzone za pomocą oceny serwera są migawką danych w czasie. Ocena serwera zapewnia dwa typy ocen.

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny, które wytęzają zalecenia na podstawie zebranych danych dotyczących wydajności | Zalecenie rozmiaru maszyny Wirtualnej jest oparte na danych wykorzystania procesora CPU i pamięci.<br/><br/> Zalecenie typu dysku (standardowe dyski HDD/SSD lub dyski zarządzane w wersji premium) opiera się na we/wy i przepływności dysków lokalnych.
**Jako jest w środowisku lokalnym** | Oceny, które nie używają danych o wydajności do zaleceń. | Zalecenie dotyczące rozmiaru maszyny Wirtualnej jest oparte na lokalnym rozmiarze maszyny Wirtualnej<br/><br> Zalecany typ dysku jest oparty na wybranym typie magazynu dla oceny.

## <a name="how-do-i-run-an-assessment"></a>Jak przeprowadzić ocenę?

Istnieje kilka sposobów, aby przeprowadzić ocenę:

- Oceniaj maszyny przy użyciu metadanych serwera zebranych przez lekkie urządzenie migracji platformy Azure. Urządzenie odnajduje maszyny lokalne i wysyła dane metadanych/wydajności komputera do usługi Azure Migrate.
- Oceniaj maszyny przy użyciu metadanych serwera importowanych w formacie CSV (values) rozdzielanych przecinkami.

## <a name="how-do-i-assess-with-the-appliance"></a>Jak ocenić urządzenie?

Jeśli wdrażasz urządzenie migracji platformy Azure w celu odnajdywania serwerów lokalnych, wykonaj następujące czynności:

1. Konfigurowanie platformy Azure i środowiska lokalnego do pracy z oceną serwera.
2. W pierwszej ocenie należy utworzyć projekt platformy Azure i dodać do niego narzędzie Oceny serwera.
3. Wdrożyć lekkie urządzenie migracji platformy Azure. Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny i dane o wydajności do usługi Azure Migrate. Urządzenie jest wdrażane jako maszyna wirtualna lub maszyna fizyczna. Nie ma potrzeby instalowania niczego na komputerach, które chcesz ocenić.
4. Po rozpoczęciu odnajdywania maszyny urządzenia można zebrać maszyny, które mają zostać ocenione w grupie, i przeprowadzić ocenę dla grupy.

Możesz wykonać nasze samouczki dotyczące [serwerów VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)lub [fizycznych,](tutorial-prepare-physical.md) aby wypróbować te kroki.

## <a name="how-do-i-assess-with-imported-data"></a>Jak oceniać za pomocą importowanych danych?

Jeśli oceniasz serwery przy użyciu pliku CSV, nie potrzebujesz urządzenia. Zamiast tego należy wykonać następujące czynności:

1. Skonfigurowana platforma Azure do pracy z oceną serwera.
2. W pierwszej ocenie należy utworzyć projekt platformy Azure i dodać do niego narzędzie Oceny serwera.
3. Pobierasz szablon CSV i dodajesz do niego dane serwera.
4. Szablon jest importowany do oceny serwera.
5. Odnajdujesz serwery dodane wraz z importem, zbierasz następnie do grupy i uruchamiasz ocenę dla grupy.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane gromadzi urządzenie?

Jeśli używasz urządzenia migracji platformy Azure do oceny, dowiedz się więcej o metadanych i danych dotyczących wydajności, które są zbierane dla [VMware](migrate-appliance.md#collected-data---vmware) i [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>W jaki sposób urządzenie oblicza dane o wydajności?

Jeśli urządzenie jest używane do odnajdowania, dane dotyczące wydajności dla ustawień obliczeniowych są zbierane w następujący sposób:

1. Urządzenie zbiera punkt próbkowania w czasie rzeczywistym:

    - **Maszyny wirtualne VMware:** Urządzenie zbiera punkt próbkowania w czasie rzeczywistym co 20-sekundowy interwał.
    - **Maszyny wirtualne funkcji Hyper-V:** punkt próbkowania w czasie rzeczywistym jest zbierany co 30-sekundowy interwał.
    - **Serwery fizyczne:** punkt próbkowania w czasie rzeczywistym jest pobierany co pięć minut. 
    
2. Urządzenie zjeżdża punkty próbki (20 sekund, 30 sekund, pięć minut), aby utworzyć pojedynczy punkt danych co 10 minut. Aby utworzyć pojedynczy punkt, urządzenie wybiera wartość szczytową ze wszystkich próbek, a następnie wysyła ją do platformy Azure.
3. Ocena serwera przechowuje wszystkie 10-minutowe przykładowe punkty w ostatnim miesiącu.
4. Podczas tworzenia oceny ocena serwera identyfikuje odpowiedni punkt danych do użycia w przypadku zmiany rozmiaru po prawej stronie, na podstawie wartości percentyla dla *historii wydajności* i *wykorzystania percentyla*.

    - Na przykład jeśli historia wydajności jest ustawiona na jeden tydzień, a wykorzystanie percentyla jest 95 percentylem, ocena serwera sortuje 10-minutowe punkty próbki dla ostatniego tygodnia w porządku rosnącym i wybiera wartość 95 percentyla dla rozmiaru prawego. 
    - 95-ta percentyl wartość upewnia się, że można zignorować wszelkie wartości odstające, które mogą być uwzględnione, jeśli wybierzesz 99 percentyl.
    - Jeśli chcesz wybrać szczytowe użycie dla okresu i nie chcesz przegapić żadnych wartości odstania, należy wybrać 99 percentyl dla wykorzystania percentyla.

5. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane wykorzystania wydajności dla każdej metryki (wykorzystanie procesora CPU, wykorzystanie pamięci, we/wy iOPS dysku (odczyt i zapis), przepływność dysku (odczyt i zapis) i przepustowość sieci (w i obecnie), które urządzenie zbiera.



## <a name="how-are-assessments-calculated"></a>W jaki sposób obliczane są oceny? 

Oceny w ocenie serwera są obliczane przy użyciu metadanych/danych o wydajności dla komputerów lokalnych. Jeśli wdrożysz urządzenie migracji platformy Azure, a następnie oceny przy użyciu danych zebranych przez urządzenie. Po uruchomieniu oceny dla maszyn importowanych przy użyciu pliku . CSV, należy podać metadane do obliczeń. Obliczenia występują w trzech etapach:

1. **Oblicz gotowość platformy Azure:** Oceń, czy maszyny nadają się do migracji na platformę Azure.
2. **Obliczanie rekomendacji dotyczących rozmiaru:** Oszacuj dane obliczeniowe, magazyn i rozmiar sieci. 
2. **Oblicz miesięczne koszty:** oblicz szacowane miesięczne koszty obliczeń i magazynowania dla uruchamiania komputerów na platformie Azure po migracji.

Obliczenia są w kolejności, a serwer komputera przechodzi do późniejszego etapu tylko wtedy, gdy przechodzi poprzedni. Na przykład jeśli serwer nie powiedzie się gotowość platformy Azure, jest oznaczony jako nieodpowiedni dla platformy Azure i zmiany rozmiaru i wyceny nie jest wykonywana dla tego serwera.


## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

Oto, co znalazło się w ocenie serwera.

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja, do której chcesz przeprowadzić migrację. Ocena serwera obsługuje obecnie te docelowe regiony platformy Azure:<br/><br/> Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Środkowe Indie, Środkowe STANY USA, Chiny Wschodnie, Wschodnia Azja, Wschodnie STANY USA, Wschodnie STANY USA2, Niemcy Środkowe, Niemcy Północno-Wschodnie, Japonia Wschodnia, Japonia Zachodnia, Korea Południowa, Północno-Środkowe STANY USA, Europa Północna, Południowo-Środkowe STANY USA, Azja Południowo-Wschodnia, Indie Południowe, Wielka Brytania Południowa, Wielka Brytania Zachodnia, US Gov Arizona, US Gov Texas, US Gov Virginia , Zachodnio-środkowe stany USA, Europa Zachodnia, Indie Zachodnie, Zachodnie stany USA i Zachodnie stany USA2.
*Docelowy dysk magazynu (w rozmiarze)** | Typ dysków do użycia w magazynie na platformie Azure. <br/><br/> Określ docelowy dysk pamięci masowej jako zarządzany w wersji premium, standardowy dysk SSD zarządzany lub zarządzany standardowy dysk twardy.
**Docelowy dysk pamięci masowej (rozmiar oparty na wydajności)** | Określ typ docelowego dysku magazynu jako automatyczny, zarządzany w wersji premium, zarządzany standardowy dysk twardy lub standardowy dysk SSD zarządzany.<br/><br/> **Automatyczne**: Zalecenie dysku opiera się na danych wydajności dysków (operacje wejścia/wyjścia na sekundę (IOPS) i przepływność).<br/><br/>**Premium/standard**: Ocena zaleca jednostkę SKU dysku w wybranym typie magazynu.<br/><br/> Jeśli chcesz osiągnąć umowy SLA maszyny Wirtualnej pojedynczego wystąpienia 99,9%, biorąc pod uwagę użycie dysków zarządzanych w wersji premium. Gwarantuje to, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w wersji premium.<br/><br/> Usługa Azure Migrate obsługuje tylko dyski zarządzane na potrzeby oceny migracji.
**Wystąpienia zarezerwowane (RIs)** | Określ [wystąpienia zarezerwowane na](https://azure.microsoft.com/pricing/reserved-vm-instances/) platformie Azure, tak aby szacunki kosztów w ocenie uwzględniać rabaty RI.<br/><br/> Programy RI są obecnie obsługiwane tylko w przypadku ofert płatności zgodnie z rzeczywistymu w usłudze Azure Migrate.
**Kryteria zmiany rozmiaru** | Używane do odpowiedniego rozmiaru maszyny wirtualnej na platformie Azure.<br/><br/> Użyj rozmiaru jako rozmiaru lub rozmiaru opartego na wydajności.
**Historia wydajności** | Używany z rozmiarami opartymi na wydajności. Określ czas trwania używany podczas oceny danych wydajności.
**Użycie percentyla** | Używany z rozmiarami opartymi na wydajności. Określa wartość percentyla próbki wydajności, która ma być używana do zmiany rozmiaru po prawej stronie. 
**Serie maszyn wirtualnych** | Określ serię maszyn wirtualnych platformy Azure, które mają zostać rozważne w przypadku zmiany rozmiaru po prawej stronie. Na przykład jeśli nie masz środowiska produkcyjnego, które wymaga maszyn wirtualnych serii A na platformie Azure, można wykluczyć serii A z listy lub serii.
**Współczynnik komfortu** | Bufor używany podczas oceny. Stosowane na podstawie danych wykorzystania komputera dla maszyn wirtualnych (procesora CPU, pamięci, dysku i sieci). Uwzględnia problemy, takie jak użycie sezonowe, krótka historia wydajności i prawdopodobnie zwiększa przyszłe użycie.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna z 20% wykorzystania zwykle powoduje dwurdzeniowej maszyny Wirtualnej. Przy współczynniku komfortu 2,0x rezultatem jest czterordzeniowa maszyna wirtualna.
**Oferta** | Wyświetla [ofertę platformy Azure,](https://azure.microsoft.com/support/legal/offer-details/) w której jesteś zarejestrowany. Ocena serwera odpowiednio szacuje koszt.
**Waluta** | Waluta rozliczeniowa twojego konta.
**Rabat (%)** | Wyświetla listę rabatów specyficznych dla subskrypcji, które otrzymujesz na górze oferty platformy Azure. Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli maszyny wirtualne platformy Azure nie będą działać 24 godziny na dobę, 7 dni w tygodniu, można określić czas trwania (dni w miesiącu i godziny dziennie) będą uruchamiane. Szacunki kosztów są odpowiednio obsługiwane.<br/><br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz gwarancję oprogramowania i kwalifikujesz się do korzystania z [usługi Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Jeśli jest ustawiona na Tak (ustawienie domyślne), ceny inne niż Windows Azure są uwzględniane dla maszyn wirtualnych z systemem Windows.

[Zapoznaj się z najlepszymi rozwiązaniami w](best-practices-assessment.md) zakresie tworzenia oceny za pomocą oceny serwera.


## <a name="calculate-readiness"></a>Oblicz gotowość

Nie wszystkie maszyny są odpowiednie do uruchamiania na platformie Azure. Ocena serwera ocenia każdy komputer lokalny i przypisuje mu kategorię gotowości. 
- **Gotowe do platformy Azure:** komputer może być migrowany w stanie— bez żadnych zmian. Rozpocznie się na platformie Azure z pełną obsługą platformy Azure.
- **Warunkowo gotowy do platformy Azure:** komputer może uruchomić na platformie Azure, ale może nie mieć pełnej obsługi platformy Azure. Na przykład komputer, na który jest uruchomiona starsza wersja systemu Windows Server nie jest obsługiwany na platformie Azure. Przed migracją tych komputerów na platformę Azure należy zachować ostrożność. Postępuj zgodnie z wytycznymi dotyczącymi korygowania sugerowanymi w ocenie, aby rozwiązać problemy z gotowością.
- **Nie jest gotowy na platformę Azure:** komputer nie zostanie uruchomiony na platformie Azure. Na przykład jeśli dysk komputera lokalnego jest więcej niż 64-TBs, nie można hostować na platformie Azure. Postępuj zgodnie ze wskazówkami dotyczącymi korygowania, aby rozwiązać problem przed migracją. 
- **Gotowość nieznany:** Migracja platformy Azure nie może określić gotowość komputera z powodu niewystarczających metadanych.

Aby obliczyć gotowość, ocena serwera przegląda właściwości komputera i ustawienia systemu operacyjnego podsumowane w poniższych tabelach. 

### <a name="machine-properties"></a>Właściwości maszyny

Ocena serwera przegląda następujące właściwości lokalnej maszyny Wirtualnej, aby ustalić, czy można ją uruchomić na platformie Azure.

**Właściwość** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchu systemu BIOS, a nie UEFI. | Warunkowo gotowy, jeśli typ rozruchu jest UEFI.
**Rdzenie** | Liczba rdzeni na komputerach musi być równa lub mniejsza niż maksymalna liczba rdzeni (128) obsługiwanych dla maszyny Wirtualnej platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, usługa Azure Migrate uwzględnia używane rdzenie do porównania. Jeśli w ustawieniach oceny określono współczynnik komfortu, liczba wykorzystanych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, usługa Azure Migrate używa przydzielonych rdzeni bez stosowania współczynnika komfortu. | Gotowy, jeśli jest mniejszy lub równy limitom.
**Pamięci** | Rozmiar pamięci komputera musi być równy lub mniejszy niż maksymalna pamięć (3892 gigabajty&nbsp;[GB] w serii M platformy Azure Standard_M128m<sup>2</sup>) dozwolona dla maszyny Wirtualnej platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli historia wydajności jest dostępna, usługa Azure Migrate uwzględnia wykorzystaną pamięć do porównania. Jeśli określono współczynnik komfortu, wykorzystana pamięć jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii, przydzielona pamięć jest używana bez stosowania współczynnika komfortu.<br/><br/> | Gotowy, jeśli w granicach.
**Dysk magazynu** | Przydzielony rozmiar dysku musi wynosić 32 TB lub mniej. Mimo że platforma Azure obsługuje dyski o pojemności 64 TB z dyskami Ultra SSD, usługa Azure Migrate: Server Assessment sprawdza obecnie 32 TB jako limity rozmiaru dysku, ponieważ nie obsługuje jeszcze ultra ssd. <br/><br/> Liczba dysków podłączonych do urządzenia musi wynosić 65 lub mniej, łącznie z dyskiem systemu operacyjnego. | Gotowy, jeśli w granicach.
**Networking** | Maszyna musi mieć 32 lub mniej interfejsów sieciowych (NIC) podłączonych do niego. | Gotowy, jeśli w granicach.

### <a name="guest-operating-system"></a>System operacyjny gościa
Wraz z właściwości maszyny wirtualnej oceny serwera analizuje system operacyjny gościa maszyn, aby ustalić, czy można go uruchomić na platformie Azure.

> [!NOTE]
> W przypadku maszyn wirtualnych VMware ocena serwera używa systemu operacyjnego określonego dla maszyny Wirtualnej w vCenter Server do obsługi analizy systemu operacyjnego gościa. W przypadku maszyn wirtualnych z systemem Linux działających na VMware obecnie nie identyfikuje dokładnej wersji jądra systemu operacyjnego gościa.

Poniższa logika jest używana przez server assessment do identyfikowania gotowości platformy Azure na podstawie systemu operacyjnego.

**System operacyjny** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
System Windows Server 2016 & wszystkich usług SPs | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
System Windows Server 2012 R2 & wszystkie usługi SPs | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
System Windows Server 2012 & wszystkich usług SPs | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
Windows Server 2008 R2 ze wszystkimi adresami SPs | Platforma Azure zapewnia pełną obsługę.| Gotowa na platformę Azure
Windows Server 2008 (32-bitowy i 64-bitowy) | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
Windows Server 2003, 2003 R2 | Te systemy operacyjne przeszły datę zakończenia pomocy technicznej i potrzebują [niestandardowej umowy pomocy technicznej (CSA)](https://aka.ms/WSosstatement) do obsługi na platformie Azure. | Warunkowo gotowy do platformy Azure. Należy rozważyć uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Te systemy operacyjne przeszły datę zakończenia pomocy technicznej. Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. | Warunkowo gotowy do platformy Azure. Zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows Client 7, 8 i 10 | Platforma Azure zapewnia pomoc techniczną tylko w [ramach subskrypcji programu Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Warunkowo gotowa na platformę Azure
Pulpit systemu Windows 10 Pro | Platforma Azure zapewnia pomoc techniczną z [prawami do hostingu wielodostępnego.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Te systemy operacyjne przeszły datę zakończenia pomocy technicznej. Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. | Warunkowo gotowy do platformy Azure. Zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Linux | Platforma Azure wspiera te [systemy operacyjne Linux](../virtual-machines/linux/endorsed-distros.md). Inne systemy operacyjne Linux może uruchomić na platformie Azure, ale zaleca się uaktualnienie systemu operacyjnego do zatwierdzonej wersji przed migracją na platformę Azure. | Gotowe do platformy Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowy, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne<br/><br/> Na przykład Oracle Solaris, Apple macOS itp., FreeBSD itp. | Platforma Azure nie wspiera tych systemów operacyjnych. Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. | Warunkowo gotowy do platformy Azure. Zaleca się zainstalowanie obsługiwanego systemu operacyjnego przed migracją na platformę Azure.  
System operacyjny określony jako **Inne** w systemie vCenter Server | Usługa Azure Migrate nie może zidentyfikować systemu operacyjnego w tym przypadku. | Nieznana gotowość. Upewnij się, że system operacyjny działający wewnątrz maszyny Wirtualnej jest obsługiwany na platformie Azure.
32-bitowe systemy operacyjne | Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowy do platformy Azure. Należy rozważyć uaktualnienie systemu operacyjnego komputera z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego przed migracją na platformę Azure.

## <a name="calculating-sizing"></a>Obliczanie rozmiaru


Po oznaczeniu komputera jako gotowe do platformy Azure, ocena serwera sprawia, że zalecenia dotyczące rozmiaru do identyfikacji jednostki SKU maszyny Wirtualnej platformy Azure i dysku. Określanie rozmiaru zależy od tego, czy używasz rozmiaru lokalnego w wersji lokalnej, czy od rozmiaru opartego na wydajności.

### <a name="calculate-sizing-as-is-on-premises"></a>Obliczanie rozmiaru (jako lokalnego)

 Jeśli używasz rozmiaru lokalnego jako— ocena serwera nie uwzględnia historii wydajności maszyn wirtualnych i dysków.

- **Rozmiar obliczeń:** przydziela jednostkę SKU maszyny Wirtualnej platformy Azure na podstawie rozmiaru przydzielonego lokalnie.
- **Rozmiar magazynu/dysku:** Ocena serwera analizuje typ magazynu określony we właściwościach oceny (standardowy dysk TWARDY/SSD/premium) i odpowiednio zaleca typ dysku. Domyślnym typem magazynu są dyski premium.
- **Rozmiary sieci:** Ocena serwera uwzględnia kartę sieciową na komputerze lokalnym.


### <a name="calculate-sizing-performance-based"></a>Obliczanie rozmiaru (oparte na wydajności)

W przypadku korzystania z rozmiaru opartego na wydajności, ocena serwera, która przedstawia zalecenia dotyczące zmiany rozmiaru w następujący sposób:

- Ocena serwera uwzględnia historię wydajności komputera w celu zidentyfikowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- Jeśli serwery zostały zaimportowane przy użyciu pliku CSV, używane są określone wartości. Ta metoda jest szczególnie przydatna, jeśli masz nadmiernie przydzielone komputera lokalnego, wykorzystanie jest niska i chcesz prawo rozmiar maszyny wirtualnej na platformie Azure, aby zaoszczędzić koszty. 
- Jeśli nie chcesz używać danych o wydajności, zresetuj kryteria zmiany rozmiaru do stanu lokalnego, zgodnie z opisem w poprzedniej sekcji.

#### <a name="calculate-storage-sizing"></a>Obliczanie rozmiaru magazynu

W przypadku zmiany rozmiaru magazynu usługa Azure Migrate próbuje mapować każdy dysk podłączony do komputera na dysk na platformie Azure i działa w następujący sposób:

1. Ocena serwera dodaje odczyt i zapis We/Wy na dysku, aby uzyskać całkowitą wymaganą liczbę we/wy we/wy. Podobnie dodaje wartości przepływności odczytu i zapisu, aby uzyskać całkowitą przepływność każdego dysku.
2. Jeśli określono typ magazynu jako automatyczny, na podstawie efektywnych i wartości przepływności we/wydzielonych, ocena serwera określa, czy dysk powinien być mapowany na standardowy dysk twardy, standardowy dysk SSD lub dysk premium na platformie Azure. Jeśli typ magazynu jest ustawiony na Standardowy dysk twardy/SSD/Premium, ocena serwera próbuje znaleźć jednostkę SKU dysku w wybranym typie pamięci masowej (standardowe dyski HDD/SSD/Premium).
3. Dyski są wybierane w następujący sposób:
    - Jeśli ocena serwera nie może znaleźć dysku z wymaganymi operacjaami We/Wy i przepływnością, oznacza to, że komputer nie nadaje się do platformy Azure.
    - Jeśli ocena serwera znajdzie zestaw odpowiednich dysków, wybierze dyski obsługujące lokalizację określoną w ustawieniach oceny.
    - Jeśli istnieje wiele kwalifikujących się dysków, program Ocena serwera wybiera dysk o najniższym koszcie.
    - Jeśli dane dotyczące wydajności dowolnego dysku są niedostępne, dane konfiguracyjne dysku (rozmiar dysku) są używane do znajdowania standardowego dysku SSD na platformie Azure.

#### <a name="calculate-network-sizing"></a>Obliczanie rozmiaru sieci

Ocena serwera próbuje znaleźć maszynę wirtualną platformy Azure, która może obsługiwać liczbę kart sieciowych dołączonych do komputera lokalnego i wydajność wymaganą przez te karty sieciowe.
- Aby uzyskać efektywną wydajność sieci lokalnej maszyny Wirtualnej, ocena serwera agreguje dane przesyłane na sekundę (MB/s) z komputera (wyjście sieci), we wszystkich kartach sieciowych i stosuje współczynnik komfortu. Używa tego numeru, aby znaleźć maszynę wirtualną platformy Azure, która może obsługiwać wymaganą wydajność sieci.
- Wraz z wydajnością sieci ocena serwera uwzględnia również, czy maszyna wirtualna platformy Azure może obsługiwać wymaganą liczbę kart sieciowych.
- Jeśli nie są dostępne żadne dane dotyczące wydajności sieci, w ocenie serwera uwzględnia się tylko liczbę kart sieciowych dla rozmiaru maszyny Wirtualnej.


#### <a name="calculate-compute-sizing"></a>Obliczanie rozmiaru obliczeń

Po obliczeniu wymagań dotyczących magazynu i sieci, Ocena serwera uwzględnia wymagania dotyczące procesora i pamięci, aby znaleźć odpowiedni rozmiar maszyny Wirtualnej na platformie Azure.
- Usługa Azure Migrate analizuje efektywne wykorzystywane rdzenie i pamięć, aby znaleźć odpowiedni rozmiar maszyny Wirtualnej na platformie Azure.
- Jeśli nie zostanie znaleziony odpowiedni rozmiar, komputer jest oznaczony jako nieodpowiedni dla platformy Azure.
- Jeśli zostanie znaleziony odpowiedni rozmiar, usługa Azure Migrate zastosuje obliczenia magazynu i sieci. Następnie stosuje ustawienia lokalizacji i warstwy cenowej dla ostatecznego zalecenia rozmiaru maszyny Wirtualnej.
- Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.


## <a name="confidence-ratings-performance-based"></a>Oceny ufności (oparte na wynikach)

Każda ocena oparta na wydajności w usłudze Azure Migrate jest skojarzona z oceną zaufania, która waha się od jednej (najniższej) do pięciu gwiazdek (najwyższa). Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru dostarczonych przez usługę Azure Migrate.

- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- W przypadku zmiany rozmiaru opartego na wydajności ocena serwera wymaga:
    - Dane wykorzystania pamięci procesora CPU i maszyny Wirtualnej.
    - Dysk We/Wy i przepływność danych dla każdego dysku podłączonego do maszyny Wirtualnej.
    - We/Wy sieci do obsługi rozmiaru opartego na wydajności dla każdej karty sieciowej podłączonej do maszyny Wirtualnej.
    - Jeśli którykolwiek z tych numerów wykorzystania nie są dostępne, zalecenia dotyczące rozmiaru może nie być wiarygodne.

> [!NOTE]
> Oceny ufności nie są przypisywane dla serwerów ocenianych przy użyciu importowanego . pliku CSV. Oceny nie mają również zastosowania do oceny lokalnej zgodnie z przepisami.
   
### <a name="ratings"></a>Klasyfikacje

W zależności od odsetka dostępnych punktów danych ocena ufności dla oceny jest następująca.

   **Dostępność punktów danych** | **Ocena zaufania**
   --- | ---
   0-20% | 1 gwiazdka
   21-40% | 2 gwiazdki
   41-60% | 3 gwiazdki
   61-80% | 4 gwiazdki
   81-100% | 5 gwiazdek

### <a name="low-confidence-ratings"></a>Niskie oceny zaufania

Oto kilka powodów, dla których ocena może uzyskać niską ocenę zaufania:

- Nie profil środowiska na czas, dla którego tworzysz oceny. Na przykład jeśli tworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać co najmniej jeden dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych, aby uzyskać zebrane.
- Niektóre maszyny wirtualne zostały zamknięte w okresie, dla którego obliczono ocenę. Jeśli wszystkie maszyny wirtualne są wyłączone przez pewien czas trwania, ocena serwera nie może zbierać danych o wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone w okresie, dla którego ocena została obliczona. Na przykład jeśli utworzono ocenę dla historii wydajności w ostatnim miesiącu, ale niektóre maszyny wirtualne zostały utworzone w środowisku tylko tydzień temu, historia wydajności nowych maszyn wirtualnych nie będzie istnieć przez cały czas trwania.

> [!NOTE]
> Jeśli ocena ufności dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia, aby urządzenie profilować środowisko, a następnie ponownie obliczyć ocenę. Jeśli nie, rozmiary oparte na wydajności mogą nie być wiarygodne. W takim przypadku zaleca się przełączenie oceny na lokalne rozmiary.

## <a name="calculate-monthly-costs"></a>Obliczanie kosztów miesięcznych

Po zakończeniu zmiany rozmiaru zalecenia dotyczące migracji platformy Azure oblicza koszty obliczeń obliczeniowych i magazynowych po migracji.

- **Koszt obliczeń:** Przy użyciu zalecanego rozmiaru maszyny Wirtualnej platformy Azure usługa Azure Migrate używa interfejsu API rozliczeń do obliczania miesięcznego kosztu maszyny Wirtualnej.
    - Obliczenia uwzględniają system operacyjny, zapewnienie oprogramowania, wystąpienia zarezerwowane, czas pracy maszyny Wirtualnej, lokalizację i ustawienia waluty.
    - Agreguje koszt na wszystkich komputerach, aby obliczyć całkowity miesięczny koszt obliczeń.
- **Koszt magazynowania:** Miesięczny koszt magazynowania komputera jest obliczany przez agregowanie miesięcznego kosztu wszystkich dysków podłączonych do komputera w następujący sposób:
    - Server Assessment oblicza całkowite miesięczne koszty magazynowania, agregując koszty magazynowania wszystkich komputerów.
    - Obecnie obliczenia nie uwzględnić oferty określone w ustawieniach oceny.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.


## <a name="next-steps"></a>Następne kroki

[Zapoznaj się z](best-practices-assessment.md) najlepszymi praktykami tworzenia ocen. 


- Dowiedz się więcej o uruchamianiu ocen dla [maszyn wirtualnych VMware,](tutorial-prepare-vmware.md) [maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md)i [serwerów fizycznych.](tutorial-prepare-physical.md)
- Dowiedz się więcej o ocenie serwerów [zaimportowanych za pomocą pliku CSV](tutorial-assess-import.md).
- Dowiedz się więcej o [konfigurowaniu wizualizacji zależności](concepts-dependency-visualization.md).
