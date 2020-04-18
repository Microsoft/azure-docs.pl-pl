---
title: Wybieranie warstwy cenowej lub jednostki SKU
titleSuffix: Azure Cognitive Search
description: 'Usługa Azure Cognitive Search może być aprowizowana w tych jednostkach SKU: bezpłatne, podstawowe i standardowe, a standard jest dostępna w różnych konfiguracjach zasobów i poziomach pojemności.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: eb11a5cc2deef372ca91c23a8b9c82e17143c85b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617710"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Wybieranie warstwy cenowej dla usługi Azure Cognitive Search

Podczas tworzenia usługi Azure Cognitive Search [zasób jest tworzony](search-create-service-portal.md) w warstwie cenowej (lub jednostce SKU), która jest ustalona przez cały okres istnienia usługi. Warstwy obejmują bezpłatne, podstawowe, standardowe i zoptymalizowane pod kątem pamięci masowej. Standard i storage Optimized są dostępne z kilkoma konfiguracjami i pojemnościami.

Większość klientów rozpoczyna warstwę Bezpłatna, aby mogli ocenić usługę. Po dokonaniu oceny jest wspólne, aby utworzyć drugą usługę w jednym z wyższych warstw dla wdrożeń deweloperskich i produkcyjnych.

## <a name="feature-availability-by-tier"></a>Dostępność funkcji według warstwy

Prawie każda funkcja jest dostępna w każdej warstwie, w tym bezpłatna, ale funkcja lub przepływ pracy, który jest intensywnie korzystający z zasobów, może nie działać dobrze, chyba że podasz jej wystarczającą pojemność. Na przykład [wzbogacenie ai](cognitive-search-concept-intro.md) ma długotrwałe umiejętności, które przewijają się w bezpłatnej usłudze, chyba że zestaw danych jest mały.

W poniższej tabeli opisano ograniczenia funkcji związane z warstwą.

| Funkcja | Ograniczenia |
|---------|-------------|
| [Indeksatory](search-indexer-overview.md) | Indeksatory nie są dostępne w S3 HD. |
| [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) | Niedostępne w warstwie Bezpłatna. |

## <a name="tiers-skus"></a>Warstwy (SKU)

Poziomy są zróżnicowane przez:

+ Ilość indeksów i indeksatorów, które można utworzyć
+ Rozmiar i szybkość partycji (pamięć fizyczna)

