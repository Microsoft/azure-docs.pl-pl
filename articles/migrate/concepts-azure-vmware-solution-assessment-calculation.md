---
title: Obliczenia dotyczące automatycznej synchronizacji w Azure Migrate | Microsoft Docs
description: Zawiera omówienie obliczeń oceny automatycznej synchronizacji w usłudze Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: mahain
ms.openlocfilehash: 400c2d91383b5f21fcd40fdbbe279bd83fcef51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576544"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Przegląd oceny serwera (Migrowanie do rozwiązania Azure VMware)

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń. Śledzi również wystąpienia chmury prywatnej i publicznej na platformie Azure. Centrum oferuje Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ocena serwera to narzędzie w Azure Migrate, które ocenia serwery lokalne na potrzeby migracji do maszyn wirtualnych platformy Azure IaaS i rozwiązania Azure VMware (Automatyczna synchronizacja). W tym artykule znajdują się informacje o sposobie obliczania ocen rozwiązania Azure VMware (Automatyczna synchronizacja).

> [!NOTE]
> Ocena rozwiązań VMware (wersja zapoznawcza) systemu Azure jest obecnie dostępna w wersji zapoznawczej i można ją utworzyć tylko dla maszyn wirtualnych VMware.

## <a name="types-of-assessments"></a>Typy ocen

Oceny tworzone za pomocą oceny serwera to migawka danych w danym momencie. Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: Ocena serwera.

