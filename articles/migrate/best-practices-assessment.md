---
title: Najlepsze rozwiązania z zakresu oceny w Azure Migrate oceny serwera
description: Wskazówki dotyczące tworzenia ocen przy użyciu oceny Azure Migrate Server.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: e007f0272a693f5117b0182dad82de2f4a6e252a
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576884"
---
# <a name="best-practices-for-creating-assessments"></a>Najlepsze rozwiązania dotyczące tworzenia ocen

[Azure Migrate](./migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm.

W tym artykule przedstawiono podsumowanie najlepszych rozwiązań dotyczących tworzenia ocen przy użyciu narzędzia do oceny Azure Migrate Server.

## <a name="about-assessments"></a>Informacje o ocenach

Oceny tworzone przy użyciu oceny Azure Migrate Server są migawką danych w danym momencie. Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: Ocena serwera:

**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md), [maszyny wirtualne funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) i [serwery fizyczne](how-to-set-up-appliance-physical.md) pod kątem migracji na platformę Azure. [Dowiedz się więcej](concepts-assessment-calculation.md)
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Za pomocą tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Kryterium określania rozmiaru
Narzędzie Server Assessment oferuje dwie opcje kryteriów określania rozmiaru:

**Kryterium określania rozmiaru** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny zawierające rekomendacje na podstawie zebranych danych dotyczących wydajności | **Ocena maszyny wirtualnej platformy Azure**: Rekomendacja dotycząca rozmiaru maszyny wirtualnej jest oparta na danych użycia procesora i pamięci.<br/><br/> Rekomendacja dotycząca typu dysku (dysk HDD/SSD w warstwie Standardowa lub dyski zarządzane w warstwie Premium) jest oparta na liczbie operacji we/wy na sekundę i przepływności dysków lokalnych.<br/><br/> **Ocena usługi Azure VMware Solution (AVS)** : Rekomendacja dotycząca węzłów usługi AVS jest oparta na danych użycia procesora i pamięci.
**Zgodnie ze środowiskiem lokalnym** | Oceny, które nie wykorzystują danych wydajności w celu wydawania rekomendacji. | **Ocena maszyny wirtualnej platformy Azure**: Rekomendacja dotycząca rozmiaru maszyny wirtualnej jest oparta na lokalnym rozmiarze maszyny wirtualnej<br/><br> Zalecany typ dysku jest określany na podstawie tego, co zostało wybrane w ustawieniu typu magazynu na potrzeby oceny.<br/><br/> **Ocena usługi Azure VMware Solution (AVS)** : Rekomendacja dotycząca węzłów usługi AVS jest oparta na lokalnym rozmiarze maszyny wirtualnej.

#### <a name="example"></a>Przykład
Jeśli na przykład masz lokalną maszynę wirtualną z czterema rdzeniami na poziomie 20% i ilość pamięci wynoszącą 8 GB i 10% wykorzystania, Ocena maszyny wirtualnej platformy Azure będzie następująca:

- **Ocena oparta na wydajności**:
    - Identyfikuje efektywne rdzenie i pamięć w oparciu o rdzeń (4 x 0,20 = 0,8) i pamięć (8 GB x 0,10 = 0,8) wykorzystanie.
    - Stosuje współczynnik komfortu określony we właściwościach oceny (powiedzmy 1.3 x), aby uzyskać wartości, które mają być używane do ustalania rozmiarów. 
    - Zaleca najbliższy rozmiar maszyny wirtualnej na platformie Azure, który może obsługiwać 1,04 rdzeni (0,8 x 1,3) i ~ 1,04 GB (0,8 x 1,3) pamięci.

- **W przypadku oceny stanu lokalnego**:
    -  Zaleca maszynę wirtualną z czterema rdzeniami; 8 GB pamięci.


## <a name="best-practices-for-creating-assessments"></a>Najlepsze rozwiązania dotyczące tworzenia ocen

Urządzenie Azure Migrate ciągle profiluje środowisko lokalne i wysyła metadane oraz dane wydajności do platformy Azure. Postępuj zgodnie z następującymi najlepszymi rozwiązaniami dotyczącymi ocen serwerów odnalezionych przy użyciu urządzenia:

