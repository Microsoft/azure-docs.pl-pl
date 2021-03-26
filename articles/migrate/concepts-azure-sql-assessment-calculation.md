---
title: Oceny usługi Azure SQL w Azure Migrate narzędziu odnajdywania i oceny
description: Informacje na temat ocen usługi Azure SQL w Azure Migrate narzędziu odnajdywania i oceny
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: c2e739a45c7915c957ca89e5b01b98afa945d03e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557194"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Przegląd oceny (Migrowanie do usługi Azure SQL)

Ten artykuł zawiera omówienie ocen migracji lokalnych wystąpień SQL Server ze środowiska VMware do baz danych Azure SQL lub wystąpień zarządzanych przy użyciu [narzędzia Azure Migrate do odnajdywania i oceny](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>Co to jest Ocena?
Ocena narzędzia odnajdywanie i ocenianie to punkt w czasie migawki danych i miara gotowości i oszacowania efektu migracji serwerów lokalnych na platformę Azure.

## <a name="types-of-assessments"></a>Typy ocen

Istnieją trzy typy ocen, które można utworzyć za pomocą narzędzia do odnajdywania i oceny Azure Migrate:.

**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Można ocenić serwery lokalne w środowisku [VMware](how-to-set-up-appliance-vmware.md) i [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) oraz [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji do maszyn wirtualnych platformy Azure przy użyciu tego typu oceny.
**Azure SQL** | Ocenianie migracji lokalnych serwerów SQL ze środowiska VMware do Azure SQL Database lub wystąpienia zarządzanego Azure SQL.
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Za pomocą tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

Ocena usługi Azure SQL zapewnia jedno kryterium ustalania wielkości:

**Kryterium określania rozmiaru** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny zawierające rekomendacje na podstawie zebranych danych dotyczących wydajności | Konfiguracja usługi Azure SQL bazuje na danych wydajności wystąpień i baz danych SQL, które obejmują: użycie procesora CPU, użycie pamięci, operacje we/wy na sekundę (pliki danych i dziennika), przepływność i czas oczekiwania operacji wejścia/wyjścia.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Jak mogę oceniać lokalne serwery SQL?

Lokalne wystąpienia SQL Server można ocenić przy użyciu konfiguracji i danych użycia zebranych przez urządzenie lekkie Azure Migrate. Urządzenie wykrywa lokalne wystąpienia programu SQL Server i bazy danych, a następnie wysyła dane konfiguracji i wydajności do Azure Migrate. [Więcej informacji](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Jak mogę oceniać z urządzeniem?
Jeśli wdrażasz urządzenie Azure Migrate w celu odnajdywania serwerów lokalnych, wykonaj następujące czynności:
1.  Skonfiguruj platformę Azure i środowisko lokalne do pracy z Azure Migrate.
2.  W pierwszej ocenie Utwórz projekt Azure Migrate i Azure Migrate Dodaj do niego narzędzie do odnajdywania i oceny.
3.  Wdróż uproszczone urządzenie Azure Migrate. Urządzenie stale odnajduje serwery lokalne i wysyła dane dotyczące konfiguracji i wydajności do Azure Migrate. Wdróż urządzenie jako maszynę wirtualną lub serwer fizyczny. Nie musisz instalować żadnych elementów na serwerach, które chcesz ocenić.

Po rozpoczęciu odnajdywania urządzenia można zbierać serwery, które chcesz ocenić do grupy, i uruchomić ocenę dla grupy z typem oceny **Azure SQL**.

Postępuj zgodnie z naszym samouczkiem, aby ocenić [wystąpienia SQL Server](tutorial-assess-sql.md) w celu wypróbowania tych kroków.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Jak urządzenie oblicza dane wydajności dla wystąpień SQL i baz danych?

Urządzenie zbiera dane dotyczące wydajności dla ustawień obliczeń z następującymi krokami:
1. Urządzenie zbiera punkt próbkowania w czasie rzeczywistym. W przypadku serwerów SQL punkt próbki jest zbierany co 30 sekund.
2. Urządzenie agreguje przykładowe punkty danych zbierane co 30 sekund przez 10 minut. Aby utworzyć punkt danych, urządzenie wybiera wartości szczytowe ze wszystkich próbek. Wysyła ona wartość maksymalną, średnią i wariancję dla każdego licznika na platformę Azure.
3. Azure Migrate przechowuje wszystkie 10-minutowe punkty danych w ciągu ostatniego miesiąca.
4. Podczas tworzenia oceny, Azure Migrate identyfikuje odpowiedni punkt danych do użycia dla odpowiedniej zmiany. Identyfikator jest oparty na wartościach percentylu dla historii wydajności i użycia percentylu.
    - Jeśli na przykład historia wydajności wynosi tydzień, a użycie percentyla to używany 95. percentyl, Ocena sortuje 10-minutowe punkty próbkowania w ostatnim tygodniu. Sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentyl dla odpowiedniej zmiany.
    - Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
    - Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, wybierz 99 percentyl dla użycia percentyla.
5. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla tych metryk, które zbiera urządzenie:
    - Użycie procesora CPU (%)
    - Użycie pamięci (%)
    - Odczyt operacji we/wy na sekundę i zapis/s (pliki danych i dziennika)
    - Odczyt MB/s i zapis MB/s (przepływność)
    - Opóźnienie operacji we/wy

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Jakie właściwości są używane do tworzenia i dostosowywania oceny usługi Azure SQL?

Oto nowości uwzględnione we właściwościach oceny usługi Azure SQL:

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację. Konfiguracja usługi Azure SQL i zalecenia dotyczące kosztów są zależne od określonej lokalizacji.
**Docelowy typ wdrożenia** | Docelowy typ wdrożenia, dla którego chcesz przeprowadzić ocenę: <br/><br/> Wybierz pozycję **zalecane**, jeśli chcesz, aby usługa Azure Migrate mogła ocenić gotowość serwerów SQL do migracji do usługi Azure SQL i usługi Azure SQL DB oraz zalecać najlepszą, docelową opcję wdrożenia, warstwę docelową, konfigurację SQL platformy Azure i oszacowania miesięczne.<br/><br/>Wybierz pozycję **Azure SQL DB**, jeśli chcesz ocenić serwery SQL do migracji do baz danych Azure SQL, a następnie przejrzyj warstwę docelową, konfigurację bazy danych SQL Azure i szacunki miesięczne.<br/><br/>Wybierz pozycję **Azure SQL mi**, jeśli chcesz ocenić serwery SQL do migracji do baz danych Azure SQL, a następnie przejrzyj warstwę docelową, konfigurację i miesięczne szacunkowe usługi Azure SQL.
**Pojemność zarezerwowana** | Określa zarezerwowaną pojemność, dzięki czemu szacowania kosztów w ocenie są uwzględniane.<br/><br/> W przypadku wybrania opcji zarezerwowanej pojemności nie można określić "rabat (%)".
**Kryterium określania rozmiaru** | Ta właściwość służy do poprawnego rozmiaru konfiguracji usługi Azure SQL. <br/><br/> Jest ona domyślnie oparta na **wydajności** , co oznacza, że Ocena będzie zbierać metryki wydajności SQL Server wystąpień i baz danych, aby zalecać optymalne i nieodpowiednie zalecenia dotyczące warstwy/lub Azure SQL Database usługi Azure SQL.
**Historia wydajności** | Historia wydajności określa czas trwania używany podczas oceniania danych wydajności.
**Użycie percentyla** | Użycie percentylu określa wartość percentylości próbki wydajności używanej dla odpowiedniej zmiany.
**Współczynnik komfortu** | Bufor używany podczas oceny. Konta IT dotyczą problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrasta w przyszłości.<br/><br/> Na przykład wystąpienie 10-rdzeniowe z wykorzystaniem 20% zwykle powoduje wystąpienie dwurdzeniowe. Ze współczynnikiem komfortu wynoszącym 2,0, wynikiem jest wystąpienie z czterema rdzeniami.
**Program oferty/licencjonowania** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , w której zarejestrowano Cię. Obecnie możesz wybrać tylko opcję płatność zgodnie z rzeczywistym użyciem i płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie. Pamiętaj, że możesz skorzystać z dodatkowego rabatu, stosując zarezerwowaną pojemność i Korzyść użycia hybrydowego platformy Azure na podstawie oferty z płatność zgodnie z rzeczywistym użyciem.
**Warstwa usług** | Najbardziej odpowiednia opcja warstwy usług w celu uwzględnienia potrzeb firmy na potrzeby migracji do Azure SQL Database i/lub wystąpienia zarządzanego Azure SQL:<br/><br/>**Zalecane** , jeśli chcesz, aby Azure Migrate zalecić najlepszą dodaną warstwę usług dla serwerów. Może to być ogólne lub krytyczne dla działania firmy. <br/><br/> **Ogólnego przeznaczenia** Jeśli chcesz, aby konfiguracja usługi Azure SQL została zaprojektowana na potrzeby obciążeń zorientowanych na budżet. [Więcej informacji](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> **Krytyczne dla działania firmy** Jeśli chcesz, aby konfiguracja usługi Azure SQL została zaprojektowana dla obciążeń o małych opóźnieniach i ma wysoką odporność na awarie i szybkie przełączanie do trybu failover. [Więcej informacji](../azure-sql/database/service-tier-business-critical.md)
**Waluta** | Waluta rozliczeń dla Twojego konta.
**Rabat (%)** | Wszystkie zniżki związane z subskrypcją, które otrzymujesz w ramach oferty platformy Azure. Ustawienie domyślne to 0%.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz już licencję SQL Serverową. <br/><br/> Jeśli to zrobisz, a zostaną one objęte aktywnym programem Software Assurance SQL Server subskrypcje, możesz zastosować Korzyść użycia hybrydowego platformy Azure po przeniesieniu licencji na platformę Azure.

[Zapoznaj się z najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen przy użyciu Azure Migrate.

## <a name="calculate-readiness"></a>Oblicz gotowość

> [!NOTE]
Ocena obejmuje tylko bazy danych znajdujące się w stanie online. W przypadku, gdy baza danych jest w innym stanie, w ocenie ignorowane są informacje dotyczące gotowości, rozmiaru i obliczeń kosztów dla takich baz danych. Jeśli chcesz ocenić takie bazy danych, zmień stan bazy danych i ponownie oblicz ocenę po pewnym czasie.

### <a name="azure-sql-readiness"></a>Gotowość usługi Azure SQL

Gotowość usługi Azure SQL dla wystąpień SQL i baz danych opiera się na sprawdzaniu zgodności funkcji z Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL:
1. Ocena usługi Azure SQL traktuje SQL Server funkcje wystąpienia, które są obecnie używane w przypadku obciążeń źródłowych SQL Server (zadania agenta SQL, połączone serwery itp.) i schematy baz danych użytkownika (tabele, widoki, wyzwalacze, procedury składowane itp.) w celu zidentyfikowania problemów ze zgodnością.
1. W przypadku braku problemów ze zgodnością gotowość zostanie oznaczona jako **gotowa** dla docelowego typu wdrożenia (Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL).
1. Jeśli występują niekrytyczne problemy ze zgodnością, takie jak obniżone lub Nieobsługiwane funkcje, które nie blokują migracji do określonego docelowego typu wdrożenia, gotowość jest oznaczona jako **gotowy** (ikona informacji o linku i niebieska) z **ostrzeżeniami** i zalecanymi wskazówkami dotyczącymi rozwiązania.
1. Jeśli istnieją jakieś problemy ze zgodnością, które mogą zablokować migrację do określonego docelowego typu wdrożenia, gotowość zostanie oznaczona jako **niegotowa** z informacjami o **problemie** i zalecanymi wskazówkami dotyczącymi korygowania.
    - Jeśli istnieje nawet jedna baza danych w wystąpieniu SQL, która nie jest gotowa dla określonego docelowego typu wdrożenia, wystąpienie zostanie oznaczone jako **niegotowe** dla tego typu wdrożenia.
1. Jeśli odnajdywanie jest nadal w toku lub występują problemy z odnajdywaniem dla wystąpienia lub bazy danych SQL, gotowość jest oznaczona jako **nieznana** , ponieważ Ocena nie może obliczyć gotowości dla tego wystąpienia SQL.

### <a name="recommended-deployment-type"></a>Zalecany typ wdrożenia

W przypadku wybrania docelowego typu wdrożenia zgodnie z **zaleceniem** we właściwościach oceny usługi azure SQL Azure Migrate zalecanym typem wdrożenia usługi Azure SQL, który jest zgodny z wystąpieniem SQL. Migrowanie do celu zalecanego przez firmę Microsoft zmniejsza całkowity nakład pracy związany z migracją. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Zalecany typ wdrożenia na podstawie gotowości usługi Azure SQL

 **Gotowość usługi Azure SQL DB** | **Gotowość do usługi Azure SQL** | **Zalecany typ wdrożenia** | **Obliczenia dotyczące konfiguracji i kosztów usługi Azure SQL.**
 --- | --- | --- | --- |
 Gotowy | Gotowy | Baza danych SQL Azure lub <br/>Usługa Azure SQL MI | Tak
 Gotowy | Niegotowe lub<br/> Nieznane | Azure SQL DB | Tak
 Niegotowe lub<br/>Nieznane | Gotowy | Usługa Azure SQL MI | Tak
 Nie gotowy | Nie gotowy | Potencjalnie gotowa na maszynę wirtualną platformy Azure | Nie
 Niegotowe lub<br/>Nieznane | Niegotowe lub<br/>Nieznane | Nieznane | Nie

> [!NOTE]
> Jeśli Zalecany typ wdrożenia jest wybrany zgodnie z **zaleceniem** we właściwościach oceny i jeśli źródło SQL Server jest dobrym rozwiązaniem dla pojedynczej bazy danych usługi Azure SQL DB i wystąpienia zarządzanego Azure SQL, Ocena zaleca określoną opcję, która optymalizuje koszt i mieści się w granicach rozmiaru i wydajności.

#### <a name="potentially-ready-for-azure-vm"></a>Potencjalnie gotowa na maszynę wirtualną platformy Azure

Jeśli wystąpienie programu SQL Server nie jest gotowe do Azure SQL Database i wystąpienia zarządzanego Azure SQL, zalecany typ wdrożenia zostanie oznaczony jako *potencjalnie gotowy dla maszyny wirtualnej platformy Azure*.
- Zaleca się utworzenie oceny w Azure Migrate z typem oceny jako "maszyna wirtualna platformy Azure", aby określić, czy serwer, na którym uruchomiono wystąpienie, jest gotowy do migracji na maszynę wirtualną platformy Azure. Należy pamiętać, że:
    - Oceny maszyn wirtualnych platformy Azure w Azure Migrate są obecnie podnoszone i zmieniane i nie będą brane pod uwagę w przypadku uruchamiania wystąpień SQL i baz danych na maszynie wirtualnej platformy Azure. 
    - Po uruchomieniu oceny maszyny wirtualnej platformy Azure na serwerze zalecany rozmiar i szacunkowy koszt będą dotyczyć wszystkich wystąpień uruchomionych na serwerze i można je będzie zmigrować na maszynę wirtualną platformy Azure przy użyciu narzędzia do migracji serwera. Przed przeprowadzeniem migracji [zapoznaj się z zaleceniami dotyczącymi wydajności](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) dla programu SQL Server w usłudze Azure Virtual Machines.


## <a name="calculate-sizing"></a>Oblicz rozmiar

### <a name="azure-sql-configuration"></a>Konfiguracja usługi Azure SQL

Po ustaleniu gotowości i zalecanym typie wdrożenia usługi Azure SQL usługa oblicza określoną warstwę usług i konfigurację usługi Azure SQL (rozmiar jednostki SKU), która może spełnić lub przekroczyć lokalną wydajność wystąpienia programu SQL:
1. Podczas odnajdywania Azure Migrate zbiera informacje o konfiguracji i wydajności wystąpienia programu SQL, które obejmują:
    - Rdzeni wirtualnych (przydzielono) i użycie procesora CPU (%)
        - Użycie procesora CPU w wystąpieniu programu SQL jest wartością procentową przydziału procesora wykorzystanego przez wystąpienie w programie SQL Server.
        - Użycie procesora CPU dla bazy danych jest wartością procentową przydziału procesora używanego przez bazę danych w wystąpieniu programu SQL Server
    - Pamięć (przydzielono) i użycie pamięci (%)
    - Odczyt operacji we/wy na sekundę i zapis/s (pliki danych i dziennika)
        - Odczyt operacji we/wy i zapisu we/wy na poziomie wystąpienia bazy danych SQL jest obliczany przez dodanie operacji odczytu we/wy i zapisu we/wy dla wszystkich baz danych odnalezionych w tym wystąpieniu.
    - Odczyt MB/s i zapis MB/s (przepływność)
    - Opóźnienie operacji we/wy
    - Łączny rozmiar bazy danych i organizacji plików
        - Rozmiar bazy danych jest obliczany przez dodanie wszystkich plików danych i dziennika.
1. Ocena agreguje wszystkie dane dotyczące konfiguracji i wydajności, a także próbuje znaleźć najlepsze dopasowanie w różnych warstwach i konfiguracjach usługi Azure SQL, a następnie wybiera konfigurację, która może być zgodna z wymaganiami dotyczącymi wydajności wystąpień SQL lub przekraczać te wymagania, optymalizując koszt.

### <a name="confidence-ratings"></a>Klasyfikacje zaufania 
Każda Ocena usługi Azure SQL jest skojarzona z oceną zaufania. Klasyfikacja z jednego (najniższego) do pięciu (najwyższa) gwiazdek. Ocena zaufania pozwala oszacować niezawodność Azure Migrate zapewniania przez zaleceń dotyczących rozmiaru.
- Ocena zaufania jest przypisana do oceny. Klasyfikacja jest oparta na dostępności punktów danych, które są konieczne do obliczenia oceny.
- W przypadku ustalania rozmiarów na podstawie wydajności Ocena zbiera dane dotyczące wydajności wszystkich wystąpień i baz danych SQL, takich jak:
    - Użycie procesora CPU (%)
    - Użycie pamięci (%)
    - Odczyt operacji we/wy na sekundę i zapis/s (pliki danych i dziennika)
    - Odczyt MB/s i zapis MB/s (przepływność)
    - Opóźnienie operacji we/wy
    
Jeśli którykolwiek z tych numerów wykorzystania nie jest dostępny, zalecenia dotyczące rozmiaru mogą być zawodne.
W tej tabeli przedstawiono oceny stopnia zaufania, które są zależne od wartości procentowej dostępnych punktów danych:

**Dostępność punktu danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

#### <a name="low-confidence-ratings"></a>Oceny o niskiej pewności
Poniżej przedstawiono kilka powodów, dla których ocena może uzyskać klasyfikację o niskiej pewności:
- Środowisko nie było profilowane w okresie, dla którego tworzysz ocenę. Jeśli na przykład zostanie utworzona Ocena z czasem trwania wydajności ustawionym na jeden dzień, należy poczekać co najmniej dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Ocena nie jest w stanie zbierać danych wydajności dla niektórych lub wszystkich serwerów w okresie oceny. W celu uzyskania oceny o wysokiej pewności upewnij się, że:
    - Serwery są zasilane na czas trwania oceny
    - Połączenia wychodzące na portach 443 są dozwolone
    - Jeśli Azure Migrate stan połączenia agenta programu SQL Server w Azure Migrate ma wartość "Connected" i sprawdza ostatni puls 
    - Czy stan połączenia usługi Azure Migrate dla wszystkich wystąpień SQL ma wartość „Połączony” w bloku odnalezionego wystąpienia SQL

    Użyj opcji „Oblicz ponownie”, aby uwzględnić najnowsze zmiany w ocenie ufności.
- Niektóre bazy danych lub wystąpienia zostały utworzone w czasie, w którym Ocena została obliczona. Załóżmy na przykład, że utworzono ocenę historii wydajności w ostatnim miesiącu, ale niektóre bazy danych lub wystąpienia zostały utworzone tylko tydzień temu. W takim przypadku dane wydajności dla nowych serwerów nie będą dostępne przez cały czas, a Ocena zaufania byłaby niska.

> [!NOTE]
> Ponieważ oceny usługi Azure SQL są oparte na wydajności, jeśli Ocena zaufania dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. W przeciwnym razie rozmiary oparte na wydajności mogą być zawodne.

## <a name="calculate-monthly-costs"></a>Oblicz miesięczne koszty
Po zakończeniu zaleceń dotyczących rozmiarów usługa Azure SQL Assessment oblicza koszty obliczeń i magazynu dla zalecanych konfiguracji usługi Azure SQL przy użyciu wewnętrznego interfejsu API cen. Agreguje koszt obliczeń i magazynowania we wszystkich wystąpieniach, aby obliczyć łączny koszt obliczeń miesięcznych. 
### <a name="compute-cost"></a>Koszt obliczeń
- Aby obliczyć koszt obliczeń dla konfiguracji usługi Azure SQL, Ocena uwzględnia następujące właściwości:
    - Korzyść użycia hybrydowego platformy Azure licencji SQL
    - Pojemność zarezerwowana
    - Lokalizacja docelowa platformy Azure
    - Waluta
    - Program oferty/licencjonowania
    - Rabat (%)

### <a name="storage-cost"></a>Koszty magazynu
- Szacowany koszt magazynu dotyczy tylko plików danych, a nie plików dziennika. 
- W celu obliczenia kosztu magazynu dla konfiguracji usługi Azure SQL należy ocenić następujące właściwości:
    - Lokalizacja docelowa platformy Azure
    - Waluta
    - Program oferty/licencjonowania
    - Rabat (%)
- Koszt magazynu kopii zapasowych nie jest uwzględniony w ocenie.
- **Azure SQL Database**
    - Minimalny koszt magazynu 5 GB jest dodawany w szacowaniu kosztów i dodatkowy koszt magazynu jest dodawany do magazynu w przyrostach 1 GB. [Więcej informacji](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Wystąpienie zarządzane Azure SQL**
    - Dla pierwszego magazynu 32 GB/wystąpienia/miesiąca nie dodano żadnego kosztu magazynu, a dodatkowy koszt magazynu jest dodawany do magazynu w przyrostach o rozmiarze 32 GB. [Więcej informacji](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Następne kroki
- [Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen. 
- Dowiedz się, jak uruchomić [ocenę usługi Azure SQL](how-to-create-azure-sql-assessment.md).