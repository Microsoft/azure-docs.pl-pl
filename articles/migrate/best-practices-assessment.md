---
title: Najlepsze rozwiązania w zakresie oceny Azure Migrate narzędzia do odnajdywania i oceny
description: Porady dotyczące tworzenia ocen przy użyciu narzędzia do odnajdywania i oceny Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: fac488ba1881b6b79139eaf2468237e546737177
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077334"
---
# <a name="best-practices-for-creating-assessments"></a>Najlepsze rozwiązania dotyczące tworzenia ocen

[Azure Migrate](./migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm.

W tym artykule przedstawiono podsumowanie najlepszych rozwiązań dotyczących tworzenia ocen przy użyciu narzędzia do odnajdywania i oceny Azure Migrate.

Oceny tworzone przy użyciu Azure Migrate: narzędzie odnajdywania i oceny to migawka danych w danym momencie. Istnieją trzy typy ocen, które można utworzyć przy użyciu Azure Migrate: odnajdywania i oceny:

**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Możesz ocenić serwery lokalne w środowisku [VMware](how-to-set-up-appliance-vmware.md) i [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) oraz [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji na platformę Azure przy użyciu tego typu oceny. [Dowiedz się więcej](concepts-assessment-calculation.md)
**Azure SQL** | Ocenianie migracji lokalnych serwerów SQL ze środowiska VMware do Azure SQL Database lub wystąpienia zarządzanego Azure SQL. [Więcej informacji](concepts-azure-sql-assessment-calculation.md)
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Za pomocą tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Jeśli liczba ocen dotyczących maszyn wirtualnych lub automatycznej wersji platformy Azure jest nieprawidłowa w narzędziu odnajdywanie i ocenianie, kliknij łączną liczbę ocen, aby przejść do wszystkich ocen i ponownie obliczyć maszyny wirtualne platformy Azure lub oceny automatycznej synchronizacji. Narzędzie odnajdywanie i ocenianie wyświetli poprawną liczbę dla tego typu oceny. 

### <a name="sizing-criteria"></a>Kryterium określania rozmiaru
Opcje ustalania rozmiarów kryteriów w Azure Migrate oceny:

**Kryterium określania rozmiaru** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny zawierające rekomendacje na podstawie zebranych danych dotyczących wydajności | **Ocena maszyny wirtualnej platformy Azure**: Rekomendacja dotycząca rozmiaru maszyny wirtualnej jest oparta na danych użycia procesora i pamięci.<br/><br/> Rekomendacja dotycząca typu dysku (dysk HDD/SSD w warstwie Standardowa lub dyski zarządzane w warstwie Premium) jest oparta na liczbie operacji we/wy na sekundę i przepływności dysków lokalnych.<br/><br/>**Ocena usługi Azure SQL**: Konfiguracja usługi Azure SQL jest oparta na danych wydajności wystąpień SQL i baz danych, w tym: użycie procesora CPU, użycie pamięci, operacje we/wy na sekundę (pliki danych i dziennika), przepływność i opóźnienie operacji wejścia/wyjścia<br/><br/>**Ocena usługi Azure VMware Solution (AVS)** : Rekomendacja dotycząca węzłów usługi AVS jest oparta na danych użycia procesora i pamięci.
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

- **Utwórz ocenę jako przewidzianą**: można tworzyć jako oceny, które natychmiast po wyświetleniu serwerów w portalu Azure Migrate. Nie można utworzyć oceny usługi Azure SQL z użyciem kryteriów ustalania rozmiarów "jako lokalnych".
- **Tworzenie oceny opartej na wydajności**: po skonfigurowaniu odnajdywania zalecamy zaczekanie co najmniej dnia przed uruchomieniem oceny opartej na wydajności:
    - Trwa zbieranie danych dotyczących wydajności. Oczekiwanie co najmniej każdego dnia zapewnia wystarczającą ilość punktów danych wydajności przed uruchomieniem oceny.
    - W przypadku uruchamiania ocen opartych na wydajności należy upewnić się, że w środowisku jest profilowanie czasu trwania oceny. Jeśli na przykład zostanie utworzona Ocena z ustawionym przez tydzień czasem trwania wydajności, należy poczekać przez co najmniej tydzień po rozpoczęciu odnajdywania dla wszystkich punktów danych, które mają być zbierane. Jeśli tego nie zrobisz, ocena nie spowoduje pobrania klasyfikacji z pięcioma gwiazdkami.
- **Oblicz ponownie oceny**: ponieważ oceny są migawkami w czasie, nie są automatycznie aktualizowane przy użyciu najnowszych danych. Aby zaktualizować ocenę przy użyciu najnowszych danych, należy ją ponownie obliczyć.

Postępuj zgodnie z następującymi najlepszymi rozwiązaniami dotyczącymi ocen serwerów zaimportowanych do Azure Migrate za pośrednictwem programu. Plik CSV:

- **Utwórz ocenę jako przewidzianą**: można tworzyć jako oceny, które natychmiast po wyświetleniu serwerów w portalu Azure Migrate.
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

- Wymagane oceny maszyn wirtualnych i automatycznej wersji systemu Azure:
    - Dane użycia procesora CPU i pamięci dla każdego z serwerów
    - Dane operacji we/wy odczytu/zapisu dla każdego dysku podłączonego do serwera lokalnego
    - Dane we/zewnątrz sieci dla każdej karty sieciowej podłączonej do serwera.
     
- Oceny usługi Azure SQL wymagają oceniania danych wydajności wystąpień SQL i baz danych, które obejmują:
    - Dane użycia procesora CPU i pamięci
    - Dane o liczbie operacji we/wy odczytu/zapisu dla danych i plików dziennika
    - Opóźnienie operacji we/wy

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

Jeśli dodasz lub usuniesz serwery z grupy po utworzeniu oceny, utworzona Ocena zostanie oznaczona jako **niezsynchronizowana**. Uruchom ocenę ponownie, abyodzwierciedlić zmiany grupy.

### <a name="outdated-assessments"></a>Nieaktualne oceny

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Ocena oceny i automatycznej synchronizacji maszyn wirtualnych platformy Azure
Jeśli istnieją zmiany na serwerach lokalnych, które znajdują się w grupie, która została oceniona, ocena jest oznaczona jako **nieaktualna**. Ocenę można oznaczyć jako "nieaktualne" z powodu co najmniej jednej zmiany w poniższych właściwościach:
- Liczba rdzeni procesora
- Przydzieloną pamięć
- Typ rozruchu lub oprogramowanie układowe
- Nazwa, wersja i architektura systemu operacyjnego
- Liczba dysków
- Liczba kart sieciowych
- Zmiana rozmiaru dysku (przydzielono GB)
- Aktualizacja właściwości karty sieciowej. Przykład: zmiany adresów MAC, Dodawanie adresów IP itp.
    
Uruchom ocenę ponownie, abyodzwierciedlić zmiany.
    
#### <a name="azure-sql-assessment"></a>Ocena usługi Azure SQL
Jeśli istnieją zmiany w lokalnych wystąpieniach SQL i bazach danych, które znajdują się w grupie, która została oceniona, ocena jest oznaczona jako **nieaktualna**. Ocenę można oznaczyć jako "nieaktualne" z jednego lub kilku poniższych powodów:
- Wystąpienie SQL zostało dodane lub usunięte z serwera
- Baza danych SQL została dodana lub usunięta z wystąpienia SQL
- Łączny rozmiar bazy danych w ramach wystąpienia serwera SQL został zmieniony o ponad 20%
- Zmień liczbę rdzeni procesora
- Zmień w przydzieloną pamięci        
  
    Uruchom ocenę ponownie, abyodzwierciedlić zmiany.

### <a name="low-confidence-rating"></a>Ocena niskiej pewności

Ocena może nie zawierać wszystkich punktów danych z kilku powodów:

- Nie profilujesz swojego środowiska przez czas trwania, dla którego tworzysz ocenę. Jeśli na przykład tworzysz ocenę z czasem trwania wydajności ustawionym na jeden tydzień, musisz poczekać co najmniej tydzień po uruchomieniu odnajdywania, aby zebrać wszystkie punkty danych. Jeśli nie możesz czekać na upłynięcie czasu trwania, zmień czas trwania wydajności na krótszy okres i oblicz ponownie ocenę.
 
- Ocena nie jest w stanie zbierać danych wydajności dla niektórych lub wszystkich serwerów w okresie oceny. W celu uzyskania oceny o wysokiej pewności upewnij się, że: 
    - Serwery są zasilane na czas trwania oceny
    - Połączenia wychodzące na portach 443 są dozwolone
    - W przypadku pamięci dynamicznej na serwerach funkcji Hyper-V jest włączona 
    - Stan połączenia agentów w Azure Migrate to "Połączono" i sprawdź ostatni puls
    - W przypadku usługi Azure SQL Assessment Azure Migrate stan połączenia dla wszystkich wystąpień SQL ma wartość "Połączono" w bloku odnalezionego wystąpienia programu SQL

    Użyj opcji „Oblicz ponownie”, aby uwzględnić najnowsze zmiany w ocenie ufności.

- W przypadku maszyn wirtualnych platformy Azure i ocen dotyczących automatycznej wersji programu po rozpoczęciu odnajdywania zostały utworzone kilka serwerów. Na przykład jeśli tworzysz ocenę dla historii wydajności dla ostatniego miesiąca, ale kilka serwerów zostało utworzonych w środowisku tylko tydzień temu. W takim przypadku dane wydajności dla nowych serwerów nie będą dostępne przez cały czas, a Ocena zaufania byłaby niska.

- W przypadku ocen usługi Azure SQL utworzono kilka wystąpień lub baz danych SQL po rozpoczęciu odnajdywania. Na przykład jeśli tworzysz ocenę dla historii wydajności dla ostatniego miesiąca, ale kilka wystąpień SQL lub baz danych zostało utworzonych w środowisku tylko tydzień temu. W takim przypadku dane wydajności dla nowych serwerów nie będą dostępne przez cały czas, a Ocena zaufania byłaby niska.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Wskazówki dotyczące narzędzia do migracji dotyczące ocen dotyczących automatycznej synchronizacji

W raporcie dotyczącym gotowości oceny usługi Azure VMware Solution (AVS) można zobaczyć następujące sugerowane narzędzia: 
- **VMware HCX lub Enterprise**: w przypadku serwerów VMware, rozwiązanie hybrydowe chmury VMware (HCX) to sugerowane narzędzie do migracji w celu migrowania obciążenia lokalnego do chmury prywatnej Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Nieznane**: w przypadku serwerów zaimportowanych za pośrednictwem pliku CSV, domyślne narzędzie migracji jest nieznane. Mimo że w przypadku serwerów w środowisku VMware zaleca się korzystanie z rozwiązania VMware hybryd Cloud Extension (HCX).


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się](concepts-assessment-calculation.md) , w jaki sposób są obliczane oceny.
- [Dowiedz się](how-to-modify-assessment.md) , jak dostosować ocenę.
