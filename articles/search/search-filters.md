---
title: Filtruj według wyników wyszukiwania
titleSuffix: Azure Cognitive Search
description: Filtruj według tożsamości zabezpieczeń użytkownika, języka, lokalizacji geograficznej lub wartości liczbowych, aby zmniejszyć wyniki wyszukiwania zapytań w usłudze Azure Wyszukiwanie poznawcze, hostowaną usługę wyszukiwania w chmurze na Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d83e5c39f97db49e2cc9b77cc806cff0a1fa6de
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355988"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtry na platformie Azure Wyszukiwanie poznawcze 

*Filtr* zawiera kryteria wyboru dokumentów używanych w zapytaniu wyszukiwanie poznawcze platformy Azure. Wyszukiwanie niefiltrowane zawiera wszystkie dokumenty w indeksie. Filtr zakresów zapytania wyszukiwania do podzbioru dokumentów. Na przykład filtr może ograniczyć wyszukiwanie pełnotekstowe do tylko tych produktów mających konkretną markę lub kolor, w odniesieniu do cen powyżej określonego progu.

Niektóre środowiska wyszukiwania nakładają wymagania filtru w ramach implementacji, ale można używać filtrów w dowolnym momencie, gdy chcesz ograniczyć wyszukiwanie przy użyciu kryteriów *opartych na wartości* (zakres wyszukiwania do typu produktu "książki" dla kategorii "non-fikcja" opublikowany przez "Simon & Schuster").

Jeśli zamiast tego cel jest celem wyszukiwania w określonych *strukturach* danych (Określanie zakresu wyszukiwania do pola Recenzje klienta), istnieją alternatywne metody opisane poniżej.

## <a name="when-to-use-a-filter"></a>Kiedy używać filtru

Filtry są podstawą dla kilku środowisk wyszukiwania, w tym "Znajdź w pobliżu", nawigacji aspektowej i filtrów zabezpieczeń, które pokazują tylko te dokumenty, które użytkownik może zobaczyć. W przypadku zaimplementowania dowolnego z tych środowisk wymagany jest filtr. Jest to filtr dołączony do zapytania wyszukiwania, który zawiera współrzędne geolokalizacji, kategorię zestawu reguł wybranego przez użytkownika lub identyfikator zabezpieczeń obiektu żądającego.

Przykładowe scenariusze obejmują:

1. Użyj filtru, aby wydzielić indeks na podstawie wartości danych w indeksie. Mając schemat z miasto, typ obudowy i walory, możesz utworzyć filtr, aby jawnie wybrać dokumenty spełniające kryteria (w Seattle, Condos, Waterfront). 

   Wyszukiwanie pełnotekstowe z tymi samymi danymi wejściowymi często generuje podobne wyniki, ale filtr jest bardziej precyzyjny, ponieważ wymaga dokładnego dopasowania okresu filtrowania do zawartości w indeksie. 

2. Użyj filtru, jeśli środowisko wyszukiwania zawiera wymagania filtru:

   * [Nawigacja aspektowa](search-faceted-navigation.md) używa filtru, aby przekazać kategorię zestawu reguł wybraną przez użytkownika.
   * Wyszukiwanie geograficzne używa filtru do przekazywania współrzędnych bieżącej lokalizacji w aplikacjach "Znajdź w pobliżu". 
   * Filtry zabezpieczeń przekazują identyfikatory zabezpieczeń jako kryteria filtrowania, gdzie dopasowanie w indeksie służy jako serwer proxy dla praw dostępu do dokumentu.

3. Użyj filtru, jeśli chcesz, aby kryteria wyszukiwania dla pola liczbowego. 

   Pola numeryczne są możliwe do pobierania w dokumencie i mogą być wyświetlane w wynikach wyszukiwania, ale nie są przeszukiwane (z uwzględnieniem wyszukiwania pełnotekstowego) indywidualnie. Jeśli potrzebujesz kryteriów wyboru opartych na danych liczbowych, Użyj filtru.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatywne metody ograniczania zakresu

Jeśli chcesz zawęzić efekt wyszukiwania, filtry nie są jedynym wyborem. Te alternatywy mogą być lepiej dopasowane, w zależności od celu:

 + `searchFields` parametr zapytania Pegs wyszukiwanie do określonych pól. Na przykład jeśli indeks zawiera oddzielne pola dla opisów języka angielskiego i hiszpańskiego, można użyć searchFields, aby określić, które pola będą używane do wyszukiwania pełnotekstowego. 

+ `$select` parametr służy do określania, które pola mają być uwzględnione w zestawie wyników, efektywnie przycinania odpowiedzi przed wysłaniem jej do aplikacji wywołującej. Ten parametr nie ogranicza zapytania ani nie zmniejsza kolekcji dokumentów, ale jeśli celem jest mniejsza odpowiedź, ten parametr jest opcją do uwzględnienia. 

