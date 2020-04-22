---
title: Oceny w ocenie serwera migracji platformy Azure
description: Dowiedz się więcej o ocenach w ocenie serwera migracji platformy Azure
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769918"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Oceny w obszarze Migracji platformy Azure: Ocena serwera

Ten artykuł zawiera omówienie ocen w [narzędziu Azure Migrate: Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Narzędzie może oceniać lokalne maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper V i serwery fizyczne w celu migracji na platformę Azure.

## <a name="whats-an-assessment"></a>Co to jest ocena?

Ocena za pomocą narzędzia Ocena serwera mierzy gotowość i szacuje wpływ migracji serwerów lokalnych na platformę Azure.

> [!NOTE]
> W usłudze Azure dla instytucji rządowych przejrzyj obsługiwane lokalizacje oceny [docelowej.](migrate-support-matrix.md#supported-geographies-azure-government) Należy zauważyć, że zalecenia dotyczące rozmiaru maszyny Wirtualnej w ocenach będą używać serii maszyn wirtualnych specjalnie dla regionów chmury rządowej. [Dowiedz się więcej](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) o typach maszyn wirtualnych.

## <a name="types-of-assessments"></a>Rodzaje ocen

Oceny utworzone za pomocą oceny serwera są migawką danych w czasie. Ocena serwera zapewnia dwa typy ocen.

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny, które wytęzają zalecenia na podstawie zebranych danych dotyczących wydajności | Zalecenie rozmiaru maszyny Wirtualnej jest oparte na danych wykorzystania procesora CPU i pamięci RAM.<br/><br/> Zalecenie typu dysku jest oparte na operacjach wejścia/wyjścia na sekundę (IOPS) i przepływności dysków lokalnych. Typy dysków to dyski Azure Standard HDD, Azure Standard SSD i Azure Premium.
**Jako jest w środowisku lokalnym** | Oceny, które nie wykorzystują danych dotyczących wydajności do zaleceń | Zalecenie rozmiaru maszyny Wirtualnej jest oparte na lokalnym rozmiarze maszyny Wirtualnej.<br/><br> Zalecany typ dysku jest oparty na wybranym typie magazynu dla oceny.

## <a name="how-do-i-run-an-assessment"></a>Jak przeprowadzić ocenę?

Istnieje kilka sposobów, aby przeprowadzić ocenę.

- Oceniaj maszyny przy użyciu metadanych serwera zebranych przez lekkie urządzenie migracji platformy Azure. Urządzenie odnajduje maszyny lokalne. Następnie wysyła metadane komputera i dane o wydajności do usługi Azure Migrate.
- Oceniaj maszyny przy użyciu metadanych serwera importowanych w formacie CSV (values separated comma).

## <a name="how-do-i-assess-with-the-appliance"></a>Jak ocenić urządzenie?

Jeśli wdrażasz urządzenie migracji platformy Azure w celu odnajdywania serwerów lokalnych, wykonaj następujące czynności:

1. Skonfiguruj platformę Azure i środowisko lokalne do pracy z oceną serwera.
1. W przypadku pierwszej oceny utwórz projekt platformy Azure i dodaj do niego narzędzie Oceny serwera.
1. Wdrażanie lekkiego urządzenia migracji platformy Azure. Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny i dane o wydajności do usługi Azure Migrate. Wdrażanie urządzenia jako maszyny wirtualnej lub komputera fizycznego. Nie musisz instalować niczego na komputerach, które chcesz ocenić.

Po rozpoczęciu odnajdywania maszyny urządzenie, można zebrać maszyny, które chcesz ocenić w grupie i uruchomić ocenę dla grupy.

Aby wypróbować te kroki, wykonaj nasze samouczki dotyczące serwerów [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)lub [fizycznych.](tutorial-prepare-physical.md)

## <a name="how-do-i-assess-with-imported-data"></a>Jak oceniać za pomocą importowanych danych?

Jeśli oceniasz serwery przy użyciu pliku CSV, nie potrzebujesz urządzenia. Zamiast tego wykonaj następujące czynności:

1. Konfigurowanie platformy Azure do pracy z oceną serwera.
1. W przypadku pierwszej oceny utwórz projekt platformy Azure i dodaj do niego narzędzie Oceny serwera.
1. Pobierz szablon CSV i dodaj do niego dane serwera.
1. Zaimportuj szablon do oceny serwera.
1. Odnajduj serwery dodane wraz z importem, zbierz je w grupie i uruchom ocenę dla grupy.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane gromadzi urządzenie?

Jeśli używasz urządzenia migracji platformy Azure do oceny, dowiedz się więcej o metadanych i danych dotyczących wydajności, które są zbierane dla [VMware](migrate-appliance.md#collected-data---vmware) i [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>W jaki sposób urządzenie oblicza dane o wydajności?

Jeśli urządzenie jest używane do odnajdowania, zbiera dane o wydajności dla ustawień obliczeniowych za pomocą następujących kroków:

1. Urządzenie zbiera punkt próbki w czasie rzeczywistym.

    - **Maszyny wirtualne VMware:** punkt próbkowania jest zbierany co 20 sekund.
    - **Maszyny wirtualne funkcji Hyper-V:** punkt próbkowania jest zbierany co 30 sekund.
    - **Serwery fizyczne:** punkt próbkowania jest zbierany co pięć minut.

1. Urządzenie łączy punkty próbki, aby utworzyć pojedynczy punkt danych co 10 minut. Aby utworzyć punkt danych, urządzenie wybiera wartości szczytowe ze wszystkich próbek. Następnie wysyła punkt danych do platformy Azure.
1. Ocena serwera przechowuje wszystkie 10-minutowe punkty danych za ostatni miesiąc.
1. Podczas tworzenia oceny, ocena serwera identyfikuje odpowiedni punkt danych do użycia do rightsizing. Identyfikacja jest oparta na wartościach percentyla dla *historii wydajności* i *wykorzystania percentyla*.

    - Na przykład jeśli historia wydajności jest jeden tydzień i wykorzystanie percentyla jest 95 percentyl, ocena serwera sortuje 10-minutowe punkty próbki w ostatnim tygodniu. Sortuje je w porządku rosnącym i wybiera 95-ta wartość percentyla dla rightsizing.
    - Wartość 95 percentyla zapewnia ignorowanie wszelkich wartości odstałych, które mogą zostać uwzględnione, jeśli wybrałeś 99 percentyl.
    - Jeśli chcesz wybrać szczytowe użycie dla okresu i nie chcesz przegapić żadnych wartości odstania, wybierz 99 percentyl dla wykorzystania percentyla.

1. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać dane o efektywnym wykorzystaniu wydajności dla tych wskaźników, które zbiera urządzenie:

    - Wykorzystanie procesora
    - Wykorzystanie pamięci RAM
    - IOPS dysku (odczyt i zapis)
    - Przepływność dysku (odczyt i zapis)
    - Przepustowość sieci (wnikanie i wyjęcie)

## <a name="how-are-assessments-calculated"></a>W jaki sposób obliczane są oceny?

Ocena serwera używa metadanych komputerów lokalnych i danych o wydajności do obliczania ocen. Jeśli wdrożysz urządzenie migracji platformy Azure, ocena używa danych, które urządzenie zbiera. Ale po uruchomieniu oceny zaimportowane przy użyciu pliku CSV, należy podać metadane do obliczeń.

Obliczenia występują w tych trzech etapach:

1. **Oblicz gotowość platformy Azure:** Oceń, czy maszyny nadają się do migracji na platformę Azure.
1. **Obliczanie rekomendacji dotyczących rozmiaru:** Oszacuj dane obliczeniowe, magazyn i rozmiar sieci.
1. **Oblicz miesięczne koszty:** oblicz szacowane miesięczne koszty obliczeń i magazynowania dla uruchamiania komputerów na platformie Azure po migracji.

Obliczenia są w powyższej kolejności. Serwer komputera przechodzi do późniejszego etapu tylko wtedy, gdy przechodzi poprzedni. Na przykład jeśli serwer nie powiedzie się etap gotowości platformy Azure, jest oznaczony jako nieodpowiedni dla platformy Azure. Obliczanie rozmiaru i kosztów nie jest wykonywane dla tego serwera.

## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

Oto, co jest zawarte w ocenie serwera:

Właściwość | Szczegóły
--- | ---
**Lokalizacja docelowa** | Lokalizacja, do której chcesz przeprowadzić migrację. Ocena serwera obsługuje obecnie te docelowe regiony platformy Azure:<br/><br/> Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Środkowe Indie, Środkowe STANY USA, Chiny Wschodnie, Wschodnia Azja, Wschodnie STANY USA, Wschodnie STANY USA 2, Niemcy Środkowe, Niemcy Północno-Wschodnie, Japonia Wschodnia, Japonia Zachodnia, Korea Południowa, Północno-Środkowe STANY USA, Europa Północna, Południowo-Środkowe STANY USA, Azja Południowo-Wschodnia, Indie Południowe, Wielka Brytania Południowa, Wielka Brytania Zachodnia, US Gov Arizona, US Gov Texas, US Gov Virginia , Zachodnie środkowe stany USA, Europa Zachodnia, Indie Zachodnie, Zachodnie stany USA i Zachodnie stany USA 2.
**Docelowy dysk magazynu (w rozmiarze)** | Typ dysku do użycia w magazynie na platformie Azure. <br/><br/> Określ docelowy dysk pamięci masowej jako zarządzany w wersji Premium, standardowy dysk SSD zarządzany lub standardowy zarządzany przez dysk twardy.
**Docelowy dysk pamięci masowej (rozmiar oparty na wydajności)** | Określa typ docelowego dysku magazynu jako automatyczny, zarządzany w wersji Premium, zarządzany przez standardowy dysk twardy lub standardowy dysk SSD zarządzany.<br/><br/> **Automatyczne**: Zalecenie dysku opiera się na danych wydajności dysków, co oznacza We/Wy i przepływność.<br/><br/>**Premium lub Standard**: Ocena zaleca jednostkę SKU dysku w wybranym typie magazynu.<br/><br/> Jeśli chcesz, aby umowa na poziomie usługi maszyny Wirtualnej jednego wystąpienia (SLA) 99,9%, należy rozważyć użycie dysków zarządzanych w wersji Premium. To użycie gwarantuje, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w wersji Premium.<br/><br/> Usługa Azure Migrate obsługuje tylko dyski zarządzane do oceny migracji.
**Wystąpienia zarezerwowanej maszyny wirtualnej platformy Azure** | Określa [wystąpienia zarezerwowane,](https://azure.microsoft.com/pricing/reserved-vm-instances/) tak aby szacunki kosztów w ocenie uwzględniać je.<br/><br/> Usługa Azure Migrate obsługuje obecnie wystąpienia zarezerwowanych maszyn wirtualnych platformy Azure tylko dla ofert płatności zgodnie z rzeczywistym użyciem.
**Kryteria zmiany rozmiaru** | Służy do rightsize maszyny Wirtualnej platformy Azure.<br/><br/> Użyj rozmiaru jako rozmiaru lub rozmiaru opartego na wydajności.
**Historia wydajności** | Używany z rozmiarami opartymi na wydajności. Historia wydajności określa czas trwania używany podczas obliczywania danych wydajności.
**Użycie percentyla** | Używany z rozmiarami opartymi na wydajności. Wykorzystanie percentyla określa wartość percentyla próbki wydajności używanej do rightsizingu.
**Serie maszyn wirtualnych** | Seria maszyn wirtualnych platformy Azure, które mają zostać rozważne w celu uzyskania praw. Na przykład jeśli nie masz środowiska produkcyjnego, które wymaga maszyn wirtualnych serii A na platformie Azure, można wykluczyć serii A z listy serii.
**Współczynnik komfortu** | Bufor używany podczas oceny. Jest on stosowany do danych procesora CPU, pamięci RAM, dysku i wykorzystania sieci dla maszyn wirtualnych. Uwzględnia problemy, takie jak sezonowe użycie, krótka historia wydajności i prawdopodobnie zwiększa przyszłe użycie.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna z 20% wykorzystania zwykle powoduje dwurdzeniowej maszyny Wirtualnej. Przy współczynniku komfortu 2.0, rezultatem jest czterordzeniowa maszyna wirtualna.
**Oferta** | [Oferta platformy Azure,](https://azure.microsoft.com/support/legal/offer-details/) w której jesteś zarejestrowany. Ocena serwera szacuje koszt tej oferty.
**Waluta** | Waluta rozliczeniowa twojego konta.
**Rabat (%)** | Wszelkie rabaty specyficzne dla subskrypcji, które otrzymujesz na podstawie oferty platformy Azure. Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Czas trwania w dniach w miesiącu i godzin dziennie dla maszyn wirtualnych platformy Azure, które nie będą działać w sposób ciągły. Szacunki kosztów są oparte na tym czasie trwania.<br/><br/> Wartości domyślne to 31 dni w miesiącu i 24 godziny na dobę.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz gwarancję oprogramowania i kwalifikujesz się do korzystania z [usługi Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Jeśli ustawienie ma wartość domyślną "Tak", ceny platformy Azure dla systemów operacyjnych innych niż Windows są uważane za maszyny wirtualne z systemem Windows.

[Zapoznaj się z najlepszymi rozwiązaniami dotyczących](best-practices-assessment.md) tworzenia oceny za pomocą oceny serwera.

## <a name="calculate-readiness"></a>Oblicz gotowość

Nie wszystkie maszyny są odpowiednie do uruchamiania na platformie Azure. Server Assessment ocenia wszystkie maszyny lokalne i przypisuje im kategorię gotowości.

- **Gotowe do platformy Azure:** komputer może być migrowany w stanie— bez żadnych zmian. Rozpocznie się na platformie Azure z pełną obsługą platformy Azure.
- **Warunkowo gotowy do platformy Azure:** komputer może uruchomić na platformie Azure, ale może nie mieć pełnej obsługi platformy Azure. Na przykład platforma Azure nie obsługuje komputera, na których jest uruchomiona stara wersja systemu Windows Server. Przed migracją tych komputerów na platformę Azure należy zachować ostrożność. Aby rozwiązać wszelkie problemy z gotowością, postępuj zgodnie z wytycznymi dotyczącymi korygowania, które sugeruje ocena.
- **Nie jest gotowy na platformę Azure:** komputer nie zostanie uruchomiony na platformie Azure. Na przykład jeśli dysk komputera lokalnego przechowuje więcej niż 64 TB, platforma Azure nie może obsługiwać komputera. Postępuj zgodnie ze wskazówkami dotyczącymi korygowania, aby rozwiązać problem przed migracją.
- **Gotowość nieznany:** Migracja platformy Azure nie może określić gotowość komputera z powodu niewystarczających metadanych.

Aby obliczyć gotowość, ocena serwera przegląda właściwości komputera i ustawienia systemu operacyjnego podsumowane w poniższych tabelach.

### <a name="machine-properties"></a>Właściwości maszyny

Ocena serwera przegląda następujące właściwości lokalnej maszyny Wirtualnej, aby ustalić, czy może działać na platformie Azure.

Właściwość | Szczegóły | Stan gotowości platformy Azure
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchu systemu BIOS, a nie UEFI. | Warunkowo gotowy, jeśli typ rozruchu jest UEFI
**Rdzenie** | Każdy komputer musi mieć nie więcej niż 128 rdzeni, co jest maksymalną liczbą, którą obsługuje maszyna wirtualna platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, usługa Azure Migrate uwzględnia używane rdzenie do porównania. Jeśli ustawienia oceny określają współczynnik komfortu, liczba wykorzystanych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, usługa Azure Migrate używa przydzielonych rdzeni bez stosowania współczynnika komfortu. | Gotowy, jeśli liczba rdzeni mieści się w limicie
**Pamięci ram** | Każdy komputer musi mieć nie więcej niż 3892 GB pamięci RAM, co jest maksymalnym rozmiarem platformy Azure M-series Standard_M128m&nbsp;<sup>2</sup> maszyny wirtualnej obsługuje. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli historia wydajności jest dostępna, usługa Azure Migrate uwzględnia wykorzystaną pamięć RAM do porównania. Jeśli określono współczynnik komfortu, wykorzystana pamięć RAM jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii, przydzielona pamięć RAM jest używana bez zastosowania współczynnika komfortu.<br/><br/> | Gotowy, jeśli ilość pamięci RAM mieści się w limicie
**Dysk magazynu** | Przydzielony rozmiar dysku nie może być większy niż 32 TB. Mimo że platforma Azure obsługuje dyski o pojemności 64 TB z dyskami Azure Ultra SSD, usługa Azure Migrate: Server Assessment obecnie sprawdza 32 TB jako limit rozmiaru dysku, ponieważ nie obsługuje jeszcze ultra ssd. <br/><br/> Liczba dysków podłączonych do urządzenia, w tym dysku systemu operacyjnego, musi wynosić 65 lub mniej. | Gotowy, jeśli rozmiar i numer dysku mieszczą się w granicach
**Networking** | Urządzenie nie może mieć dołączonych więcej niż 32 interfejsów sieciowych (NIC). | Gotowość, jeśli liczba kart sieciowych mieści się w limicie

### <a name="guest-operating-system"></a>System operacyjny gościa

Wraz z przeglądaniem właściwości maszyny Wirtualnej, ocena serwera analizuje system operacyjny gościa komputera, aby ustalić, czy można go uruchomić na platformie Azure.

> [!NOTE]
> Do obsługi analizy gości dla maszyn wirtualnych VMware, Ocena serwera używa systemu operacyjnego określonego dla maszyny Wirtualnej w vCenter Server. W przypadku maszyn wirtualnych z systemem Linux uruchomionych na platformie VMware ocena serwera obecnie nie identyfikuje wersji jądra systemu operacyjnego gościa.

Ocena serwera używa następującej logiki do identyfikowania gotowości platformy Azure na podstawie systemu operacyjnego:

**System operacyjny** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
Windows Server 2016 i wszystkie usługi SPs | Platforma Azure zapewnia pełną obsługę. | Gotowość do platformy Azure.
Windows Server 2012 R2 i wszystkie SPs | Platforma Azure zapewnia pełną obsługę. | Gotowość do platformy Azure.
Windows Server 2012 i wszystkie usługi SPs | Platforma Azure zapewnia pełną obsługę. | Gotowość do platformy Azure.
Windows Server 2008 R2 ze wszystkimi adresami SPs | Platforma Azure zapewnia pełną obsługę.| Gotowość do platformy Azure.
Windows Server 2008 (32-bitowy i 64-bitowy) | Platforma Azure zapewnia pełną obsługę. | Gotowość do platformy Azure.
Systemy Windows Server 2003 i Windows Server 2003 R2 | Te systemy operacyjne przeszły daty zakończenia pomocy technicznej i potrzebują [niestandardowej umowy pomocy technicznej (CSA)](https://aka.ms/WSosstatement) do obsługi na platformie Azure. | Warunkowo gotowy do platformy Azure. Należy rozważyć uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 i MS-DOS | Te systemy operacyjne przeszły swoje daty zakończenia pomocy technicznej. Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. | Warunkowo gotowy do platformy Azure. Zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows 7, Windows 8 i Windows 10 | Platforma Azure zapewnia pomoc techniczną tylko z [subskrypcją programu Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Warunkowo gotowy do platformy Azure.
Windows 10 Pro | Platforma Azure zapewnia pomoc techniczną z [prawami do hostingu wielodostępnego.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Warunkowo gotowy do platformy Azure.
Windows Vista i Windows XP Professional | Te systemy operacyjne przeszły swoje daty zakończenia pomocy technicznej. Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. | Warunkowo gotowy do platformy Azure. Zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Linux | Zobacz [systemy operacyjne Linux,](../virtual-machines/linux/endorsed-distros.md) które platforma Azure zatwierdza. Inne systemy operacyjne Linux mogą zostać uruchomiony na platformie Azure. Zaleca się jednak uaktualnienie systemu operacyjnego do zatwierdzonej wersji przed migracją na platformę Azure. | Gotowe do platformy Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowy, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne, takie jak Oracle Solaris, Apple macOS i FreeBSD | Platforma Azure nie wspiera tych systemów operacyjnych. Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. | Warunkowo gotowy do platformy Azure. Zaleca się zainstalowanie obsługiwanego systemu operacyjnego przed migracją na platformę Azure.  
System operacyjny określony jako **Inne** w systemie vCenter Server | Usługa Azure Migrate nie może zidentyfikować systemu operacyjnego w tym przypadku. | Nieznana gotowość. Upewnij się, że platforma Azure obsługuje system operacyjny uruchomiony wewnątrz maszyny Wirtualnej.
32-bitowe systemy operacyjne | Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowy do platformy Azure. Rozważ uaktualnienie do 64-bitowego systemu operacyjnego przed migracją na platformę Azure.

## <a name="calculating-sizing"></a>Obliczanie rozmiaru

Po oznaczeniu komputera jako gotowe do platformy Azure, ocena serwera sprawia, że zalecenia dotyczące rozmiaru. Zalecenia te identyfikują jednostkę SKU maszyny Wirtualnej platformy Azure i dysku. Określanie rozmiaru zależy od tego, czy używasz rozmiaru lokalnego w wersji lokalnej, czy rozmiaru opartego na wydajności.

### <a name="calculate-sizing-as-is-on-premises"></a>Obliczanie rozmiaru (jako lokalnego)

 Jeśli używasz rozmiaru lokalnego jako— ocena serwera nie uwzględnia historii wydajności maszyn wirtualnych i dysków.

- **Rozmiary obliczeniowe:** Ocena serwera przydziela jednostkę SKU maszyny Wirtualnej platformy Azure na podstawie rozmiaru przydzielonego lokalnie.
- **Rozmiar magazynu i dysku**: Ocena serwera analizuje typ magazynu określony we właściwościach oceny i zaleca odpowiedni typ dysku. Możliwe typy pamięci masowej to Standard HDD, Standard SSD i Premium. Domyślnym typem magazynu jest Premium.
- **Rozmiary sieci:** Ocena serwera uwzględnia kartę sieciową na komputerze lokalnym.

### <a name="calculate-sizing-performance-based"></a>Obliczanie rozmiaru (oparte na wydajności)

W przypadku korzystania z rozmiaru opartego na wydajności ocena serwera przedstawia następujące zalecenia dotyczące rozmiaru:

- Ocena serwera uwzględnia historię wydajności komputera w celu zidentyfikowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- W przypadku importowania serwerów przy użyciu pliku CSV używane są określone wartości. Ta metoda jest szczególnie przydatna, jeśli masz nadmierną alokację komputera lokalnego, wykorzystanie jest niskie i chcesz rightsize maszyny wirtualnej platformy Azure, aby zaoszczędzić koszty.
- Jeśli nie chcesz używać danych o wydajności, zresetuj kryteria zmiany rozmiaru do stanu lokalnego, zgodnie z opisem w poprzedniej sekcji.

#### <a name="calculate-storage-sizing"></a>Obliczanie rozmiaru magazynu

W przypadku zmiany rozmiaru magazynu usługa Azure Migrate próbuje zamapować każdy dysk dołączony do komputera na dysk platformy Azure. Zmiana rozmiaru działa w następujący sposób:

1. Ocena serwera dodaje odczyt i zapis We/Wy na dysku, aby uzyskać całkowitą wymaganą liczbę we/wy we/wy. Podobnie dodaje wartości przepływności odczytu i zapisu, aby uzyskać całkowitą przepływność każdego dysku.
1. Jeśli określono typ magazynu jako automatyczny, wybrany typ jest oparty na efektywnych we/wy i wartości przepływności. Ocena serwera określa, czy dysk ma być mapowany na dysk standardowy, standardowy dysk SSD lub Dysk Premium na platformie Azure. Jeśli typ magazynu jest ustawiony na jeden z tych typów dysków, ocena serwera próbuje znaleźć jednostkę SKU dysku w wybranym typie magazynu.
1. Dyski są wybierane w następujący sposób:
    - Jeśli ocena serwera nie może znaleźć dysku z wymaganymi operacjaami We/Wy i przepływnością, oznacza to, że komputer nie nadaje się do platformy Azure.
    - Jeśli ocena serwera znajdzie zestaw odpowiednich dysków, wybierze dyski obsługujące lokalizację określoną w ustawieniach oceny.
    - Jeśli istnieje wiele kwalifikujących się dysków, program Ocena serwera wybiera dysk o najniższym koszcie.
    - Jeśli dane dotyczące wydajności dowolnego dysku są niedostępne, rozmiar dysku konfiguracyjnego jest używany do znajdowania standardowego dysku SSD na platformie Azure.

#### <a name="calculate-network-sizing"></a>Obliczanie rozmiaru sieci

Ocena serwera próbuje znaleźć maszynę wirtualną platformy Azure, która obsługuje liczbę i wymaganą wydajność kart sieciowych dołączonych do komputera lokalnego.

- Aby uzyskać efektywną wydajność sieci lokalnej maszyny Wirtualnej, program Server Assessment agreguje szybkość transmisji danych z komputera (wyjście sieci) we wszystkich kartach sieciowych. Następnie stosuje współczynnik komfortu. Używa wynikowej wartości, aby znaleźć maszynę wirtualną platformy Azure, która może obsługiwać wymaganą wydajność sieci.
- Wraz z wydajnością sieci ocena serwera uwzględnia również, czy maszyna wirtualna platformy Azure może obsługiwać wymaganą liczbę kart sieciowych.
- Jeśli dane dotyczące wydajności sieci są niedostępne, ocena serwera uwzględnia tylko liczbę kart sieciowych dla rozmiaru maszyny Wirtualnej.

#### <a name="calculate-compute-sizing"></a>Obliczanie rozmiaru obliczeń

Po obliczeniu wymagań dotyczących magazynu i sieci, Ocena serwera uwzględnia wymagania dotyczące procesora i pamięci RAM, aby znaleźć odpowiedni rozmiar maszyny Wirtualnej na platformie Azure.

- Usługa Azure Migrate analizuje efektywne wykorzystywane rdzenie i pamięć RAM, aby znaleźć odpowiedni rozmiar maszyny wirtualnej platformy Azure.
- Jeśli nie zostanie znaleziony odpowiedni rozmiar, komputer jest oznaczony jako nieodpowiedni dla platformy Azure.
- Jeśli zostanie znaleziony odpowiedni rozmiar, usługa Azure Migrate zastosuje obliczenia magazynu i sieci. Następnie stosuje ustawienia lokalizacji i warstwy cenowej dla ostatecznego zalecenia rozmiaru maszyny Wirtualnej.
- Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.

## <a name="confidence-ratings-performance-based"></a>Oceny ufności (oparte na wynikach)

Każda ocena oparta na wydajności w usłudze Azure Migrate jest skojarzona z oceną zaufania. Ocena waha się od jednej (najniższej) do pięciu (najwyższych) gwiazdek. Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru, które zapewnia usługa Azure Migrate.

- Ocena zaufania jest przypisana do oceny. Ocena jest oparta na dostępności punktów danych, które są potrzebne do obliczenia oceny.
- W przypadku zmiany rozmiaru opartego na wydajności ocena serwera wymaga:
    - Dane dotyczące wykorzystania pamięci RAM procesora i maszyny Wirtualnej.
    - Dysk We/Wy i przepływność danych dla każdego dysku podłączonego do maszyny Wirtualnej.
    - We/Wy sieci do obsługi rozmiaru opartego na wydajności dla każdej karty sieciowej podłączonej do maszyny Wirtualnej.

Jeśli którykolwiek z tych numerów wykorzystania nie jest dostępny, zalecenia dotyczące rozmiaru mogą być zawodne.

> [!NOTE]
> Oceny ufności nie są przypisywane dla serwerów ocenianych przy użyciu importowanego pliku CSV. Oceny nie mają również zastosowania do oceny lokalnej zgodnie z przepisami.

### <a name="ratings"></a>Klasyfikacje

W tej tabeli przedstawiono oceny zaufania, które zależą od odsetka dostępnych punktów danych:

   **Dostępność punktów danych** | **Ocena zaufania**
   --- | ---
   0-20% | 1 gwiazdka
   21-40% | 2 gwiazdki
   41-60% | 3 gwiazdki
   61-80% | 4 gwiazdki
   81-100% | 5 gwiazdek

### <a name="low-confidence-ratings"></a>Niskie oceny zaufania

Oto kilka powodów, dla których ocena może uzyskać niską ocenę zaufania:

- Nie profiluj środowiska przez czas, dla którego tworzysz ocenę. Na przykład jeśli tworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać co najmniej dzień po rozpoczęciu odnajdywania, aby wszystkie punkty danych zostały zebrane.
- Niektóre maszyny wirtualne zostały zamknięte w czasie, dla którego ocena została obliczona. Jeśli wszystkie maszyny wirtualne są wyłączone przez pewien czas trwania, ocena serwera nie może zbierać danych o wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone w czasie, dla którego ocena została obliczona. Załóżmy na przykład, że utworzono ocenę dla historii wydajności ostatniego miesiąca, ale niektóre maszyny wirtualne zostały utworzone zaledwie tydzień temu. Historia wydajności nowych maszyn wirtualnych nie będzie istnieć przez cały czas trwania.

> [!NOTE]
> Jeśli ocena ufności jakiejkolwiek oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia na profil otoczenia przez urządzenie, a następnie ponowne obliczenie oceny. W przeciwnym razie rozmiary oparte na wydajności mogą być zawodne. W takim przypadku zaleca się przełączenie oceny na lokalne rozmiary.

## <a name="calculate-monthly-costs"></a>Obliczanie kosztów miesięcznych

Po zakończeniu zmiany rozmiaru zalecenia dotyczące migracji platformy Azure oblicza koszty obliczeń obliczeniowych i magazynowych po migracji.

- **Koszt obliczeń:** Usługa Azure Migrate używa zalecanego rozmiaru maszyny Wirtualnej platformy Azure i interfejsu API rozliczeń platformy Azure do obliczania miesięcznego kosztu maszyny Wirtualnej.

    W obliczeniach uwzględni się:
    - System operacyjny
    - Zapewnienie oprogramowania
    - Wystąpienia zarezerwowane
    - Czas pracy maszyny wirtualnej
    - Lokalizacja
    - Ustawienia waluty

    Ocena serwera agreguje koszt na wszystkich komputerach, aby obliczyć całkowity miesięczny koszt obliczeń.

- **Koszt magazynowania:** Miesięczny koszt magazynowania komputera jest obliczany przez agregowanie miesięcznego kosztu wszystkich dysków podłączonych do komputera.

    Server Assessment oblicza całkowite miesięczne koszty magazynowania, agregując koszty magazynowania wszystkich komputerów. Obecnie obliczenia nie uwzględnić oferty określone w ustawieniach oceny.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.

## <a name="next-steps"></a>Następne kroki

[Zapoznaj się z](best-practices-assessment.md) najlepszymi praktykami tworzenia ocen. 

- Dowiedz się więcej o uruchamianiu ocen dla [maszyn wirtualnych VMware,](tutorial-prepare-vmware.md) [maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md)i [serwerów fizycznych.](tutorial-prepare-physical.md)
- Dowiedz się więcej o ocenie serwerów [zaimportowanych za pomocą pliku CSV](tutorial-assess-import.md).
- Dowiedz się więcej o [konfigurowaniu wizualizacji zależności](concepts-dependency-visualization.md).
