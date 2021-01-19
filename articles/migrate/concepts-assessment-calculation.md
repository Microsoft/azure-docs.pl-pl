---
title: Oceny maszyn wirtualnych platformy Azure w ramach oceny Azure Migrate Server
description: Dowiedz się więcej na temat ocen w Azure Migrate oceny serwera
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 9bdf907ede2c09f7e314df619cd81059956f17dc
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567747"
---
# <a name="server-assessment-overview-migrate-to-azure-vms"></a>Przegląd oceny serwera (Migrowanie do maszyn wirtualnych platformy Azure)

Ten artykuł zawiera omówienie ocen w Azure Migrate: Narzędzie do [oceny serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Narzędzie może ocenić lokalne maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne do migracji na platformę Azure.

## <a name="whats-an-assessment"></a>Co to jest Ocena?

Ocena narzędzia do oceny serwera mierzy gotowość i szacuje skutek migracji serwerów lokalnych na platformę Azure.

> [!NOTE]
> W Azure Government, przejrzyj obsługiwane lokalizacje oceny [docelowej](migrate-support-matrix.md#supported-geographies-azure-government) . Należy pamiętać, że zalecenia dotyczące rozmiaru maszyny wirtualnej w ramach ocen będą używać serii maszyn wirtualnych przeznaczonych dla regionów w chmurze dla instytucji rządowych. [Dowiedz się więcej](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) o typach maszyn wirtualnych.

## <a name="types-of-assessments"></a>Typy ocen

Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: Ocena serwera.

**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md), [maszyny wirtualne funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) i [serwery fizyczne](how-to-set-up-appliance-physical.md) pod kątem migracji na platformę Azure.
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) pod kątem migracji do usługi Azure VMware Solution (AVS).[Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

Oceny tworzone za pomocą oceny serwera to migawka danych w danym momencie. Ocena maszyn wirtualnych platformy Azure w ramach oceny serwera oferuje dwie opcje kryteriów ustalania rozmiarów:

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny zawierające rekomendacje na podstawie zebranych danych dotyczących wydajności | Zalecenie dotyczące rozmiaru maszyny wirtualnej bazuje na danych użycia procesora CPU i pamięci RAM.<br/><br/> Zalecenia dotyczące typu dysku opierają się na operacjach wejścia/wyjścia na sekundę (IOPS) i przepływności dysków lokalnych. Typy dysków to Azure HDD w warstwie Standardowa, Azure SSD w warstwie Standardowa i Azure Premium Disks.
**Zgodnie ze środowiskiem lokalnym** | Oceny, które nie używają danych wydajności do wykonywania zaleceń | Zalecenie dotyczące rozmiaru maszyny wirtualnej bazuje na lokalnym rozmiarze maszyny wirtualnej.<br/><br> Zalecany typ dysku jest oparty na wybranym typie magazynu dla oceny.

## <a name="how-do-i-run-an-assessment"></a>Jak mogę uruchomić ocenę?

Istnieje kilka sposobów na przeprowadzenie oceny.

- Ocenianie maszyn przy użyciu metadanych serwera zbieranych przez uproszczone urządzenie Azure Migrate. Urządzenie umożliwia odnalezienie maszyn lokalnych. Następnie wysyła metadane maszyn i dane wydajności do Azure Migrate.
- Ocenianie maszyn przy użyciu metadanych serwera zaimportowanych w formacie wartości rozdzielanych przecinkami (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Jak mogę oceniać z urządzeniem?

Jeśli wdrażasz urządzenie Azure Migrate w celu odnajdywania serwerów lokalnych, wykonaj następujące czynności:

1. Skonfiguruj platformę Azure i środowisko lokalne do pracy z oceną serwera.
1. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do oceny serwera.
1. Wdróż uproszczone urządzenie Azure Migrate. Urządzenie nieustannie odnajduje maszyny lokalne i wysyła metadane maszyn i dane wydajności do Azure Migrate. Wdróż urządzenie jako maszynę wirtualną lub maszynę fizyczną. Nie musisz instalować żadnych elementów na maszynach, które chcesz ocenić.

Po rozpoczęciu odnajdywania urządzenia przez urządzenie można zbierać maszyny do oceny w grupie i uruchamiać ocenę dla grupy z typem oceny **maszyny wirtualnej platformy Azure**.

Postępuj zgodnie z naszymi samouczkami dotyczącymi [rozwiązań VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)i [serwerów fizycznych](./tutorial-discover-physical.md) , aby wypróbować te kroki.

## <a name="how-do-i-assess-with-imported-data"></a>Jak mogę oceniać z zaimportowanymi danymi?

Jeśli oceniasz serwery przy użyciu pliku CSV, nie potrzebujesz urządzenia. Zamiast tego wykonaj następujące czynności:

1. Skonfiguruj platformę Azure do pracy z oceną serwera.
1. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do oceny serwera.
1. Pobierz szablon CSV i Dodaj do niego dane serwera.
1. Zaimportuj szablon do oceny serwera.
1. Odnajdź serwery dodane podczas importowania, Zbierz je do grupy i uruchom ocenę dla grupy z typem oceny na **maszynie wirtualnej platformy Azure**.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane są zbierane przez urządzenie?

Jeśli używasz urządzenia Azure Migrate do oceny, Dowiedz się więcej na temat metadanych i danych wydajności, które są zbierane dla oprogramowania [VMware](migrate-appliance.md#collected-data---vmware) i [funkcji Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak urządzenie oblicza dane wydajności?

Jeśli urządzenie jest używane do odnajdywania, zbiera dane o wydajności dla ustawień obliczeń z następującymi krokami:

1. Urządzenie zbiera punkt próbkowania w czasie rzeczywistym.

    - **Maszyny wirtualne VMware**: punkt próbki jest zbierany co 20 sekund.
    - **Maszyny wirtualne funkcji Hyper-V**: punkt próbki jest zbierany co 30 sekund.
    - **Serwery fizyczne**: punkt próbki jest zbierany co pięć minut.

1. Urządzenie łączy przykładowe punkty, aby utworzyć jeden punkt danych co 10 minut dla serwerów VMware i funkcji Hyper-V, a co 5 minut dla serwerów fizycznych. Aby utworzyć punkt danych, urządzenie wybiera wartości szczytowe ze wszystkich próbek. Następnie wysyła punkt danych do platformy Azure.
1. Ocena serwera przechowuje wszystkie 10-minutowe punkty danych w ciągu ostatniego miesiąca.
1. Podczas tworzenia oceny Ocena serwera identyfikuje odpowiedni punkt danych do użycia dla odpowiedniej zmiany. Identyfikator jest oparty na wartościach percentylu dla *historii wydajności* i *użycia percentylu*.

    - Jeśli na przykład historia wydajności wynosi tydzień, a użycie percentyla to używany 95. percentyl, Ocena serwera sortuje 10-minutowe punkty próbkowania w ostatnim tygodniu. Sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentyl dla odpowiedniej zmiany.
    - Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
    - Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, wybierz 99 percentyl dla użycia percentyla.

1. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla tych metryk, które zbiera urządzenie:

    - Wykorzystanie procesora
    - Użycie pamięci RAM
    - Liczba operacji we/wy na sekundę (odczyt i zapis)
    - Przepływność dysku (odczyt i zapis)
    - Przepływność sieci (do i z)

## <a name="how-are-azure-vm-assessments-calculated"></a>Jak są obliczane oceny maszyn wirtualnych platformy Azure?

Funkcja oceny serwera używa metadanych i danych wydajności maszyn lokalnych w celu obliczenia ocen. W przypadku wdrożenia urządzenia Azure Migrate oceny używa danych zbieranych przez urządzenie. Jednak w przypadku uruchomienia oceny zaimportowanej przy użyciu pliku CSV należy podać metadane dla obliczenia.

Obliczenia odbywają się w tych trzech etapach:

1. **Oblicz gotowość platformy Azure**: Oceń, czy maszyny są odpowiednie do migracji na platformę Azure.
1. **Obliczanie zaleceń dotyczących ustalania rozmiaru**: oszacowanie obliczeń, magazynu i rozmiaru sieci.
1. **Oblicz miesięczne koszty**: Oblicz szacowane miesięczne koszty obliczeń i magazynowania na potrzeby uruchamiania maszyn na platformie Azure po migracji.

Obliczenia są zgodne z poprzednią kolejnością. Serwer maszynowy przechodzi do późniejszego etapu tylko wtedy, gdy przekaże poprzednią. Jeśli na przykład serwer ulegnie awarii na etapie gotowości platformy Azure, zostanie oznaczony jako nieodpowiedni dla platformy Azure. Ustalanie rozmiarów i kosztów nie jest wykonywane dla tego serwera.

## <a name="whats-in-an-azure-vm-assessment"></a>Co to jest ocena maszyny wirtualnej platformy Azure?

Oto nowości w ocenie maszyny wirtualnej platformy Azure w ramach oceny serwera:

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja, do której chcesz przeprowadzić migrację. Ocena serwera obsługuje obecnie następujące docelowe regiony platformy Azure:<br/><br/> Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Indie Środkowe, środkowe stany USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Niemcy środkowe, Niemcy Południowo-Wschodnia, Japonia Południowo-Wschodnia, Japonia Zachodnia, Korea środkowa, Południowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Zachodnie Zjednoczone Królestwo Południowe Zjednoczone Królestwo Indie Południowe , Zachodnio-środkowe stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i zachodnie stany USA 2.
**Docelowy dysk magazynujący (w przypadku zmiany wielkości liter)** | Typ dysku do użycia na potrzeby magazynu na platformie Azure. <br/><br/> Określ docelowy dysk magazynujący jako zarządzany przez usługę Premium, zarządzany SSD w warstwie Standardowa lub HDD w warstwie Standardowa.
**Docelowy dysk magazynujący (ustalanie wielkości na podstawie wydajności)** | Określa typ docelowego dysku magazynującego, który ma być zarządzany przez funkcję automatycznej, w warstwie Premium — zarządzany HDD w warstwie Standardowa lub SSD w warstwie Standardowa.<br/><br/> **Automatyczne**: zalecenie dysku opiera się na danych wydajności dysków, co oznacza liczbę IOPS i przepływność.<br/><br/>**Premium lub standard**: Ocena zaleca użycie jednostki SKU dysku w wybranym typie magazynu.<br/><br/> Jeśli chcesz uzyskać umowę na poziomie usług (SLA) na jednym wystąpieniu maszyny wirtualnej o wartości 99,9%, rozważ użycie dysków zarządzanych w warstwie Premium. Pozwala to zagwarantować, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium.<br/><br/> Azure Migrate obsługuje tylko dyski zarządzane na potrzeby oceny migracji.
**Azure Reserved VM Instances** | Określa [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) , aby oszacować koszt w ocenie wziąć pod uwagę.<br/><br/> Po wybraniu opcji "zarezerwowane wystąpienia" rabat (%) i nie mają zastosowania właściwości "czas pracy maszyny wirtualnej".<br/><br/> Azure Migrate obecnie obsługuje Azure Reserved VM Instances tylko w przypadku ofert z opcją płatność zgodnie z rzeczywistym użyciem.
**Kryterium określania rozmiaru** | Używane do rightsize maszyny wirtualnej platformy Azure.<br/><br/> Używaj wielkości liter lub wielkości na podstawie wydajności.
**Historia wydajności** | Używane z rozmiarem opartym na wydajności. Historia wydajności określa czas trwania używany podczas oceniania danych wydajności.
**Użycie percentyla** | Używane z rozmiarem opartym na wydajności. Użycie percentylu określa wartość percentylości próbki wydajności używanej dla odpowiedniej zmiany.
**Serie maszyn wirtualnych** | Seria maszyn wirtualnych platformy Azure, którą chcesz uwzględnić dla odpowiedniej zmiany. Na przykład jeśli nie masz środowiska produkcyjnego wymagającego maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię z listy serii.
**Współczynnik komfortu** | Bufor używany podczas oceny. Jest on stosowany do danych procesora CPU, pamięci RAM, dysku i sieci dla maszyn wirtualnych. Konta IT dotyczą problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrasta w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna z wykorzystaniem 20% zwykle skutkuje dwurdzeniową maszyną wirtualną. Ze współczynnikiem komfortu wynoszącym 2,0, w zamian powstaje wielordzeniowa maszyna wirtualna.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , w której zarejestrowano Cię. Ocena serwera szacuje koszt dla tej oferty.
**Waluta** | Waluta rozliczeń dla Twojego konta.
**Rabat (%)** | Wszystkie zniżki związane z subskrypcją, które otrzymujesz w ramach oferty platformy Azure. Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Czas trwania w dniach miesięcznie i godzin dziennie maszyn wirtualnych platformy Azure, które nie będą działać w sposób ciągły. Oszacowania kosztów bazują na tym czasie.<br/><br/> Wartości domyślne to 31 dni miesięcznie i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz program Software Assurance i kwalifikujesz się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Jeśli ustawienie ma wartość domyślną "tak", ceny platformy Azure dla systemów operacyjnych innych niż Windows są brane pod uwagę w przypadku maszyn wirtualnych z systemem Windows.
**Subskrypcja EA** | Określa, że subskrypcja Umowa Enterprise (EA) jest używana do szacowania kosztów. Uwzględnia rabat dotyczący subskrypcji. <br/><br/> Pozostaw ustawienia dla wystąpień zarezerwowanych, Rabat (%) i właściwości czasu działania maszyny wirtualnej z ustawieniami domyślnymi.


[Zapoznaj się z najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen przy użyciu oceny serwera.

## <a name="calculate-readiness"></a>Oblicz gotowość

Nie wszystkie maszyny są odpowiednie do uruchamiania na platformie Azure. Ocena maszyn wirtualnych platformy Azure ocenia wszystkie maszyny lokalne i przypisuje im kategorię gotowości.

- **Gotowe do użycia na platformie Azure**: maszynę można migrować na platformę Azure bez wprowadzania żadnych zmian. Zostanie ona uruchomiona na platformie Azure z pełną pomocą techniczną platformy Azure.
- **Warunkowo gotowy na platformę Azure**: maszyna może zacząć pracę na platformie Azure, ale może nie mieć pełnej pomocy technicznej platformy Azure. Na przykład platforma Azure nie obsługuje komputera, na którym działa Starsza wersja systemu Windows Server. Przed przeprowadzeniem migracji tych maszyn na platformę Azure należy zachować ostrożność. Aby rozwiązać wszelkie problemy z gotowością, postępuj zgodnie z instrukcjami oceny sugestii.
- **Nie gotowy na platformę Azure**: maszyna nie zostanie uruchomiona na platformie Azure. Na przykład jeśli dysk maszyny lokalnej przechowuje więcej niż 64 TB, platforma Azure nie może hostować maszyny. Postępuj zgodnie ze wskazówkami dotyczącymi korygowania, aby rozwiązać problem przed migracją.
- **Nieznane gotowość**: Azure Migrate nie może ustalić gotowości maszyny z powodu niewystarczających metadanych.

Aby obliczyć gotowość, Ocena serwera sprawdza właściwości komputera i ustawienia systemu operacyjnego podsumowane w poniższych tabelach.

### <a name="machine-properties"></a>Właściwości maszyny

W przypadku oceny maszyny wirtualnej na platformie Azure Ocena serwera sprawdza następujące właściwości lokalnej maszyny wirtualnej w celu ustalenia, czy można uruchamiać je na maszynach wirtualnych platformy Azure.

Właściwość | Szczegóły | Stan gotowości platformy Azure
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchowym systemu BIOS, a nie z interfejsem UEFI. | Warunkowo gotowe, jeśli typ rozruchu to UEFI
**Rdzenie** | Każda maszyna nie może mieć więcej niż 128 rdzeni, co jest maksymalną liczbą obsługiwaną przez maszynę wirtualną platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate traktuje wykorzystane rdzenie do porównania. Jeśli ustawienia oceny określają współczynnik komfortu, Liczba użytych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, Azure Migrate używa przyznanych rdzeni, aby zastosować współczynnik komfortu. | Gotowe, jeśli liczba rdzeni przekracza limit
**NIEGO** | Każda maszyna nie może mieć więcej niż 3 892 GB pamięci RAM, czyli maksymalnego rozmiaru obsługiwanego przez maszynę wirtualną z serii M Standard_M128m &nbsp; <sup>2</sup> . [Dowiedz się więcej](../virtual-machines/sizes.md).<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate uwzględnia wykorzystanie pamięci RAM do porównania. W przypadku określenia współczynnika komfortu wykorzystanie pamięci RAM jest mnożone przez współczynnik komfortu.<br/><br/> Jeśli nie ma żadnej historii, przydzieloną pamięć RAM zostanie użyta do zastosowania współczynnika komfortu.<br/><br/> | Gotowe, jeśli ilość pamięci RAM mieści się w limicie
**Dysk magazynu** | Przydzielony rozmiar dysku nie może być większy niż 32 TB. Mimo że platforma Azure obsługuje dyski 64 TB z dyskami SSD w warstwie Ultra platformy Azure, Azure Migrate: Ocena serwera obecnie sprawdza, czy rozmiar dysku wynosi 32 TB, ponieważ nie obsługuje jeszcze SSD w warstwie Ultra. <br/><br/> Liczba dysków dołączonych do maszyny, łącznie z dyskiem systemu operacyjnego, nie może być większa niż 65. | Gotowe, jeśli rozmiar i liczba dysków przekraczają limity
**Sieć** | Komputer nie może mieć więcej niż 32 interfejsów sieciowych (nic). | Gotowe, jeśli liczba kart sieciowych mieści się w limicie

### <a name="guest-operating-system"></a>System operacyjny gościa

W przypadku oceny maszyny wirtualnej na platformie Azure wraz z przeglądaniem właściwości maszyny wirtualnej Ocena serwera jest sprawdzana w systemie operacyjnym gościa maszyny, aby określić, czy można uruchamiać ją na platformie Azure.

> [!NOTE]
> Aby obsłużyć analizę gościa maszyn wirtualnych VMware, Ocena serwera używa systemu operacyjnego określonego dla maszyny wirtualnej w vCenter Server. Jednak vCenter Server nie zapewnia wersji jądra dla systemów operacyjnych maszyn wirtualnych z systemem Linux. Aby odnaleźć wersję, należy skonfigurować [odnajdywanie aplikacji](./how-to-discover-applications.md). Następnie urządzenie odnajduje informacje o wersji przy użyciu poświadczeń gościa określonych podczas konfigurowania funkcji odnajdywania aplikacji.


W celu identyfikacji gotowości platformy Azure opartej na systemie operacyjnym Ocena serwera korzysta z następującej logiki:

**System operacyjny** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
Windows Server 2016 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2012 R2 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2012 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2008 R2 ze wszystkimi usługami SPs | Platforma Azure oferuje pełną pomoc techniczną.| Gotowe na platformę Azure.
Windows Server 2008 (32-bitowe i 64-bit) | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2003 i Windows Server 2003 R2 | Te systemy operacyjne przekazały swoje daty końca obsługi i potrzebują [niestandardowej umowy pomocy technicznej (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) w celu uzyskania pomocy technicznej na platformie Azure. | Warunkowo gotowy na platformę Azure. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 i MS-DOS | Te systemy operacyjne przekazały swoje daty końca wsparcia. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Windows 7, Windows 8 i Windows 10 | Platforma Azure zapewnia pomoc techniczną tylko w przypadku [subskrypcji programu Visual Studio.](../virtual-machines/windows/client-images.md) | Warunkowo gotowy na platformę Azure.
Windows 10 Pro | Platforma Azure zapewnia pomoc techniczną z [wielodostępnymi prawami hostingu.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Warunkowo gotowy na platformę Azure.
Windows Vista i Windows XP Professional | Te systemy operacyjne przekazały swoje daty końca wsparcia. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Linux | Zapoznaj się z [systemami operacyjnymi Linux](../virtual-machines/linux/endorsed-distros.md) , które są zatwierdzone przez platformę Azure. Inne systemy operacyjne Linux mogą zacząć działać na platformie Azure. Jednak przed przeprowadzeniem migracji na platformę Azure zalecamy uaktualnienie systemu operacyjnego do wersji zatwierdzonej. | Gotowe na platformę Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowe, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne, takie jak Oracle Solaris, Apple macOS i FreeBSD | Na platformie Azure nie są zatwierdzona te systemy operacyjne. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy zainstalowanie obsługiwanego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.  
System operacyjny określony jako **inny** w vCenter Server | W takim przypadku Azure Migrate nie może zidentyfikować systemu operacyjnego. | Nieznane gotowość. Upewnij się, że platforma Azure obsługuje system operacyjny działający na maszynie wirtualnej.
32-bitowe systemy operacyjne | Maszyna może zaczynać się na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowy na platformę Azure. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie do 64-bitowego systemu operacyjnego.

## <a name="calculating-sizing"></a>Obliczanie rozmiaru

Po oznaczeniu maszyny jako gotowej na platformie Azure Ocena serwera spowoduje zaleceń dotyczących rozmiarów w ocenie maszyny wirtualnej platformy Azure. Te zalecenia identyfikują maszynę wirtualną platformy Azure i jednostkę SKU dysku. Obliczenia rozmiarów zależą od tego, czy używasz jako lokalizacji lokalnej, czy ustalania rozmiarów na podstawie wydajności.

### <a name="calculate-sizing-as-is-on-premises"></a>Obliczanie rozmiaru (w trybie lokalnym)

 Jeśli używasz jako lokalizacji lokalnej, Ocena serwera nie uwzględnia historii wydajności maszyn wirtualnych i dysków w ramach oceny maszyny wirtualnej platformy Azure.

- **Ustalanie rozmiaru obliczeń**: Ocena serwera PRZYDZIELA jednostkę SKU maszyny wirtualnej platformy Azure na podstawie rozmiaru przydzielonego lokalnie.
- **Rozmiar magazynu i dysku**: Ocena serwera sprawdza typ magazynu określony we właściwościach oceny i zaleca odpowiedni typ dysku. Możliwe typy magazynów to HDD w warstwie Standardowa, SSD w warstwie Standardowa i Premium. Domyślny typ magazynu to Premium.
- **Ustalanie wielkości sieci**: Ocena serwera traktuje kartę sieciową na komputerze lokalnym.

### <a name="calculate-sizing-performance-based"></a>Obliczanie rozmiaru (opartego na wydajności)

W przypadku korzystania z ustalania rozmiarów na podstawie wydajności w ocenie maszyny wirtualnej platformy Azure Ocena serwera sprawia, że zaleceń dotyczących rozmiarów jest następująca:

- Ocena serwera traktuje historię wydajności maszyny w celu zidentyfikowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- W przypadku zaimportowania serwerów przy użyciu pliku CSV podane wartości są używane. Ta metoda jest szczególnie przydatna, jeśli masz nadmiarową przydzieloną maszynę lokalną, wykorzystanie jest niskie i chcesz rightsize maszynę wirtualną platformy Azure w celu oszczędności kosztów.
- Jeśli nie chcesz używać danych dotyczących wydajności, zresetuj kryteria ustalania wielkości tak, aby były lokalne, zgodnie z opisem w poprzedniej sekcji.

#### <a name="calculate-storage-sizing"></a>Oblicz rozmiar magazynu

W przypadku określania wielkości magazynu w ramach oceny maszyny wirtualnej platformy Azure Azure Migrate próbuje zmapować każdy dysk dołączony do maszyny do dysku platformy Azure. Ustalanie wielkości przebiega w następujący sposób:

1. Ocena serwera umożliwia dodanie operacji odczytu i zapisu na dysku, aby uzyskać łączną liczbę operacji we/wy na sekundę. Podobnie dodaje wartości przepływności odczytu i zapisu w celu uzyskania całkowitej przepływności każdego dysku. W przypadku ocen opartych na imporcie istnieje możliwość zapewnienia łącznej liczby operacji we/wy, całkowitej przepływności i łącznej liczby operacji. dysków w zaimportowanym pliku bez określania poszczególnych ustawień dysków. W takim przypadku pozostało, że rozmiar poszczególnych dysków zostanie pominięty, a podane dane są używane bezpośrednio do obliczania wielkości i wybrania odpowiedniej jednostki SKU maszyny wirtualnej.

1. Jeśli typ magazynu został określony jako automatyczny, wybrany typ jest oparty na wartościach operacji wejścia/wyjścia na sekundę. Ocena serwera określa, czy dysk ma zostać zmapowany na dysk HDD w warstwie Standardowa, SSD w warstwie Standardowa lub Premium na platformie Azure. Jeśli typ magazynu jest ustawiony na jeden z tych typów dysków, Ocena serwera próbuje znaleźć jednostkę SKU dysku w wybranym typie magazynu.
1. Dyski są wybierane w następujący sposób:
    - Jeśli Ocena serwera nie może znaleźć dysku z wymaganą liczbą operacji we/wy i przepływności, oznacza to, że maszyna jest nieodpowiednia dla platformy Azure.
    - Jeśli Ocena serwera odnajdzie zestaw odpowiednich dysków, wybiera dyski, które obsługują lokalizację określoną w ustawieniach oceny.
    - Jeśli istnieje wiele kwalifikujących się dysków, Ocena serwera wybiera dysk o najniższym koszcie.
    - Jeśli dane dotyczące wydajności dla dowolnego dysku są niedostępne, rozmiar dysku konfiguracji jest używany do znalezienia dysku SSD w warstwie Standardowa na platformie Azure.

#### <a name="calculate-network-sizing"></a>Oblicz rozmiar sieci

W przypadku oceny maszyny wirtualnej na platformie Azure Ocena serwera próbuje znaleźć maszynę wirtualną platformy Azure, która obsługuje liczbę i wymaganą wydajność kart sieciowych podłączonych do maszyny lokalnej.

- W celu uzyskania efektywnej wydajności sieci lokalnej maszyny wirtualnej Ocena serwera agreguje szybkość transmisji danych z komputera (w sieci) między wszystkimi kartami sieciowymi. Następnie stosuje współczynnik komfortu. Używa wynikowej wartości, aby znaleźć maszynę wirtualną platformy Azure, która może obsługiwać wymaganą wydajność sieci.
- Wraz z wydajnością sieci Ocena serwera uwzględnia również, czy maszyna wirtualna platformy Azure może obsługiwać wymaganą liczbę kart sieciowych.
- Jeśli dane dotyczące wydajności sieci są niedostępne, Ocena serwera uwzględnia tylko liczbę kart sieciowych na potrzeby ustalania rozmiarów maszyn wirtualnych.

#### <a name="calculate-compute-sizing"></a>Oblicz rozmiar obliczeń

Po obliczeniu wymagań dotyczących magazynu i sieci Ocena serwera uwzględnia wymagania dotyczące procesora CPU i pamięci RAM, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.

- Azure Migrate sprawdza efektywne wykorzystanie rdzeni i pamięci RAM w celu znalezienia odpowiedniego rozmiaru maszyny wirtualnej platformy Azure.
- Jeśli odpowiedni rozmiar nie zostanie znaleziony, oznacza to, że komputer jest oznaczony jako nieodpowiedni dla platformy Azure.
- Jeśli zostanie znaleziony odpowiedni rozmiar, Azure Migrate stosuje obliczenia magazynu i sieci. Następnie stosowane są ustawienia warstwy i cennika dla ostatecznego zalecenia dotyczącego rozmiaru maszyny wirtualnej.
- Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.

## <a name="confidence-ratings-performance-based"></a>Klasyfikacje zaufania (oparte na wydajności)

Każda Ocena na podstawie wydajności maszyny wirtualnej platformy Azure w Azure Migrate jest skojarzona z oceną zaufania. Klasyfikacja z jednego (najniższego) do pięciu (najwyższa) gwiazdek. Ocena zaufania pozwala oszacować niezawodność Azure Migrate zapewniania przez zaleceń dotyczących rozmiaru.

- Ocena zaufania jest przypisana do oceny. Klasyfikacja jest oparta na dostępności punktów danych, które są konieczne do obliczenia oceny.
- W przypadku ustalania wielkości na podstawie wydajności, wymagania dotyczące oceny serwera:
    - Dane użycia procesora CPU i pamięci RAM.
    - Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do maszyny wirtualnej.
    - We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do maszyny wirtualnej.

Jeśli którykolwiek z tych numerów wykorzystania nie jest dostępny, zalecenia dotyczące rozmiaru mogą być zawodne.

> [!NOTE]
> Klasyfikacje zaufania nie są przypisywane do serwerów ocenionych przy użyciu zaimportowanego pliku CSV. Klasyfikacje nie mają również zastosowania do oceny lokalnej.

### <a name="ratings"></a>Klasyfikacje

W tej tabeli przedstawiono oceny stopnia zaufania, które są zależne od wartości procentowej dostępnych punktów danych:

   **Dostępność punktów danych** | **Ocena zaufania**
   --- | ---
   0-20% | 1 gwiazdka
   21-40% | 2 gwiazdki
   41-60% | 3 gwiazdki
   61-80% | 4 gwiazdki
   81-100% | 5 gwiazdek

### <a name="low-confidence-ratings"></a>Oceny o niskiej pewności

Poniżej przedstawiono kilka powodów, dla których ocena może uzyskać klasyfikację o niskiej pewności:

- Twoje środowisko nie było profilem przez czas, w którym tworzysz ocenę. Jeśli na przykład zostanie utworzona Ocena z czasem trwania wydajności ustawionym na jeden dzień, należy poczekać co najmniej dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Ocena nie jest w stanie zebrać danych wydajności dla niektórych lub wszystkich maszyn wirtualnych w okresie oceny. W celu uzyskania oceny o wysokiej pewności upewnij się, że: 
    - Maszyny wirtualne są zasilane na czas trwania oceny
    - Połączenia wychodzące na portach 443 są dozwolone
    - Dla pamięci dynamicznej maszyn wirtualnych funkcji Hyper-V jest włączona 
    
    Użyj opcji „Oblicz ponownie”, aby uwzględnić najnowsze zmiany w ocenie ufności.

- Niektóre maszyny wirtualne zostały utworzone w czasie, w którym obliczono ocenę. Załóżmy na przykład, że utworzono ocenę historii wydajności w ostatnim miesiącu, ale niektóre maszyny wirtualne zostały utworzone tylko przez tydzień temu. W tym przypadku dane wydajności dla nowych maszyn wirtualnych nie będą dostępne przez cały czas trwania i ocena ufności będzie niska.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. W przeciwnym razie rozmiary oparte na wydajności mogą być zawodne. W takim przypadku zaleca się przełączenie oceny do rozmiarów lokalnych.

## <a name="calculate-monthly-costs"></a>Oblicz miesięczne koszty

Po zakończeniu ustalania rozmiarów zaleceń dla maszyny wirtualnej platformy Azure w Azure Migrate oblicza koszty obliczeń i magazynowania po migracji.

- **Koszt obliczeń**: Azure Migrate używa zalecanego rozmiaru maszyny wirtualnej platformy Azure i interfejsu API rozliczeń platformy Azure, aby obliczyć miesięczny koszt maszyny wirtualnej.

    Obliczenia uwzględniają:
    - System operacyjny
    - Software Assurance
    - Wystąpienia zarezerwowane
    - Czas pracy maszyny wirtualnej
    - Lokalizacja
    - Ustawienia waluty

    Funkcja oceny serwera agreguje koszt wszystkich maszyn w celu obliczenia łącznego miesięcznego kosztu obliczeniowego.

- **Koszt magazynu**: miesięczny koszt magazynu dla maszyny jest obliczany przez zsumowanie miesięcznego kosztu wszystkich dysków dołączonych do maszyny.

    Ocena serwera oblicza łączne miesięczne koszty magazynowania, sumując koszty magazynowania wszystkich maszyn. Obecnie obliczenia nie uwzględniają ofert określonych w ustawieniach oceny.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.

## <a name="next-steps"></a>Następne kroki

[Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen. 

- Dowiedz się więcej o uruchamianiu ocen dla [maszyn wirtualnych VMware](./tutorial-discover-vmware.md), [maszyn wirtualnych funkcji Hyper-V](./tutorial-discover-hyper-v.md)i [serwerów fizycznych](./tutorial-discover-physical.md).
- Dowiedz się więcej na temat oceniania serwerów [zaimportowanych przy użyciu pliku CSV](./tutorial-discover-import.md).
- Dowiedz się więcej na temat konfigurowania [wizualizacji zależności](concepts-dependency-visualization.md).