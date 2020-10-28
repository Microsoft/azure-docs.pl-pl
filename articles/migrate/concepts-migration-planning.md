---
title: Kompiluj plan migracji przy użyciu Azure Migrate | Microsoft Docs
description: Zawiera wskazówki dotyczące kompilowania planu migracji przy użyciu Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: 6629f02178b7c00ccc849e2ccfc3f0a48f419a7f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735555"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Tworzenie planu migracji za pomocą usługi Azure Migrate

Ten artykuł zawiera krótki przewodnik ułatwiający Kompilowanie planu migracji na platformie Azure przy użyciu [Azure Migrate](migrate-services-overview.md).Jeśli masz inne pytania, zapoznaj się z następującymi zasobami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate
- Pytania dotyczące [urządzenia Azure Migrate](common-questions-appliance.md)
- Pytania dotyczące [migracji serwera](common-questions-server-migration.md)
- Uzyskaj odpowiedzi na pytania na [forum Azure Migrate](https://docs.microsoft.com/answers/topics/azure-migrate.html)

## <a name="define-the-goals-of-cloud-migration"></a>Definiowanie celów migracji do chmury

Przed rozpoczęciem opracowywania planu migracji należy zrozumieć i oszacować [motywację](/azure/cloud-adoption-framework/strategy/motivations) do przejścia do chmury, która może pomóc w tworzeniu bardziej pomyślnych wyników firmy. Zgodnie z opisem [struktury wdrażania chmury dla systemu Azure](/azure/cloud-adoption-framework) , mogą istnieć różne wyzwalacze i podejścia do migracji odpowiednie dla Twojej firmy:  

**Krytyczne zdarzenia biznesowe** | **Wynik migracji**
--- | ---
Wyjście centrum danych | Redukcja kosztów
Fuzja, pozyskiwanie lub zbycie | Zmniejszenie liczby dostawców lub technicznych
Zmniejszenie wydatków inwestycyjnych | Optymalizacja operacji wewnętrznych
Koniec wsparcia dla technologii o kluczowym znaczeniu | Zwiększ elastyczność biznesową
Odpowiedź na zmiany zgodności z przepisami | Przygotowanie do nowych możliwości technicznych
Nowe wymagania dotyczące suwerenności danych | Skalowanie w celu spełnienia wymagań dotyczących rynku
Zmniejszenie przerw w działaniu i poprawa stabilności IT | Skalowanie w celu spełnienia wymagań geograficznych

Motywacja migracji może również pomóc w odzwierciedleniu strategicznych celów i rezultatów, które mają zostać osiągnięte przez Migrowanie na platformę Azure. Następnym krokiem jest zidentyfikowanie i zaplanowanie ścieżki migracji do platformy Azure, która jest dostosowana do obciążeń. Azure Migrate: Narzędzie do oceny serwera ułatwia ocenę obciążeń lokalnych i udostępnia wskazówki i narzędzia ułatwiające Migrowanie.

## <a name="understand-your-digital-estate"></a>Zapoznaj się z cyfrą

Zacznij od znajomości lokalnej infrastruktury, aplikacji i zależności, aby ułatwić zidentyfikowanie obciążeń, które chcesz zmigrować do platformy Azure, i uzyskaj zoptymalizowane projekcje kosztów. Narzędzie do oceny serwera pomoże odpowiedzieć na następujące pytania:

### <a name="what-workloads-are-in-use"></a>Jakie obciążenia są używane?

Użyj uproszczonego urządzenia Azure Migrate, aby przeprowadzić wykrywanie maszyn wirtualnych, maszyn wirtualnych programu VMware i serwerów fizycznych bez wykorzystania agentów. Funkcja odnajdywania ciągłego zbiera dane dotyczące konfiguracji maszyny i metadanych wydajności, a także służy do uzyskiwania spisu zainstalowanych aplikacji oraz ról/funkcji uruchomionych na maszynach lokalnych. Urządzenie Azure Migrate zbiera następujące informacje:

- Szczegóły metadanych maszyn, dysków i kart sieciowych

- Zainstalowane aplikacje, w tym aplikacje i role/funkcje  

- Dane dotyczące wydajności, w tym wykorzystanie procesora i pamięci, operacje we/wy na dysku i przepływność

Następnie wyeksportuj listę spisu aplikacji, aby znaleźć wszystkie wystąpienia SQL Server uruchomione w ramach obciążeń i użyć narzędzia do oceny bazy danych Azure Migrate:, aby zrozumieć ich gotowość.

 ![Spis aplikacji w portalu](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Eksport spisu aplikacji](./media/concepts-migration-planning/application-inventory-export.png)

Wraz z danymi odnajdywania w narzędziu do oceny serwera można korzystać z istniejących danych CMDB, aby utworzyć nasz widok serwera i bazy danych oraz zrozumieć dystrybucję serwera w jednostkach gospodarczych, właścicielach aplikacji, lokalizacje geograficzne itp.

### <a name="what-dependencies-exist-between-workloads"></a>Jakie zależności istnieją między obciążeniami?

Po znalezieniu serwerów Użyj mapowania zależności bez agenta, aby wizualizować i identyfikować zależności między serwerami i strategie optymalizacji dotyczące przenoszenia serwerów zależnych na platformę Azure. Wizualizacja pomaga zrozumieć, czy niektóre maszyny są używane, czy też może zostać zlikwidowane zamiast migracji.  Upewnij się, że analizujesz zależności, aby upewnić się, że nic nie zostało pozostawione i unikaj niespodziewanego przestoju podczas migracji. Po wykonaniu mapowania spisu aplikacji i zależności można utworzyć grupy o wysokim poziomie pewności i rozpocząć ocenianie serwerów.

 ![Mapowanie zależności](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Czy są one zoptymalizowane i poprawnie dopasowane?

Ponieważ platforma Azure zapewnia elastyczność zmiany rozmiaru chmury w miarę upływu czasu, migracja jest okazją do zoptymalizowania zasobów procesora i pamięci przypisywanych do serwerów. Utwórz ocenę dla grupy identyfikowanej wcześniej, aby zrozumieć historię wydajności obciążeń, które będą bardzo ważne w przypadku poszczególnych jednostek SKU maszyn wirtualnych i zaleceń dotyczących dysków na platformie Azure.

## <a name="assess-your-readiness-for-migration"></a>Ocena gotowości do migracji

### <a name="readiness-and-suitability-analysis-for-azure"></a>Gotowość i analiza przydatności na platformie Azure
Wyeksportuj raport oceny maszyn wirtualnych platformy Azure i przefiltruj je według następujących kategorii gotowości, aby zrozumieć gotowość maszyny wirtualnej na platformie Azure:

- **Gotowe do użycia na platformie Azure** : te maszyny można migrować na platformę Azure bez wprowadzania żadnych zmian  

- **Warunkowo gotowy na platformę Azure** : można migrować te maszyny do platformy Azure, ale konieczne będzie wprowadzenie drobnych zmian na tych serwerach zgodnie ze wskazówkami dotyczącymi korygowania uzyskanymi w ocenie

- **Nie gotowy na platformę Azure** : nie można migrować tych maszyn na platformę Azure jako-i trzeba rozwiązać problemy zgodnie z zaleceniami dotyczącymi korygowania przed migracją

- **Nieznane gotowość** : Azure Migrate nie może ustalić gotowości maszyny z powodu niewystarczających metadanych

Korzystając z ocen baz danych, można ocenić gotowość do migracji SQL Server danych do Azure SQL Database lub wystąpień zarządzanych usługi Azure SQL. Procent stanu gotowości do migracji jest widoczny dla każdego wystąpienia programu SQL Server. Ponadto dla każdego wystąpienia można zobaczyć zalecaną wartość docelową na platformie Azure, potencjalni bloki migracji, liczbę zmian powodujących zmiany, gotowość do usługi Azure SQL DB/VM Azure SQL oraz poziom zgodności. Można Dig bardziej szczegółowo zrozumieć wpływ blokowania migracji i rekomendacji, aby je rozwiązać.

 ![Oceny bazy danych](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Zalecenia dotyczące ustalania wielkości

Po oznaczeniu maszyny jako gotowej na platformie Azure Ocena serwera sprawia, że zalecenia dotyczące zmiany rozmiarów umożliwiają zidentyfikowanie maszyny wirtualnej platformy Azure i jednostki SKU dysku dla maszyn wirtualnych. Możesz zobaczyć zalecenia dotyczące ustalania wielkości na podstawie historii wydajności (aby zoptymalizować zasoby podczas migracji) lub na podstawie konfiguracji lokalnej bez uwzględnienia historii wydajności. W przypadku baz danych można zobaczyć zalecenia dotyczące jednostki SKU bazy danych, warstwy cenowej i poziomu obliczeniowego w ocenie bazy danych.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Ocena obliczeń w celu uzyskania szacowanych kosztów uruchamiania obciążeń na platformie Azure

Opcja zmiany rozmiarów *po prawej stronie w obszarze oceny* pozwala zoptymalizować obciążenia na platformie Azure. Oprócz odpowiedniej zmiany istnieje kilka innych ścieżek, które ułatwiają zaoszczędzenie kosztów:

- **Wystąpienia zarezerwowane** : z wystąpieniami zarezerwowanymi możesz znacząco obniżyć koszty w porównaniu z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem, korzystając z rocznych lub 3-letnich terminów w systemach Windows i Linux.

- **Korzyści z używania hybrydowej platformy Azure** : możesz przenieść licencje na system Windows Server w ramach pakietu Software Assurance do platformy Azure i połączyć je z opcjami wystąpień zarezerwowanych

- **Oferta Umowa Enterprise (EA)** : Umowa Enterprise oferuje wbudowane oszczędności dotyczące subskrypcji

- **Oferty** : istnieje wiele ofert platformy Azure, na przykład Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie i Enterprise — tworzenie i testowanie, które udostępniają mniejsze stawki za tworzenie i testowanie maszyn wirtualnych

- **Czas działania maszyny wirtualnej** : można wspomnieć o czasie trwania dni miesięcznie i godzin dziennie, gdy maszyny wirtualne platformy Azure zostaną uruchomione w celu zmniejszenia kosztów

- **Region docelowy** : można utworzyć wiele ocen w różnych regionach, aby porównać, czy migracja do pewnego regionu w lokalizacji geograficznej może być bardziej opłacalna

- **Zalecenia dotyczące wydajności** : najlepszym rozwiązaniem jest próba użycia zaleceń dotyczących maszyn wirtualnych platformy Azure, które pomogą w zaoszczędzeniu kosztów chmury

### <a name="visualize-data"></a>Wizualizowanie danych

Możesz zobaczyć raport oceny serwera z gotowością platformy Azure i dystrybucją miesięcznych kosztów w portalu, a także wyeksportować ocenę, aby zastosować więcej wizualizacji danych odnajdywania i oceny w celu zaawansowania planu migracji. Można utworzyć wiele ocen dla różnych kombinacji właściwości i wybrać zestaw właściwości, które najlepiej sprawdzają się w firmie.  

 ![Omówienie ocen](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Oceń przerwy i potencjalnych blokad

Podczas określania aplikacji i infrastruktury źródłowej do migracji Zidentyfikuj ograniczenia przestoju dla tych aplikacji i poszukaj wszelkich zależności operacyjnych między aplikacjami i podstawową infrastrukturą. Ta analiza może pomóc w planowaniu migracji, które spełniają cel czasu odzyskiwania (RTO) i zapewniają minimalną utratę danych. Przed przeprowadzeniem migracji zalecamy przejrzenie i łagodzenie wszelkich problemów ze zgodnością lub nieobsługiwanych funkcji, które mogą blokować Migrowanie serwerów i baz danych SQL za pomocą raportu oceny serwera i Azure Migrate: zalecenia dotyczące oceny bazy danych.

### <a name="first-workloads-to-target-and-approach"></a>Pierwsze obciążenia docelowe i podejście

Teraz, gdy masz wszystkie najważniejsze informacje na potrzeby podejmowania decyzji dotyczących migracji, należy określić priorytety aplikacji i obciążeń, które mają być migrowane jako pierwsze. Opracowuj podejście "Zastosuj i Dowiedz się", aby przeprowadzić migrację zamierzonych aplikacji w sposób systematyczny i kontrolowany, tak aby można było wycofać wszelkie wady w tej strategii przed rozpoczęciem przeprowadzania migracji w trybie pełnej skali. Można również użyć czynników strategicznych, takich jak-złożoność i czas, aby przeprowadzić migrację, pilność biznesową, środowisko produkcyjne i nieprodukcyjne, zgodność i wymagania dotyczące zabezpieczeń, wiedzę o aplikacji itp., aby określić priorytety grup aplikacji do migracji.

Oto kilka zalecanych strategii migracji:

- Ustaw **priorytety szybkiego serwera WINS** : możesz użyć raportów oceny, aby zidentyfikować niskie wysunięte owoce, w tym serwery i bazy danych, które są w pełni gotowe i wymagają minimalnego wysiłku do migracji na platformę Azure:
    - Gotowe na platformę Azure: Eksportuj raport oceny i odfiltruj wszystkie maszyny, które są gotowe do platformy Azure. Może to być Twoja pierwsza grupa maszyn, którą można podnieść i przenieść za pomocą narzędzia do migracji Azure Migrate: serwera.
    - Koniec obsługi systemu operacyjnego: Eksportuj raport oceny i odfiltruj wszystkie komputery z systemami operacyjnymi Windows Server 2008 i Windows Server 2008 R2. Te jednostki SKU są końcami pomocy technicznej i tylko platforma Azure oferuje bezpłatne 3-letnie aktualizacje zabezpieczeń podczas migrowania ich do platformy Azure. Podczas łączenia, Korzyść użycia hybrydowego platformy Azure i używania wystąpień zarezerwowanych, zapisywanie może być znacznie wyższe.
    - Migracja SQL Server: Użyj zaleceń dotyczących oceny bazy danych do migrowania baz danych gotowych do baz danych Azure SQL Database przy użyciu Azure Migrate: migracja bazy danych i baz danych gotowych do maszyny wirtualnej Azure SQL przy użyciu Azure Migrate: migracji serwera.
    - Zakończenie obsługi oprogramowania: wyeksportuj spis aplikacji i przefiltruj dowolne oprogramowanie/rozszerzenia, które mogą osiągnąć koniec wsparcia. Należy określić priorytety tych aplikacji.
    - Wstępnie udostępnione maszyny wirtualne: Eksportuj raport oceny i odfiltruj maszyny z niskim użyciem procesora CPU (%) i użycie pamięci (%).  Możesz użyć tej możliwości do migrowania do prawnej maszyny wirtualnej na platformie Azure i zapisania tego, co płacisz, za nieużywane zasoby.
    - Ograniczenia pojemności: Eksportuj raport oceny i odfiltruj maszyny z wysokim wykorzystaniem procesora CPU (%) i użycie pamięci (%).  Można zapobiec dzieleniu i zwiększaniu wydajności nieograniczonej maszyny wirtualnej, przechodząc do platformy Azure i korzystaj z funkcji automatycznego skalowania w celu spełnienia wymagań. Możesz również przejrzeć raport oceny, aby zrozumieć ograniczenia dotyczące magazynu, analizując liczbę operacji we/wy dysku i przepływność i Znajdź zalecany typ dysku, który najlepiej odpowiada Twoim potrzebom.

- **Zacznij od małych i niewielkich** : Zacznij od przenoszenia aplikacji i obciążeń, które są minimalnymi zagrożeniami i mniej skomplikowane, aby stworzyć zaufanie do strategii migracji. Możesz również przeanalizować zalecenia dotyczące Azure Migrate oceny z repozytorium CMDB organizacji, aby znaleźć i zmigrować obciążenia środowiska deweloperskiego/testowego w ramach migracji pilotażowej. Informacje z tych pilotażów mogą być używane podczas migrowania obciążeń produkcyjnych.  

- **Zgodność z wymaganiami prawnymi/** obowiązującymi w branży: platforma Azure utrzymuje największe portfolio zgodności w branży, w sensie szerokiej i szczegółowej oferty. Dzięki temu można ustalić priorytety migracji do platformy Azure i zachować zgodność z krajowymi, regionalnymi i branżowymi standardami oraz przepisami. Jest to szczególnie ważne w przypadku organizacji, które zajmują się ochroną lub zawierają informacje poufne lub są w wysoce regulowanej branży, gdzie standardy i regulacje Abound i w niektórych przypadkach mogą być często zmieniane, co utrudnia utrzymywanie się w programie.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Finalizowanie planu migracji i przygotowanie do migracji

Przed zakończeniem planu migracji upewnij się, że te zagadnienia dotyczące migracji kluczy nie odgrywają przeszkód w planowaniu migracji:

- Oceń ograniczenia przepustowości sieci i opóźnień, które mogą powodować nieprzewidziane opóźnienia i zakłócać szybkość replikacji migracji.

- Bufor w czasie w celu przeprowadzenia testów wydajności i akceptacji użytkowników w zmigrowanych aplikacjach lub wykonywania dowolnych dostosowań aplikacji po migracji, takich jak aktualizowanie parametrów połączenia bazy danych i konfiguracji serwera sieci Web, wykonywanie uruchomienie produkcyjne i czyszczenie itp.

- Zapoznaj się z zalecanymi uprawnieniami platformy Azure oraz modelem dostępu do serwera/bazy danych i modelu uprawnień potrzebnych do migracji.

- Przygotuj swoją organizację i zadbaj o to, aby pracownicy dostosowali transformację cyfrową. Solidna Platforma szkoleniowa jest ważna w przypadku pomyślnej zmiany organizacyjnej. Zapoznaj się z bezpłatnymi szkoleniami dostępnymi na [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), w tym kursami dotyczącymi podstawowych rozwiązań dotyczących platformy Azure, architektury rozwiązania i zabezpieczeń. Zachęcaj zespół do eksplorowania [certyfikacji platformy Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   .  

- Uzyskaj pomoc techniczną dotyczącą Twojej implementacji, jeśli jest to wymagane. W wielu organizacjach jest wybierana pomoc na zewnątrz na potrzeby obsługi migracji do chmury. Aby szybko i bezpiecznie przenieść się na platformę Azure za pomocą spersonalizowanej pomocy, weź pod uwagę [dostawcę usług zarządzanych przez eksperta platformy Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   lub [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Utwórz plan migracji do chmury, który zawiera szczegółowe informacje dotyczące listy/grup aplikacji, które mają zostać zmigrowane, dostępności aplikacji i bazy danych oraz ograniczeń przestoju i żądanych punktów kontrolnych migracji. Plan migracji powinien również określać, jak długo trwa kopiowanie danych i zawiera bufor considerate na potrzeby testowania po migracji i działań uruchomienie produkcyjne. Testy po migracji muszą obejmować przypadki użycia funkcjonalnego, integracji, zabezpieczeń i testowania wydajności, aby upewnić się, że migrowane aplikacje działają zgodnie z oczekiwaniami, a wszystkie obiekty bazy danych i relacje danych zostały pomyślnie przeniesione do chmury.  

Korzystając z tej analizy, można utworzyć plan migracji i zadeklarować okno obsługi do migrowania aplikacji i baz danych z minimalnym niezerowym przestojem i ograniczyć potencjalny wpływ na działania operacyjne/biznesowe podczas migracji.  

Zalecamy, aby zawsze testować i korzystać z funkcji *migracji testowej* Azure Migrate przed rozpoczęciem migracji na platformę Azure w trybie pełnej skali. Te rzeczywiste dane pomogą oszacować rzeczywisty czas trwania i wprowadzić niezbędne dostosowania do planu migracji. Migracja testowa umożliwia również odnajdywanie potencjalnych problemów z planem migracji i ich rozwiązywanie przed rzeczywistą migracją.  

Gdy wszystko będzie gotowe do migracji, użyj *Narzędzia migracji serwera* Azure Migrate i *usługi migracji danych* Azure Migrate, aby zapewnić bezproblemowe i zintegrowane środowisko migracji z kompleksowym śledzeniem. Narzędzie do migracji serwera obsługuje migrację maszyn wirtualnych i serwerów hostowanych lokalnie w centrach danych klientów lub dowolnej innej chmurze prywatnej lub publicznej, w tym AWS, GCP itp. bez przestojów. Azure Database Migration Service to w pełni zarządzana usługa, która umożliwia bezproblemowe Migrowanie z wielu źródeł baz danych do platform danych platformy Azure z minimalnym czasem przestoju.  

> [!NOTE]
> W przypadku maszyn wirtualnych VMware Ocena serwera używa systemu operacyjnego określonego dla maszyny wirtualnej w vCenter Server do obsługi analizy systemu operacyjnego gościa. W przypadku maszyn wirtualnych z systemem Linux działających w oprogramowaniu VMware obecnie nie jest dostępna dokładna wersja jądra systemu operacyjnego gościa.

## <a name="next-steps"></a>Następne kroki

- Zbadaj [podróż migracji w chmurze](/azure/architecture/cloud-adoption/getting-started/migrate)   w strukturze wdrażania w chmurze platformy Azure.
- [Rozpocznij pracę](https://youtu.be/wFfq3YPxYHE) z Azure Migrate.
- Utwórz ocenę dla [maszyn wirtualnych VMware](./tutorial-assess-vmware-azure-vm.md) lub [maszyn wirtualnych funkcji Hyper-V](tutorial-assess-hyper-v.md).
