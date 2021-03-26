---
title: Typy zapytań
titleSuffix: Azure Cognitive Search
description: Dowiedz się więcej na temat typów zapytań obsługiwanych w Wyszukiwanie poznawcze, w tym swobodnego tekstu, filtru, autouzupełniania i sugestii, wyszukiwania geograficznego, zapytań systemowych i wyszukiwania dokumentów.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 21012848ba3624df6110eaea182beccc4646d234
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609279"
---
# <a name="querying-in-azure-cognitive-search"></a>Wykonywanie zapytań w usłudze Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze oferuje bogaty język zapytań do obsługi szerokiego zakresu scenariuszy — od wyszukiwania swobodnego tekstu do wysoce określonych wzorców zapytań. W tym artykule opisano żądania zapytań oraz rodzaje zapytań, które można utworzyć.

W Wyszukiwanie poznawcze zapytanie jest pełną specyfikacją **`search`** operacji rundy, z parametrami, które informują o wykonywaniu zapytania i kształtują odwracanie odpowiedzi. Parametry i parsery określają typ żądania zapytania. Poniższy przykład zapytania jest niezależną kwerendą tekstową z operatorem Boolean przy użyciu [dokumentów wyszukiwania (interfejs API REST)](/rest/api/searchservice/search-documents), przeznaczonych dla kolekcji ["Przykłady dokumentów indeksu hoteli](search-get-started-portal.md) ".

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Parametry używane podczas wykonywania zapytania obejmują:

+ **`queryType`** Ustawia parser, który jest [domyślnym prostym analizatorem zapytań](search-query-simple-examples.md) (optymalnym dla wyszukiwania pełnotekstowego) lub [pełnym parserem zapytań Lucene](search-query-lucene-examples.md) , używanym do zaawansowanych konstrukcji zapytań, takich jak wyrażenia regularne, wyszukiwanie w sąsiedztwie, rozmyte i wieloznaczne wyszukiwanie, aby określić nazwę.

+ **`searchMode`** Określa, czy dopasowania są oparte na kryteriach "All" lub "any" w wyrażeniu. Wartość domyślna to Any.

+ **`search`** zawiera kryteria dopasowywania, zwykle całe warunki lub frazy, z operatorami lub bez. Każde pole, które jest przypisane do *wyszukiwania* w schemacie indeksu, jest kandydatem dla tego parametru.

+ **`searchFields`** ogranicza wykonywanie zapytania do określonych pól z możliwością wyszukiwania. Podczas opracowywania warto używać tej samej listy pól do wyboru i wyszukiwania. W przeciwnym razie dopasowanie może opierać się na wartościach pól, które nie są widoczne w wynikach, tworząc niepewną, dlaczego dokument został zwrócony.

Parametry używane do kształtowania odpowiedzi:

+ **`select`** określa pola, które mają zostać zwrócone w odpowiedzi. W instrukcji SELECT można używać tylko pól oznaczonych jako możliwe do *pobierania* w indeksie.

+ **`top`** Zwraca określoną liczbę najlepiej pasujących dokumentów. W tym przykładzie zwracane są tylko 10 trafień. Aby wyświetlić wyniki, można użyć klauzuli TOP i Skip (niepokazywany).

+ **`count`** informuje o tym, ile dokumentów w całym indeksie jest zgodnych, które mogą być większe niż te, które są zwracane. 

+ **`orderby`** jest używany, jeśli chcesz sortować wyniki według wartości, takiej jak Klasyfikacja lub lokalizacja. W przeciwnym razie wartość domyślna to użycie oceny przydatności do rangi wyników. Pole musi być przypisane do *sortowania* jako kandydat dla tego parametru.

Powyższa lista jest reprezentatywna, ale nie pełna. Aby zapoznać się z pełną listą parametrów żądania zapytania, zobacz [dokumenty wyszukiwania (interfejs API REST)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy zapytań

Z kilkoma istotnymi wyjątkami żądanie zapytania iteruje w odniesieniu do odwróconych indeksów, które są uporządkowane w celu szybkiego skanowania, gdzie dopasowanie można znaleźć w dowolnym polu w dowolnej liczbie dokumentów wyszukiwania. W Wyszukiwanie poznawcze podstawową metodą znajdowania dopasowania jest wyszukiwanie pełnotekstowe lub filtry, ale można również zaimplementować inne dobrze znane środowiska wyszukiwania, takie jak Autouzupełnianie lub wyszukiwanie lokalizacji geograficznej. Pozostała część tego artykułu podsumowuje zapytania w Wyszukiwanie poznawcze i zawiera linki do dodatkowych informacji i przykładów.

## <a name="full-text-search"></a>Wyszukiwanie pełnotekstowe

Jeśli aplikacja wyszukiwania zawiera pole wyszukiwania, które zbiera dane wejściowe, wówczas wyszukiwanie pełnotekstowe jest prawdopodobnie operacją tworzenia kopii zapasowej. Wyszukiwanie pełnotekstowe akceptuje warunki lub frazy, które zostały przesłane do **`search`** parametru we wszystkich polach z *możliwością wyszukiwania* w indeksie. Opcjonalne operatory logiczne w ciągu zapytania mogą określać kryteria dołączania lub wykluczania. Zarówno prosty parser, jak i pełny Analizator obsługują wyszukiwanie pełnotekstowe.

W Wyszukiwanie poznawcze wyszukiwanie pełnotekstowe jest kompilowane w aparacie zapytań Apache Lucene. Ciągi zapytania w wyszukiwaniu pełnotekstowym przechodzą na analizę leksykalną w celu zwiększenia wydajności skanowania. Analiza obejmuje małe wielkości liter, usuwanie wyrazów stop, takich jak "a" i zmniejszanie warunków do pierwotnych formularzy głównych. Domyślną analizatorem jest standardowa Lucene.

Gdy zostaną znalezione pasujące terminy, aparat zapytań odtworzy dokument wyszukiwania zawierający dopasowanie przy użyciu klucza dokumentu lub identyfikatora do złożenia wartości pól, ustala rangę dokumentów w kolejności przydatności i zwraca górną 50 (domyślnie) w odpowiedzi lub inną liczbę, jeśli został określony **`top`** .

Jeśli wdrażasz wyszukiwanie pełnotekstowe, zrozumienie, w jaki sposób dana zawartość jest tokenem, ułatwi debugowanie wszelkich anomalii zapytań. Zapytania dotyczące ciągów z wyrazami lub znaków specjalnych mogą wymagać użycia analizatora innego niż domyślne standardowe Lucene, aby upewnić się, że indeks zawiera właściwe tokeny. Wartość domyślną można zastąpić [analizatory języka](index-add-language-analyzers.md#language-analyzer-list) lub [wyspecjalizowane analizatory](index-add-custom-analyzers.md#built-in-analyzers) modyfikujące analizę leksykalną. Przykładem jest [słowo kluczowe](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , które traktuje całą zawartość pola jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kody ZIP, identyfikatory i nazwy produktów. Aby uzyskać więcej informacji, zobacz [częściowe wyszukiwanie terminów i wzorce ze znakami specjalnymi](search-query-partial-matching.md).

Jeśli przewidujesz duże użycie operatorów logicznych, co jest bardziej podobne w przypadku indeksów zawierających duże bloki tekstu (pole zawartości lub długie opisy), pamiętaj, aby przetestować zapytania z **`searchMode=Any|All`** parametrem, aby oszacować wpływ tego ustawienia na wyszukiwanie wartości logicznych.

## <a name="autocomplete-and-suggested-queries"></a>Autouzupełnianie i sugerowane zapytania

[Autouzupełnianie lub sugerowane wyniki](search-add-autocomplete-suggestions.md) są alternatywą dla **`search`** tych nieudanych żądań zapytań, opartych na częściowych danych wejściowych ciągu (po każdym znaku) w funkcji wyszukiwania w trakcie wpisywania. Można używać **`autocomplete`** parametrów i **`suggestions`** razem lub oddzielnie, zgodnie z opisem w [tym samouczku](tutorial-csharp-type-ahead-and-suggestions.md), ale nie można ich używać z **`search`** . Zarówno ukończone warunki, jak i sugerowane zapytania są wyprowadzane z zawartości indeksu. Aparat nigdy nie zwróci ciągu lub sugestii, które nie są istniejące w indeksie. Aby uzyskać więcej informacji, zobacz [Autouzupełnianie (interfejs API REST)](/rest/api/searchservice/autocomplete) i [sugestie (interfejs API REST)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Filtruj wyszukiwanie

Filtry są szeroko stosowane w aplikacjach, które zawierają Wyszukiwanie poznawcze. Na stronach aplikacji filtry są często wizualizowane jako aspekty w strukturach nawigacji linków dla filtrowania ukierunkowanego na użytkownika. Filtry są również używane wewnętrznie w celu uwidocznienia wycinków indeksowanej zawartości. Na przykład możesz zainicjować stronę wyszukiwania przy użyciu filtru dla kategorii produktu lub języka, jeśli indeks zawiera pola w języku angielskim i francuskim.

Mogą również być potrzebne filtry do wywołania wyspecjalizowanego formularza zapytania, zgodnie z opisem w poniższej tabeli. Możesz użyć filtru z nieokreślonym wyszukiwaniem ( **`search=*`** ) lub ciągiem zapytania, który zawiera warunki, frazy, operatory i wzorce.

| Scenariusz filtru | Opis |
|-----------------|-------------|
| Filtry zakresu | Na platformie Azure Wyszukiwanie poznawcze zapytania zakresu są kompilowane przy użyciu parametru filtru. Aby uzyskać więcej informacji i przykładów, zobacz [przykład Filter Range](search-query-simple-examples.md#example-5-range-filters). |
| Wyszukiwanie lokalizacji geograficznej | Jeśli pole z możliwością wyszukiwania jest [typu EDM. geographyPoint względem](/rest/api/searchservice/supported-data-types), można utworzyć wyrażenie filtru dla kontrolek wyszukiwania "Znajdź w pobliżu" lub "mapowanie". Pola, które są używane do wyszukiwania geograficznego, zawierają współrzędne. Aby uzyskać więcej informacji i przykład, zobacz [przykład wyszukiwania geograficznego](search-query-simple-examples.md#example-6-geo-search). |
| Nawigacja aspektowa | Struktura nawigacji aspektów jest obiektem nawigacyjnym w nawigacji ukierunkowanej na użytkownika po wywołaniu filtru w odpowiedzi na `onclick` zdarzenie w aspekcie. W związku z tym aspekty i filtry są gotowe do użycia. Jeśli dodasz nawigację aspektów, będziesz potrzebować filtrów do ukończenia tego środowiska. Aby uzyskać więcej informacji, zobacz [How to Build a facet Filter](search-filters-facets.md). |

> [!NOTE]
> Tekst używany w wyrażeniu filtru nie jest analizowany podczas przetwarzania zapytania. Przyjmuje się, że tekst wejściowy jest Verbatim, który jest zgodny z wielkością liter, który powiedzie się lub zakończy się niepowodzeniem. Wyrażenia filtru są konstruowane przy użyciu [składni OData](query-odata-filter-orderby-syntax.md) i przekazywać **`filter`** parametr we wszystkich polach z możliwością *filtrowania* w indeksie. Aby uzyskać więcej informacji, zobacz [filtry na platformie Azure wyszukiwanie poznawcze](search-filters.md).

## <a name="document-look-up"></a>Wyszukiwanie dokumentów

W przeciwieństwie do wcześniej opisanych formularzy zapytań, ta jedna pobiera jeden [dokument wyszukiwania według identyfikatora](/rest/api/searchservice/lookup-document), bez odpowiedniego wyszukiwania lub skanowania indeksu. Żądany i zwracany jest tylko jeden dokument. Gdy użytkownik wybierze element w wynikach wyszukiwania, pobranie dokumentu i wypełnianie strony szczegółów z polami jest typowym odpowiedzią, a wyszukiwanie dokumentu jest operacją, która go obsługuje.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Wyszukiwanie zaawansowane: rozmyte, symbole wieloznaczne, bliskość, wyrażenie regularne

Zaawansowana forma zapytania zależy od pełnego analizatora Lucene i operatorów, które wyzwalają określone zachowanie zapytania.

| Typ zapytania | Użycie | Przykłady i więcej informacji |
|------------|--------|------------------------------|
| [Wyszukiwanie polowe](query-lucene-syntax.md#bkmk_fields) | **`search`**  konstruktora **`queryType=full`**  | Utwórz złożone wyrażenie zapytania określające jedno pole. <br/>[Przykład wyszukiwania w polu](search-query-lucene-examples.md#example-1-fielded-search) |
| [Wyszukiwanie rozmyte](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** konstruktora **`queryType=full`** | Dopasowuje się do warunków mających podobną konstrukcję lub pisownię. <br/>[Przykład wyszukiwania rozmytego](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [Wyszukiwanie w sąsiedztwie](query-lucene-syntax.md#bkmk_proximity) | **`search`** konstruktora **`queryType=full`** | Znajduje terminy, które są blisko siebie w dokumencie. <br/>[Przykład wyszukiwania w sąsiedztwie](search-query-lucene-examples.md#example-3-proximity-search) |
| [zwiększenie warunków](query-lucene-syntax.md#bkmk_termboost) | **`search`** konstruktora **`queryType=full`** | Określa wyższy poziom dokumentu, jeśli zawiera on podwyższony termin względem innych, które nie są. <br/>[Przykład zwiększania warunków](search-query-lucene-examples.md#example-4-term-boosting) |
| [Wyszukiwanie wyrażeń regularnych](query-lucene-syntax.md#bkmk_regex) | **`search`** konstruktora **`queryType=full`** | Dopasowuje się w oparciu o zawartość wyrażenia regularnego. <br/>[Przykład wyrażenia regularnego](search-query-lucene-examples.md#example-5-regex) |
|  [Wyszukiwanie przy użyciu symboli wieloznacznych lub prefiksów](query-lucene-syntax.md#bkmk_wildcard) | **`search`** parametr z * *_`~`_* lub **`?`** , **`queryType=full`**| Dopasowuje się na podstawie prefiksu i tyldy ( `~` ) lub pojedynczego znaku ( `?` ). <br/>[Przykład wyszukiwania symboli wieloznacznych](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Następne kroki

Aby bliżej zapoznać się z implementacją zapytania, przejrzyj przykłady dla każdej składni. Jeśli dopiero zaczynasz wyszukiwanie pełnotekstowe, lepiej przyjrzeć się działaniu aparatu zapytań.

+ [Przykłady prostych zapytań](search-query-simple-examples.md)
+ [Przykłady zapytań składni Lucene w celu tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md)git