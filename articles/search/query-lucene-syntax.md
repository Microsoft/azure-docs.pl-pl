---
title: Składnia zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Informacje dotyczące pełnej składni zapytań Lucene w usłudze Azure Wyszukiwanie poznawcze w przypadku symboli wieloznacznych, wyszukiwania rozmytego, wyrażenia regularnego i innych zaawansowanych konstrukcji zapytań.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: fc3662d8198e6ab6ab215ac1e9e8eac585f4250b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801591"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Składnia zapytań Lucene w usłudze Azure Wyszukiwanie poznawcze

Podczas tworzenia zapytań można wybrać składnię [analizatora zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) dla zapytań wyspecjalizowanych: symbol wieloznaczny, Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, wyrażenia regularne. Większość składni analizatora zapytań Lucene jest [zaimplementowana w usłudze Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md), z wyjątkiem *wyszukiwania zakresu* , które są tworzone za pomocą **`$filter`** wyrażeń. 

Aby użyć pełnej składni Lucene, należy ustawić dla elementu querytype wartość "Full" i przekazać wyrażenie zapytania dla symboli wieloznacznych, rozmytego wyszukiwania lub jednego z innych formularzy zapytań obsługiwanych przez pełną składnię. W pozostałej części wyrażenia zapytań są udostępniane w **`search`** parametrze żądania [przeszukiwania dokumentów (interfejs API REST)](/rest/api/searchservice/search-documents) .

## <a name="example-full-syntax"></a>Przykład (Pełna składnia)

Poniższy przykład to żądanie wyszukiwania skonstruowane przy użyciu pełnej składni. W tym konkretnym przykładzie przedstawiono sposób wyszukiwania w polu i zwiększania warunków. Szuka hoteli, w których pole Category zawiera termin "budżet". Wszystkie dokumenty zawierające frazę "ostatnio Renovated" są bardziej klasyfikowane jak w wyniku okresu zwiększania wartości (3).  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

Gdy nie jest określony dla żadnego typu zapytania, **`searchMode`** parametr jest istotny w tym przykładzie. Zawsze, gdy operatory znajdują się w zapytaniu, należy ogólnie ustawić, `searchMode=all` Aby upewnić się, że *wszystkie* kryteria są zgodne.  

