---
title: Utwórz indeks wyszukiwania w Azure Portal
titleSuffix: Azure Cognitive Search
description: Utwórz, Załaduj i zbadaj pierwszy indeks wyszukiwania za pomocą Kreatora importu danych w Azure Portal. Ten przewodnik Szybki Start używa fikcyjnego zestawu danych hotelu do próbkowania.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1be165bfe7cca44e8a928933c3c8fe926ad7d4c9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694839"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w Azure Portal

Utwórz swój pierwszy indeks wyszukiwania za pomocą kreatora **importu danych** i wbudowanego przykładowego źródła danych zawierającego fikcyjne dane hotelowe. Kreator poprowadzi Cię przez proces tworzenia indeksu wyszukiwania (Hotele-Sample-index), dzięki czemu możesz pisać interesujące zapytania w ciągu kilku minut. 

Mimo że nie będziesz używać opcji w tym przewodniku Szybki Start, Kreator zawiera stronę dla wzbogacania AI, dzięki czemu można wyodrębnić tekst i strukturę z plików obrazów i tekstu bez struktury. Aby zapoznać się z podobnym przewodnikiem zawierającym wzbogacenie AI, zobacz [Szybki Start: Tworzenie zestawu umiejętności poznawczych](cognitive-search-quickstart-blob.md).