- **Utwórz ocenę jako przewidzianą**w ramach oceny: możesz utworzyć jako ocenę, gdy maszyny będą widoczne w portalu Azure Migrate.
- **Tworzenie oceny opartej na wydajności**: po skonfigurowaniu odnajdywania zalecamy zaczekanie co najmniej dnia przed uruchomieniem oceny opartej na wydajności:
    - Trwa zbieranie danych dotyczących wydajności. Oczekiwanie co najmniej każdego dnia zapewnia wystarczającą ilość punktów danych wydajności przed uruchomieniem oceny.
    - W przypadku uruchamiania ocen opartych na wydajności należy upewnić się, że w środowisku jest profilowanie czasu trwania oceny. Jeśli na przykład zostanie utworzona Ocena z ustawionym przez tydzień czasem trwania wydajności, należy poczekać przez co najmniej tydzień po rozpoczęciu odnajdywania dla wszystkich punktów danych, które mają być zbierane. Jeśli tego nie zrobisz, ocena nie spowoduje pobrania klasyfikacji z pięcioma gwiazdkami.
- **Oblicz ponownie oceny**: ponieważ oceny są migawkami w czasie, nie są automatycznie aktualizowane przy użyciu najnowszych danych. Aby zaktualizować ocenę przy użyciu najnowszych danych, należy ją ponownie obliczyć.

Postępuj zgodnie z następującymi najlepszymi rozwiązaniami dotyczącymi ocen serwerów zaimportowanych do Azure Migrate za pośrednictwem programu. Plik CSV:

- **Utwórz ocenę jako przewidzianą**w ramach oceny: możesz utworzyć jako ocenę, gdy maszyny będą widoczne w portalu Azure Migrate.
- **Tworzenie oceny opartej na wydajności**: Dzięki temu można uzyskać lepszy szacunkowy koszt, szczególnie w przypadku nadmiernej aprowizacji zasobów serwera w środowisku lokalnym. Jednak dokładność oceny bazującej na wydajności zależy od danych wydajności określonych przez użytkownika dla serwerów. 
- **Oblicz ponownie oceny**: ponieważ oceny są migawkami w czasie, nie są automatycznie aktualizowane przy użyciu najnowszych danych. Aby zaktualizować ocenę przy użyciu najnowszych zaimportowanych danych, należy ją ponownie obliczyć.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>FTT parametry zmiany wielkości dla oceny automatycznej synchronizacji

Aparat magazynu używany w ramach automatycznej synchronizacji to sieci vSAN. Zasady magazynu vSAN definiują wymagania dotyczące magazynu dla maszyn wirtualnych. Te zasady gwarantują wymagany poziom usług dla maszyn wirtualnych, ponieważ określają sposób przydzielania magazynu do maszyny wirtualnej. Dostępne są następujące kombinacje FTT-RAID: 

**Tolerowana liczba niepowodzeń** | **Konfiguracja RAID** | **Minimalna wymagana liczba hostów** | **Uwagi dotyczące rozmiaru**
--- | --- | --- | --- 
1 | RAID-1 (dublowanie) | 3 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 200 GB.
1 | RAID-5 (kodowanie wymazywania) | 4 | Maszyna wirtualna o pojemności 100 GB będzie korzystać ze 133,33 GB
2 | RAID-1 (dublowanie) | 5 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 300 GB.
2 | RAID-6 (kodowanie wymazywania) | 6 | Maszyna wirtualna o pojemności 100 GB będzie korzystać ze 150 GB.
3 | RAID-1 (dublowanie) | 7 | Maszyna wirtualna o pojemności 100 GB będzie korzystać z 400 GB.


## <a name="best-practices-for-confidence-ratings"></a>Najlepsze rozwiązania dotyczące klasyfikacji zaufania

Po uruchomieniu ocen opartych na wydajności, Ocena zaufania z 1-gwiazdka (najniższa) do 5-gwiazdka (najwyższa) jest przyznawana dla oceny. Aby efektywnie używać klasyfikacji zaufania:
- Azure Migrate oceny serwera wymaga danych użycia procesora CPU/pamięci maszyn wirtualnych.
- Dla każdego dysku podłączonego do lokalnej maszyny wirtualnej wymagane są dane operacji we/wy odczytu/zapisu.
- Dla każdej karty sieciowej podłączonej do maszyny wirtualnej potrzebuje ona danych z sieci.

