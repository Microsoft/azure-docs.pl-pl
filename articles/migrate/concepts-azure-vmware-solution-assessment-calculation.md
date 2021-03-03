---
title: Obliczenia dotyczące automatycznej synchronizacji w Azure Migrate | Microsoft Docs
description: Zawiera omówienie obliczeń oceny automatycznej synchronizacji w usłudze Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: b3975d30fca1f7f542f27742ef8408b1feecc146
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727196"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Przegląd oceny (Migrowanie do rozwiązania Azure VMware)

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń. Śledzi również wystąpienia chmury prywatnej i publicznej na platformie Azure. Centrum oferuje Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ocena serwera to narzędzie w Azure Migrate, które ocenia serwery lokalne na potrzeby migracji do maszyn wirtualnych platformy Azure IaaS i rozwiązania Azure VMware (Automatyczna synchronizacja). W tym artykule znajdują się informacje o sposobie obliczania ocen rozwiązania Azure VMware (Automatyczna synchronizacja).

> [!NOTE]
> Ocenę rozwiązań VMware (Automatyczna synchronizacja) można utworzyć tylko dla maszyn wirtualnych VMware.

## <a name="types-of-assessments"></a>Typy ocen

Oceny tworzone za pomocą Azure Migrate to migawka danych w danym momencie. Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate:

