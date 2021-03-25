---
title: Oceny maszyn wirtualnych platformy Azure w Azure Migrate
description: Dowiedz się więcej na temat ocen w Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 16c3b59bcfa14cc02f13dadd726e0380d934598b
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023813"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Przegląd oceny (migrowanie do maszyn wirtualnych platformy Azure)

Ten artykuł zawiera omówienie ocen w Azure Migrate narzędziu [odnajdywania i oceny](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Narzędzie może ocenić serwery lokalne w środowisku wirtualnym VMware i funkcji Hyper-V oraz serwerach fizycznych do migracji na platformę Azure.

## <a name="whats-an-assessment"></a>Co to jest Ocena?

Ocena narzędzia wykrywania i oceny mierzy gotowość i ocenia efekt migracji serwerów lokalnych na platformę Azure.

> [!NOTE]
> W Azure Government, przejrzyj obsługiwane lokalizacje oceny [docelowej](migrate-support-matrix.md#supported-geographies-azure-government) . Należy pamiętać, że zalecenia dotyczące rozmiaru maszyny wirtualnej w ramach ocen będą używać serii maszyn wirtualnych przeznaczonych dla regionów w chmurze dla instytucji rządowych. [Dowiedz się więcej](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) o typach maszyn wirtualnych.

## <a name="types-of-assessments"></a>Typy ocen

Istnieją trzy typy ocen, które można utworzyć przy użyciu Azure Migrate: odnajdywania i oceny.

***Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. Można ocenić serwery lokalne w środowisku [VMware](how-to-set-up-appliance-vmware.md) i [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) oraz [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji do maszyn wirtualnych platformy Azure przy użyciu tego typu oceny.
**Azure SQL** | Ocenianie migracji lokalnych serwerów SQL ze środowiska VMware do Azure SQL Database lub wystąpienia zarządzanego Azure SQL.
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Za pomocą tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

Oceny tworzone za pomocą Azure Migrate to migawka danych w danym momencie. Ocena maszyny wirtualnej platformy Azure oferuje dwie opcje kryteriów ustalania rozmiarów:

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny zawierające rekomendacje na podstawie zebranych danych dotyczących wydajności | Zalecenie dotyczące rozmiaru maszyny wirtualnej bazuje na danych użycia procesora CPU i pamięci RAM.<br/><br/> Zalecenia dotyczące typu dysku opierają się na operacjach wejścia/wyjścia na sekundę (IOPS) i przepływności dysków lokalnych. Typy dysków to Azure HDD w warstwie Standardowa, Azure SSD w warstwie Standardowa i Azure Premium Disks.
**Zgodnie ze środowiskiem lokalnym** | Oceny, które nie używają danych wydajności do wykonywania zaleceń | Zalecenie dotyczące rozmiaru maszyny wirtualnej bazuje na rozmiarze serwera lokalnego.<br/><br> Zalecany typ dysku jest oparty na wybranym typie magazynu dla oceny.

## <a name="how-do-i-run-an-assessment"></a>Jak mogę uruchomić ocenę?

Istnieje kilka sposobów na przeprowadzenie oceny.

- Oceniaj serwery za pomocą metadanych serwera zbieranych przez uproszczone urządzenie Azure Migrate. Urządzenie odnajduje serwery lokalne. Następnie wysyła metadane serwera i dane wydajności do Azure Migrate.
- Ocenianie serwerów za pomocą metadanych serwera zaimportowanych w formacie wartości rozdzielanych przecinkami (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Jak mogę oceniać z urządzeniem?

Jeśli wdrażasz urządzenie Azure Migrate w celu odnajdywania serwerów lokalnych, wykonaj następujące czynności:

1. Skonfiguruj platformę Azure i środowisko lokalne do pracy z Azure Migrate.
1. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do odnajdywania i oceny.
1. Wdróż uproszczone urządzenie Azure Migrate. Urządzenie stale odnajduje serwery lokalne i wysyła metadane serwera i dane wydajności do Azure Migrate. Wdróż urządzenie jako maszynę wirtualną lub serwer fizyczny. Nie musisz instalować żadnych elementów na serwerach, które chcesz ocenić.

Po rozpoczęciu odnajdywania przez urządzenie można zbierać serwery, które mają być oceniane do grupy, i uruchamiać ocenę dla grupy z typem oceny na **maszynie wirtualnej platformy Azure**.

Postępuj zgodnie z naszymi samouczkami dotyczącymi [rozwiązań VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)i [serwerów fizycznych](./tutorial-discover-physical.md) , aby wypróbować te kroki.

## <a name="how-do-i-assess-with-imported-data"></a>Jak mogę oceniać z zaimportowanymi danymi?

Jeśli oceniasz serwery przy użyciu pliku CSV, nie potrzebujesz urządzenia. Zamiast tego wykonaj następujące czynności:

1. Skonfiguruj platformę Azure do pracy z Azure Migrate
1. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do odnajdywania i oceny.
1. Pobierz szablon CSV i Dodaj do niego dane serwera.
1. Zaimportuj szablon do Azure Migrate
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
1. W ramach oceny są przechowywane wszystkie 10-minutowe punkty danych w ciągu ostatniego miesiąca.
1. Podczas tworzenia oceny Ocena wskazuje odpowiedni punkt danych do użycia dla odpowiedniej zmiany. Identyfikator jest oparty na wartościach percentylu dla *historii wydajności* i *użycia percentylu*.

    - Jeśli na przykład historia wydajności wynosi tydzień, a użycie percentyla to używany 95. percentyl, Ocena sortuje 10-minutowe punkty próbkowania w ostatnim tygodniu. Sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentyl dla odpowiedniej zmiany.
    - Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
    - Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, wybierz 99 percentyl dla użycia percentyla.

1. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla tych metryk, które zbiera urządzenie:

    - Wykorzystanie procesora
    - Użycie pamięci RAM
    - Liczba operacji we/wy na sekundę (odczyt i zapis)
    - Przepływność dysku (odczyt i zapis)
    - Przepływność sieci (do i z)

## <a name="how-are-azure-vm-assessments-calculated"></a>Jak są obliczane oceny maszyn wirtualnych platformy Azure?

W ramach oceny są wykorzystywane metadane i dane wydajności serwerów lokalnych w celu obliczenia ocen. W przypadku wdrożenia urządzenia Azure Migrate oceny używa danych zbieranych przez urządzenie. Jednak w przypadku uruchomienia oceny zaimportowanej przy użyciu pliku CSV należy podać metadane dla obliczenia.

Obliczenia odbywają się w tych trzech etapach:

1. **Oblicz gotowość platformy Azure**: Oceń, czy serwery są odpowiednie do migracji na platformę Azure.
1. **Obliczanie zaleceń dotyczących ustalania rozmiaru**: oszacowanie obliczeń, magazynu i rozmiaru sieci.
1. **Oblicz miesięczne koszty**: Oblicz szacowane miesięczne koszty obliczeń i magazynowania na potrzeby uruchamiania serwerów na platformie Azure po migracji.

Obliczenia są zgodne z poprzednią kolejnością. Serwer serwera przechodzi do późniejszego etapu tylko wtedy, gdy przekaże poprzednią. Jeśli na przykład serwer ulegnie awarii na etapie gotowości platformy Azure, zostanie oznaczony jako nieodpowiedni dla platformy Azure. Ustalanie rozmiarów i kosztów nie jest wykonywane dla tego serwera.

## <a name="whats-in-an-azure-vm-assessment"></a>Co to jest ocena maszyny wirtualnej platformy Azure?

Oto nowości uwzględnionych w ocenie maszyny wirtualnej platformy Azure:

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja, do której chcesz przeprowadzić migrację. Ocena obecnie obsługuje następujące docelowe regiony platformy Azure:<br/><br/> Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Indie Środkowe, środkowe stany USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Niemcy środkowe, Niemcy Południowo-Wschodnia, Japonia Południowo-Wschodnia, Japonia Zachodnia, Korea środkowa, Południowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Zachodnie Zjednoczone Królestwo Południowe Zjednoczone Królestwo Indie Południowe , Zachodnio-środkowe stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i zachodnie stany USA 2.
**Docelowy dysk magazynujący (w przypadku zmiany wielkości liter)** | Typ dysku do użycia na potrzeby magazynu na platformie Azure. <br/><br/> Określ docelowy dysk magazynujący jako zarządzany przez usługę Premium, zarządzany SSD w warstwie Standardowa lub HDD w warstwie Standardowa.
**Docelowy dysk magazynujący (ustalanie wielkości na podstawie wydajności)** | Określa typ docelowego dysku magazynującego, który ma być zarządzany przez funkcję automatycznej, w warstwie Premium — zarządzany HDD w warstwie Standardowa lub SSD w warstwie Standardowa.<br/><br/> **Automatyczne**: zalecenie dysku opiera się na danych wydajności dysków, co oznacza liczbę IOPS i przepływność.<br/><br/>**Premium lub standard**: Ocena zaleca użycie jednostki SKU dysku w wybranym typie magazynu.<br/><br/> Jeśli chcesz uzyskać umowę na poziomie usług (SLA) na jednym wystąpieniu maszyny wirtualnej o wartości 99,9%, rozważ użycie dysków zarządzanych w warstwie Premium. Pozwala to zagwarantować, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium.<br/><br/> Azure Migrate obsługuje tylko dyski zarządzane na potrzeby oceny migracji.
**Azure Reserved VM Instances** | Określa [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) , aby oszacować koszt w ocenie wziąć pod uwagę.<br/><br/> Po wybraniu opcji "zarezerwowane wystąpienia" rabat (%) i nie mają zastosowania właściwości "czas pracy maszyny wirtualnej".<br/><br/> Azure Migrate obecnie obsługuje Azure Reserved VM Instances tylko w przypadku ofert z opcją płatność zgodnie z rzeczywistym użyciem.
**Kryterium określania rozmiaru** | Używane do rightsize maszyny wirtualnej platformy Azure.<br/><br/> Używaj wielkości liter lub wielkości na podstawie wydajności.
**Historia wydajności** | Używane z rozmiarem opartym na wydajności. Historia wydajności określa czas trwania używany podczas oceniania danych wydajności.
**Użycie percentyla** | Używane z rozmiarem opartym na wydajności. Użycie percentylu określa wartość percentylości próbki wydajności używanej dla odpowiedniej zmiany.
**Serie maszyn wirtualnych** | Seria maszyn wirtualnych platformy Azure, którą chcesz uwzględnić dla odpowiedniej zmiany. Na przykład jeśli nie masz środowiska produkcyjnego wymagającego maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię z listy serii.
**Współczynnik komfortu** | Bufor używany podczas oceny. Jest on stosowany do danych procesora CPU, pamięci RAM, dysku i sieci dla maszyn wirtualnych. Konta IT dotyczą problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrasta w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna z wykorzystaniem 20% zwykle skutkuje dwurdzeniową maszyną wirtualną. Ze współczynnikiem komfortu wynoszącym 2,0, w zamian powstaje wielordzeniowa maszyna wirtualna.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , w której zarejestrowano Cię. Ocena szacuje koszt tej oferty.
**Waluta** | Waluta rozliczeń dla Twojego konta.
**Rabat (%)** | Wszystkie zniżki związane z subskrypcją, które otrzymujesz w ramach oferty platformy Azure. Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Czas trwania w dniach miesięcznie i godzin dziennie maszyn wirtualnych platformy Azure, które nie będą działać w sposób ciągły. Oszacowania kosztów bazują na tym czasie.<br/><br/> Wartości domyślne to 31 dni miesięcznie i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz program Software Assurance i kwalifikujesz się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Jeśli ustawienie ma wartość domyślną "tak", ceny platformy Azure dla systemów operacyjnych innych niż Windows są brane pod uwagę w przypadku maszyn wirtualnych z systemem Windows.
**Subskrypcja EA** | Określa, że subskrypcja Enterprise Agreement (EA) jest używana do szacowania kosztów. Uwzględnia rabat dotyczący subskrypcji. <br/><br/> Pozostaw ustawienia dla wystąpień zarezerwowanych, Rabat (%) i właściwości czasu działania maszyny wirtualnej z ustawieniami domyślnymi.


[Zapoznaj się z najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen przy użyciu Azure Migrate.

## <a name="calculate-readiness"></a>Oblicz gotowość

Nie wszystkie serwery są odpowiednie do uruchamiania na platformie Azure. Ocena maszyn wirtualnych platformy Azure ocenia wszystkie serwery lokalne i przypisuje im kategorię gotowości.

- **Gotowe do użycia na platformie Azure**: serwer można migrować na platformę Azure bez wprowadzania żadnych zmian. Zostanie ona uruchomiona na platformie Azure z pełną pomocą techniczną platformy Azure.
- **Warunkowo gotowy na platformę Azure**: serwer może zacząć korzystać z platformy Azure, ale może nie mieć pełnej pomocy technicznej platformy Azure. Na przykład platforma Azure nie obsługuje serwera, na którym działa Starsza wersja systemu Windows Server. Przed przeprowadzeniem migracji tych serwerów do platformy Azure należy zachować ostrożność. Aby rozwiązać wszelkie problemy z gotowością, postępuj zgodnie z instrukcjami oceny sugestii.
- **Nie gotowy na platformę Azure**: serwer nie zostanie uruchomiony na platformie Azure. Na przykład jeśli dysk serwera lokalnego przechowuje więcej niż 64 TB, platforma Azure nie może hostować serwera. Postępuj zgodnie ze wskazówkami dotyczącymi korygowania, aby rozwiązać problem przed migracją.
- **Nieznane gotowość**: Azure Migrate nie może ustalić gotowości serwera z powodu niewystarczających metadanych.

Aby obliczyć gotowość, Ocena przegląda właściwości serwera i ustawienia systemu operacyjnego podsumowane w poniższych tabelach.

### <a name="server-properties"></a>Właściwości serwera

W przypadku oceny maszyny wirtualnej platformy Azure Ocena ocenia następujące właściwości lokalnej maszyny wirtualnej, aby określić, czy można uruchamiać ją na maszynach wirtualnych platformy Azure.

Właściwość | Szczegóły | Stan gotowości platformy Azure
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchowym systemu BIOS, a nie z interfejsem UEFI. | Warunkowo gotowe, jeśli typ rozruchu to UEFI
**Rdzenie** | Każdy serwer nie może mieć więcej niż 128 rdzeni, co jest maksymalną liczbą obsługiwaną przez maszynę wirtualną platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate traktuje wykorzystane rdzenie do porównania. Jeśli ustawienia oceny określają współczynnik komfortu, Liczba użytych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, Azure Migrate używa przyznanych rdzeni, aby zastosować współczynnik komfortu. | Gotowe, jeśli liczba rdzeni przekracza limit
**NIEGO** | Każdy serwer nie może mieć więcej niż 3 892 GB pamięci RAM, czyli maksymalnego rozmiaru obsługiwanego przez maszynę wirtualną z serii M Standard_M128m &nbsp; <sup>2</sup> . [Dowiedz się więcej](../virtual-machines/sizes.md).<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate uwzględnia wykorzystanie pamięci RAM do porównania. W przypadku określenia współczynnika komfortu wykorzystanie pamięci RAM jest mnożone przez współczynnik komfortu.<br/><br/> Jeśli nie ma żadnej historii, przydzieloną pamięć RAM zostanie użyta do zastosowania współczynnika komfortu.<br/><br/> | Gotowe, jeśli ilość pamięci RAM mieści się w limicie
**Dysk magazynu** | Przydzielony rozmiar dysku nie może być większy niż 32 TB. Mimo że platforma Azure obsługuje dyski 64 TB z dyskami SSD w warstwie Ultra platformy Azure, Ocena obecnie sprawdza, czy rozmiar dysku wynosi 32 TB, ponieważ nie obsługuje jeszcze SSD w warstwie Ultra. <br/><br/> Liczba dysków podłączonych do serwera, łącznie z dyskiem systemu operacyjnego, nie może być większa niż 65. | Gotowe, jeśli rozmiar i liczba dysków przekraczają limity
**Sieć** | Serwer nie może mieć więcej niż 32 interfejsów sieciowych (nic). | Gotowe, jeśli liczba kart sieciowych mieści się w limicie

### <a name="guest-operating-system"></a>System operacyjny gościa

Ocena maszyny wirtualnej platformy Azure wraz z przeglądaniem właściwości maszyny wirtualnej jest sprawdzana w systemie operacyjnym gościa serwera, aby określić, czy może on działać na platformie Azure.

> [!NOTE]
> Aby obsłużyć analizę gościa dla maszyn wirtualnych VMware, Ocena używa systemu operacyjnego określonego dla maszyny wirtualnej w vCenter Server. Jednak vCenter Server nie zapewnia wersji jądra dla systemów operacyjnych maszyn wirtualnych z systemem Linux. Aby odnaleźć wersję, należy skonfigurować [odnajdywanie aplikacji](./how-to-discover-applications.md). Następnie urządzenie odnajduje informacje o wersji przy użyciu poświadczeń gościa określonych podczas konfigurowania funkcji odnajdywania aplikacji.


W ramach oceny jest stosowana następująca logika do identyfikowania gotowości platformy Azure w oparciu o system operacyjny:

**System operacyjny** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
Windows Server 2016 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2012 R2 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2012 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2008 R2 ze wszystkimi usługami SPs | Platforma Azure oferuje pełną pomoc techniczną.| Gotowe na platformę Azure.
Windows Server 2008 (32-bitowe i 64-bit) | Platforma Azure oferuje pełną pomoc techniczną. | Gotowe na platformę Azure.
Windows Server 2003 i Windows Server 2003 R2 | Te systemy operacyjne przekazały swoje daty końca obsługi i potrzebują [niestandardowej umowy pomocy technicznej (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) w celu uzyskania pomocy technicznej na platformie Azure. | Warunkowo gotowy na platformę Azure. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 i MS-DOS | Te systemy operacyjne przekazały swoje daty końca wsparcia. Serwer może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Windows 7, Windows 8 i Windows 10 | Platforma Azure zapewnia pomoc techniczną tylko w przypadku [subskrypcji programu Visual Studio.](../virtual-machines/windows/client-images.md) | Warunkowo gotowy na platformę Azure.
Windows 10 Pro | Platforma Azure zapewnia pomoc techniczną z [wielodostępnymi prawami hostingu.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Warunkowo gotowy na platformę Azure.
Windows Vista i Windows XP Professional | Te systemy operacyjne przekazały swoje daty końca wsparcia. Serwer może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Linux | Zapoznaj się z [systemami operacyjnymi Linux](../virtual-machines/linux/endorsed-distros.md) , które są zatwierdzone przez platformę Azure. Inne systemy operacyjne Linux mogą zacząć działać na platformie Azure. Jednak przed przeprowadzeniem migracji na platformę Azure zalecamy uaktualnienie systemu operacyjnego do wersji zatwierdzonej. | Gotowe na platformę Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowe, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne, takie jak Oracle Solaris, Apple macOS i FreeBSD | Na platformie Azure nie są zatwierdzona te systemy operacyjne. Serwer może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy zainstalowanie obsługiwanego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.  
System operacyjny określony jako **inny** w vCenter Server | W takim przypadku Azure Migrate nie może zidentyfikować systemu operacyjnego. | Nieznane gotowość. Upewnij się, że platforma Azure obsługuje system operacyjny działający na maszynie wirtualnej.
32-bitowe systemy operacyjne | Serwer może zaczynać się na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowy na platformę Azure. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie do 64-bitowego systemu operacyjnego.

## <a name="calculating-sizing"></a>Obliczanie rozmiaru

Gdy serwer zostanie oznaczony jako gotowy na platformie Azure, ocena zostanie zasugerowana w ramach oceny maszyn wirtualnych platformy Azure. Te zalecenia identyfikują maszynę wirtualną platformy Azure i jednostkę SKU dysku. Obliczenia rozmiarów zależą od tego, czy używasz jako lokalizacji lokalnej, czy ustalania rozmiarów na podstawie wydajności.

### <a name="calculate-sizing-as-is-on-premises"></a>Obliczanie rozmiaru (w trybie lokalnym)

 Jeśli używasz jako lokalizacji lokalnej, ocena nie uwzględnia historii wydajności maszyn wirtualnych i dysków w ramach oceny maszyny wirtualnej platformy Azure.

- **Ustalanie rozmiaru obliczeń**: Ocena PRZYDZIELA jednostkę SKU maszyny wirtualnej platformy Azure na podstawie rozmiaru przydzielonego lokalnie.
- **Rozmiar magazynu i dysku**: Ocena jest zgodna z typem magazynu określonym we właściwościach oceny i zalecanym typem dysku. Możliwe typy magazynów to HDD w warstwie Standardowa, SSD w warstwie Standardowa i Premium. Domyślny typ magazynu to Premium.
- **Ustalanie wielkości sieci**: Ocena jest uważana za kartę sieciową na serwerze lokalnym.

### <a name="calculate-sizing-performance-based"></a>Obliczanie rozmiaru (opartego na wydajności)

W przypadku korzystania z ustalania rozmiarów na podstawie wydajności w ramach oceny maszyny wirtualnej platformy Azure Ocena wprowadza zalecenia dotyczące rozmiarów w następujący sposób:

- Ocena traktuje historię wydajności serwera w celu zidentyfikowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- W przypadku zaimportowania serwerów przy użyciu pliku CSV podane wartości są używane. Ta metoda jest szczególnie przydatna w przypadku nadmiarowego przydzielenia lokalnego serwera, wykorzystanie jest niskie i chcesz rightsize maszynę wirtualną platformy Azure w celu oszczędności kosztów.
- Jeśli nie chcesz używać danych dotyczących wydajności, zresetuj kryteria ustalania wielkości tak, aby były lokalne, zgodnie z opisem w poprzedniej sekcji.

#### <a name="calculate-storage-sizing"></a>Oblicz rozmiar magazynu

W przypadku określania wielkości magazynu w ramach oceny maszyny wirtualnej platformy Azure Azure Migrate próbuje zmapować każdy dysk dołączony do serwera na dysk platformy Azure. Ustalanie wielkości przebiega w następujący sposób:

1. W ramach oceny dodano liczbę operacji we/wy odczytu i zapisu dysku, aby uzyskać łączną liczbę operacji we/wy na sekundę. Podobnie dodaje wartości przepływności odczytu i zapisu w celu uzyskania całkowitej przepływności każdego dysku. W przypadku ocen opartych na imporcie istnieje możliwość zapewnienia łącznej liczby operacji we/wy, całkowitej przepływności i łącznej liczby operacji. dysków w zaimportowanym pliku bez określania poszczególnych ustawień dysków. W takim przypadku pozostało, że rozmiar poszczególnych dysków zostanie pominięty, a podane dane są używane bezpośrednio do obliczania wielkości i wybrania odpowiedniej jednostki SKU maszyny wirtualnej.

1. Jeśli typ magazynu został określony jako automatyczny, wybrany typ jest oparty na wartościach operacji wejścia/wyjścia na sekundę. Ocena określa, czy dysk ma zostać zmapowany na dysk HDD w warstwie Standardowa, SSD w warstwie Standardowa lub Premium na platformie Azure. Jeśli typ magazynu jest ustawiony na jeden z tych typów dysków, Ocena próbuje znaleźć jednostkę SKU dysku w wybranym typie magazynu.
1. Dyski są wybierane w następujący sposób:
    - Jeśli ocena nie może znaleźć dysku z wymaganą liczbą operacji we/wy i przepływności, oznacza serwer jako nieodpowiedni dla platformy Azure.
    - Jeśli Ocena znajdzie zestaw odpowiednich dysków, wybiera dyski, które obsługują lokalizację określoną w ustawieniach oceny.
    - Jeśli istnieje wiele kwalifikujących się dysków, Ocena wybiera dysk o najniższym koszcie.
    - Jeśli dane dotyczące wydajności dla dowolnego dysku są niedostępne, rozmiar dysku konfiguracji jest używany do znalezienia dysku SSD w warstwie Standardowa na platformie Azure.

#### <a name="calculate-network-sizing"></a>Oblicz rozmiar sieci

W przypadku oceny maszyny wirtualnej na platformie Azure Ocena próbuje znaleźć maszynę wirtualną platformy Azure, która obsługuje liczbę i wymaganą wydajność kart sieciowych podłączonych do serwera lokalnego.

- W celu uzyskania efektywnej wydajności sieci na serwerze lokalnym Ocena agreguje szybkość transmisji danych z serwera (w sieci) między wszystkimi kartami sieciowymi. Następnie stosuje współczynnik komfortu. Używa wynikowej wartości, aby znaleźć maszynę wirtualną platformy Azure, która może obsługiwać wymaganą wydajność sieci.
- Wraz z wydajnością sieci Ocena uwzględnia również, czy maszyna wirtualna platformy Azure może obsługiwać wymaganą liczbę kart sieciowych.
- Jeśli dane dotyczące wydajności sieci są niedostępne, Ocena uwzględnia tylko liczbę kart sieciowych na potrzeby ustalania rozmiarów maszyn wirtualnych.

#### <a name="calculate-compute-sizing"></a>Oblicz rozmiar obliczeń

Po obliczeniu wymagań dotyczących magazynu i sieci Ocena uwzględnia wymagania dotyczące procesora CPU i pamięci RAM, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.

- Azure Migrate sprawdza efektywne wykorzystanie rdzeni i pamięci RAM w celu znalezienia odpowiedniego rozmiaru maszyny wirtualnej platformy Azure.
- Jeśli nie zostanie znaleziony odpowiedni rozmiar, serwer zostanie oznaczony jako nieodpowiedni dla platformy Azure.
- Jeśli zostanie znaleziony odpowiedni rozmiar, Azure Migrate stosuje obliczenia magazynu i sieci. Następnie stosowane są ustawienia warstwy i cennika dla ostatecznego zalecenia dotyczącego rozmiaru maszyny wirtualnej.
- Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.

## <a name="confidence-ratings-performance-based"></a>Klasyfikacje zaufania (oparte na wydajności)

Każda Ocena na podstawie wydajności maszyny wirtualnej platformy Azure w Azure Migrate jest skojarzona z oceną zaufania. Klasyfikacja z jednego (najniższego) do pięciu (najwyższa) gwiazdek. Ocena zaufania pozwala oszacować niezawodność Azure Migrate zapewniania przez zaleceń dotyczących rozmiaru.

- Ocena zaufania jest przypisana do oceny. Klasyfikacja jest oparta na dostępności punktów danych, które są konieczne do obliczenia oceny.
- W przypadku ustalania rozmiarów wydajności na podstawie oceny wymagane są:
    - Dane użycia procesora CPU i pamięci RAM.
    - Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do serwera.
    - We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do serwera.

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

- Środowisko nie było profilowane w okresie, dla którego tworzysz ocenę. Jeśli na przykład zostanie utworzona Ocena z czasem trwania wydajności ustawionym na jeden dzień, należy poczekać co najmniej dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Ocena nie jest w stanie zbierać danych wydajności dla niektórych lub wszystkich serwerów w okresie oceny. W celu uzyskania oceny o wysokiej pewności upewnij się, że: 
    - Serwery są zasilane na czas trwania oceny
    - Połączenia wychodzące na portach 443 są dozwolone
    - W przypadku pamięci dynamicznej na serwerach funkcji Hyper-V jest włączona 
    
    Użyj opcji „Oblicz ponownie”, aby uwzględnić najnowsze zmiany w ocenie ufności.

- Niektóre serwery zostały utworzone w czasie, w którym obliczono ocenę. Załóżmy na przykład, że utworzono ocenę historii wydajności w ostatnim miesiącu, ale niektóre serwery zostały utworzone tylko na tydzień temu. W takim przypadku dane wydajności dla nowych serwerów nie będą dostępne przez cały czas, a Ocena zaufania byłaby niska.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. W przeciwnym razie rozmiary oparte na wydajności mogą być zawodne. W takim przypadku zaleca się przełączenie oceny do rozmiarów lokalnych.

## <a name="calculate-monthly-costs"></a>Oblicz miesięczne koszty

Po zakończeniu ustalania rozmiarów zaleceń dla maszyny wirtualnej platformy Azure w Azure Migrate oblicza koszty obliczeń i magazynowania po migracji.

- **Koszt obliczeń**: Azure Migrate używa zalecanego rozmiaru maszyny wirtualnej platformy Azure i interfejsu API rozliczeń platformy Azure, aby obliczyć miesięczny koszt dla serwera.

    Obliczenia uwzględniają:
    - System operacyjny
    - Software Assurance
    - Wystąpienia zarezerwowane
    - Czas pracy maszyny wirtualnej
    - Lokalizacja
    - Ustawienia waluty

    Ocena agreguje koszt na wszystkich serwerach, aby obliczyć łączny koszt obliczeń miesięcznych.

- **Koszt magazynu**: miesięczny koszt magazynu dla serwera jest obliczany przez zsumowanie miesięcznego kosztu wszystkich dysków dołączonych do serwera.

    Ocena oblicza łączne miesięczne koszty magazynowania, sumując koszty magazynowania wszystkich serwerów. Obecnie obliczenia nie uwzględniają ofert określonych w ustawieniach oceny.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.

## <a name="next-steps"></a>Następne kroki

[Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen. 

- Dowiedz się więcej na temat uruchamiania ocen w przypadku serwerów działających w środowisku programu [VMware](./tutorial-discover-vmware.md) i [funkcji Hyper-V ](./tutorial-discover-hyper-v.md) oraz [serwerach fizycznych](./tutorial-discover-physical.md).
- Dowiedz się więcej na temat oceniania serwerów [zaimportowanych przy użyciu pliku CSV](./tutorial-discover-import.md).
- Dowiedz się więcej na temat konfigurowania [wizualizacji zależności](concepts-dependency-visualization.md).