W zależności od wartości procentowej punktów danych dostępnych dla wybranego czasu trwania Ocena zaufania dla oceny jest pokazana w poniższej tabeli.

   **Dostępność punktu danych** | **Ocena zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek


## <a name="common-assessment-issues"></a>Typowe problemy z oceną

Poniżej przedstawiono sposób rozwiązywania niektórych typowych problemów ze środowiskiem, które mają wpływ na oceny.

###  <a name="out-of-sync-assessments"></a>Oceny braku synchronizacji

Jeśli dodasz lub usuniesz maszyny z grupy po utworzeniu oceny, utworzona Ocena zostanie oznaczona jako **niezsynchronizowana**. Uruchom ocenę ponownie, aby**Recalculate**odzwierciedlić zmiany grupy.

### <a name="outdated-assessments"></a>Nieaktualne oceny

Jeśli istnieją lokalne zmiany w maszynach wirtualnych, które znajdują się w grupie, która została oceniona, ocena jest oznaczona jako **nieaktualna**. Ocenę można oznaczyć jako "nieaktualne" z powodu co najmniej jednej zmiany w poniższych właściwościach:

- Liczba rdzeni procesora
- Przydzieloną pamięć
- Typ rozruchu lub oprogramowanie układowe
- Nazwa, wersja i architektura systemu operacyjnego
- Liczba dysków
- Liczba kart sieciowych
- Zmiana rozmiaru dysku (przydzielono GB)
- Aktualizacja właściwości karty sieciowej. Przykład: zmiany adresów MAC, Dodawanie adresów IP itp.

Uruchom ocenę ponownie, aby**Recalculate**odzwierciedlić zmiany.

### <a name="low-confidence-rating"></a>Ocena niskiej pewności

Ocena może nie zawierać wszystkich punktów danych z kilku powodów:

- Nie profilujesz swojego środowiska przez czas trwania, dla którego tworzysz ocenę. Jeśli na przykład tworzysz *ocenę wydajnościową* z upływem czasu trwania wydajności ustawioną na jeden tydzień, musisz poczekać przez co najmniej tydzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania. Zawsze można kliknąć pozycję **Oblicz ponownie** , aby wyświetlić najnowszą stosowną ocenę zaufania. Klasyfikacja zaufania ma zastosowanie tylko w przypadku tworzenia oceny *opartej na wydajności* .

- Kilka maszyn wirtualnych zostało wyłączonych w czasie, dla którego jest obliczana ocena. Jeśli niektóre maszyny wirtualne zostały odłączona od zasilania na pewien czas, narzędzie Server Assessment nie będzie mogło zebrać danych o wydajności dla tego okresu.

- Kilka maszyn wirtualnych zostało utworzonych po uruchomieniu odnajdywania w narzędziu Server Assessment. Jeśli na przykład tworzysz ocenę dla historii wydajności za ostatni miesiąc, ale kilka maszyn wirtualnych zostało utworzonych w środowisku tylko tydzień temu. W tym przypadku dane wydajności dla nowych maszyn wirtualnych nie będą dostępne przez cały czas trwania i ocena ufności będzie niska.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Wskazówki dotyczące narzędzia do migracji dotyczące ocen dotyczących automatycznej synchronizacji

W raporcie dotyczącym gotowości oceny usługi Azure VMware Solution (AVS) można zobaczyć następujące sugerowane narzędzia: 
- **VMware HCX lub Enterprise**: w przypadku maszyn VMware, rozwiązanie hybrydowe w chmurze VMware (HCX) to sugerowane narzędzie do migracji, które umożliwia migrowanie lokalnego obciążenia do chmury prywatnej platformy Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Nieznane**: w przypadku maszyn zaimportowanych za pośrednictwem pliku CSV domyślne narzędzie do migracji jest nieznane. Mimo że w przypadku maszyn VMware zaleca się korzystanie z rozwiązania hybrydowego chmury VMware (HCX).


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się](concepts-assessment-calculation.md) , w jaki sposób są obliczane oceny.
- [Dowiedz się](how-to-modify-assessment.md) , jak dostosować ocenę.