**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Możesz ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md), [maszyny wirtualne funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)i [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji na platformę Azure przy użyciu tego typu oceny. [Dowiedz się więcej](concepts-assessment-calculation.md)
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) pod kątem migracji do usługi Azure VMware Solution (AVS).[Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

Ocena rozwiązań VMware na platformie Azure (Automatyczna synchronizacja) w ramach oceny serwera oferuje dwie opcje kryteriów ustalania rozmiarów:

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Ocenia na podstawie zebranych danych wydajności lokalnych maszyn wirtualnych. | **Zalecany rozmiar węzła**: w oparciu o dane użycia procesora CPU i pamięci oraz typ węzła, typ magazynu i ustawienie FTT wybrane do oceny.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar węzła**: na podstawie rozmiaru lokalnego maszyny wirtualnej wraz z typem węzła, typem magazynu i USTAWIENIEm FTT, które zostało wybrane do oceny.

## <a name="how-do-i-run-an-assessment"></a>Jak mogę uruchomić ocenę?

Istnieje kilka sposobów na przeprowadzenie oceny.

- Ocenianie maszyn przy użyciu metadanych serwera zbieranych przez uproszczone urządzenie Azure Migrate. Urządzenie umożliwia odnalezienie maszyn lokalnych. Następnie wysyła metadane maszyn i dane wydajności do Azure Migrate.
- Ocenianie maszyn przy użyciu metadanych serwera zaimportowanych w formacie wartości rozdzielanych przecinkami (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Jak mogę oceniać z urządzeniem?

Jeśli wdrażasz urządzenie Azure Migrate w celu odnajdywania serwerów lokalnych, wykonaj następujące czynności:

1. Skonfiguruj platformę Azure i środowisko lokalne do pracy z oceną serwera.
2. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do oceny serwera.
3. Wdróż uproszczone urządzenie Azure Migrate. Urządzenie nieustannie odnajduje maszyny lokalne i wysyła metadane maszyn i dane wydajności do Azure Migrate. Wdróż urządzenie jako maszynę wirtualną. Nie musisz instalować żadnych elementów na maszynach, które chcesz ocenić.

Po rozpoczęciu odnajdywania urządzenia przez urządzenie można zbierać maszyny do oceny w grupie i uruchamiać ocenę dla grupy z typem oceny **rozwiązanie Azure VMware (Automatyczna synchronizacja)**.

Utwórz pierwszą ocenę rozwiązania Azure VMware (Automatyczna synchronizacja), wykonując kroki opisane [tutaj](how-to-create-azure-vmware-solution-assessment.md).

## <a name="how-do-i-assess-with-imported-data"></a>Jak mogę oceniać z zaimportowanymi danymi?

Jeśli oceniasz serwery przy użyciu pliku CSV, nie potrzebujesz urządzenia. Zamiast tego wykonaj następujące czynności:

1. Skonfiguruj platformę Azure do pracy z oceną serwera.
2. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do oceny serwera.
3. Pobierz szablon CSV i Dodaj do niego dane serwera.
4. Zaimportuj szablon do oceny serwera.
5. Odnajdź serwery dodane podczas importowania, Zbierz je do grupy i uruchom ocenę dla grupy z typem oceny **rozwiązanie Azure VMware (Automatyczna synchronizacja)**.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane są zbierane przez urządzenie?

Jeśli używasz urządzenia Azure Migrate do oceny, Dowiedz się więcej na temat metadanych i danych wydajności zbieranych dla programu [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak urządzenie oblicza dane wydajności?

Jeśli urządzenie jest używane do odnajdywania, zbiera dane o wydajności dla ustawień obliczeń z następującymi krokami:

1. Urządzenie zbiera punkt próbkowania w czasie rzeczywistym.

    - **Maszyny wirtualne VMware**: punkt próbki jest zbierany co 20 sekund.

2. Urządzenie łączy przykładowe punkty, aby utworzyć jeden punkt danych co 10 minut. Aby utworzyć punkt danych, urządzenie wybiera wartości szczytowe ze wszystkich próbek. Następnie wysyła punkt danych do platformy Azure.
3. Ocena serwera przechowuje wszystkie 10-minutowe punkty danych w ciągu ostatniego miesiąca.
4. Podczas tworzenia oceny Ocena serwera identyfikuje odpowiedni punkt danych do użycia dla odpowiedniej zmiany. Identyfikator jest oparty na wartościach percentylu dla *historii wydajności* i *użycia percentylu*.

    - Jeśli na przykład historia wydajności wynosi tydzień, a użycie percentyla to używany 95. percentyl, Ocena serwera sortuje 10-minutowe punkty próbkowania w ostatnim tygodniu. Sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentyl dla odpowiedniej zmiany.
    - Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
    - Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, wybierz 99 percentyl dla użycia percentyla.

5. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla tych metryk, które zbiera urządzenie:

    - Wykorzystanie procesora
    - Użycie pamięci RAM
    - Liczba operacji we/wy na sekundę (odczyt i zapis)
    - Przepływność dysku (odczyt i zapis)
    - Przepływność sieci (do i z)

Następujące dane dotyczące wydajności są zbierane, ale nie są używane w zastosowaniach zaleceń dotyczących oceny automatycznej synchronizacji:
  - Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do maszyny wirtualnej.
  - We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do maszyny wirtualnej.

## <a name="how-are-avs-assessments-calculated"></a>Jak są obliczane oceny automatycznej synchronizacji?

Funkcja oceny serwera używa metadanych i danych wydajności maszyn lokalnych w celu obliczenia ocen. W przypadku wdrożenia urządzenia Azure Migrate oceny używa danych zbieranych przez urządzenie. Jednak w przypadku uruchomienia oceny zaimportowanej przy użyciu pliku CSV należy podać metadane dla obliczenia.

Obliczenia odbywają się w tych trzech etapach:

1. **Oblicz gotowość rozwiązania Azure VMware (Automatyczna synchronizacja)**: czy lokalne maszyny wirtualne są odpowiednie do migracji do rozwiązania Azure VMware (Automatyczna synchronizacja).
2. **Oblicz liczbę węzłów i ich użycie w wielu węzłach**: Szacowana liczba węzłów synchronizacji wymaganych do uruchamiania maszyn wirtualnych oraz przewidywanego użycia procesora CPU, pamięci i magazynu we wszystkich węzłach.
3. **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty wszystkich węzłów platformy Azure VMware (Automatyczna synchronizacja) z uruchomionymi lokalnymi maszynami wirtualnymi.

Obliczenia są zgodne z poprzednią kolejnością. Serwer maszynowy przechodzi do późniejszego etapu tylko wtedy, gdy przekaże poprzednią. Jeśli na przykład serwer ulegnie awarii na etapie gotowości do automatycznej synchronizacji, zostanie oznaczony jako nieodpowiedni dla platformy Azure. Ustalanie wielkości i obliczeń kosztów nie jest wykonywane dla tego serwera

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Co to jest ocena rozwiązania Azure VMware (Automatyczna synchronizacja)?

Tutaj nowości zamieszczono w ocenie automatycznej synchronizacji w ocenie serwera:


| **Właściwość** | **Szczegóły** 
| - | - 
| **Lokalizacja docelowa** | Określa lokalizację chmury prywatnej automatycznej synchronizacji, do której chcesz przeprowadzić migrację.<br/><br/> Ocena automatycznej synchronizacji w ocenie serwera obsługuje obecnie następujące regiony docelowe: Wschodnie stany USA, Europa Zachodnia, zachodnie stany USA. 
| **Typ magazynu** | Określa aparat magazynu, który ma być używany w ramach automatycznej synchronizacji.<br/><br/> Oceny wersji zaautomatycznej obsługują tylko sieci vSAN jako domyślny typ magazynu. 
**Wystąpienia zarezerwowane (RIs)** | Ta właściwość pomaga określić zarezerwowane wystąpienia w automatycznej synchronizacji. Usługi RIs nie są obecnie obsługiwane w przypadku węzłów automatycznej synchronizacji. 
**Typ węzła** | Określa [Typ węzła automatycznej synchronizacji](../azure-vmware/concepts-private-clouds-clusters.md) , używany do mapowania lokalnych maszyn wirtualnych. Domyślny typ węzła to AV36. <br/><br/> Azure Migrate będzie wymagała wymaganej liczby węzłów do migracji maszyn wirtualnych do automatycznej synchronizacji. 
**Ustawienie FTT, poziom RAID** | Określa, że ma to zastosowanie do niedopuszczalnych połączeń i kombinacji RAID. Wybrana opcja FTT z wymaganiami dotyczącymi lokalnego dysku maszyny wirtualnej określi łączny magazyn sieci vSAN wymagany w ramach automatycznej synchronizacji. 
**Kryterium ustalania wielkości** | Ustawia kryteria do użycia dla maszyn wirtualnych o *odpowiednim rozmiarze* na potrzeby automatycznej synchronizacji. Możesz wybrać opcję ustalania rozmiarów na *podstawie wydajności* lub *jako lokalne* , bez uwzględniania historii wydajności. 
**Historia wydajności** | Określa czas, jaki należy wziąć pod uwagę podczas oceniania danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium ustalania wielkości jest *oparte na wydajności*. 
**Użycie percentyla** | Określa wartość percentylości zestawu próbek wydajności, który ma być brany pod uwagę w przypadku zmiany wielkości liter. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest oparty na wydajności.
**Współczynnik komfortu** | Azure Migrate oceny serwera traktuje bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. 
**Oferta** | Wyświetla [ofertę platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , która jest zarejestrowana. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Przedstawia walutę rozliczeń dla Twojego konta. 
**Rabat (%)** | Wyświetla rabat związany z subskrypcją, który otrzymujesz w górnej części oferty platformy Azure. Ustawienie domyślne to 0%. 
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz program Software Assurance i kwalifikujesz się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Chociaż nie ma to wpływu na ceny rozwiązań VMware platformy Azure ze względu na cenę opartą na węzłach, klienci mogą nadal stosować licencje lokalnych systemów operacyjnych (Microsoft based) w ramach automatycznej synchronizacji przy użyciu korzyści użycia hybrydowego platformy Azure. Inni dostawcy systemu operacyjnego oprogramowania będą musieli podać własne warunki licencjonowania, takie jak RHEL. 
**vCPU nadsubskrypcji** | Określa stosunek liczby rdzeni wirtualnych związanych z jednym rdzeniem fizycznym w węźle automatyczna synchronizacja. Wartość domyślna w obliczeniach to 4 vCPU: 1 rdzeń fizyczny w wersji zaautomatycznej. <br/><br/> Użytkownicy interfejsu API mogą ustawić tę wartość jako liczbę całkowitą. Należy zauważyć, że vCPU nad> 4:1 może mieć wpływ na obciążenia, w zależności od użycia procesora CPU. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analiza przydatności rozwiązań VMware na platformie Azure (Automatyczna synchronizacja)

Ocena automatycznej synchronizacji w ocenie serwera ocenia każdą lokalną maszynę wirtualną w celu zapewnienia jej przydatności do automatycznej synchronizacji, przeglądając właściwości maszyny. Przypisuje także każdej ocenianej maszynie do jednej z następujących kategorii przydatności:

- **Gotowe do automatycznej synchronizacji**: maszynę można migrować na platformę Azure (Automatyczna synchronizacja) bez wprowadzania żadnych zmian. Rozpocznie się to za pomocą automatycznej synchronizacji z pełną obsługą wersji zaautomatycznej.
- **Gotowe z warunkami**: maszyna wirtualna może mieć problemy ze zgodnością z bieżącą wersją vSphere, a także wymaganie narzędzi VMware i innych ustawień, zanim będzie można uzyskać pełną funkcjonalność z maszyny wirtualnej w ramach automatycznej synchronizacji.
- **Nie jest gotowe do automatycznej synchronizacji**: maszyna wirtualna nie będzie uruchamiana w wersji zaautomatycznej. Jeśli na przykład lokalna maszyna wirtualna VMware ma dołączone urządzenie zewnętrzne, takie jak dysk CD-ROM, operacja VMware VMotion zakończy się niepowodzeniem (w przypadku używania VMware VMotion).
- **Nieznane gotowość**: Azure Migrate nie mógł ustalić gotowości maszyny z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

Ocena serwera przegląduje właściwości maszyny w celu określenia gotowości platformy Azure na maszynę lokalną.

### <a name="machine-properties"></a>Właściwości maszyny

Ocena serwera sprawdza poniższą Właściwość lokalnej maszyny wirtualnej w celu ustalenia, czy można ją uruchomić na platformie Azure VMware (Automatyczna synchronizacja).


| **Właściwość** | **Szczegóły** | **Stan gotowości do automatycznej synchronizacji** 
| - | - | - 
| **Protokół internetowy** | Usługa AVS nie obsługuje obecnie adresowania internetowego IPv6.<br/><br/> Aby uzyskać wskazówki dotyczące rozwiązywania problemów w razie wykrycia protokołu IPv6 na maszynie, skontaktuj się z lokalnym zespołem MSFT AVS GBB.| Protokół internetowy gotowości warunkowej


### <a name="guest-operating-system"></a>System operacyjny gościa

Obecnie oceny automatycznej synchronizacji nie sprawdzają systemu operacyjnego w ramach analizy gotowości. Wszystkie systemy operacyjne uruchomione na lokalnych maszynach wirtualnych mogą być uruchamiane na platformie Azure VMware (Automatyczna synchronizacja).

Wraz z właściwościami maszyny wirtualnej Ocena serwera sprawdza system operacyjny gościa maszyn, aby określić, czy może on działać na platformie Azure.


## <a name="sizing"></a>Ustalanie rozmiaru

Gdy maszyna zostanie oznaczona jako gotowa do automatycznej synchronizacji, Ocena automatycznej synchronizacji w ocenie serwera powoduje zaleceń dotyczących rozmiarów węzłów, które obejmują identyfikowanie odpowiednich wymagań lokalnych maszyn wirtualnych i znalezienie łącznej liczby wymaganych węzłów synchronizacji. Zalecenia te różnią się w zależności od określonych właściwości oceny.

- Jeśli Ocena korzysta z *ustalania wielkości na podstawie wydajności*, Azure Migrate traktuje historię wydajności maszyny, aby wprowadzić odpowiednie zalecenia dotyczące zmiany wielkości dla automatycznej synchronizacji. Ta metoda jest szczególnie przydatna, jeśli masz nadmiernie przydzieloną lokalną maszynę wirtualną, ale wykorzystanie jest niskie i chcesz uzyskać informacje o odpowiednim rozmiarze maszyny wirtualnej w celu zaoszczędzenia kosztów. Ta metoda pomoże zoptymalizować rozmiary podczas migracji.
- Jeśli nie chcesz uwzględniać danych wydajności dotyczących rozmiarów maszyn wirtualnych i chcesz przełączyć maszyny lokalne w taki sposób, aby była automatyczna synchronizacja, możesz ustawić kryteria ustalania rozmiarów *jako lokalne*. Następnie Ocena serwera spowoduje rozmiary maszyn wirtualnych w oparciu o konfigurację lokalną, bez uwzględniania danych użycia. 


### <a name="ftt-sizing-parameters"></a>Parametry zmiany wielkości FTT

Aparat magazynu używany w ramach automatycznej synchronizacji to sieci vSAN. zasady magazynu sieci vSAN definiują wymagania dotyczące magazynu dla maszyn wirtualnych. Te zasady gwarantują wymagany poziom usług dla maszyn wirtualnych, ponieważ określają sposób przydzielania magazynu do maszyny wirtualnej. Dostępne są następujące kombinacje FTT-Raid: 

**Tolerowana liczba niepowodzeń** | **Konfiguracja RAID** | **Minimalna wymagana liczba hostów** | **Uwagi dotyczące rozmiaru**
--- | --- | --- | --- 
1 | RAID-1 (dublowanie) | 3 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 200 GB.
1 | RAID-5 (kodowanie wymazywania) | 4 | Maszyna wirtualna o pojemności 100 GB będzie korzystać ze 133,33 GB
2 | RAID-1 (dublowanie) | 5 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 300 GB.
2 | RAID-6 (kodowanie wymazywania) | 6 | Maszyna wirtualna o pojemności 100 GB będzie korzystać ze 150 GB.
3 | RAID-1 (dublowanie) | 7 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 400 GB.

### <a name="performance-based-sizing"></a>Ustalanie wielkości na podstawie wydajności

W przypadku ustalania rozmiarów na podstawie wydajności Ocena serwera rozpoczyna się od dysków podłączonych do maszyny wirtualnej, a następnie kart sieciowych. Następnie mapuje wymagania maszyny wirtualnej na odpowiednią liczbę węzłów w celu automatycznej synchronizacji. Urządzenie Azure Migrate profiluje lokalne środowisko do zbierania danych wydajności dotyczących procesora CPU, pamięci, dysków i karty sieciowej.

**Kroki zbierania danych wydajności:**

1. W przypadku maszyn wirtualnych VMware urządzenie Azure Migrate zbiera punkt próbkowania w czasie rzeczywistym co 20-sekundowy interwał. 
2. Urządzenie zbiera przykładowe punkty zebrane co 10 minut i wysyła maksymalną wartość dla ostatnich 10 minut do oceny serwera.
3. W ramach oceny serwera są przechowywane wszystkie 10-minutowe punkty próbkowania przez ostatni miesiąc. Następnie, w zależności od właściwości oceny określonych dla *historii wydajności* i *użycia percentylu*, identyfikuje odpowiedni punkt danych do użycia w celu zmiany wielkości liter. Na przykład jeśli historia wydajności jest ustawiona na 1 dzień, a wykorzystanie percentylu to używany 95. percentyl, Ocena serwera używa 10-minutowych punktów próbkowania w ciągu ostatniego dnia, sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentylu dla odpowiedniego wymiaru.
4. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla każdej metryki (użycie procesora CPU, użycie pamięci, operacje we/wy dysku (odczyt i zapis), przepływność dysku (odczyt i zapis) oraz przepływność sieci (w i wychodząca), które zbiera urządzenie.

Po ustaleniu wartości efektywnego wykorzystania magazyn, Sieć i rozmiar obliczeń są obsługiwane w następujący sposób.

**Rozmiar magazynu**: Azure Migrate używa całkowitej ilości lokalnego miejsca na dysku maszyny wirtualnej jako parametru obliczeń, aby określić wymagania dotyczące automatycznej synchronizacji sieci vSAN, oprócz ustawienia FTT wybranego przez klienta. FTT — niepowodzenia do tolerowania, a także wymaganie minimalnej liczby węzłów na FTT opcji spowoduje określenie całkowitego magazynu sieci vSAN wymaganego w połączeniu z wymaganiem dysku maszyny wirtualnej.

**Ustalanie rozmiaru sieci**: ocena serwera obecnie nie uwzględnia żadnych ustawień sieci na potrzeby ocen w usłudze AVS.

**Ustalanie wielkości obliczeń**: po obliczeniu wymagań dotyczących magazynu Ocena serwera uwzględnia wymagania dotyczące procesora i pamięci, aby określić liczbę węzłów wymaganych do automatycznej synchronizacji na podstawie typu węzła.

- W oparciu o kryteria ustalania wielkości oceny serwera są sprawdzane na podstawie wydajności lub konfiguracji lokalnej maszyny wirtualnej. Ustawienie współczynnika komfortu umożliwia określenie współczynnika wzrostu klastra. Obecnie domyślnie funkcja wielowątkowości jest włączona, więc 36 węzłów podstawowych będzie mieć 72 rdzenie wirtualne. 4 rdzenie wirtualne na fizyczne służą do ustalania progów procesora CPU dla klastra przy użyciu standardu VMware o wartości nieprzekraczającej 80% użycia, co pozwala na obsługę konserwacji lub niepowodzeń bez naruszania dostępności klastra. Obecnie nie ma dostępnego przesłonięcia, aby zmienić wartości nadsubskrypcji, a firma Microsoft może mieć tę możliwość w przyszłych wersjach.

### <a name="as-on-premises-sizing"></a>Zgodnie z rozmiarem lokalnym

Jeśli używasz *jako lokalizacji lokalnej*, Ocena serwera nie uwzględnia historii wydajności maszyn wirtualnych i dysków. Zamiast tego przydziela węzły automatycznej synchronizacji na podstawie rozmiaru przydzielonego lokalnie. Domyślny typ magazynu to sieci vSAN w wersji zaautomatycznej.

## <a name="confidence-ratings"></a>Klasyfikacje zaufania

Każda Ocena oparta na wydajności w Azure Migrate jest skojarzona z oceną zaufania z zakresu od jednej (najniższej) do pięciu gwiazdek (najwyższa).

- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate.
- Klasyfikacje zaufania nie mają zastosowania w przypadku ocen *lokalnych* .
- W przypadku ustalania wielkości na podstawie wydajności oceny wersji zabudowanej w ocenie serwera potrzebują danych użycia dla procesora CPU i pamięci maszyny wirtualnej. Następujące dane są zbierane, ale nie są używane w zastosowaniach zaleceń dotyczących automatycznej synchronizacji:
  - Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do maszyny wirtualnej.
  - We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do maszyny wirtualnej.

  Jeśli którykolwiek z tych numerów wykorzystania jest niedostępny w vCenter Server, zalecenie dotyczące rozmiaru może być niezawodne.

W zależności od wartości procentowej dostępnych punktów danych Ocena zaufania dla oceny przebiega w następujący sposób.


| **Dostępność punktów danych** | **Ocena zaufania** |
| - | - |
| 0-20% | 1 gwiazdka |
| 21-40% | 2 gwiazdki |
| 41-60% | 3 gwiazdki |
| 61-80% | 4 gwiazdki |
| 81-100% | 5 gwiazdek |

### <a name="low-confidence-ratings"></a>Oceny o niskiej pewności

Poniżej przedstawiono kilka powodów, dla których ocena może uzyskać klasyfikację o niskiej pewności:

- Twoje środowisko nie było profilem przez czas, w którym tworzysz ocenę. Jeśli na przykład utworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać co najmniej dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Niektóre maszyny wirtualne zostały wyłączone w okresie, dla którego została obliczona Ocena. Jeśli wszystkie maszyny wirtualne są wyłączone przez pewien czas, Ocena serwera nie może zebrać danych wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone w okresie, dla którego została obliczona Ocena. Na przykład jeśli utworzono ocenę dla historii wydajności w ostatnim miesiącu, ale niektóre maszyny wirtualne zostały utworzone w danym środowisku tylko tydzień temu, historia wydajności nowych maszyn wirtualnych nie będzie miała na cały czas trwania.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. Jeśli tego nie zrobisz, ustalanie rozmiarów na podstawie wydajności może być niezawodne. W takim przypadku zaleca się przełączenie oceny do rozmiarów lokalnych.

## <a name="monthly-cost-estimation"></a>Oszacowanie kosztu miesięcznego

Po zakończeniu ustalania rozmiarów Azure Migrate oblicza łączny koszt uruchamiania obciążeń lokalnych w ramach automatycznej synchronizacji, mnożąc liczbę węzłów synchronizacji wymaganych przez cenę węzła. Koszt na maszynę wirtualną jest obliczany przez podzielenie łącznego kosztu przez liczbę maszyn wirtualnych w ocenie. 
- Obliczenia pobierają liczbę wymaganych węzłów, typ węzła i lokalizację.
- Agreguje koszt we wszystkich węzłach, aby obliczyć łączny koszt miesięczny.
- Koszty są wyświetlane w walucie określonej w ustawieniach oceny.

Zgodnie z cennikiem rozwiązania Azure VMware (Automatyczna synchronizacja) na węzeł łączny koszt nie ma kosztu obliczeniowego i dystrybucji kosztów magazynu. [Więcej informacji](../azure-vmware/introduction.md)

Pamiętaj, że jako wersja zapoznawcza rozwiązanie Azure VMware (Automatyczna synchronizacja), ceny węzłów w ocenie są cenami wersji zapoznawczej. Aby uzyskać wskazówki, należy skontaktować się z lokalnym zespołem usługi MSFT GBB.

## <a name="migration-tool-guidance"></a>Wskazówki dotyczące narzędzia do migracji

W raporcie dotyczącym gotowości oceny usługi Azure VMware Solution (AVS) można zobaczyć następujące sugerowane narzędzia: 
- **VMware HCX lub Enterprise**: w przypadku maszyn VMware, rozwiązanie hybrydowe w chmurze VMware (HCX) to sugerowane narzędzie do migracji, które umożliwia migrowanie lokalnego obciążenia do chmury prywatnej platformy Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Nieznane**: w przypadku maszyn zaimportowanych za pośrednictwem pliku CSV domyślne narzędzie do migracji jest nieznane. Mimo że w przypadku maszyn VMware zaleca się użycie rozwiązania hybrydowego chmury VMware (HCX).

## <a name="next-steps"></a>Następne kroki

Utwórz ocenę dla [maszyn wirtualnych VMware o automatycznej synchronizacji](how-to-create-azure-vmware-solution-assessment.md).