Wybrana warstwa określa stawkę do zaliczania. Poniższy zrzut ekranu z witryny Azure portal przedstawia dostępne warstwy minus cennik (który można znaleźć w portalu i na [stronie cennika](https://azure.microsoft.com/pricing/details/search/). **Bezpłatne**, **Podstawowe**i **Standard** są najpopularniejszymi poziomami.

**Free** tworzy ograniczoną usługę wyszukiwania dla mniejszych projektów, w tym szybki start i samouczki. Wewnętrznie repliki i partycje współużytkowane przez wielu subskrybentów. Nie można skalować bezpłatnej usługi ani uruchamiać znaczących obciążeń.

**Podstawowe** i **standardowe** są najczęściej używanymi warstwami rozliczanymi, przy czym **standard** jest wartością domyślną. Dzięki dedykowanym zasobom pod kontrolą można wdrażać większe projekty, optymalizować wydajność i ustawiać pojemność.

![Warstwy cenowe usługi Azure Cognitive Search](media/search-sku-tier/tiers.png "Warstwy cenowe usługi Azure Cognitive Search")

Niektóre warstwy są zoptymalizowane pod kątem niektórych typów pracy. Na przykład **Standard 3 High Density (S3 HD)** jest *trybem hostingu* dla S3, gdzie podstawowy sprzęt jest zoptymalizowany pod kątem dużej liczby mniejszych indeksów i jest przeznaczony do scenariuszy wielodostępności. S3 HD ma takie samo ładowanie na jednostkę jak S3, ale sprzęt jest zoptymalizowany pod kątem szybkiego odczytu plików na dużej liczbie mniejszych indeksów.

**Warstwy zoptymalizowane pod kątem magazynu** oferują większą pojemność pamięci masowej po niższej cenie za TB niż warstwy standardowe. Podstawowym kompromisem jest większe opóźnienie kwerendy, które należy sprawdzić dla określonych wymagań aplikacji.  Aby dowiedzieć się więcej na temat zagadnień dotyczących wydajności tej warstwy, zobacz [Zagadnienia dotyczące wydajności i optymalizacji.](search-performance-optimization.md)

Więcej informacji na temat różnych warstw można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/search/), w [artykule Limity usługi w usłudze Azure Cognitive Search](search-limits-quotas-capacity.md) oraz na stronie portalu podczas inicjowania obsługi administracyjnej usługi.

## <a name="billable-events"></a>Zdarzenia podlegające rozliczani

Rozwiązanie oparte na usłudze Azure Cognitive Search może ponieść koszty w następujący sposób:

+ Stały koszt samej usługi, działającej 24x7, przy minimalnej konfiguracji (jedna partycja i replika)
+ Koszt przyrostowy podczas skalowania w górę (dodawanie replik lub partycji)
+ Opłaty za przepustowość (transfer danych wychodzących) 
+ Wyszukiwanie poznawcze (dołączanie usług Cognitive Services do wzbogacania sztucznej inteligencji lub używanie magazynu platformy Azure dla magazynu wiedzy)

### <a name="service-costs"></a>Koszty serwisu

W przeciwieństwie do maszyn wirtualnych lub innych zasobów, które mogą być "wstrzymane", aby uniknąć opłat, usługa Azure Cognitive Search jest zawsze dostępna na sprzęcie przeznaczonym do wyłącznego użytku. W związku z tym utworzenie usługi jest zdarzeniem podlegającym rozliczaniu, które rozpoczyna się podczas tworzenia usługi i kończy się po usunięciu usługi. 

Minimalna opłata to pierwsza jednostka wyszukiwania (jedna replika x jedna partycja) według stawki podlegającej rozliczaniu. To minimum jest ustalona dla okresu istnienia usługi, ponieważ usługa nie może działać na nic mniej niż tej konfiguracji. Poza minimum można dodawać repliki i partycje niezależnie od siebie. Przyrostowy wzrost pojemności za pośrednictwem replik i partycji zwiększy rachunek na podstawie następującej formuły: [(repliki x partycje x rate)](#search-units), gdzie stawka jest naliczana zależy od wybranej warstwy cenowej.

Podczas szacowania kosztów rozwiązania wyszukiwania należy pamiętać, że ceny i pojemność nie są liniowe. (Podwojenie zdolności produkcyjnych ponad dwukrotnie podwaja koszt.) Na przykład jak działa formuła, zobacz [Jak przydzielić repliki i partycje](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Opłaty za przepustowość

Korzystanie z [indeksatorów usługi Azure Cognitive Search](search-indexer-overview.md) może mieć wpływ na rozliczenia, w zależności od lokalizacji usług. Możesz całkowicie wyeliminować opłaty za ruch wychodzący danych, jeśli utworzysz usługę Azure Cognitive Search w tym samym regionie co dane. Oto kilka informacji ze [strony cennika przepustowości:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ Firma Microsoft nie pobiera opłat za żadne dane przychodzące do żadnej usługi na platformie Azure ani za żadne dane wychodzące z usługi Azure Cognitive Search.
+ W rozwiązaniach wielousługowych nie ma żadnych opłat za przesyłanie danych przez przewód, gdy wszystkie usługi znajdują się w tym samym regionie.

Opłaty dotyczą danych wychodzących, jeśli usługi znajdują się w różnych regionach. Te opłaty nie są w rzeczywistości częścią rachunku usługi Azure Cognitive Search. Są one wymienione tutaj, ponieważ jeśli używasz danych lub indeksatorów wzbogaconych o AI do ściągania danych z różnych regionów, zobaczysz koszty odzwierciedlone w ogólnym rachunku.

### <a name="ai-enrichment-with-cognitive-services"></a>Wzbogacanie SI o usługi Cognitive Services

W przypadku [wzbogacania sztucznej inteligencji](cognitive-search-concept-intro.md)należy zaplanować [dołączenie rozliczanego zasobu usługi Azure Cognitive Services](cognitive-search-attach-cognitive-services.md)w tym samym regionie co usługa Azure Cognitive Search w warstwie cenowej S0 do przetwarzania płatności zgodnie z rzeczywistym użyciem. Nie ma stałego kosztu związanego z dołączaniem usług Cognitive Services. Płacisz tylko za przetwarzanie, którego potrzebujesz.

| Operacja | Wpływ rozliczeń |
|-----------|----------------|
| Pękanie dokumentów, ekstrakcja tekstu | Bezpłatna |
| Pękanie dokumentów, ekstrakcja obrazu | Rozliczane w zależności od liczby obrazów wyodrębnionych z dokumentów. W [konfiguracji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** jest parametrem wyzwalającym wyodrębnianie obrazu. Jeśli **imageAction** jest ustawiona na "none" (domyślnie), nie zostanie naliczona opłata za wyodrębnienie obrazu. Szybkość wyodrębniania obrazu jest udokumentowana na stronie [szczegółów cennika](https://azure.microsoft.com/pricing/details/search/) usługi Azure Cognitive Search.|
| [Wbudowane umiejętności poznawcze](cognitive-search-predefined-skills.md) | Rozliczane w takim samym tempie, jak w przypadku wykonania zadania bezpośrednio przy użyciu usług Cognitive Services. |
| Umiejętności niestandardowe | Niestandardowa umiejętność to funkcjonalność, którą udostępniasz. Koszt używania umiejętności niestandardowej zależy wyłącznie od tego, czy kod niestandardowy wywołuje inne usługi taryfowe. |

Funkcja [wzbogacenia przyrostowego (podglądu)](cognitive-search-incremental-indexing-conceptual.md) umożliwia zapewnienie pamięci podręcznej, która umożliwia indeksatorowi zwiększenie wydajności w uruchamianiu tylko umiejętności poznawczych, które są niezbędne, jeśli zmodyfikujesz swój zestaw umiejętności w przyszłości, oszczędzając czas i pieniądze.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Formuła rozliczeń (R x P = SU)

Najważniejszą koncepcją rozliczeń do zrozumienia dla operacji usługi Azure Cognitive Search jest *jednostka wyszukiwania* (SU). Ponieważ usługa Azure Cognitive Search zależy od replik i partycji do indeksowania i zapytań, nie ma sensu rozliczać tylko przez jedną lub drugą. Zamiast tego rozliczenia opiera się na złożonych obu.

SU jest produktem *replik* i *partycji używanych* przez usługę: **(R x P = SU)**.

Każda usługa rozpoczyna się od jednej SU (jedna replika pomnożona przez jedną partycję) jako minimum. Maksymalna wartość każdej usługi to 36 SUs. To maksimum można osiągnąć na wiele sposobów: 6 partycji x 6 replik lub 3 partycje x 12 replik, na przykład. Często używa się mniej niż całkowita pojemność (na przykład 3-repliki, 3-partycji usługi rozliczane jako 9 SUs). Zobacz [partycji i repliki kombinacji](search-capacity-planning.md#chart) wykres prawidłowych kombinacji.

Stawka rozliczeniowa jest godzinowa na SU. Każdy poziom ma coraz wyższą stawkę. Wyższe warstwy są wyposażone w większe i szybsze partycje, co przyczynia się do ogólnej wyższej stawki godzinowej dla tej warstwy. Stawki dla każdej warstwy można wyświetlić na stronie [szczegółów cennika.](https://azure.microsoft.com/pricing/details/search/)

Większość klientów przynosi tylko część całkowitej pojemności online, trzymając resztę w rezerwie. W przypadku rozliczeń liczba partycji i replik, które są przesuwające do trybu online, obliczana według formuły SU, określa, co płacisz co godzinę.

## <a name="how-to-manage-costs"></a>Jak zarządzać kosztami

Poniższe sugestie mogą pomóc w utrzymaniu kosztów na minimalnym poziomie:

+ Utwórz wszystkie zasoby w tym samym regionie lub w jak najmniejszej liczby regionów, aby zminimalizować lub wyeliminować obciążenia przepustowością.

+ Skonsoliduj wszystkie usługi w jedną grupę zasobów, taką jak Azure Cognitive Search, Cognitive Services i inne usługi platformy Azure używane w rozwiązaniu. W witrynie Azure portal znajdź grupę zasobów i użyj poleceń **zarządzania kosztami,** aby uzyskać wgląd w rzeczywiste i przewidywane wydatki.

+ Należy wziąć pod uwagę usługi Azure Web App dla aplikacji front-end, tak aby żądania i odpowiedzi pozostają w granicach centrum danych.

+ Skalowanie w górę dla operacji intensywnie korzystających z zasobów, takich jak indeksowanie, a następnie dostosować w dół dla regularnych obciążeń zapytań. Rozpocznij od minimalnej konfiguracji usługi Azure Cognitive Search (jedna usługa SU składająca się z jednej partycji i jednej repliki), a następnie monitoruj aktywność użytkownika w celu zidentyfikowania wzorców użycia, które wskazują na potrzebę zwiększenia pojemności. Jeśli istnieje przewidywalny wzorzec, może być w stanie zsynchronizować skalę z aktywnością (należy napisać kod, aby zautomatyzować to).

Ponadto odwiedź stronę [Rozliczenia i zarządzanie kosztami,](https://docs.microsoft.com/azure/billing/billing-getting-started) aby uzyskać wbudowane narzędzia i funkcje związane z wydatkami.

Tymczasowe zamknięcie usługi wyszukiwania nie jest możliwe. Dedykowane zasoby są zawsze operacyjne, przydzielane do wyłącznego użytku przez cały okres użytkowania usługi. Usunięcie usługi jest trwałe, a także usuwa skojarzone z nią dane.

Jeśli chodzi o samą usługę, jedynym sposobem obniżenia rachunku jest zmniejszenie replik i partycji do poziomu, który nadal zapewnia akceptowalną wydajność i [zgodność z SLA,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)lub utworzyć usługę na niższej warstwie (stawki godzinowe S1 są niższe niż stawki S2 lub S3). Zakładając, że aprowizowanie usługi na dolnym końcu projekcji obciążenia, jeśli przerastają usługi, można utworzyć drugą usługę większych warstw, odbudować indeksy w drugiej usłudze, a następnie usunąć pierwszą.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak ocenić wymagania dotyczące zdolności produkcyjnych

W usłudze Azure Cognitive Search pojemność jest skonstruowana jako *repliki* i *partycje*.

+ Repliki są wystąpieniami usługi wyszukiwania. Każda replika obsługuje jedną kopię indeksu ze zrównoważonym obciążeniem. Na przykład usługa z sześcioma replikami ma sześć kopii każdego indeksu załadowanego do usługi.

+ Partycje przechowują indeksy i automatycznie dzielą dane z możliwością wyszukiwania. Dwie partycje podzielić indeks na pół, trzy partycje podzielić go na trzecie i tak dalej. Pod względem pojemności *rozmiar partycji* jest podstawową cechą różnicującą między warstwami.

> [!NOTE]
> Wszystkie warstwy zoptymalizowane pod kątem standardowego i magazynu obsługują [elastyczne kombinacje replik i partycji,](search-capacity-planning.md#chart) dzięki czemu można [zoptymalizować system pod kątem szybkości lub pamięci masowej,](search-performance-optimization.md) zmieniając wagę. Warstwa Basic oferuje do trzech replik o wysokiej dostępności, ale ma tylko jedną partycję. Warstwy bezpłatne nie zapewniają dedykowanych zasobów: zasoby obliczeniowe są współużytkowane przez wielu subskrybentów.

### <a name="evaluating-capacity"></a>Ocena zdolności produkcyjnych

Pojemność i koszty prowadzenia usługi idą w parze. Warstwy nakładają limity na dwa poziomy: magazyn i zasoby. Należy pomyśleć o obu, ponieważ niezależnie od tego, który limit można osiągnąć pierwszy jest skuteczny limit.

Wymagania biznesowe zazwyczaj określają liczbę potrzebnych indeksów. Na przykład może być potrzebny indeks globalny dla dużego repozytorium dokumentów. Możesz też potrzebować wielu indeksów opartych na regionie, aplikacji lub niszy biznesowej.

Aby określić rozmiar indeksu, należy [utworzyć jeden](search-create-index-portal.md). Jego rozmiar będzie oparty na importowanych danych i konfiguracji indeksu, takich jak czy można włączyć sugestów, filtrowanie i sortowanie. Aby uzyskać więcej informacji na temat wpływu konfiguracji na rozmiar, zobacz [Tworzenie indeksu podstawowego ](search-what-is-an-index.md).

W przypadku wyszukiwania pełnotekstowego struktura danych podstawowych jest odwróconą strukturą [indeksu,](https://en.wikipedia.org/wiki/Inverted_index) która ma inne właściwości niż dane źródłowe. W przypadku odwróconego indeksu rozmiar i złożoność zależą od zawartości, niekoniecznie od ilości danych, które do niego podawane. Duże źródło danych o wysokiej nadmiarowości może spowodować mniejszy indeks niż mniejszy zestaw danych, który zawiera bardzo zmienną zawartość. Dlatego rzadko można wywnioskować rozmiar indeksu na podstawie rozmiaru oryginalnego zestawu danych.

> [!NOTE] 
> Mimo że szacowanie przyszłych potrzeb w zakresie indeksów i pamięci masowej może wydawać się zgadywaniem, warto to zrobić. Jeśli pojemność warstwy okaże się zbyt niska, musisz udostępnić nową usługę w wyższej warstwie, a następnie [ponownie załadować indeksy.](search-howto-reindex.md) Nie ma uaktualnienia usługi w miejscu z jednej jednostki SKU do drugiej.
>

### <a name="estimate-with-the-free-tier"></a>Oszacuj za pomocą warstwy Bezpłatna

Jednym z podejść do szacowania pojemności jest rozpoczęcie od warstwy bezpłatna. Należy pamiętać, że bezpłatna usługa oferuje maksymalnie trzy indeksy, 50 MB pamięci masowej i 2 minuty czasu indeksowania. Oszacowanie przewidywanego rozmiaru indeksu za pomocą tych ograniczeń może być trudne, ale są to kroki:

+ [Utwórz bezpłatną usługę](search-create-service-portal.md).
+ Przygotuj mały, reprezentatywny zestaw danych.
+ [Tworzenie początkowego indeksu w portalu](search-create-index-portal.md) i zanotuj jego rozmiar. Funkcje i atrybuty mają wpływ na magazyn. Na przykład dodanie sugestów (kwerendy typu search-as-you- type) zwiększy wymagania dotyczące magazynu. Korzystając z tego samego zestawu danych, można spróbować utworzyć wiele wersji indeksu, z różnymi atrybutami w każdym polu, aby zobaczyć, jak zmieniają się wymagania dotyczące magazynu. Aby uzyskać więcej informacji, zobacz ["Implikacje magazynu" w tworzenie indeksu podstawowego](search-what-is-an-index.md#index-size).

Przybliżonym oszacowaniu w ręku można podwoić tę kwotę do budżetu dla dwóch indeksów (rozwoju i produkcji), a następnie odpowiednio wybrać warstwę.

### <a name="estimate-with-a-billable-tier"></a>Szacowanie za pomocą warstwy podlegania rozliczaniu

Dedykowane zasoby mogą pomieścić większe czasy próbkowania i przetwarzania dla bardziej realistycznych oszacowań ilości indeksu, rozmiaru i woluminów zapytań podczas opracowywania. Niektórzy klienci wskakują bezpośrednio z warstwą podlegania rozliczalności, a następnie ponownie oceniają w miarę dojrzewania projektu dewelopera.

1. [Przejrzyj limity usług w każdej warstwie,](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) aby ustalić, czy niższe warstwy mogą obsługiwać liczbę potrzebnych indeksów. W warstwach Podstawowe, S1 i S2 limity indeksów to odpowiednio 15, 50 i 200. Warstwa zoptymalizowana do magazynowania ma limit 10 indeksów, ponieważ jest przeznaczona do obsługi małej liczby bardzo dużych indeksów.

1. [Tworzenie usługi w warstwie podlegania rozliczaniu:](search-create-service-portal.md)

    + Zacznij od niskiego, w basic lub S1, jeśli nie masz pewności co do przewidywanego obciążenia.
    + Zacznij wysoko, w S2 lub nawet S3, jeśli wiesz, że masz zamiar mieć dużą skalę indeksowania i zapytań obciążenia.
    + Zacznij od zoptymalizowanego do magazynowania, w L1 lub L2, jeśli indeksujesz dużą ilość danych i obciążenie zapytania jest stosunkowo niska, podobnie jak w przypadku wewnętrznej aplikacji biznesowej.

1. [Tworzenie początkowego indeksu,](search-create-index-portal.md) aby określić, jak dane źródłowe przekłada się na indeks. Jest to jedyny sposób szacowania rozmiaru indeksu.

1. [Monitorowanie magazynu, limitów usług, woluminu zapytań i opóźnień](search-monitor-usage.md) w portalu. Portal pokazuje zapytania na sekundę, ograniczone zapytania i opóźnienie wyszukiwania. Wszystkie te wartości mogą pomóc w podjęciu decyzji, czy wybrano odpowiednią warstwę. 

Numer indeksu i rozmiar są równie ważne dla analizy. Jest to spowodowane maksymalne limity są osiągane przez pełne wykorzystanie magazynu (partycje) lub przez maksymalne limity zasobów (indeksy, indeksy i tak dalej), w zależności od tego, co nastąpi wcześniej. Portal pomaga śledzić zarówno bieżące użycie, jak i maksymalne limity obok siebie na stronie Przegląd.

> [!NOTE]
> Wymagania dotyczące przechowywania mogą być zawyżone, jeśli dokumenty zawierają dane obce. W idealnym przypadku dokumenty zawierają tylko te dane potrzebne do wyszukiwania. Dane binarne nie można przeszukiwać i powinny być przechowywane oddzielnie (być może w tabeli platformy Azure lub magazynu obiektów blob). Następnie należy dodać pole do indeksu, aby pomieścić odwołanie do adresu URL danych zewnętrznych. Maksymalny rozmiar pojedynczego dokumentu wynosi 16 MB (lub mniej, jeśli przesyłasz zbiorczo wiele dokumentów w jednym żądaniu). Aby uzyskać więcej informacji, zobacz [Limity usług w usłudze Azure Cognitive Search](search-limits-quotas-capacity.md).
>

**Zagadnienia dotyczące woluminu kwerendy**

Zapytania na sekundę (QPS) jest ważną metryką podczas dostrajania wydajności, ale zazwyczaj jest to tylko uwzględnienie warstwy, jeśli oczekujesz dużej liczby zapytań na początku.

Warstwy standardowe mogą zapewnić równowagę replik i partycji. Można zwiększyć realizacji kwerendy, dodając repliki do równoważenia obciążenia lub dodać partycje do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizowanym usłudze.

Jeśli oczekujesz od samego początku dużych trwałych woluminów zapytań, należy wziąć pod uwagę wyższe warstwy standardowe, poparte bardziej zaawansowanym sprzętem. Następnie można przełączyć partycje i repliki w tryb offline, a nawet przełączyć się do usługi niższej warstwy, jeśli te woluminy zapytań nie występują. Aby uzyskać więcej informacji na temat obliczania przepływności zapytania, zobacz [Wydajność i optymalizacja usługi Azure Cognitive Search](search-performance-optimization.md).

Warstwy zoptymalizowane pod kątem magazynu są przydatne w przypadku dużych obciążeń danych, obsługując bardziej ogólny dostępny magazyn indeksu, gdy wymagania dotyczące opóźnień zapytań są mniej ważne. Nadal należy używać dodatkowych replik do równoważenia obciążenia i dodatkowych partycji do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizowanym usłudze.

**Umowy o gwarantowanym poziomie usług**

Funkcje warstwy bezpłatnej i wersji zapoznawczej nie zapewniają [umów na poziomie usług (SLA).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) Dla wszystkich warstw podlegania rozliczania ujedne są skuteczne, gdy aprowizujesz wystarczającą nadmiarowość dla usługi. Musisz mieć dwie lub więcej replik dla zapytań (odczytu) ŁAS. Musisz mieć trzy lub więcej replik dla zapytań i indeksowania (odczytu i zapisu) ŁAW. Liczba partycji nie wpływa na ławy SLA.

## <a name="tips-for-tier-evaluation"></a>Wskazówki dotyczące oceny poziomów

+ Zezwalaj metrykom na tworzenie wokół zapytań i zbieraj dane wokół wzorców użycia (zapytania w godzinach pracy, indeksowanie poza godzinami szczytu). Użyj tych danych, aby informować o decyzjach dotyczących aprowizacji usług. Chociaż nie jest to praktyczne w rytmie godzinowym lub dziennym, można dynamicznie dostosowywać partycje i zasoby, aby uwzględnić planowane zmiany w woluminach zapytań. Można również pomieścić nieplanowane, ale trwałe zmiany, jeśli poziomy posiadają wystarczająco długo, aby uzasadnić podjęcie działań.

+ Należy pamiętać, że jedyną wadą w ramach inicjowania obsługi administracyjnej jest, że może być konieczne zburzyć usługi, jeśli rzeczywiste wymagania są większe niż prognoz. Aby uniknąć zakłóceń w działaniu usługi, należy utworzyć nową usługę w wyższej warstwie i uruchomić ją obok siebie, dopóki wszystkie aplikacje i żądania docelowe nowego punktu końcowego.

## <a name="next-steps"></a>Następne kroki

Zacznij od warstwy bezpłatnej i skompiluj indeks początkowy przy użyciu podzbioru danych, aby zrozumieć jego właściwości. Struktura danych w usłudze Azure Cognitive Search jest odwróconą strukturą indeksu. Rozmiar i złożoność odwróconego indeksu zależy od zawartości. Należy pamiętać, że wysoce nadmiarowa zawartość ma tendencję do mniejszego indeksu niż zawartość o bardzo nieregularnej zawartości. Więc charakterystyki zawartości, a nie rozmiar zestawu danych określają wymagania dotyczące magazynu indeksu.

Po wstępnym oszacowaniu rozmiaru indeksu [aprowizuj usługę podlegania rozliczaniu](search-create-service-portal.md) w jednej z warstw omówionych w tym artykule: Zoptymalizowana pod kątem podstawowej, standardowej lub magazynu. Rozluźnij wszelkie sztuczne ograniczenia dotyczące rozmiaru danych i [odbuduj indeks,](search-howto-reindex.md) aby uwzględnić wszystkie dane, które mają być przeszukiwane.

[Przydzielanie partycji i replik w](search-capacity-planning.md) razie potrzeby, aby uzyskać wydajność i skalę, której potrzebujesz.

Jeśli wydajność i pojemność są w porządku, gotowe. W przeciwnym razie ponownie utwórz usługę wyszukiwania w innej warstwie, która jest bardziej zgodna z twoimi potrzebami.

> [!NOTE]
> Jeśli masz pytania, opublikuj w [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).