Aby uzyskać więcej informacji na temat każdego z parametrów, zobacz [Wyszukiwanie dokumentów > żądanie > parametry zapytania](/rest/api/searchservice/search-documents#query-parameters).


## <a name="how-filters-are-executed"></a>Jak są wykonywane filtry

W czasie zapytania Analizator filtru akceptuje kryteria jako dane wejściowe, konwertuje wyrażenie na cząstkowe wyrażenia logiczne reprezentowane jako drzewo, a następnie szacuje drzewo filtrów na polach z możliwością filtrowania w indeksie.

Filtrowanie odbywa się wspólnie z wyszukiwaniem, co umożliwia kwalifikowanie dokumentów do uwzględnienia w przetwarzaniu podrzędnym na potrzeby pobierania dokumentów i oceny znaczenia. W przypadku sparowania z wyszukiwanym ciągiem filtr efektywnie zmniejsza zestaw odwołań dla kolejnej operacji wyszukiwania. W przypadku użycia samodzielne (na przykład gdy ciąg zapytania jest pusty, gdzie `search=*` ), kryteria filtru są jedynymi danymi wejściowymi. 

## <a name="defining-filters"></a>Definiowanie filtrów
Filtry są wyrażeniami OData, przegubowo wykorzystując [podzbiór składni protokołu OData w wersji v4 obsługiwanej w usłudze Azure wyszukiwanie poznawcze](/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Można określić jeden filtr dla każdej operacji **wyszukiwania** , ale filtr może zawierać wiele pól, wiele kryteriów, a jeśli używana jest funkcja **IsMatch** , wiele wyrażeń wyszukiwania pełnotekstowego. W wieloczęściowym wyrażeniu filtru można określić predykaty w dowolnej kolejności (z zastrzeżeniem reguł pierwszeństwa operatora). Jeśli spróbujesz zmienić rozmieszczenie predykatów w określonej sekwencji, nie ma znaczącego zwiększenia wydajności.

Jednym z limitów w wyrażeniu filtru jest maksymalny rozmiar żądania. Całe żądanie, włącznie z filtrem, może być maksymalnie 16 MB dla POST lub 8 KB dla GET. Istnieje również limit liczby klauzul w wyrażeniu filtru. Dobrym warunkiem jest to, że jeśli masz setki klauzul, jesteś narażony na działanie w limicie. Zalecamy projektowanie aplikacji w taki sposób, że nie generują one filtrów o nieograniczonej wielkości.

Poniższe przykłady przedstawiają definicje filtrów prototypowe w kilku interfejsach API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrowanie wzorców użycia

Poniższe przykłady ilustrują kilka wzorców użycia dla scenariuszy filtrowania. Aby uzyskać więcej sugestii, zobacz [przykłady składni wyrażeń OData >](./search-query-odata-filter.md#examples).

+ Autonomiczna **$Filter** bez ciągu zapytania, przydatna, gdy wyrażenie filtru jest w stanie w pełni kwalifikować dokumenty. Bez ciągu zapytania nie ma żadnej leksykalnej ani analizy językowej, nie oceniania ani klasyfikacji. Zwróć uwagę, że ciąg wyszukiwania jest tylko gwiazdką, co oznacza, że pasuje do wszystkich dokumentów.

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Kombinacja ciągu zapytania i **$Filter** , gdzie filtr tworzy podzestaw, a ciąg zapytania zawiera dane wejściowe dla wyszukiwania pełnotekstowego w filtrowanym podzbiorze. Dodanie warunków ("drogi odległości") wprowadza wyniki wyszukiwania w wynikach, gdzie dokumenty, które najlepiej pasują do warunków, są bardziej klasyfikowane. Użycie filtru z ciągiem zapytania jest najbardziej typowym wzorcem użycia.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Zapytania złożone, oddzielone znakami "lub", z których każda ma własne kryteria filtrowania (na przykład "Beagles" w "Dog" lub "Siamese" w "Cat"). Wyrażenia połączone z `or` są oceniane indywidualnie, przy czym Unia dokumentów dopasowuje każde wyrażenie wysłane z powrotem w odpowiedzi. Ten wzorzec użycia jest uzyskiwany za pomocą `search.ismatchscoring` funkcji. Możesz również użyć wersji bez oceniania, `search.ismatch` .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Istnieje również możliwość łączenia wyszukiwania pełnotekstowego za pośrednictwem `search.ismatchscoring` filtrów z użyciem `and` zamiast `or` , ale jest to funkcja równoważna z użyciem `search` `$filter` parametrów i w żądaniu wyszukiwania. Na przykład następujące dwa zapytania dają ten sam wynik:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Zapoznaj się z następującymi artykułami, aby uzyskać kompleksowe wskazówki dotyczące konkretnych przypadków użycia:

+ [Filtry aspektów](search-filters-facets.md)
+ [Filtry języków](search-filters-language.md)
+ [Dostosowanie do zabezpieczeń](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Wymagania dotyczące pól dla filtrowania

W interfejsie API REST filtrowanie jest domyślnie *włączone* w przypadku pól prostych. Pola z możliwością filtrowania zwiększają rozmiar indeksu; Upewnij się `"filterable": false` , że ustawiono pola, które nie są planowane do użycia w filtrze. Aby uzyskać więcej informacji na temat ustawień definicji pól, zobacz [create index](/rest/api/searchservice/create-index).

W zestawie SDK platformy .NET filtr jest domyślnie *wyłączony* . Można ustawić pole do filtrowania, ustawiając [Właściwość IsFiltered](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) odpowiedniego obiektu [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) na `true` . W poniższym przykładzie atrybut jest ustawiany we `BaseRate` właściwości klasy modelu, która jest mapowana na definicję indeksu.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Tworzenie istniejącego pola do filtrowania

Nie można modyfikować istniejących pól, aby umożliwić ich filtrowanie. Zamiast tego należy dodać nowe pole lub ponownie skompilować indeks. Aby uzyskać więcej informacji na temat ponownego kompilowania indeksu lub ponownego wypełniania pól, zobacz [jak ponownie skompilować indeks wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Podstawy filtrów tekstu

Filtry tekstu dopasowują pola ciągów do ciągów literałów, które są podano w filtrze. W przeciwieństwie do wyszukiwania pełnotekstowego nie ma żadnej analizy leksykalnej ani dzielenia wyrazów dla filtrów tekstowych, dlatego porównania są tylko dla dokładnych dopasowań. Załóżmy na przykład, że pole *f* zawiera "Sunny Day", nie jest `$filter=f eq 'Sunny'` zgodne, ale `$filter=f eq 'sunny day'` będzie. 

Ciągi tekstowe są rozróżniane wielkości liter. Nie istnieje małe litery wyrazów z wielką literą: `$filter=f eq 'Sunny day'` nie znaleziono "Sunny Day".

### <a name="approaches-for-filtering-on-text"></a>Podejścia do filtrowania tekstu

| Podejście | Opis | Kiedy stosować |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funkcja, która dopasowuje pole do rozdzielanej listy ciągów. | Zalecane dla [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) oraz filtrów, w których wiele wartości tekstowych musi być dopasowanych do pola ciągu. Funkcja **Search.in** została zaprojektowana w celu przyspieszenia i jest znacznie szybsza niż jawne porównanie pola z każdym ciągiem przy użyciu `eq` i `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funkcja, która umożliwia mieszanie operacji wyszukiwania pełnotekstowego z ścisłymi operacjami filtru logicznego w tym samym wyrażeniu filtru. | Użyj pozycji **Wyszukaj. IsMatch** (lub jej równoważnej ocenie **Wyszukaj. ismatchscoring** ), jeśli chcesz, aby wiele kombinacji filtru wyszukiwania w jednym żądaniu. Można go również użyć dla filtru *zawiera* filtr, aby odfiltrować ciąg częściowy w większym ciągu. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Wyrażenie zdefiniowane przez użytkownika składające się z pól, operatorów i wartości. | Użyj tego, jeśli chcesz znaleźć dokładne dopasowania między polem ciągu a wartością ciągu. |

## <a name="numeric-filter-fundamentals"></a>Podstawy filtru liczbowego

Pola liczbowe nie znajdują się `searchable` w kontekście wyszukiwania pełnotekstowego. Tylko ciągi są objęte wyszukiwaniem pełnotekstowym. Jeśli na przykład wprowadzisz 99,99 jako termin wyszukiwania, nie będziesz otrzymywać elementów z ceną za $99,99. Zamiast tego zobaczysz elementy mające numer 99 w polach ciągów dokumentu. W związku z tym, jeśli masz dane liczbowe, założono, że będą używane do filtrów, w tym zakresów, zestawów reguł, grup i tak dalej. 

Dokumenty zawierające pola liczbowe (Cena, rozmiar, jednostka SKU, identyfikator) udostępniają te wartości w wynikach wyszukiwania, jeśli pole jest oznaczone `retrievable` . W tym miejscu jest to, że wyszukiwanie pełnotekstowe nie ma zastosowania do typów pól liczbowych.

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj **Eksploratora wyszukiwania** w portalu, aby przesłać zapytania z parametrami **$Filter** . [Indeks z przykładem liczby rzeczywistej](search-get-started-portal.md) zawiera interesujące wyniki dla następujących filtrowanych zapytań, które są wklejane do paska wyszukiwania:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Aby obejść więcej przykładów, zobacz [składnia wyrażenia filtru OData > przykłady](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Zobacz też

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/search-documents)
+ [Prosta składnia zapytań](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Składnia zapytań Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Obsługiwane typy danych](/rest/api/searchservice/supported-data-types)