| **Typ oceny** | **Szczegóły** |
| - | - |
| **Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. Można ocenić serwery lokalne działające w środowisku[VMware](how-to-set-up-appliance-vmware.md) lub [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) oraz [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji na platformę Azure przy użyciu tego typu oceny. [Dowiedz się więcej](concepts-assessment-calculation.md) |
| **Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające Migrowanie lokalnych maszyn wirtualnych VMware lub serwerów do[ rozwiązania Azure VMware (Automatyczna synchronizacja)](../azure-vmware/introduction.md).  Możesz ocenić serwery lokalne działające w środowisku [VMware](how-to-set-up-appliance-vmware.md) , aby przeprowadzić migrację do rozwiązania Azure VMware (Automatyczna synchronizacja) przy użyciu tego typu oceny. [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md) |

Ocena rozwiązania Azure VMware (Automatyczna synchronizacja) oferuje dwie opcje kryteriów ustalania rozmiarów:

| **Ocena** | **Szczegóły** | **Dane** |
| - | - | - |
| **Na podstawie wydajności** | Ocenia na podstawie zebranych danych wydajności lokalnych maszyn wirtualnych. | **Zalecany rozmiar węzła**: w oparciu o dane użycia procesora CPU i pamięci oraz typ węzła, typ magazynu i ustawienie FTT wybrane do oceny. |
| **Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar węzła**: na podstawie rozmiaru lokalnego maszyny wirtualnej wraz z typem węzła, typem magazynu i USTAWIENIEm FTT, które zostało wybrane do oceny. |

## <a name="how-do-i-run-an-assessment"></a>Jak mogę uruchomić ocenę?

Istnieje kilka sposobów na przeprowadzenie oceny.

- Ocenianie maszyn przy użyciu metadanych serwera zbieranych przez uproszczone urządzenie Azure Migrate. Urządzenie umożliwia odnalezienie maszyn lokalnych. Następnie wysyła metadane maszyn i dane wydajności do Azure Migrate. Pozwala to na większą precyzję.
- Ocenianie maszyn przy użyciu metadanych serwera zaimportowanych w formacie wartości rozdzielanych przecinkami (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Jak mogę oceniać z urządzeniem?

Jeśli wdrażasz urządzenie Azure Migrate w celu odnajdywania serwerów lokalnych, wykonaj następujące czynności:

1. Skonfiguruj platformę Azure i środowisko lokalne do pracy z Azure Migrate.
2. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do oceny serwera.
3. Wdróż uproszczone urządzenie Azure Migrate. Urządzenie nieustannie odnajduje maszyny lokalne i wysyła metadane maszyn i dane wydajności do Azure Migrate. Wdróż urządzenie jako maszynę wirtualną. Nie musisz instalować żadnych elementów na maszynach, które chcesz ocenić.

Po rozpoczęciu odnajdywania urządzenia przez urządzenie można zbierać maszyny do oceny w grupie i uruchamiać ocenę dla grupy z typem oceny **rozwiązanie Azure VMware (Automatyczna synchronizacja)**.

Utwórz pierwszą ocenę rozwiązania Azure VMware (Automatyczna synchronizacja), wykonując kroki opisane [tutaj](how-to-create-azure-vmware-solution-assessment.md).

## <a name="how-do-i-assess-with-imported-data"></a>Jak mogę oceniać z zaimportowanymi danymi?

Jeśli oceniasz serwery przy użyciu pliku CSV, nie potrzebujesz urządzenia. Zamiast tego wykonaj następujące czynności:

1. Skonfiguruj platformę Azure do pracy z Azure Migrate.
2. W pierwszej ocenie Utwórz projekt platformy Azure i Dodaj do niego narzędzie do oceny serwera.
3. Pobierz szablon CSV i Dodaj do niego dane serwera.
4. Zaimportuj szablon do Azure Migrate.
5. Odnajdź serwery dodane podczas importowania, Zbierz je do grupy i uruchom ocenę dla grupy z typem oceny **rozwiązanie Azure VMware (Automatyczna synchronizacja)**.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane są zbierane przez urządzenie?

Jeśli używasz urządzenia Azure Migrate do oceny, Dowiedz się więcej na temat metadanych i danych wydajności zbieranych dla programu [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak urządzenie oblicza dane wydajności?

Jeśli urządzenie jest używane do odnajdywania, zbiera dane o wydajności dla ustawień obliczeń z następującymi krokami:

1. Urządzenie zbiera punkt próbkowania w czasie rzeczywistym.

   - **Maszyny wirtualne VMware**: punkt próbki jest zbierany co 20 sekund.
2. Urządzenie łączy przykładowe punkty, aby utworzyć jeden punkt danych co 10 minut. Aby utworzyć punkt danych, urządzenie wybiera wartości szczytowe ze wszystkich próbek. Następnie wysyła punkt danych do platformy Azure.
3. Azure Migrate przechowuje wszystkie 10-minutowe punkty danych w ciągu ostatniego miesiąca.
4. Podczas tworzenia oceny Ocena wskazuje odpowiedni punkt danych do użycia dla odpowiedniej zmiany. Identyfikator jest oparty na wartościach percentylu dla *historii wydajności* i *użycia percentylu*.

   - Jeśli na przykład historia wydajności wynosi tydzień, a użycie percentyla to używany 95. percentyl, Ocena sortuje 10-minutowe punkty próbkowania w ostatnim tygodniu. Sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentyl dla odpowiedniej zmiany.
   - Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
   - Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, wybierz 99 percentyl dla użycia percentyla.
5. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla tych metryk, które zbiera urządzenie:

   - Wykorzystanie procesora
   - Użycie pamięci RAM

Następujące dane dotyczące wydajności są zbierane, ale nie są używane w zastosowaniach zaleceń dotyczących oceny automatycznej synchronizacji:

- Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do maszyny wirtualnej.
- We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do maszyny wirtualnej.

## <a name="how-are-avs-assessments-calculated"></a>Jak są obliczane oceny automatycznej synchronizacji?

Ocena automatycznej synchronizacji używa danych o metadanych i wydajności maszyn lokalnych w celu obliczenia ocen. W przypadku wdrożenia urządzenia Azure Migrate oceny używa danych zbieranych przez urządzenie. Jednak w przypadku uruchomienia oceny zaimportowanej przy użyciu pliku CSV należy podać metadane dla obliczenia.

Obliczenia odbywają się w tych trzech etapach:

1. **Oblicz gotowość rozwiązania Azure VMware (Automatyczna synchronizacja)**: czy lokalne maszyny wirtualne są odpowiednie do migracji do rozwiązania Azure VMware (Automatyczna synchronizacja).
2. **Oblicz liczbę węzłów synchronizacji i użycia między węzłami**: Szacowana liczba węzłów synchronizacji wymaganych do uruchamiania maszyn wirtualnych VMware oraz przewidywany procesor CPU, pamięć i wykorzystanie magazynu we wszystkich węzłach.
3. **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty wszystkich węzłów platformy Azure VMware (Automatyczna synchronizacja) z uruchomionymi lokalnymi maszynami wirtualnymi.

Obliczenia są zgodne z poprzednią kolejnością. Serwer maszynowy przechodzi do późniejszego etapu tylko wtedy, gdy przekaże poprzednią. Jeśli na przykład serwer ulegnie awarii na etapie gotowości do automatycznej synchronizacji, zostanie oznaczony jako nieodpowiedni dla platformy Azure. Ustalanie wielkości i obliczeń kosztów nie jest wykonywane dla tego serwera

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Co to jest ocena rozwiązania Azure VMware (Automatyczna synchronizacja)?

Oto nowości zawarta w ocenie automatycznej synchronizacji:

| **Właściwość** | **Szczegóły** |
| - | - |
| **Lokalizacja docelowa** | Określa lokalizację chmury prywatnej automatycznej synchronizacji, do której chcesz przeprowadzić migrację. |
| **Typ magazynu** | Określa aparat magazynu, który ma być używany w ramach automatycznej synchronizacji. Automatyczna synchronizacja obsługuje obecnie tylko sieci vSAN jako domyślny typ magazynu, ale więcej opcji magazynu będzie można uzyskać zgodnie z harmonogramem. |
| **Wystąpienia zarezerwowane (RIs)** | Ta właściwość pomaga określić zarezerwowane wystąpienia w automatycznej synchronizacji, jeśli zakupiono i termin wystąpienia zarezerwowanego. Służy do obliczania kosztów. |
| **Typ węzła** | Określa [Typ węzła do automatycznej synchronizacji](../azure-vmware/concepts-private-clouds-clusters.md) używany do użycia na platformie Azure. Domyślny typ węzła to AV36. Więcej typów węzłów może być dostępnych w przyszłości.  Azure Migrate będzie wymagała wymaganej liczby węzłów do migracji maszyn wirtualnych do automatycznej synchronizacji. |
| **Ustawienie FTT, poziom RAID** | Określa prawidłową kombinację niepowodzeń dla kombinacji wartości dopuszczalnych i RAID. W przypadku wybrania opcji FTT połączonej z poziomem RAID i wymagania dotyczące lokalnego dysku maszyny wirtualnej zostaną określone łączne magazyny sieci vSAN wymagane w ramach automatycznej synchronizacji. Łączny dostępny magazyn po obliczeniach obejmuje również miejsce zarezerwowane dla obiektów zarządzania, takich jak vCenter i b) 25% zapasu magazynu wymaganego dla operacji sieci vSAN. |
| **Kryterium ustalania wielkości** | Ustawia kryteria do użycia w celu określenia pamięci, wymagania dotyczące procesora i magazynu dla węzłów automatycznej synchronizacji. Możesz wybrać opcję ustalania rozmiarów na *podstawie wydajności* lub *jako lokalne* , bez uwzględniania historii wydajności. Aby po prostu podnieść i przesunąć wybór jako lokalny. Aby uzyskać dostęp oparty na użyciu, wybierz pozycję wydajność na podstawie. |
| **Historia wydajności** | Określa czas, jaki należy wziąć pod uwagę podczas oceniania danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium ustalania wielkości jest *oparte na wydajności*. |
| **Użycie percentyla** | Określa wartość percentylości zestawu próbek wydajności, który ma być brany pod uwagę w przypadku zmiany wielkości liter. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest oparty na wydajności. |
| **Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Ten bufor jest stosowany na podstawie danych użycia maszyny dla maszyn wirtualnych (procesor CPU, pamięć i dysk). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości. Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. |
| **Oferta** | Wyświetla [ofertę platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , która jest zarejestrowana. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty. |
| **Waluta** | Przedstawia walutę rozliczeń dla Twojego konta. |
| **Rabat (%)** | Wyświetla rabat związany z subskrypcją, który otrzymujesz w górnej części oferty platformy Azure. Ustawienie domyślne to 0%. |
| **Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz program Software Assurance i kwalifikujesz się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Mimo że nie ma to wpływu na ceny rozwiązań VMware platformy Azure ze względu na cenę opartą na węzłach, klienci mogą nadal korzystać z lokalnych licencji systemu operacyjnego lub SQL (opartych na programie) w ramach automatycznej synchronizacji przy użyciu korzyści hybrydowych platformy Azure. Inni dostawcy systemu operacyjnego oprogramowania będą musieli podać własne warunki licencjonowania, takie jak RHEL. |
| **vCPU nadsubskrypcji** | Określa stosunek liczby rdzeni wirtualnych związanych z jednym rdzeniem fizycznym w węźle automatyczna synchronizacja. Wartość domyślna w obliczeniach to 4 vCPU: 1 rdzeń fizyczny w wersji zaautomatycznej. Użytkownicy interfejsu API mogą ustawić tę wartość jako liczbę całkowitą. Należy zauważyć, że vCPU nad> 4:1 może mieć wpływ na obciążenia, w zależności od użycia procesora CPU. Podczas ustalania rozmiarów zawsze przyjmuje się użycie dla wybranych rdzeni 100%. |
| **Współczynnik zatwierdzeń pamięci** | Określa stosunek pamięci przez zatwierdzenie w klastrze. Wartość 1 reprezentuje 100% wykorzystania pamięci, 0,5 na przykład jest 50%, a 2 będzie używać 200% dostępnej pamięci. Można dodawać tylko wartości z prze0,5 do 10 do jednego miejsca dziesiętnego. |
| **Deduplikowanie i współczynnik kompresji** | Określa, że przewidywany współczynnik deduplikacji i kompresji dla obciążeń. Rzeczywistą wartość można uzyskać z lokalnej konfiguracji sieci vSAN lub magazynu. Różnice te różnią się w zależności od obciążenia. Wartość 3 oznacza, że dla dysku 300 GB zostanie użyty dysk o pojemności 100 GB. Wartość 1 oznacza brak dekompresu lub kompresji. Można dodawać wartości od 1 do 10 do jednego miejsca dziesiętnego. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analiza przydatności rozwiązań VMware na platformie Azure (Automatyczna synchronizacja)

Ocena wersji zapoznaj się z oceną poszczególnych lokalnych maszyn wirtualnych w celu zapewnienia jej przydatności do automatycznej synchronizacji, przeglądając właściwości maszyny. Przypisuje także każdej ocenianej maszynie do jednej z następujących kategorii przydatności:

- **Gotowe do automatycznej synchronizacji**: maszynę można migrować na platformę Azure (Automatyczna synchronizacja) bez wprowadzania żadnych zmian. Rozpocznie się to za pomocą automatycznej synchronizacji z pełną obsługą wersji zaautomatycznej.
- **Gotowe z warunkami**: maszyna wirtualna może mieć problemy ze zgodnością z bieżącą wersją vSphere, a także wymaganie narzędzi VMware i innych ustawień, zanim będzie można uzyskać pełną funkcjonalność z maszyny wirtualnej w ramach automatycznej synchronizacji.
- **Nie jest gotowe do automatycznej synchronizacji**: maszyna wirtualna nie będzie uruchamiana w wersji zaautomatycznej. Jeśli na przykład lokalna maszyna wirtualna VMware ma dołączone urządzenie zewnętrzne, takie jak dysk CD-ROM, operacja VMware VMotion zakończy się niepowodzeniem (w przypadku używania VMware VMotion).
- **Nieznane gotowość**: Azure Migrate nie mógł ustalić gotowości maszyny z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

Ocenia przegląd właściwości maszyny w celu określenia gotowości platformy Azure na maszynę lokalną.

### <a name="machine-properties"></a>Właściwości maszyny

Ocena ocenia poniższą Właściwość lokalnej maszyny wirtualnej w celu ustalenia, czy może ona być uruchamiana w rozwiązaniu Azure VMware (Automatyczna synchronizacja).

| **Właściwość** | **Szczegóły** | **Stan gotowości do automatycznej synchronizacji** |
| - | - | - |
| **Protokół internetowy** | Na platformie Azure obecnie nie jest obsługiwane adresowanie internetowe protokołu IPv6. Aby uzyskać wskazówki dotyczące rozwiązywania problemów w razie wykrycia protokołu IPv6 na maszynie, skontaktuj się z lokalnym zespołem MSFT AVS GBB. | Protokół internetowy gotowości warunkowej |

### <a name="guest-operating-system"></a>System operacyjny gościa

Obecnie oceny automatycznej synchronizacji nie sprawdzają systemu operacyjnego w ramach analizy gotowości. Wszystkie systemy operacyjne uruchomione na lokalnych maszynach wirtualnych mogą być uruchamiane na platformie Azure VMware (Automatyczna synchronizacja).

Wraz z właściwościami maszyny wirtualnej Ocena jest sprawdzana w systemie operacyjnym gościa maszyn, aby określić, czy można je uruchomić na platformie Azure.

## <a name="sizing"></a>Ustalanie rozmiaru

Gdy maszyna zostanie oznaczona jako gotowa do automatycznej synchronizacji, Ocena wersji zapoznaj się z zaleceniami dotyczącymi rozmiarów węzłów, które obejmują identyfikowanie odpowiednich wymagań lokalnych maszyn wirtualnych i znalezienie łącznej liczby wymaganych węzłów synchronizacji. Zalecenia te różnią się w zależności od określonych właściwości oceny.

- Jeśli Ocena korzysta z *ustalania wielkości na podstawie wydajności*, Azure Migrate traktuje historię wydajności maszyny, aby wprowadzić odpowiednie zalecenia dotyczące zmiany wielkości dla automatycznej synchronizacji. Ta metoda jest szczególnie przydatna, jeśli masz nadmiernie przydzieloną lokalną maszynę wirtualną, ale wykorzystanie jest niskie i chcesz uzyskać informacje o odpowiednim rozmiarze maszyny wirtualnej w celu zaoszczędzenia kosztów. Ta metoda pomoże zoptymalizować rozmiary podczas migracji.
- Jeśli nie chcesz uwzględniać danych wydajności dla zmiany rozmiarów maszyn wirtualnych i chcesz przełączyć maszyny lokalne w taki sposób, aby była automatyczna synchronizacja, możesz ustawić kryteria ustalania wielkości na * jako lokalne *. Następnie Ocena zmieni rozmiar maszyn wirtualnych w oparciu o konfigurację lokalną, bez uwzględniania danych użycia.

### <a name="ftt-sizing-parameters"></a>Parametry zmiany wielkości FTT

Aparat magazynu używany w ramach automatycznej synchronizacji to sieci vSAN. zasady magazynu sieci vSAN definiują wymagania dotyczące magazynu dla maszyn wirtualnych. Te zasady gwarantują wymagany poziom usług dla maszyn wirtualnych, ponieważ określają sposób przydzielania magazynu do maszyny wirtualnej. Dostępne są następujące kombinacje FTT-Raid:

| **Tolerowana liczba niepowodzeń** | **Konfiguracja RAID** | **Minimalna wymagana liczba hostów** | **Uwagi dotyczące rozmiaru** |
| - | - | - | - |
| 1 | RAID-1 (dublowanie) | 3 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 200 GB. |
| 1 | RAID-5 (kodowanie wymazywania) | 4 | Maszyna wirtualna o pojemności 100 GB będzie korzystać ze 133,33 GB |
| 2 | RAID-1 (dublowanie) | 5 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 300 GB. |
| 2 | RAID-6 (kodowanie wymazywania) | 6 | Maszyna wirtualna o pojemności 100 GB będzie korzystać ze 150 GB. |
| 3 | RAID-1 (dublowanie) | 7 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 400 GB. |

### <a name="performance-based-sizing"></a>Ustalanie wielkości na podstawie wydajności

W przypadku ustalania rozmiarów opartych na wydajności Azure Migrate profiluje lokalne środowisko do zbierania danych wydajności dla procesora CPU, pamięci i dysku. W związku z tym, ustalanie rozmiarów na podstawie wydajności dla automatycznej synchronizacji będzie uwzględniać przydzielone miejsce na dysku i użycie wybranego percentyla pamięci i procesora CPU. Na przykład jeśli maszyna wirtualna ma przydzieloną 4vCores, ale tylko 25%, automatyczna synchronizacja wersji będzie mieć rozmiar dla 1vCore dla tej maszyny wirtualnej.

**Kroki zbierania danych wydajności:**

1. W przypadku maszyn wirtualnych VMware urządzenie Azure Migrate zbiera punkt próbkowania w czasie rzeczywistym co 20-sekundowy interwał.
2. Urządzenie zbiera przykładowe punkty zebrane co 10 minut i wysyła maksymalną wartość dla ostatnich 10 minut do Azure Migrate.
3. Azure Migrate przechowuje wszystkie 10-minutowe punkty próbkowania dla ostatniego miesiąca. Następnie, w zależności od właściwości oceny określonych dla *historii wydajności* i *użycia percentylu*, identyfikuje odpowiedni punkt danych do użycia w celu zmiany wielkości liter. Na przykład jeśli historia wydajności jest ustawiona na 1 dzień, a wykorzystanie percentylu to używany 95. percentyl, Azure Migrate używa 10-minutowych punktów próbkowania w ciągu ostatniego dnia, sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentylu dla odpowiedniego ustawienia.
4. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla każdej metryki (użycie procesora CPU i użycie pamięci) zbierane przez urządzenie.

Po ustaleniu wartości efektywnego wykorzystania magazyn, Sieć i rozmiar obliczeń są obsługiwane w następujący sposób.

**Rozmiar magazynu**: Azure Migrate używa całkowitej ilości lokalnego miejsca na dysku maszyny wirtualnej jako parametru obliczeń, aby określić wymagania dotyczące automatycznej synchronizacji sieci vSAN, oprócz ustawienia FTT wybranego przez klienta. FTT — niepowodzenia do tolerowania, a także wymaganie minimalnej liczby węzłów na FTT opcji spowoduje określenie całkowitego magazynu sieci vSAN wymaganego w połączeniu z wymaganiem dysku maszyny wirtualnej. Wykorzystanie magazynu nie jest obecnie brane pod uwagę, a logika sprawdza tylko przydzieloną ilość pamięci na maszynę wirtualną.

**Ustalanie wielkości sieci**: obecnie oceny wersji zaautomatycznej nie są uwzględniane żadne ustawienia sieci.

**Ustalanie wielkości obliczeń**: po obliczeniu wymagań dotyczących magazynu, Ocena automatycznej synchronizacji uwzględnia wymagania dotyczące procesora i pamięci, aby określić liczbę węzłów wymaganych do automatycznej synchronizacji na podstawie typu węzła.

- W oparciu o kryteria ustalania wersji, Ocena automatycznej synchronizacji analizuje dane maszyn wirtualnych oparte na wydajności lub konfigurację lokalnej maszyny wirtualnej. Ustawienie współczynnika komfortu umożliwia określenie współczynnika wzrostu klastra. Obecnie domyślnie funkcja wielowątkowości jest włączona, więc 36 węzłów podstawowych będzie mieć 72 rdzenie wirtualne. 4 rdzenie wirtualne na fizyczne służą do ustalania progów procesora CPU dla klastra przy użyciu standardu VMware o wartości nieprzekraczającej 80% użycia, co pozwala na obsługę konserwacji lub niepowodzeń bez naruszania dostępności klastra. Obecnie nie ma dostępnego przesłonięcia, aby zmienić wartości nadsubskrypcji, a firma Microsoft może mieć tę możliwość w przyszłych wersjach.

### <a name="as-on-premises-sizing"></a>Zgodnie z rozmiarem lokalnym

Jeśli używasz *jako lokalnego określania rozmiarów*, Ocena automatycznej synchronizacji nie uwzględnia historii wydajności maszyn wirtualnych i dysków. Zamiast tego przydziela węzły automatycznej synchronizacji na podstawie rozmiaru przydzielonego lokalnie. Domyślny typ magazynu to sieci vSAN w wersji zaautomatycznej.

[Dowiedz się więcej](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) na temat sposobu przeglądania oceny rozwiązań VMware platformy Azure.

### <a name="cpu-utilization-on-avs-nodes"></a>Użycie procesora w węzłach automatyczna synchronizacja

Użycie procesora założono na 100% użycia dostępnych rdzeni. Aby zmniejszyć liczbę wymaganych węzłów, można zwiększyć nadsubskrypcję z 4:1 na wypowiedzenie 6:1 na podstawie charakterystyki obciążenia i środowiska lokalnego. W przeciwieństwie do dysku, na potrzeby automatycznej synchronizacji nie są stosowane żadne ograniczenia dotyczące użycia procesora CPU, dlatego klienci muszą upewnić się, że klaster działa optymalnie, tak aby odpowiednio dopasować "Uruchamianie gorąca". Aby zapewnić większy pokój do wzrostu, Zmniejsz nadsubskrypcję lub Zwiększ wartość współczynnika wzrostu.

Użycie procesora CPU również już dotyczy obciążeń związanych z zarządzaniem przez program vCenter, NSX Manager i inne mniejsze zasoby.

### <a name="memory-utilization-on-avs-nodes"></a>Użycie pamięci w węzłach automatycznej synchronizacji

Użycie pamięci pokazuje łączną ilość pamięci ze wszystkich węzłów a wymagania dotyczące serwera lub obciążeń. Ilość pamięci może być w pełni subskrybowana i ponowna synchronizacja nie obejmuje żadnych limitów i jest klientem, aby zapewnić optymalną wydajność klastra dla obciążeń.

Użycie pamięci również już dotyczy kosztów związanych z zarządzaniem przez program vCenter, NSX Manager i inne mniejsze zasoby.

### <a name="storage-utilization-on-avs-nodes"></a>Wykorzystanie magazynu w węzłach automatycznej synchronizacji

Użycie magazynu jest obliczane na podstawie następującej sekwencji:

1. Wymagany rozmiar maszyny wirtualnej (przydzielony jako lub ilość zajętego miejsca na podstawie wydajności)
2. Zastosuj współczynnik wzrostu, jeśli istnieje
3. Dodawanie narzutów związanych z zarządzaniem i stosowanie współczynnika FTT
4. Zastosuj kompresję i współczynnik kompresji
5. Zastosuj wymagany 25% zapasu czasu dla sieci vSAN
6. Wyniki dostępnego magazynu dla maszyn wirtualnych z całkowitego magazynu, w tym narzuty zarządzania.

Dostępny magazyn w klastrze z 3 węzłami będzie oparty na domyślnych zasadach magazynu, które są RAID-1 i używają szerokiej aprowizacji. Podczas obliczania dla wymazywania kodu lub RAID-5 na przykład wymagane jest co najmniej 4 węzły. Należy pamiętać, że w obszarze automatyczna synchronizacja zasady magazynu należy zmienić w celu zapewnienia większej ilości miejsca.

## <a name="confidence-ratings"></a>Klasyfikacje zaufania

Każda Ocena oparta na wydajności w Azure Migrate jest skojarzona z oceną zaufania z zakresu od jednej (najniższej) do pięciu gwiazdek (najwyższa).

- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate.
- Klasyfikacje zaufania nie mają zastosowania w przypadku ocen *lokalnych* .
- W przypadku ustalania wielkości na podstawie wydajności oceny automatycznej synchronizacji wymagają danych użycia dla procesora CPU i pamięci maszyn wirtualnych. Następujące dane są zbierane, ale nie są używane w zastosowaniach zaleceń dotyczących automatycznej synchronizacji:

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

- Twoje środowisko nie było profilem przez czas, w którym tworzysz ocenę. Jeśli na przykład zostanie utworzona Ocena z czasem trwania wydajności ustawionym na jeden dzień, należy poczekać co najmniej dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Ocena nie jest w stanie zebrać danych wydajności dla niektórych lub wszystkich maszyn wirtualnych w okresie oceny. W celu uzyskania oceny o wysokiej pewności upewnij się, że:

  - Maszyny wirtualne są zasilane na czas trwania oceny
  - Połączenia wychodzące na portach 443 są dozwolone
  - Dla pamięci dynamicznej maszyn wirtualnych funkcji Hyper-V jest włączona

  Użyj opcji „Oblicz ponownie”, aby uwzględnić najnowsze zmiany w ocenie ufności.
- Niektóre maszyny wirtualne zostały utworzone w czasie, w którym obliczono ocenę. Załóżmy na przykład, że utworzono ocenę historii wydajności w ostatnim miesiącu, ale niektóre maszyny wirtualne zostały utworzone tylko przez tydzień temu. W tym przypadku dane wydajności dla nowych maszyn wirtualnych nie będą dostępne przez cały czas trwania i ocena ufności będzie niska.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. Jeśli tego nie zrobisz, ustalanie rozmiarów na podstawie wydajności może być niezawodne. W takim przypadku zaleca się przełączenie oceny do rozmiarów lokalnych.

## <a name="monthly-cost-estimation"></a>Oszacowanie kosztu miesięcznego

Po zakończeniu ustalania rozmiarów Azure Migrate oblicza łączny koszt uruchamiania obciążeń lokalnych w ramach automatycznej synchronizacji, mnożąc liczbę węzłów synchronizacji wymaganych przez cenę węzła. Koszt na maszynę wirtualną jest obliczany przez podzielenie łącznego kosztu przez liczbę maszyn wirtualnych w ocenie.

- Obliczenia pobierają liczbę wymaganych węzłów, typ węzła i lokalizację.
- Agreguje koszt we wszystkich węzłach, aby obliczyć łączny koszt miesięczny.
- Koszty są wyświetlane w walucie określonej w ustawieniach oceny.

Zgodnie z cennikiem rozwiązania Azure VMware (Automatyczna synchronizacja) na węzeł łączny koszt nie ma kosztu obliczeniowego i dystrybucji kosztów magazynu. [Więcej informacji](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>Wskazówki dotyczące narzędzia do migracji

W raporcie dotyczącym gotowości oceny usługi Azure VMware Solution (AVS) można zobaczyć następujące sugerowane narzędzia:

- **VMware HCX lub Enterprise**: w przypadku maszyn VMware, rozwiązanie hybrydowe w chmurze VMware (HCX) to sugerowane narzędzie do migracji, które umożliwia migrowanie lokalnego obciążenia do chmury prywatnej platformy Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Nieznane**: w przypadku maszyn zaimportowanych za pośrednictwem pliku CSV domyślne narzędzie do migracji jest nieznane. Mimo że w przypadku maszyn VMware zaleca się użycie rozwiązania hybrydowego chmury VMware (HCX).

## <a name="next-steps"></a>Następne kroki

Utwórz ocenę dla [maszyn wirtualnych VMware o automatycznej synchronizacji](how-to-create-azure-vmware-solution-assessment.md).
