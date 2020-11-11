---
title: Kompiluj plan migracji przy użyciu Azure Migrate | Microsoft Docs
description: Zawiera wskazówki dotyczące kompilowania planu migracji przy użyciu Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: rajosh
ms.openlocfilehash: 8f37814e29ce0089c26e235123768296efc2c0b0
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504928"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Tworzenie planu migracji za pomocą usługi Azure Migrate

Postępuj zgodnie z tym artykułem, aby skompilować plan migracji na platformę Azure przy użyciu [Azure Migrate](migrate-services-overview.md). 

## <a name="define-cloud-migration-goals"></a>Definiowanie celów migracji do chmury

Przed rozpoczęciem, zrozumienie i Ocena [motywacji](/azure/cloud-adoption-framework/strategy/motivations) do przejścia do chmury może przyczynić się do pomyślnego wyniku działania biznesowego. Jak wyjaśniono w [strukturze wdrażania w chmurze](/azure/cloud-adoption-framework), istnieje wiele wyzwalaczy i wyników.   

**Wydarzenie biznesowe** | **Wynik migracji**
--- | ---
Wyjście centrum danych | Cost (Koszt) 
Fuzja, pozyskiwanie lub zbycie | Obniżka w zakresie dostawcy/złożoności technicznej
Zmniejszenie wydatków inwestycyjnych | Optymalizacja operacji wewnętrznych
Koniec wsparcia dla technologii o kluczowym znaczeniu | Zwiększ elastyczność biznesową
Odpowiedź na zmiany zgodności z przepisami | Przygotowanie do nowych możliwości technicznych
Nowe wymagania dotyczące suwerenności danych | Skalowanie w celu spełnienia wymagań dotyczących rynku
Zmniejszenie przerw w działaniu i ulepszenia stabilności IT | Skalowanie w celu spełnienia wymagań geograficznych

Zidentyfikowanie motywacji pomoże Ci w przypięciu strategicznych celów migracji. Następnym krokiem jest zidentyfikowanie i zaplanowanie ścieżki migracji dopasowanej do obciążeń. [Azure Migrate: Narzędzie do oceny serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool) ułatwia ocenę obciążeń lokalnych i udostępnia wskazówki i narzędzia ułatwiające Migrowanie.

## <a name="understand-your-digital-estate"></a>Zapoznaj się z cyfrą

Zacznij od zidentyfikowania lokalnej infrastruktury, aplikacji i zależności. Pomaga to identyfikować obciążenia na potrzeby migracji na platformę Azure oraz zbierać zoptymalizowane projekcje kosztów. Narzędzie do oceny serwera ułatwia identyfikowanie obciążeń, które są używane, zależności między obciążeniami i optymalizacją obciążeń.

### <a name="workloads-in-use"></a>Obciążenia w użyciu

Azure Migrate używa uproszczonego urządzenia Azure Migrate do wykrywania agentów lokalnych maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, innych maszyn zwirtualizowanych i serwerów fizycznych. Funkcja ciągłego odnajdowania zbiera informacje o konfiguracji komputera i metadane wydajności, a także dane aplikacji. Oto, co urządzenie zbiera z maszyn lokalnych: 

- Metadane maszyny, dysku i karty sieciowej.

- Zainstalowane aplikacje, role i funkcje.

- Dane dotyczące wydajności, w tym wykorzystanie procesora i pamięci, operacje we/wy na dysku i przepływność.

Po zebraniu danych można wyeksportować listę spisu aplikacji, aby znaleźć aplikacje i SQL Server wystąpienia uruchomione na maszynach. Aby zrozumieć gotowość SQL Server, można użyć narzędzia do oceny bazy danych Azure Migrate:.

 ![Spis aplikacji w portalu](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Eksport spisu aplikacji](./media/concepts-migration-planning/application-inventory-export.png)

Wraz z danymi wykrytymi za pomocą narzędzia do oceny serwera, można użyć danych bazy danych zarządzania konfiguracją (CMDB) do utworzenia widoku dotyczącego serwera i bazy danych, a także zrozumieć, jak serwery są dystrybuowane między jednostkami biznesowymi, właścicielami aplikacji, lokalizacje geograficzne itp. Ułatwia to decydowanie o obciążeniach, które mają być priorytetowe dla migracji. 