## <a name="prerequisites"></a>Wymagania wstępne

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Azure Wyszukiwanie poznawcze (dowolna warstwa, dowolny region). [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

### <a name="check-for-space"></a>Sprawdzanie ilości wolnego miejsca

Wielu klientów zaczyna od bezpłatnej usługi. Warstwa Bezpłatna jest ograniczona do trzech indeksów, trzech źródeł danych i trzech indeksatorów. Przed rozpoczęciem upewnij się, że dysponujesz miejscem na dodatkowe elementy. W ramach tego samouczka tworzony jest jeden obiekt każdego typu.

Zapoznaj się ze stroną przegląd usługi, aby dowiedzieć się, ile indeksów, indeksatorów i źródeł danych już istnieje. 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="Listy indeksów, indeksatorów i źródeł danych":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Tworzenie indeksu i ładowanie danych

Zapytania wyszukiwania używają iteracyjnie [*indeksu*](search-what-is-an-index.md) zawierającego dane z możliwością wyszukiwania, metadane i dodatkowe konstrukcje, które optymalizują określone zachowania związane z wyszukiwaniem.

W tym samouczku użyjemy wbudowanego przykładowego zestawu danych, który można przeszukiwać przy użyciu [*indeksatora*](search-indexer-overview.md) za pomocą [kreatora **importu danych**](search-import-data-portal.md). Indeksator to przeszukiwarka specyficzna dla źródła, mająca możliwość odczytu metadanych i zawartości z obsługiwanych źródeł danych platformy Azure. Indeksatory są zazwyczaj używane programowo, ale w portalu można uzyskać do nich dostęp za pośrednictwem kreatora **Importowanie danych**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Krok 1 — Uruchomienie Kreatora importowania danych i utworzenie źródła danych

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

1. [Znajdź usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) i na stronie Przegląd kliknij pozycję **Importuj dane** na pasku poleceń, aby utworzyć i wypełnić indeks wyszukiwania.

   :::image type="content" source="media/search-get-started-portal/import-data-cmd.png" alt-text="Polecenie importu danych":::

1. W Kreatorze kliknij pozycję **Połącz z danymi**  >    >  **przykładowymi hoteli**. To źródło danych jest wbudowane. Podczas tworzenia własnego źródła danych należy określić nazwę, typ i informacje o połączeniu. Po utworzeniu staje się ono „istniejącym źródłem danych”, które może zostać ponownie użyte w innych operacjach importu.

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="Wybieranie przykładowego zestawu danych":::

1. Przejdź do następnej strony.

### <a name="step-2---skip-the-enrich-content-page"></a>Krok 2. pomijanie strony "wzbogacanie zawartości"

Kreator obsługuje tworzenie [potoku wzbogacania AI](cognitive-search-concept-intro.md) do dołączania Cognitive Servicesych algorytmów AI do indeksowania. 

Na razie pominiemy ten krok i przejdziemy bezpośrednio do kroku **Dostosowywanie indeksu docelowego**.

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="Pomijanie kroku Umiejętności poznawcze":::

> [!TIP]
> Możesz przejść przez przykładową procedurę indeksowania AI w [przewodniku szybki start](cognitive-search-quickstart-blob.md) lub [samouczku](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Krok 3 — Konfigurowanie indeksu

Dla wbudowanych indeksów przykładowych hoteli jest zdefiniowany domyślny schemat indeksu. Z wyjątkiem kilku zaawansowanych przykładów filtru zapytania w dokumentacji i przykłady, które są przeznaczone dla indeksu hotelu-Samples, będą uruchamiane w tej definicji indeksu:

:::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="Wygenerowany indeks hoteli":::

Zwykle w ćwiczeniu opartym na kodzie tworzenie indeksu zostało zakończone przed załadowaniem danych. Kreator importu danych skrapla te kroki, generując podstawowy indeks dla dowolnego źródła danych, które można przeszukać. Indeks wymaga co najmniej nazwy i kolekcji pól. Jedno z pól powinno być oznaczone jako klucz dokumentu w celu jednoznacznego zidentyfikowania poszczególnych dokumentów. Ponadto można określić analizatory języka lub sugestory, aby stosować autouzupełnianie lub sugerowane zapytania.

Pola mają typy danych i atrybuty. Pola wyboru w górnej części są *atrybutami indeksu*, które kontrolują sposób używania pola.

+ **Pobieranie** oznacza, że pole jest wyświetlane na liście wyników wyszukiwania. Można oznaczyć pojedyncze pola jako limity dla wyników wyszukiwania, czyszcząc to pole wyboru, na przykład dla pól używanych tylko w wyrażeniach filtru.
+ **Klucz** to unikatowy identyfikator dokumentu. Zawsze jest ciągiem znaków i jest wymagany.
+ Atrybuty **Możliwość filtrowania**, **Z możliwością sortowania** i **Możliwość tworzenia aspektów** określają, czy pola mogą być używane w strukturze nawigacji filtrowania, sortowania lub tworzenia aspektów.
+ **Wyszukiwanie** oznacza, że pole jest uwzględniane podczas wyszukiwania pełnotekstowego. Ciągi można przeszukiwać. Pól liczbowych i logicznych często nie można wyszukiwać.

Wymagania dotyczące rozmiaru magazynu nie są zależne od Twojego wyboru. Na przykład jeśli ustawisz atrybut **Możliwość pobierania** dla wielu pól, nie zwiększy to wymaganego rozmiaru magazynu.

Domyślnie kreator skanuje źródło danych w poszukiwaniu unikatowych identyfikatorów jako podstawy dla pola klucza. *Ciągi* są przypisywane do **pobierania** i **wyszukiwania**. *Liczby całkowite* są przypisywane do **pobierania**, **filtrowania**, **sortowania** i **tworzenia.**

1. Zaakceptuj wartości domyślne.

   Jeśli ponownie uruchomisz kreatora przy użyciu istniejącego źródła danych hoteli, indeks nie zostanie skonfigurowany z domyślnymi atrybutami. Konieczne będzie ręczne wybranie atrybutów podczas przyszłych importów. 

1. Przejdź do następnej strony.

### <a name="step-4---configure-indexer"></a>Krok 4 — Konfigurowanie indeksatora

W kreatorze **importu danych** kliknij pozycję **Indeksator** > **Nazwa**, a następnie wpisz nazwę indeksatora.

Ten obiekt definiuje proces wykonywalny. Możesz go uruchamiać w ramach harmonogramu cyklicznego, ale na razie użyj opcji domyślnej, aby od razu uruchomić indeksator jeden raz.

Kliknij przycisk **Prześlij**, aby utworzyć i od razu uruchomić indeksator.

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="Indeksator hoteli":::

## <a name="monitor-progress"></a>Monitorowanie postępu

Kreator powinien przenieść Cię do listy Indeksatory, gdzie można monitorować postęp. W przypadku samonawigacji przejdź na stronę przegląd i kliknij kartę **indeksatory** .

Aktualizacja strony w portalu może wymagać kilku minut, ale na liście powinien być widoczny nowo utworzony indeksator w stanie wskazującym na trwające indeksowanie lub jego pomyślne zakończenie wraz z liczbą zindeksowanych dokumentów.

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="Komunikat o postępie indeksatora":::

## <a name="view-the-index"></a>Wyświetlanie indeksu

Strona przegląd usługi zawiera linki do zasobów utworzonych w usłudze Azure Wyszukiwanie poznawcze.  Aby wyświetlić właśnie utworzony indeks, kliknij pozycję **Indeksy** na liście linków. 

Poczekaj na odświeżenie strony portalu. Po kilku minutach powinien zostać wyświetlony indeks z liczbą dokumentów i rozmiarem magazynu.

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="Lista Indeksy na pulpicie nawigacyjnym usługi":::

Z tej listy możesz kliknąć właśnie utworzony indeks *hoteli* , wyświetlić schemat indeksu. i opcjonalnie dodać nowe pola. 

Karta **Pola** zawiera schemat indeksu. Jeśli piszesz zapytania i chcesz sprawdzić, czy pole jest filtrowane lub do sortowania, na tej karcie są wyświetlane atrybuty.

Przewiń listę w dół, aby wprowadzić nowe pole. Mimo że zawsze można utworzyć nowe pole, w większości przypadków nie można zmienić istniejących pól. Istniejące pola mają fizyczną reprezentację w usłudze wyszukiwania i nie są modyfikowane, a nawet w kodzie. Aby całkowicie zmienić istniejące pole, utwórz nowy indeks, usuwając poprzednią wersję.

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="Przykładowa definicja indeksu":::

Inne konstrukcje, takie jak profile oceniania i opcje CORS, można dodać w dowolnym momencie.

Aby dobrze zaznajomić się z elementami, które można i których nie można edytować podczas projektowania indeksu, poświęć chwilę na zapoznanie się z opcjami definicji indeksu. Opcje wyświetlane na szaro oznaczają, że danej wartości nie można zmodyfikować ani usunąć. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Tworzenie zapytań za pomocą Eksploratora wyszukiwania

Indeks wyszukiwania powinien być teraz gotowy do wykonywania względem niego zapytań za pomocą wbudowanej strony zapytań, noszącej nazwę [**Eksplorator wyszukiwania**](search-explorer.md). Zawiera ona pole wyszukiwania, umożliwiające testowanie dowolnych ciągów zapytań.

**Eksplorator wyszukiwania** jest przystosowany tylko do obsługi [żądań interfejsu API REST](/rest/api/searchservice/search-documents), ale akceptuje zarówno [prostą składnię zapytań](/rest/api/searchservice/simple-query-syntax-in-azure-search), jak i składnię [pełnego analizatora zapytań Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search), a także wszystkie parametry wyszukiwania dostępne w ramach operacji [interfejsu API REST wyszukiwania dokumentów](/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> Poniższe kroki przedstawiono na stronie 6m08s na temat wideo z [omówieniem usługi Azure wyszukiwanie poznawcze](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="Polecenie Eksploratora wyszukiwania":::

1. Z listy rozwijanej **indeks** wybierz pozycję  *Hotele-przykład — indeks*. Kliknij listę rozwijaną **wersja interfejsu API** , aby zobaczyć, które interfejsy API REST są dostępne. W przypadku zapytań poniżej Użyj ogólnie dostępnej wersji (2020-06-30).

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="Polecenia indeksu i interfejsu API":::

1. Na pasku wyszukiwania wklej poniższe ciągi zapytań, a następnie kliknij pozycję **Wyszukaj**.

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="Ciąg zapytania i przycisk Wyszukaj":::

## <a name="example-queries"></a>Przykładowe zapytania

Można wprowadzać terminy i frazy (podobnie jak w wyszukiwaniu Bing lub Google) lub w pełni określone wyrażenia zapytania. Wyniki są zwracane w postaci pełnych dokumentów JSON.

### <a name="simple-query-with-top-n-results"></a>Proste zapytanie z pierwszymi N wynikami

#### <a name="example-string-query-searchspa"></a>Przykład (zapytanie w postaci ciągu znaków): `search=spa`

+ Parametr **wyszukiwania** służy do wprowadzania słowa kluczowego wyszukiwania dla wyszukiwania pełnotekstowego, w tym przypadku zwracającego dane hotelu dla tych zawierających *Spa* w dowolnym polu możliwym do przeszukania w dokumencie.

+ **Eksplorator wyszukiwania** zwraca wyniki w formacie JSON, które są szczegółowe i trudne do odczytania, jeśli dokumenty mają gęstą strukturę. Jest to zamierzone. Widoczność całego dokumentu jest ważna dla celów programowania, zwłaszcza podczas testów. Aby zwiększyć wygodę użytkowania, należy napisać kod [obsługujący wyniki wyszukiwania](search-pagination-page-layout.md) w celu wyróżnienia istotnych elementów.

+ Dokumenty składają się ze wszystkich pól oznaczonych jako możliwe do pobierania w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij pozycję *Hotele — przykład* na liście **indeksy** .

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Przykład (zapytanie sparametryzowane): `search=spa&$count=true&$top=10`

+ **&** Symbol służy do dołączania parametrów wyszukiwania, które można określić w dowolnej kolejności.

+ Parametr **$Count = true** zwraca łączną liczbę wszystkich zwróconych dokumentów. Ta wartość jest wyświetlana na początku listy wyników wyszukiwania. Możesz zweryfikować zapytania filtru, monitorując zmiany raportowane przez parametr **$count=true**. Zmniejszająca się liczba wskazuje, że filtr działa.

+ **$Top = 10** zwraca najwyższy rangę 10 dokumentów z sumy. Domyślnie usługa Azure Wyszukiwanie poznawcze zwraca pierwsze najlepsze dopasowanie 50. Tę liczbę możesz zwiększyć lub zmniejszyć za pomocą parametru **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a> Filtrowanie zapytania

Żądania wyszukiwania zawierają filtry, jeśli zostanie dodany parametr **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Przykład (filtrowane): `search=beach&$filter=Rating gt 4`

+ Parametr **$filter** zwraca wyniki spełniające podane kryteria. W takim przypadku oceny są większe niż 4.

+ Składnia filtru to konstrukcja OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

### <a name="facet-the-query"></a><a name="facet-query"></a> Tworzenie aspektu zapytania

Filtry aspektów są uwzględniane w żądaniach wyszukiwania. Korzystając z parametru facet, można zwrócić zagregowaną liczbę dokumentów, które są zgodne z podaną wartością aspektu.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Przykład (aspektowe ze zmniejszeniem zakresu): `search=*&facet=Category&$top=2`

+ Parametr **search=*** to puste wyszukiwanie. Puste wyszukiwania umożliwiają znalezienie wszystkiego. Jednym z powodów przesłania pustego zapytania może być potrzeba przeprowadzenia filtrowania lub utworzenia aspektów pełnego zestawu dokumentów. Na przykład, chcesz, aby struktura nawigacji aspektów zawierała wszystkie hotele w indeksie.
+ Parametr **facet** zwraca strukturę nawigacji, którą można przekazać do kontrolki interfejsu użytkownika. Zwraca ona kategorie i liczbę elementów. W takim przypadku kategorie są oparte na polu wygodnie o nazwie *Category*. Na platformie Azure Wyszukiwanie poznawcze nie ma agregacji, ale można przybliżyć agregację za pośrednictwem `facet` , co daje liczbę dokumentów w każdej kategorii.

+ Parametr **$top=2** powoduje zwrócenie dwóch dokumentów, co pokazuje, że za pomocą parametru `top` możesz zmniejszyć lub zwiększyć liczbę wyników.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Przykład (aspektowe z wartościami liczbowymi): `search=spa&facet=Rating`

+ To zapytanie jest zestawem reguł dla klasyfikacji podczas wyszukiwania tekstu na potrzeby *Spa*. *Klasyfikacja* warunku może być określona jako aspekt, ponieważ pole jest oznaczone jako możliwe do pobrania, z możliwością filtrowania i do zastosowania w indeksie, a wartości, które zawiera (liczbowe, od 1 do 5), są odpowiednie do kategoryzacji list w grupach.

+ Aspekty mogą być tworzone tylko na podstawie pól z możliwością filtrowania. W wynikach mogą być zwracane tylko pola z możliwością pobierania.

+ Pole *Rating* ma wartość zmiennoprzecinkową o podwójnej precyzji, a grupowanie będzie precyzyjne. Aby uzyskać więcej informacji na temat grupowania według interwału (na przykład "3 gwiazdki Rating", "4 gwiazdki Rating" itp.), zobacz [jak zaimplementować nawigację aspektową w usłudze Azure wyszukiwanie poznawcze](./search-faceted-navigation.md#filter-based-on-a-range).

### <a name="highlight-search-results"></a><a name="highlight-query"></a> Wyróżnianie wyników wyszukiwania

Wyróżnianie trafień oznacza dodawanie formatowania do tekstu zgodnego ze słowem kluczowym w przypadku znalezienia dopasowań w określonym polu. Jeśli termin wyszukiwania jest głęboko ukryty w opisie, możliwe jest dodanie wyróżnienia trafień w celu jego łatwiejszego zauważenia.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Przykład (wyróżnienie): `search=beach&highlight=Description`

+ W tym przykładzie sformatowany wyraz *sekwencje* jest łatwiejszy w polu Opis.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Przykład (analiza lingwistyczna): `search=beaches&highlight=Description`

+ Wyszukiwanie pełnotekstowe rozpoznaje podstawowe odmiany w formularzach programu Word. W takim przypadku wyniki wyszukiwania zawierają wyróżniony tekst dla elementu "sekwencje", w przypadku hoteli, w których pola z możliwością wyszukiwania będą znajdować się w odpowiedzi na wyszukiwanie za pomocą słowa kluczowego "plażach". Różne rodzaje tego samego wyrazu mogą być wyświetlane w wynikach z powodu przeprowadzenia analizy lingwistycznej. 

+ Platforma Azure Wyszukiwanie poznawcze obsługuje analizatory 56 z obu programów — Lucene i Microsoft. Domyślnie używany przez usługę Azure Wyszukiwanie poznawcze jest standardowym analizatorem Lucene.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Testowanie wyszukiwania rozmytego

Domyślnie nieprawidłowo napisane terminy zapytań, takie jak *Seatle* dla "Seattle", nie zwracają dopasowań w typowym wyszukiwaniu. Poniższy przykład nie zwraca żadnych wyników.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Przykład (błąd pisowni, nieobsługiwany): `search=seatle`

Aby obsłużyć błędy pisowni, można użyć wyszukiwania rozmytego. Wyszukiwanie rozmyte jest używane w przypadku korzystania z pełnej składni zapytań Lucene, co wymaga wykonania dwóch czynności: ustawienia parametru **queryType=full** w zapytaniu i dodania znaku **~** do wyszukiwanego ciągu.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Przykład (błąd pisowni, obsługiwany): `search=seatle~&queryType=full`

Ten przykład zwraca teraz dokumenty, które zawierają dopasowania w "Seattle".

Gdy parametr **queryType** jest nieokreślony, używany jest domyślny prosty analizator zapytań. Prosty analizator zapytań jest szybszy, ale jeśli wymagane jest użycie wyszukiwania rozmytego, wyrażeń regularnych, wyszukiwania w sąsiedztwie lub innych zaawansowanych typów zapytań, konieczne będzie korzystanie z pełnej składni.

Wyszukiwanie rozmyte i wyszukiwanie z użyciem symboli wieloznacznych ma wpływ na wyniki wyszukiwania. W przypadku tych formatów zapytań nie jest wykonywana analiza lingwistyczna. Przed użyciem wyszukiwania rozmytego i wieloznacznego Sprawdź, [w jaki sposób wyszukiwanie pełnotekstowe działa na platformie Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md#stage-2-lexical-analysis) i poszukaj sekcji dotyczącej wyjątków w analizie leksykalnej.

Aby uzyskać więcej informacji na temat scenariuszy zapytań włączonych przez analizator pełnych zapytań, zobacz [składnia zapytań Lucene w usłudze Azure wyszukiwanie poznawcze](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Testowanie wyszukiwania geoprzestrzennego

 Wyszukiwanie geoprzestrzenne jest obsługiwane za pośrednictwem [typu danych edm.GeographyPoint](/rest/api/searchservice/supported-data-types) względem pola zawierającego współrzędne. Wyszukiwanie geograficzne jest rodzajem filtru określonego w artykule [Filter OData syntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Przykład (filtry współrzędnych geograficznych): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

To zapytanie przykładowe służy do przefiltrowania wszystkich wyników danych pozycyjnych, które znajdują się w odległości mniejszej niż 5 km od danego punktu (określonego jako szerokość i długość geograficzna). Dodając parametr **$count**, możesz sprawdzić, ile wyników zostanie zwróconych, gdy zmienisz odległość lub współrzędne.

Wyszukiwanie geoprzestrzenne jest przydatne, jeśli aplikacja wyszukiwania ma funkcję „znajdź w pobliżu” lub używa nawigacji mapy. Nie jest to jednak wyszukiwanie pełnotekstowe. Jeśli masz wymagania użytkownika do wyszukiwania w mieście lub kraju/regionie według nazwy, Dodaj pola zawierające nazwy miasta lub kraju/regionu, a także współrzędne.

## <a name="takeaways"></a>Wnioski

Ten samouczek zapewnia szybkie wprowadzenie do usługi Azure Wyszukiwanie poznawcze przy użyciu Azure Portal.

Opisano tworzenie indeksu wyszukiwania przy użyciu kreatora **Importowanie danych**. Zostały przedstawione [indeksatory](search-indexer-overview.md), a także podstawowy przepływ pracy projektowania indeksu, w tym [obsługiwane modyfikacje opublikowanego indeksu](/rest/api/searchservice/update-index).

Przy użyciu **Eksploratora wyszukiwania** w witrynie Azure Portal przedstawiono podstawową składnię zapytań w ramach praktycznych przykładów kluczowych możliwości, takich jak filtry, wyróżnianie trafień, wyszukiwanie rozmyte i wyszukiwanie geograficzne.

Przedstawiono również sposób znajdowania indeksów, indeksatorów i źródeł danych w portalu. Dla dowolnego nowego źródła danych możesz przy użyciu portalu szybko sprawdzić jego definicje lub kolekcje pól przy minimalnym nakładzie pracy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Użyj Kreatora portalu do wygenerowania gotowej do użycia aplikacji sieci Web, która działa w przeglądarce. Możesz wypróbować ten kreator na utworzonym małym indeksie lub użyć jednego z wbudowanych zestawów danych przykładowych, aby skorzystać z bardziej zaawansowanego wyszukiwania.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji demonstracyjnej w portalu](search-create-app-portal.md)