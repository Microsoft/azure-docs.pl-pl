---
title: Tworzenie zapytania semantycznego
titleSuffix: Azure Cognitive Search
description: Ustaw typ zapytania semantycznego, aby dołączyć modele uczenia głębokiego do przetwarzania zapytań, wnioskowanie o zamiar i kontekście w ramach rangi wyszukiwania i jej przydatności.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654666"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Utwórz zapytanie dla podpisów semantycznych w Wyszukiwanie poznawcze

> [!IMPORTANT]
> Wyszukiwanie semantyczne jest w publicznej wersji zapoznawczej, dostępne za pomocą interfejsu API REST i Azure Portal. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Te funkcje są rozliczane. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

W tym artykule dowiesz się, jak sformułować żądanie wyszukiwania używające klasyfikacji semantycznej i zwracające napisy semantyczne (oraz opcjonalnie [odpowiedzi semantyczne](semantic-answers.md)), z wyróżnieniami na najbardziej odpowiednie terminy i frazy. Zarówno napisy, jak i odpowiedzi są zwracane w zapytaniach, które są tworzone przy użyciu typu zapytania "semantyka".

Podpisy i odpowiedzi są wyodrębniane Verbatim z tekstu w dokumencie wyszukiwania. Podsystem semantyczny określa, jaka część zawartości ma cechy podpisu lub odpowiedzi, ale nie tworzy nowych zdań ani fraz. Z tego powodu zawartość obejmująca wyjaśnienia lub definicje działają najlepiej w przypadku wyszukiwania semantycznego.

## <a name="prerequisites"></a>Wymagania wstępne

+ Usługa wyszukiwania w warstwie Standardowa (S1, S2, S3), która znajduje się w jednym z następujących regionów: Północno-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Wschodnie stany USA 2, Europa Północna, Europa Zachodnia. Jeśli masz istniejącą usługę S1 lub większą w jednym z tych regionów, możesz poprosić o dostęp bez konieczności tworzenia nowej usługi.