Aby uzyskać więcej przykładów, zobacz [przykłady składni zapytań Lucene](search-query-lucene-examples.md). Aby uzyskać szczegółowe informacje na temat żądania zapytania i parametrów, w tym wyszukiwania, zobacz [dokumenty wyszukiwania (interfejs API REST)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Podstawy składni  

Poniższe podstawowe informacje o składni dotyczą wszystkich zapytań, które używają składni Lucene.  

### <a name="operator-evaluation-in-context"></a>Obliczanie operatora w kontekście

Umieszczanie określa, czy symbol jest interpretowany jako operator, czy po prostu innego znaku w ciągu.

Na przykład w pełnej składni Lucene jest wyrażenie tyldy (~) używane do wyszukiwania rozmytego i wyszukiwania bliskości. Po umieszczeniu w cudzysłowie, ~ wywołuje wyszukiwanie zbliżeniowe. Po umieszczeniu na końcu okresu, ~ wywołuje rozmyte wyszukiwanie.

W ramach terminu, takiego jak "Business ~ analityk", znak nie jest obliczany jako operator. W takim przypadku zakładając, że zapytanie jest wyrażeniem terminu lub frazy, [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) z [analizą leksykalną](search-lucene-query-architecture.md#stage-2-lexical-analysis) powoduje przełączenie ~ i przerywa okres "Business" analityka w dwóch: Business lub analityku.

Powyższym przykładem jest Tylda (~), ale ta sama zasada ma zastosowanie do każdego operatora.

### <a name="escaping-special-characters"></a>Znaki specjalne ucieczki

Aby użyć dowolnego operatora wyszukiwania jako części tekstu wyszukiwania, należy wprowadzić znak ucieczki, wpisując jego prefiks pojedynczym ukośnikiem odwrotnym ( `\` ). Na przykład dla wyszukiwania wieloznacznego `https://` , gdzie `://` jest częścią ciągu zapytania, należy określić `search=https\:\/\/*` . Podobnie wzorzec numeru telefonu może wyglądać następująco `\+1 \(800\) 642\-7676` .

Znaki specjalne, które wymagają ucieczki, są następujące:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Mimo że ucieczki przechowuje tokeny, [Analiza leksykalna](search-lucene-query-architecture.md#stage-2-lexical-analysis) podczas indeksowania może je rozdzielić. Na przykład standardowy Analizator Lucene spowoduje przerwanie wyrazów w łącznikach, odstępach i innych znakach. Jeśli w ciągu zapytania są wymagane znaki specjalne, może być konieczne Analizator, który zachowuje je w indeksie. Niektóre z nich to analizatory [języka](index-add-language-analyzers.md)naturalnego firmy Microsoft, które zachowują wyrazy z wyrazami lub niestandardową, aby uzyskać bardziej złożone wzorce. Aby uzyskać więcej informacji, zobacz [częściowe terminy, wzorce i znaki specjalne](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie znaków niebezpiecznych i zastrzeżonych w adresach URL

Upewnij się, że wszystkie znaki niebezpieczne i zarezerwowane są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest identyfikatorem fragmentu/kotwicą w adresie URL. Znak musi być zakodowany `%23` w przypadku użycia w adresie URL. "&" i "=" są przykładami znaków zarezerwowanych, ponieważ oddzielają one parametry i określają wartości na platformie Azure Wyszukiwanie poznawcze. Aby uzyskać więcej informacji, zobacz [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

Niebezpieczne znaki to ``" ` < > # % { } | \ ^ ~ [ ]`` . Znaki zarezerwowane są `; / ? : @ = + &` .

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Operatory logiczne

Operatory logiczne można osadzić w ciągu zapytania, aby zwiększyć dokładność dopasowania. Pełna składnia obsługuje operatory tekstowe oprócz operatorów znaków. Zawsze określaj operatory wartości tekstowych (i, lub, nie) we wszystkich wersalikach.

|Operator tekstowy | Znak | Przykład | Użycie |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | Określa warunki, które muszą zawierać dopasowanie. W przykładzie aparat zapytań będzie szukał dokumentów zawierających zarówno `wifi` i `luxury` . Znak plus ( `+` ) jest używany dla wymaganych warunków. Na przykład program `+wifi +luxury` określa, że oba warunki muszą znajdować się gdzieś w polu jednego dokumentu.|
| LUB | `|` | `wifi | luxury` | Znajduje dopasowanie, gdy zostanie znaleziony termin. W przykładzie aparat zapytań zwróci dopasowanie do dokumentów zawierających jeden `wifi` lub `luxury` lub oba te elementy. Ponieważ lub jest domyślnym operatorem połączenia, można go również pozostawić, tak aby `wifi luxury` był odpowiednikiem  `wifi | luxury` .|
| NOT | `!`, `-` | `wifi –luxury` | Zwraca dopasowania w dokumentach, które wykluczają termin. Program przeszuka na przykład `wifi –luxury` dokumenty, które mają termin, `wifi` ale nie `luxury` . <br/><br/>`searchMode`Parametr żądania zapytania kontroluje, czy termin z operatorem NOT jest ANDed lub logicznie innym warunkiem w zapytaniu (przy założeniu, że nie ma `+` `|` operatora OR w innych warunkach). Prawidłowe wartości to include `any` lub `all` .  <br/><br/>`searchMode=any` zwiększa odwoływanie zapytań przez dołączenie większej liczby wyników i domyślnie `-` będzie interpretowane jako "lub" nie ". Na przykład program `wifi -luxury` będzie pasował do dokumentów, które zawierają termin `wifi` lub te, które nie zawierają warunków `luxury` .  <br/><br/>`searchMode=all` zwiększa precyzję zapytań, dołączając mniejszą liczbę wyników i domyślnie — będzie interpretowana jako "i". Na przykład program `wifi -luxury` będzie pasował do dokumentów zawierających termin `wifi` i nie zawiera terminu "możliwość zaprojektowania". Jest to raczej bardziej intuicyjne zachowanie `-` operatora. W związku z tym należy rozważyć użycie zamiast tego, `searchMode=all` `searchMode=any` Jeśli chcesz zoptymalizować wyszukiwanie pod kątem precyzji zamiast odwołania, *a* użytkownicy często używają `-` operatora w wyszukiwaniach.<br/><br/>Podczas decydowania o `searchMode` ustawieniu należy wziąć pod uwagę wzorce interakcji użytkownika dotyczące zapytań w różnych aplikacjach. Użytkownicy poszukujący informacji mogą dołączać operator do zapytania, w przeciwieństwie do witryn handlu elektronicznego, które mają bardziej wbudowaną strukturę nawigacji. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Wyszukiwanie polowe

Można zdefiniować operację wyszukiwania w polu z `fieldName:searchExpression` składnią, gdzie wyrażenie wyszukiwania może być pojedynczym słowem lub frazą lub bardziej skomplikowanym wyrażeniem w nawiasach, opcjonalnie z operatorami logicznymi. Oto kilka przykładów:  

- Gatunek: nie historia Jazz  

- artyści:("mile Davis" "Jan Coltrane")

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były oceniane jako pojedyncze jednostki, w tym przypadku wyszukiwanie dwóch odrębnych artystów w `artists` polu.  

Pole określone w elemencie `fieldName:searchExpression` musi być `searchable` polem.  Aby uzyskać szczegółowe informacje na temat sposobu używania atrybutów indeksu w definicjach pól, zobacz [create index](/rest/api/searchservice/create-index) .  

> [!NOTE]
> W przypadku korzystania z wyrażeń wyszukiwania w polu nie trzeba używać `searchFields` parametru, ponieważ każde wyrażenie wyszukiwania w polu ma jawnie określoną nazwę pola. Jednak nadal można użyć `searchFields` parametru, jeśli chcesz uruchomić kwerendę, w której niektóre części są objęte zakresem określonego pola, a reszta może mieć zastosowanie do kilku pól. Na przykład zapytanie `search=genre:jazz NOT history&searchFields=description` byłoby zgodne tylko z `jazz` `genre` polem, podczas gdy byłoby zgodne `NOT history` z `description` polem. Nazwa pola podana w `fieldName:searchExpression` zawsze ma pierwszeństwo przed `searchFields` parametrem, co oznacza, że w tym przykładzie nie trzeba dołączać do `genre` `searchFields` parametru.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Wyszukiwanie rozmyte

Wyszukiwanie rozmyte umożliwia znalezienie dopasowań w warunkach, które mają podobną konstrukcję, i rozszerza termin do maksymalnie 50 warunków, które spełniają kryteria odległości co najmniej dwóch. Aby uzyskać więcej informacji, zobacz [Wyszukiwanie rozmyte](search-query-fuzzy.md).

Aby wykonać Wyszukiwanie rozmyte, Użyj symbolu "~" na końcu pojedynczego słowa z opcjonalnym parametrem, liczbą z przedziału od 0 do 2 (wartość domyślna), która określa odległość edycji. Na przykład "Blue ~" lub "Blue ~ 1" zwróci "Blue", "Blues" i "Glue".

Wyszukiwanie rozmyte może być stosowane tylko do warunków, a nie fraz, ale do każdego terminu można dołączać pojedyncze części nazwy lub frazy. Na przykład "Unviersty ~ of ~" Wshington ~ "byłoby zgodne z" University of Waszyngton ".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Wyszukiwanie w sąsiedztwie

Wyszukiwania w sąsiedztwie są używane do znajdowania terminów blisko siebie w dokumencie. Wstaw symbol tyldy "~" na końcu frazy, a po niej liczbę słów, które tworzą granicę bliskości. Na przykład `"hotel airport"~5` w dokumencie znajdą się terminy "Hotel" i "Lotnisko" w 5 wyrazach innych.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Zwiększenie warunków

Zwiększenie warunków dotyczy klasyfikacji dokumentu, jeśli zawiera on podwyższony termin względem dokumentów, które nie zawierają warunków. Różni się to od profilów oceniania w tych profilach oceniania, a nie konkretnych warunków.  

Poniższy przykład pomaga zilustrować różnice. Załóżmy, że istnieje profil oceniania, który zwiększa zgodność w określonym polu, podyktuj *gatunek* w  [przykładowym musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). Zwiększenie okresu może służyć do dalszej promocji niektórych wyszukiwanych terminów wyższych niż inne. Na przykład program `rock^2 electronic` będzie ulepszał dokumenty, które zawierają terminy wyszukiwania w polu gatunek powyżej innych pól, które można wyszukiwać w indeksie. Ponadto dokumenty zawierające wyszukiwany termin *skały* są wyższe niż w przypadku innych wyszukiwanych warunków w postaci *elektronicznej* w wyniku okresu zwiększenia wartości (2).  

 Aby zwiększyć okres korzystania z karetki, "^", symbol z współczynnikem wzrostu (liczbą) na końcu wyszukiwanego okresu. Możesz również poprawić frazy. Im wyższy współczynnik zwiększania wydajności, tym bardziej istotny termin będzie odnosić się do innych wyszukiwanych terminów. Domyślnie współczynnik zwiększania wynosi 1. Chociaż współczynnik zwiększania wartości musi być dodatni, może być mniejszy niż 1 (na przykład 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Wyszukiwanie wyrażeń regularnych  
 Wyszukiwanie w wyrażeniu regularnym wyszukuje dopasowanie na podstawie wzorców, które są prawidłowe w ramach oprogramowania Apache Lucene, zgodnie z opisem w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html). W Wyszukiwanie poznawcze na platformie Azure wyrażenie regularne jest ujęte między ukośnikami `/` .

 Na przykład, aby znaleźć dokumenty zawierające "Motel" lub "Hotel", określ `/[mh]otel/` . Wyszukiwania wyrażeń regularnych są dopasowywane do pojedynczych wyrazów.

Niektóre narzędzia i języki nakładają dodatkowe wymagania dotyczące znaków ucieczki. W przypadku formatu JSON ciągi zawierające ukośnik są wyprowadzane z ukośnikiem odwrotnym: "microsoft.com/azure/", `search=/.*microsoft.com\/azure\/.*/` gdzie `search=/.* <string-placeholder>.*/` konfiguruje wyrażenie regularne i `microsoft.com\/azure\/` jest ciągiem z odwróconym ukośnikiem.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Wyszukiwanie symboli wieloznacznych

Można użyć ogólnie rozpoznanej składni dla wielu `*` symboli wieloznacznych () lub pojedynczych ( `?` ). Na przykład wyrażenie zapytania `search=alpha*` zwraca wartość "alfanumeryczne" lub "alfabetyczne". Zwróć uwagę, że Analizator zapytań Lucene obsługuje używanie tych symboli z pojedynczym terminem, a nie frazą.

Pełna składnia Lucene obsługuje Dopasowywanie prefiksów, wrostkowe i sufiksów. Jednak jeśli wszystko, co jest potrzebne, jest dopasowanie prefiksów, można użyć prostej składni (dopasowanie prefiksu jest obsługiwane w obu).

Dopasowanie sufiksu, Where `*` lub `?` poprzedzające ciąg (as in `search=/.*numeric./` ) lub wrostkowe, wymaga pełnej składni Lucene, a także ogranicznika ukośnika w wyrażeniach regularnych `/` . Nie można użyć znaku * ani? Symbol jako pierwszy znak okresu lub w okresie, bez `/` . 

> [!NOTE]  
> Zgodnie z regułą dopasowanie wzorców jest powolne, dlatego warto poznać alternatywne metody, takie jak Edge n-gram tokenizacji, które tworzy tokeny dla sekwencji znaków w danym okresie. Indeks będzie większy, ale zapytania mogą działać szybciej, w zależności od konstrukcji wzorca i długości ciągów, które są indeksowane.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Wpływ analizatora na zapytania z symbolami wieloznacznymi

Podczas analizowania zapytania zapytania, które są formułowane jako prefiks, sufiks, symbole wieloznaczne lub wyrażenia regularne są przesyłane jako-do drzewa zapytań, pomijając [analizę leksykalną](search-lucene-query-architecture.md#stage-2-lexical-analysis). Dopasowania będą znajdować się tylko wtedy, gdy indeks zawiera ciągi w formacie używanym przez zapytanie. W większości przypadków podczas indeksowania będzie potrzebna Analizator, który zachowuje integralność ciągów, aby częściowe dopasowanie warunku i wzorca zakończyło się powodzeniem. Aby uzyskać więcej informacji, zobacz [częściowe wyszukiwanie warunków na platformie Azure wyszukiwanie poznawcze zapytań](search-query-partial-matching.md).

Rozważ sytuację, w której można chcieć, aby zapytanie wyszukiwania "terminat *" zwracało wyniki zawierające takie terminy jak "Przerwij", "zakończenie" i "zakończenia".

W przypadku korzystania z analizatora pl. Lucene (w języku angielskim) zastosowanie ma agresywne wyszukanie każdego z nich. Na przykład, "Przerwij", "zakończenie", "zakończenia" spowoduje, że wszystkie tokeny zostaną rozpodzielone na token "termi" w indeksie. Po drugiej stronie terminy w zapytaniach używające symboli wieloznacznych lub wyszukiwania rozmytego nie są analizowane wcale., dlatego nie będą miały wyników pasujących do zapytania "terminat *".

Z drugiej strony analizatory firmy Microsoft (w tym przypadku, The en. Microsoft Analyzer) są nieco bardziej zaawansowane i używają Lematyzacja zamiast rdzeni. Oznacza to, że wszystkie wygenerowane tokeny powinny być prawidłowymi wyrazami w języku angielskim. Na przykład, "zakończenie", "zakończenia" i "zakończenie" będzie zbyt całkowicie w indeksie i byłoby to preferowany wybór dla scenariuszy, które są zależne od dużej ilości symboli wieloznacznych i wyszukiwania rozmytego.

## <a name="scoring-wildcard-and-regex-queries"></a>Ocenianie symboli wieloznacznych i wyrażeń regularnych

Usługa Azure Wyszukiwanie poznawcze używa oceniania opartego na częstotliwościach ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) na potrzeby zapytań tekstowych. Jednakże w przypadku zapytań wieloznacznych i wyrażeń regularnych, w których zakres terminów może być bardzo szeroki, współczynnik częstotliwości jest ignorowany, aby zapobiec rozliczeniu na dopasowania od rzadkich warunków. Wszystkie dopasowania są traktowane równo w przypadku wyszukiwania symboli wieloznacznych i wyrażeń regularnych.

## <a name="special-characters"></a>Znaki specjalne

W pewnych okolicznościach można wyszukać znak specjalny, taki jak emoji "❤" lub znak "€". W takich przypadkach upewnij się, że analizator nie filtruje tych znaków. Analizator standardowy pomija wiele znaków specjalnych, z wyłączeniem ich z indeksu.

Analizatory, które będą tokenize znaki specjalne, obejmują Analyzer "białych", który uwzględnia wszystkie sekwencje znaków oddzielone odstępami jako tokeny (więc ciąg "❤" byłby uznawany za token). Ponadto Analizator języka, taki jak Microsoft English Analyzer ("en. Microsoft"), przyjmuje ciąg "€" jako token. Możesz [przetestować Analizator](/rest/api/searchservice/test-analyzer) , aby zobaczyć, jakie tokeny generuje dla danego zapytania.

W przypadku używania znaków Unicode upewnij się, że symbole są prawidłowo w adresie URL zapytania (na przykład dla "❤" będzie używana sekwencja ucieczki `%E2%9D%A4+` ). Program ogłaszający automatycznie wykonuje to tłumaczenie.  

## <a name="precedence-grouping"></a>Priorytet (grupowanie)

Za pomocą nawiasów można tworzyć podzapytania, w tym operatory w instrukcji języka nawiasów. Program przeszuka na przykład `motel+(wifi|luxury)` dokumenty zawierające termin "Motel" i "Wi-Fi" lub "możliwość zaprojektowania" (lub oba te elementy).

Grupowanie pól jest podobne, ale zakresy grupowanie do jednego pola. Na przykład `hotelAmenities:(gym+(wifi|pool))` szuka pola "hotelAmenities" dla "treningów" i "Wi-Fi", "treningów" i "Pool".  

## <a name="query-size-limits"></a>Limity rozmiaru zapytań

Istnieje ograniczenie rozmiaru zapytań, które można wysłać do usługi Azure Wyszukiwanie poznawcze. W szczególności można mieć co najwyżej 1024 klauzul (wyrażenia oddzielone znakami i, lub itd.). Obowiązuje również limit około 32 KB na rozmiar każdego pojedynczego okresu zapytania. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zalecamy zaprojektowanie go w taki sposób, aby nie generował zapytań o nieograniczonego rozmiaru.  

## <a name="see-also"></a>Zobacz też

+ [Przykłady zapytań dla prostego wyszukiwania](search-query-simple-examples.md)
+ [Przykłady zapytań dla pełnego wyszukiwania Lucene](search-query-lucene-examples.md)
+ [Wyszukaj dokumenty](/rest/api/searchservice/Search-Documents)
+ [Składnia wyrażenia OData dla filtrów i sortowania](query-odata-filter-orderby-syntax.md)   
+ [Prosta Składnia zapytania w usłudze Azure Wyszukiwanie poznawcze](query-simple-syntax.md)