### <a name="dependencies-between-workloads"></a>Zależności między obciążeniami

Po odnajdywaniu serwera można [analizować zależności](concepts-dependency-visualization.md), wizualizować i identyfikować zależności między serwerami oraz strategie optymalizacji dotyczące przenoszenia serwerów zależnych na platformę Azure. Wizualizacja pomaga zrozumieć, czy niektóre maszyny są używane, czy mogą zostać zlikwidowane, a nie migrowane.  Analizowanie zależności pomaga upewnić się, że nic nie zostanie pozostawione i nie będzie miało przerwy podczas migracji. Dzięki wykonaniu spisu aplikacji i analizie zależności można utworzyć grupy serwerów o wysokim poziomie pewności i zacząć oceniać je.

 ![Mapowanie zależności](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Optymalizacja i rozmiar

System Azure zapewnia elastyczność zmiany rozmiaru chmury w miarę upływu czasu, a migracja umożliwia optymalizację zasobów procesora i pamięci przypisywanych do serwerów. Tworzenie oceny na serwerach, które znasz, pomaga zrozumieć historię wydajności obciążeń. Jest to kluczową kwestią dla praw do wielkości jednostek SKU maszyn wirtualnych platformy Azure i zaleceń dotyczących dysku na platformie Azure.

## <a name="assess-migration-readiness"></a>Ocena gotowości do migracji


### <a name="readinesssuitability-analysis"></a>Gotowość/analiza przydatności

Możesz wyeksportować raport oceny i odfiltrować te kategorie, aby zrozumieć gotowość platformy Azure:

- **Gotowe do użycia na platformie Azure** : maszyny można migrować na platformę Azure bez wprowadzania żadnych zmian. 
- **Warunkowo gotowy na platformę Azure** : maszyny można migrować do platformy Azure, ale wymaga to drobnych zmian, zgodnie z wskazówki dotyczące korygowania zawarte w ocenie.
- **Nie gotowy na platformie Azure** : nie można migrować maszyn na platformę Azure jako-is. Przed migracją należy naprawić problemy zgodnie z zaleceniami dotyczącymi korygowania. 
- **Nieznane gotowość** : Azure Migrate nie może określić gotowości maszyny z powodu niewystarczających metadanych.

Korzystając z ocen baz danych, można ocenić gotowość SQL Server danych na potrzeby migracji do Azure SQL Database lub wystąpień zarządzanych usługi Azure SQL. Ocena przedstawia wartość procentową stanu gotowości do migracji dla każdego wystąpienia programu SQL Server. Ponadto dla każdego wystąpienia można zobaczyć zalecaną wartość docelową na platformie Azure, potencjalne bloki migracji, liczbę istotnych zmian, gotowość do usługi Azure SQL DB lub maszynę wirtualną Azure SQL oraz poziom zgodności. Można Dig bardziej szczegółowo zrozumieć wpływ blokowania migracji oraz Zalecenia dotyczące ich rozwiązania.

 ![Oceny bazy danych](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Zalecenia dotyczące ustalania wielkości

Po oznaczeniu maszyny jako gotowej na platformie Azure Ocena serwera udostępnia zalecenia dotyczące zmiany rozmiarów, które identyfikują jednostkę SKU i typ dysku maszyny wirtualnej platformy Azure dla maszyn. Zalecenia dotyczące ustalania rozmiarów można uzyskać na podstawie historii wydajności (w celu zoptymalizowania zasobów podczas migracji) lub na podstawie ustawień maszyny lokalnej bez historii wydajności. W ocenie bazy danych można zobaczyć zalecenia dotyczące jednostki SKU bazy danych, warstwy cenowej i poziomu obliczeniowego.  

### <a name="get-compute-costs"></a>Pobierz koszty obliczeń

Opcja ustalania rozmiaru na podstawie wydajności w Azure Migrate oceny pomaga w przypadku maszyn wirtualnych o odpowiednim rozmiarze i powinna być używana jako najlepsze rozwiązanie do optymalizowania obciążeń na platformie Azure. Oprócz zmiany wielkości liter dostępne są kilka innych opcji ułatwiających zaoszczędzenie kosztów platformy Azure: 

- **Wystąpienia zarezerwowane** : z [wystąpieniami zarezerwowanymi (ri)](https://azure.microsoft.com/pricing/reserved-vm-instances/)można znacząco obniżyć koszty w porównaniu z [cennikiem z opcją płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)użyciem.
- **Korzyść użycia hybrydowego platformy Azure** : za pomocą [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)można przenieść licencje na system Windows Server z aktywnym programem Software Assurance lub z subskrypcją systemu Linux na platformę Azure oraz łączyć z opcjami wystąpień zarezerwowanych.
- **Umowa Enterprise** : [umowy Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) platformy Azure mogą oferować oszczędności dla subskrypcji i usług platformy Azure.
- **Oferty** : istnieje wiele [ofert platformy Azure](https://azure.microsoft.com/support/legal/offer-details/). Na przykład [płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/pricing/dev-test/)lub [Enterprise — tworzenie i testowanie oferty](https://azure.microsoft.com/offers/ms-azr-0148p/), aby zapewnić niższe stawki za tworzenie i testowanie maszyn wirtualnych
- **Czas działania maszyny wirtualnej** : możesz przejrzeć dni miesięcznie i godziny dziennie, w których działają maszyny wirtualne platformy Azure. Wyłączenie maszyn, gdy nie są używane, może obniżyć koszty (nie dotyczy to usług RIs).
- **Region docelowy** : można tworzyć oceny w różnych regionach, aby ustalić, czy migracja do określonego regionu może być bardziej opłacalna. 

### <a name="visualize-data"></a>Wizualizowanie danych

Raporty dotyczące oceny serwera można wyświetlać (z informacjami o gotowości platformy Azure i rozkładem kosztów miesięcznych) w portalu. Możesz również wyeksportować ocenę i wzbogacić plan migracji z dodatkowymi wizualizacjami. Można utworzyć wiele ocen z różnymi kombinacjami właściwości i wybrać zestaw właściwości, które najlepiej sprawdzają się w firmie.  

 ![Omówienie ocen](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Oceń przerwy/blokować

Po ustaleniu aplikacji i obciążeń, które chcesz zmigrować, zidentyfikuj dla nich ograniczenia przestoju i poszukaj wszelkich zależności operacyjnych między aplikacjami a podstawową infrastrukturą. Ta analiza ułatwia planowanie migracji, które spełniają cel czasu odzyskiwania (RTO) i zapewnia minimalną utratę danych. Przed przeprowadzeniem migracji zalecamy przejrzenie i rozwiązanie wszelkich problemów ze zgodnością lub nieobsługiwanych funkcji, które mogą blokować migrację serwera/bazy danych SQL. W tym celu można uzyskać pomoc dotyczącą raportu oceny serwera Azure Migrate i oceny Azure Migrate Database. 

### <a name="prioritize-workloads"></a>Określanie priorytetów obciążeń

Po zebraniu informacji o spisie możesz zidentyfikować aplikacje i obciążenia, które mają zostać zmigrowane jako pierwsze. Opracowuj podejście "Zastosuj i Dowiedz się", aby przeprowadzić migrację aplikacji w sposób systematyczny i kontrolowany, dzięki czemu możesz odejść wszelkie wady przed rozpoczęciem migracji w trybie pełnej skali.

Aby określić priorytety kolejności migracji, można użyć czynników strategicznych, takich jak złożoność, czas do migracji, pilność biznesowa, zagadnienia dotyczące produkcji/nieprodukcyjnego, zgodność, wymagania dotyczące zabezpieczeń, wiedza aplikacji itp. 

Kilka zaleceń:

- **Ustalanie priorytetu szybkiego usługi WINS** : Użyj raportów oceny, aby identyfikować niedrogie owoce, w tym serwery i bazy danych, które są w pełni gotowe i wymagają minimalnego wysiłku do migracji na platformę Azure. W tabeli przedstawiono kilka sposobów, aby to zrobić.

    **Stan** | **Akcja**
    --- | ---
    **Gotowe maszyny wirtualne platformy Azure** | Wyeksportuj raport oceny i przefiltruj wszystkie maszyny z stanem *gotowe na platformę Azure*. Może to być pierwsza grupa maszyn, które zostały podniesione i przesunięte na platformę Azure przy użyciu narzędzia do [migracji Azure Migrate: Server](migrate-services-overview.md#azure-migrate-server-migration-tool) .
    **Końcowe systemy operacyjne** | Wyeksportuj raport oceny i odfiltruj wszystkie maszyny z systemem Windows Server 2008 R2/Windows Server 2008. Te systemy operacyjne mają na celu zakończenie obsługi, a tylko platforma Azure oferuje bezpłatne trzy lata aktualizacji zabezpieczeń podczas migracji na platformę Azure. W przypadku łączenia Korzyść użycia hybrydowego platformy Azure i używania usług RIs oszczędności mogą być znacznie wyższe.
    **Migracja SQL Server** | Zalecenia dotyczące oceny bazy danych służą do migrowania baz danych, które są gotowe do Azure SQL Database przy użyciu narzędzia Azure Migrate do migracji bazy danych. Przygotuj bazy danych do maszyny wirtualnej Azure SQL za pomocą narzędzia do migracji Azure Migrate: Server.
    **Oprogramowanie końcowe dla pomocy technicznej** | Wyeksportuj spis aplikacji i odfiltruj każde oprogramowanie/rozszerzenia, które mogą dotrzeć do końca wsparcia. Określ priorytet tych aplikacji do migracji.
    **Maszyny z obsługą administracyjną** | Wyeksportuj raport oceny i odfiltruj dla maszyn z niskim użyciem procesora CPU (%) i użycie pamięci (%).  Przeprowadź migrację na maszynę wirtualną platformy Azure o odpowiednim rozmiarze i oszczędzaj koszty dotyczące nieużywanych zasobów.
    **Maszyny z nadmierną obsługą administracyjną** | Eksportowanie raportu oceny i filtru dla maszyn o wysokim poziomie użycia procesora CPU (%) i użycie pamięci (%).  Rozwiąż ograniczenia pojemności, Zapobiegaj dzieleniu maszyn z ograniczoną ilością i zwiększaj wydajność dzięki migracji tych maszyn na platformę Azure. Na platformie Azure Użyj funkcji skalowania automatycznego w celu spełnienia wymagań.<br/><br/> Analizuj raporty oceny, aby zbadać ograniczenia magazynu. Analizuj liczbę operacji we/wy dysku i przepływność oraz zalecany typ dysku.

- **Zacznij od początku, a następnie wybierz pozycję Big (** Rozpocznij), przenosząc aplikacje i obciążenia, które składają się na minimalne ryzyko i złożoność, aby stworzyć zaufanie do strategii migracji. Analizuj zalecenia dotyczące oceny Azure Migrate wraz z repozytorium CMDB, aby znaleźć i zmigrować obciążenia deweloperskie/testowe, które mogą być kandydatami do migracji pilotażowych. Opinie i informacje o migracjach pilotażowych mogą być przydatne, gdy rozpoczniesz Migrowanie obciążeń produkcyjnych.  
- **Zgodność** : platforma Azure utrzymuje największy portfel zgodności w branży, pod względem szerokiej i szczegółowej oferty. Wymagania dotyczące zgodności umożliwiają określanie priorytetów migracji, dzięki czemu aplikacje i obciążenia są zgodne z krajowymi, regionalnymi i branżowymi standardami i przepisami obowiązującymi w branży. Jest to szczególnie prawdziwe w przypadku organizacji, które zajmują się procesem o krytycznym znaczeniu dla działalności firmy, przechowują informacje poufne lub są w mocno regulowanej branży. W tych rodzajach organizacji, normach i przepisach Abound i często zmieniają się, trudno jest zachować.  

## <a name="finalize-the-migration-plan"></a>Finalizowanie planu migracji

Przed zakończeniem planu migracji upewnij się, że rozważasz i ograniczasz inne potencjalne bloki, wykonując następujące czynności: 

- **Wymagania dotyczące sieci** : Oceń ograniczenia przepustowości sieci i opóźnienia, co może spowodować nieprzewidziane opóźnienia i zakłócenia w zakresie replikacji migracji.
- **Ulepszenia testowania/po migracji** : Zezwalaj na bufor czasu na przeprowadzenie testów wydajności i akceptacji użytkowników dla zmigrowanych aplikacji, a także konfigurowanie/dostosowywanie aplikacji po migracji, takich jak aktualizowanie parametrów połączenia bazy danych, Konfigurowanie serwerów sieci Web, przecinanie/oczyszczanie itp.
- **Uprawnienia** : Zapoznaj się z zalecanymi uprawnieniami platformy Azure i rolami dostępu serwera/bazy danych i uprawnieniami wymaganymi do migracji.
- **Szkolenie** : Przygotuj organizację do przekształcenia cyfrowego. Solidna Platforma szkoleniowa jest ważna w przypadku pomyślnej zmiany organizacyjnej. Zapoznaj się z bezpłatnym szkoleniem dotyczącym [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), w tym kursów dotyczących podstawowych rozwiązań dotyczących platformy Azure, architektury rozwiązania i zabezpieczeń. Zachęcaj zespół do eksplorowania [certyfikatów platformy Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF).  
- **Obsługa implementacji** : Uzyskaj pomoc techniczną dotyczącą Twojej implementacji, jeśli jej potrzebujesz. W wielu organizacjach jest wybierana pomoc na zewnątrz na potrzeby obsługi migracji do chmury. Aby szybko i bezpiecznie przenieść się na platformę Azure za pomocą spersonalizowanej pomocy, weź pod uwagę [dostawcę usług zarządzanych przez eksperta platformy Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)lub [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Utwórz plan migracji w chmurze z uwzględnieniem szczegółowych informacji o aplikacjach, które chcesz zmigrować, dostępności aplikacji/bazy danych, ograniczeń przestoju i punktów kontrolnych migracji. Plan uważa, jak długo trwa kopiowanie danych i zawiera realistyczny bufor do testowania po migracji oraz działania wycinania. 

Plan testowania po migracji powinien obejmować funkcje, integrację, zabezpieczenia i testy wydajności oraz przypadki użycia, aby zapewnić, że zmigrowane aplikacje działają zgodnie z oczekiwaniami oraz że wszystkie obiekty bazy danych i relacje danych zostaną pomyślnie przeniesione do chmury.  

Utwórz plan migracji i Zadeklaruj okno obsługi w celu przeprowadzenia migracji aplikacji i baz danych z minimalnym niezerowym przestojem i Ogranicz potencjalny wpływ na działania operacyjne i biznesowe podczas migracji.  

## <a name="migrate"></a>Migrate

Zalecamy uruchomienie migracji testowej w Azure Migrate, przed rozpoczęciem migracji w trybie pełnej skali. Migracja testowa ułatwia oszacowanie czasu trwania i dostosowanie planu migracji. Zapewnia możliwość wykrywania potencjalnych problemów i ich usuwania przed pełną migracją.

Gdy wszystko będzie gotowe do migracji, Azure Migrate Użyj narzędzia do migracji serwera i usługi Azure Data Migration Service (DMS) w celu zapewnienia bezproblemowego i zintegrowanego środowiska migracji z kompleksowym śledzeniem.

- Za pomocą narzędzia migracji serwera można migrować lokalne maszyny wirtualne i serwery oraz maszyny wirtualne znajdujące się w innej chmurze prywatnej lub publicznej (w tym AWS, GCP) bez przestojów.
- Usługa Azure DMS udostępnia w pełni zarządzaną usługę, która została zaprojektowana w celu zapewnienia bezproblemowej migracji z wielu źródeł baz danych do platform danych platformy Azure przy minimalnym przestoju.  

## <a name="next-steps"></a>Następne kroki

- Zbadaj [podróż migracji w chmurze](/azure/architecture/cloud-adoption/getting-started/migrate)   w strukturze wdrażania w chmurze platformy Azure.
- Zapoznaj się z [krótkim omówieniem](migrate-services-overview.md) Azure Migrate i obejrzyj [film wprowadzający](https://youtu.be/wFfq3YPxYHE).
- Dowiedz się więcej o ocenianiu maszyn wirtualnych do migracji na [maszyny wirtualne platformy Azure](concepts-assessment-calculation.md).