+ Dostęp do wersji zapoznawczej wyszukiwania semantycznego: [rejestracja](https://aka.ms/SemanticSearchPreviewSignup)

+ Istniejący indeks wyszukiwania zawierający zawartość w języku angielskim

+ Klient wyszukiwania do wysyłania zapytań

  Klient wyszukiwania musi obsługiwać interfejsy API REST w wersji zapoznawczej na żądanie zapytania. Do interfejsów API w wersji zapoznawczej można użyć programu [Poster](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)lub kodu. Możesz również użyć [Eksploratora wyszukiwania](search-explorer.md) w Azure Portal, aby przesłać zapytanie semantyczne.

+ [Żądanie zapytania](/rest/api/searchservice/preview-api/search-documents) musi zawierać opcję semantyczną i inne parametry opisane w tym artykule.

## <a name="whats-a-semantic-query"></a>Co to jest zapytanie semantyczne?

W Wyszukiwanie poznawcze zapytanie jest sparametryzowanym żądaniem, które określa przetwarzanie zapytań i kształt odpowiedzi. *Zapytanie semantyczne* dodaje parametry, które wywołują semantyczny model klasyfikacji, który może ocenić kontekst i znaczenie pasujących wyników, podwyższyć poziom dopasowania do góry i zwrócić semantykę odpowiedzi i podpisy.

Poniższe żądanie jest reprezentatywne dla minimalnej kwerendy semantycznej (bez odpowiedzi).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Podobnie jak w przypadku wszystkich zapytań w Wyszukiwanie poznawcze, żądanie odwołuje się do kolekcji dokumentów jednego indeksu. Co więcej, zapytanie semantyczne przeprowadzi tę samą sekwencję analizy, analizy, skanowania i oceny jako zapytania niesemantycznego. 

Różnica polega na znaczeniu i ocenie. Zgodnie z definicją w tej wersji zapoznawczej, zapytanie semantyczne jest jednym, którego *wyniki* są ponownie klasyfikowane przy użyciu modelu języka semantycznego, co pozwala na wykorzystanie dopasowań uznawanych za najbardziej odpowiednie przez rangę semantyczną, a nie wyniki przypisane przez domyślny algorytm klasyfikacji podobieństwa.

Tylko górne dopasowania 50 z wyników początkowych mogą być semantycznie klasyfikowane i wszystkie zawierają podpisy w odpowiedzi. Opcjonalnie można określić **`answer`** parametr w żądaniu w celu wyodrębnienia potencjalnej odpowiedzi. Aby uzyskać więcej informacji, zobacz [odpowiedzi semantyczne](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Wykonywanie zapytań przy użyciu Eksploratora wyszukiwania

[Eksplorator wyszukiwania](search-explorer.md) został zaktualizowany w celu uwzględnienia opcji zapytań semantycznych. Te opcje staną się widoczne w portalu po wykonaniu następujących kroków:

1. [Utwórz konto](https://aka.ms/SemanticSearchPreviewSignup) i Admittance usługi wyszukiwania w programie w wersji zapoznawczej

1. Otwórz Portal przy użyciu następującej składni: `https://portal.azure.com/?feature.semanticSearch=true`

Opcje zapytania obejmują przełączniki umożliwiające włączenie zapytań semantycznych, searchFields i korekcji pisowni. Wymagane parametry zapytania można także wkleić do ciągu zapytania.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Opcje zapytania w Eksploratorze wyszukiwania" border="true":::

## <a name="query-using-rest"></a>Zapytanie przy użyciu REST

Użyj [dokumentów wyszukiwania (wersja zapoznawcza REST)](/rest/api/searchservice/preview-api/search-documents) , aby programowo sformułować żądanie.

Odpowiedź zawiera podpisy i wyróżnianie automatycznie. Jeśli chcesz, aby odpowiedź zawierała korekcję pisowni lub odpowiedzi, **`speller`** w żądaniu Dodaj opcjonalny lub **`answers`** parametr.

Poniższy przykład używa hoteli-Sample-index do tworzenia żądania zapytania semantycznego z odpowiedziami semantycznymi i napisami:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Poniższa tabela zawiera podsumowanie parametrów zapytania używanych w kwerendzie semantycznej, dzięki czemu można je zobaczyć całościowo. Aby uzyskać listę wszystkich parametrów, zobacz [Wyszukiwanie dokumentów (wersja zapoznawcza REST)](/rest/api/searchservice/preview-api/search-documents)

| Parametr | Typ | Opis |
|-----------|-------|-------------|
| Znak | Ciąg | Prawidłowe wartości to proste, pełne i semantyczne. Dla zapytań semantycznych jest wymagana wartość "semantyka". |
| queryLanguage | Ciąg | Wymagane dla zapytań semantycznych. Obecnie jest zaimplementowana tylko wartość "en-us". |
| searchFields | Ciąg | Rozdzielana przecinkami lista pól do przeszukiwania. Określa pola, w których występuje Klasyfikacja semantyczna, z której są wyodrębniane napisy i odpowiedzi. </br></br>W przeciwieństwie do prostych i pełnych typów zapytań, kolejność, w której pola są wyświetlane, określa pierwszeństwo. Aby uzyskać więcej informacji na temat sposobu użycia, zobacz [krok 2: Set searchFields](#searchfields). |
| sprawdzania pisowni | Ciąg | Opcjonalny parametr, niecharakterystyczny dla zapytań semantycznych, który poprawia błędne pisownię warunków przed osiągnięciem przez nich aparatu wyszukiwania. Aby uzyskać więcej informacji, zobacz [Dodawanie poprawek pisowni do zapytań](speller-how-to-add.md). |
| uzyskiwan |Ciąg | Parametry opcjonalne, które określają, czy odpowiedzi semantyczne są uwzględniane w wyniku. Obecnie tylko "Wyodrębnianie" jest implementowane. Odpowiedzi można skonfigurować tak, aby zwracały maksymalnie pięć. Wartość domyślna to 1. Ten przykład przedstawia liczbę trzech odpowiedzi: "Extract \| count3" ". Aby uzyskać więcej informacji, zobacz [Zwróć odpowiedzi semantyczne](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formułowanie żądania

W tej sekcji opisano parametry zapytania niezbędne do przeszukiwania semantycznego.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Krok 1. Ustawianie wartości querytype i queryLanguage

Dodaj następujące parametry do reszty. Oba parametry są wymagane.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

QueryLanguage musi być zgodna z wszystkimi [analizatorami języka](index-add-language-analyzers.md) przypisanymi do definicji pól w schemacie indeksu. Jeśli queryLanguage ma wartość "en-us", wszystkie analizatory języka muszą być również w języku angielskim ("en. Microsoft" lub "en. Lucene"). Wszystkie analizatory języka niezależny od, takie jak słowo kluczowe lub proste, nie mają konfliktu z wartościami queryLanguage.

W żądaniu zapytania, jeśli używasz również [korekcji pisowni](speller-how-to-add.md), ustawiana queryLanguage jest stosowana równomiernie z pisownią, odpowiedziami i napisami. Nie istnieje przesłonięcie poszczególnych części. 

Gdy zawartość w indeksie wyszukiwania może być złożona w wielu językach, dane wejściowe zapytania najprawdopodobniej są w jednym. Aparat wyszukiwania nie sprawdza zgodności queryLanguage, analizatora języka i języka, w którym składa się zawartość, dlatego należy odpowiednio wprowadzić zakres zapytań, aby uniknąć tworzenia nieprawidłowych wyników.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Krok 2. Ustawianie searchFields

Parametr searchFields służy do identyfikowania fragmentów, które mają zostać ocenione pod kątem "podobieństwa semantycznego" do zapytania. W przypadku wersji zapoznawczej nie zaleca się pozostawienia searchFields pustej, ponieważ model wymaga wskazówki co do tego, jakie pola są najważniejsze do przetworzenia.

Kolejność searchFields jest krytyczna. Jeśli używasz już searchFields w istniejącym kodzie dla prostych lub pełnych zapytań Lucene, ponownie odwiedź ten parametr, aby sprawdzić kolejność pól podczas przełączania do typu zapytania semantycznego.

Dla co najmniej dwóch searchFields:

+ Uwzględnij tylko pola ciągów i pola ciągów najwyższego poziomu w kolekcjach. Jeśli dołączysz pola niebędące ciągami lub pola niższego poziomu w kolekcji, nie ma błędów, ale te pola nie będą używane w klasyfikacji semantycznej.

+ Pierwsze pole powinno być zawsze zwięzłe (takie jak tytuł lub nazwa), najlepiej pod 25 słowami.

+ Jeśli indeks zawiera pole adresu URL, które jest typu tekstowego (np `www.domain.com/name-of-the-document-and-other-details` ., a nie na stanowisko, na przykład `www.domain.com/?id=23463&param=eis` ), umieść je na liście (lub najpierw Jeśli nie ma zwięzłego pola title).

+ Obserwuj te pola według opisowych pól, w których można znaleźć odpowiedzi na zapytania semantyczne, takie jak główna zawartość dokumentu.

Jeśli określone jest tylko jedno pole, użyj pola opisowego, w którym można znaleźć odpowiedzi na zapytania semantyczne, takie jak główna zawartość dokumentu. 

#### <a name="step-3-remove-orderby-clauses"></a>Krok 3. Usuwanie klauzul orderBy

Usuń wszystkie klauzule orderBy, jeśli istnieją one w istniejącym żądaniu. Wynik semantyczny jest używany do porządkowania wyników, a jeśli dołączysz jawną logikę sortowania, zwracany jest błąd HTTP 400.

#### <a name="step-4-add-answers"></a>Krok 4. Dodawanie odpowiedzi

Opcjonalnie możesz dodać "odpowiedzi", jeśli chcesz dołączyć dodatkowe przetwarzanie, które zapewnia odpowiedź. Odpowiedzi (i podpisy) są wyodrębniane z fragmentów znalezionych w polach wymienionych w searchFields. Pamiętaj, aby uwzględnić pola rozbudowane zawartości w programie searchFields, aby uzyskać najlepsze odpowiedzi w odpowiedzi. Aby uzyskać więcej informacji, zobacz [jak zwracać semantykę odpowiedzi](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>Krok 5. Dodawanie innych parametrów

Ustaw wszystkie inne parametry, które mają być używane w żądaniu. Parametry, takie jak [pisownia](speller-how-to-add.md), [wybór](search-query-odata-select.md)i liczba zwiększają jakość żądania i czytelności odpowiedzi.

Opcjonalnie można dostosować styl podświetlania zastosowany do napisów. Podpisy mają wyróżnione formatowanie dla fragmentów klucza w dokumencie, który podsumowuje odpowiedź. Wartość domyślna to `<em>`. Jeśli chcesz określić typ formatowania (na przykład żółte tło), możesz ustawić highlightPreTag i highlightPostTag.

## <a name="evaluate-the-response"></a>Ocenianie odpowiedzi

Podobnie jak w przypadku wszystkich zapytań, odpowiedź składa się ze wszystkich pól oznaczonych jako możliwy do pobierania lub tylko te pola wymienione w parametrze SELECT. Zawiera on oryginalny wynik istotności i może również obejmować licznik lub wyniki wsadowe w zależności od sposobu sformułowania żądania.

W kwerendzie semantycznej odpowiedź zawiera dodatkowe elementy: nowy, semantycznie ranga Ocena istotności, napisy w postaci zwykłego tekstu i z wyróżnieniami oraz opcjonalnie odpowiedź.

W aplikacji klienckiej można tworzyć struktury strony wyszukiwania w taki sposób, aby zawierała podpis jako opis dopasowania, a nie całej zawartości określonego pola. Jest to przydatne, gdy pojedyncze pola są zbyt gęste dla strony wyników wyszukiwania.

Odpowiedź na powyższe przykładowe zapytanie zwraca następujące dopasowanie jako pierwsze pobranie. Podpisy są zwracane automatycznie, z zwykłym tekstem i wyróżnionymi wersjami. Odpowiedzi są pomijane z przykładu, ponieważ nie można określić dla tego konkretnego zapytania i korpus.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Następne kroki

Odwołaj tę klasyfikację semantyczną i odpowiedzi są kompilowane nad początkowym zestawem wyników. Każda logika, która poprawia jakość początkowych wyników, będzie przekazywać do wyszukiwania semantycznego. W następnym kroku zapoznaj się z funkcjami, które przyczyniają się do wyników początkowych, w tym analizatorów, które wpływają na sposób tworzenia tokenów ciągów, profile oceniania, które mogą dostosowywać wyniki, oraz domyślny algorytm istotności.

+ [Analizatory do przetwarzania tekstu](search-analyzers.md)
+ [Algorytm klasyfikacji podobieństwa](index-similarity-and-scoring.md)
+ [Profile oceniania](index-add-scoring-profiles.md)
+ [Omówienie wyszukiwania semantycznego](semantic-search-overview.md)
+ [Algorytm klasyfikacji semantycznej](semantic-ranking.md)
