---
title: Dodaj Autouzupełnianie do pola wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz akcje zapytania wyszukiwanie zgodnie z typem na platformie Azure Wyszukiwanie poznawcze przez utworzenie sugestii i formułowanie żądań, które umożliwiają Autouzupełnianie pola wyszukiwania z zakończonymi warunkami lub frazami. Możesz również zwrócić sugerowane dopasowania.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 668b987dd8b367c143a91dc5adb11848321a9d5a
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044417"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Dodawanie funkcji Autouzupełnianie i sugestii do aplikacji klienckich przy użyciu usługi Azure Wyszukiwanie poznawcze

Wyszukiwanie zgodnie z Twoim typem jest powszechną techniką w celu zwiększenia produktywności zapytań. Na platformie Azure Wyszukiwanie poznawcze to środowisko jest obsługiwane przez *funkcję autouzupełniania*, która kończy termin lub frazę w oparciu o częściowe dane wejściowe (kończąc "mikro" "Microsoft"). Drugie środowisko użytkownika to *sugestie* lub krótka lista pasujących dokumentów (zwracająca tytuły książek z identyfikatorem, aby można było utworzyć link do strony szczegółów dotyczącej tej książki). Zarówno Autouzupełnianie, jak i sugestie są predykatem według dopasowania w indeksie. Usługa nie będzie oferować zapytań, które zwracają wyniki zerowe.

Aby zaimplementować te środowiska na platformie Azure Wyszukiwanie poznawcze, potrzebne są:

+ Definicja programu *sugerującego* , która jest osadzona w schemacie indeksu.
+ *Zapytanie* określające interfejs API [funkcji Autouzupełnianie](/rest/api/searchservice/autocomplete) lub [sugestii](/rest/api/searchservice/suggestions) dla żądania.
+ *Kontrolka interfejsu użytkownika* do obsługi interakcji typu "Wyszukaj jako" w aplikacji klienckiej. Zalecamy używanie w tym celu istniejącej biblioteki języka JavaScript.

Na platformie Azure Wyszukiwanie poznawcze w indeksie wyszukiwania są pobierane automatyczne zapytania i sugerowane wyniki z wybranych pól, które zostały zarejestrowane za pomocą sugestii. Program sugerujący jest częścią indeksu i określa, które pola będą dostarczać zawartość, która wykonuje zapytanie, sugeruje wynik lub wykonuje oba te operacje. Gdy indeks zostanie utworzony i załadowany, struktura danych sugerująca jest tworzona wewnętrznie w celu przechowywania prefiksów używanych do dopasowywania w ramach zapytań częściowych. W przypadku sugestii, wybór odpowiednich pól, które są unikatowe lub co najmniej nie powtarzające się, jest istotny dla środowiska. Aby uzyskać więcej informacji, zobacz [Tworzenie sugestii](index-add-suggesters.md).

Pozostała część tego artykułu koncentruje się na zapytaniach i kodzie klienta. Używa języka JavaScript i języka C# do ilustrowania najważniejszych punktów. Przykłady interfejsu API REST są używane do zwięzłego prezentowania każdej operacji. Aby uzyskać linki do kompleksowych przykładów kodu, zobacz [następne kroki](#next-steps).

## <a name="set-up-a-request"></a>Skonfiguruj żądanie

Elementy żądania obejmują jedno z interfejsów API wyszukiwania jako typ, częściowe zapytanie i sugestię. Poniższy skrypt ilustruje składniki żądania przy użyciu interfejsu API REST Autouzupełnianie jako przykładu.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName** udostępnia pola sugerowane przez sugestie, które służą do uzupełniania terminów lub sugestii. W przypadku sugestii w szczególności Lista pól powinna składać się z tych, które oferują wyraźne wybory spośród pasujących wyników. W witrynie, która sprzedaje gry komputerowe, pole może być tytułem gry.

Parametr **Search** zawiera częściowe zapytanie, w którym znaki są przekazywane do żądania zapytania za pomocą kontrolki Autouzupełnianie jQuery. W powyższym przykładzie "minecraf" to statyczna ilustracja przedstawiająca przekazaną przez formant.

Interfejsy API nie nakładają wymagań dotyczących minimalnej długości dla zapytania częściowego; może to być tylko jeden znak. Jednak funkcja autouzupełniania jQuery zapewnia minimalną długość. Typowy jest co najmniej dwa znaki.

Dopasowania znajdują się na początku terminu w dowolnym miejscu w ciągu wejściowym. "Quick Brown Fox", zarówno Autouzupełnianie, jak i sugestie, są zgodne z częściowymi wersjami "" "," Quick "," Brown "lub" Fox ", ale nie na częściowych wrostkowech, takich jak" RowN "lub" OX-". Ponadto każde dopasowanie ustawia zakres dla rozszerzeń podrzędnych. Częściowe zapytanie dotyczące "szybkiej br" będzie zgodne z "Quick Brown" lub "Quick chleb", ale ani "brązowy" lub "chleb" nie będzie się zgadzać, chyba że "krótki" poprzedza.

### <a name="apis-for-search-as-you-type"></a>Interfejsy API dla wyszukiwania w trakcie pisania

Skorzystaj z poniższych linków, aby uzyskać informacje o stronach REST i .NET SDK:

+ [Sugestie interfejsu API REST](/rest/api/searchservice/suggestions) 
+ [Autouzupełnianie interfejsu API REST](/rest/api/searchservice/autocomplete) 
+ [SuggestAsync, Metoda](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync, Metoda](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Tworzenie struktury odpowiedzi

Odpowiedzi na potrzeby automatycznego uzupełniania i sugestii są następujące: [Funkcja autouzupełniania](/rest/api/searchservice/autocomplete#response) zwraca listę warunków, [sugestie](/rest/api/searchservice/suggestions#response) zwracają warunki i identyfikator dokumentu, aby można było pobrać dokument (Użyj interfejsu API [dokumentu wyszukiwania](/rest/api/searchservice/lookup-document) do pobrania określonego dokumentu dla strony szczegółów).

Odpowiedzi są dostosowane do parametrów żądania:

+ W przypadku autouzupełniania ustaw wartość [**AutoCompleteMode**](/rest/api/searchservice/autocomplete#query-parameters) , aby określić, czy uzupełnianie tekstu występuje na jednym lub dwóch warunkach. 

+ W przypadku sugestii Ustaw [**$SELECT**](/rest/api/searchservice/suggestionse#query-parameters) , aby zwracały pola zawierające unikatowe lub odróżniające wartości, takie jak nazwy i opisy. Unikaj pól, które zawierają zduplikowane wartości (takie jak kategoria lub miasto).

Następujące dodatkowe parametry mają zastosowanie zarówno do autouzupełniania, jak i sugestii, ale mogą być bardziej potrzebne w przypadku sugestii, zwłaszcza gdy Sugerowana wartość zawiera wiele pól.

| Parametr | Użycie |
|-----------|-------|
| **searchFields** | Ogranicz zapytanie do określonych pól. |
| **$filter** | Zastosuj kryteria dopasowania w zestawie wyników ( `$filter=Category eq 'ActionAdventure'` ). |
| **$top** | Ogranicz wyniki do określonego numeru ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Dodawanie kodu interakcji użytkownika

Automatyczne wypełnianie terminu zapytania lub usuwanie listy pasujących linków wymaga kodu interakcji użytkownika, zazwyczaj języka JavaScript, który może zużywać żądania ze źródeł zewnętrznych, takich jak Autouzupełnianie lub zapytania sugestii dotyczące Azure Searchego indeksu poznawczego.

Chociaż można napisać ten kod natywnie, znacznie łatwiej jest używać funkcji z istniejącej biblioteki JavaScript. W tym artykule pokazano dwa, jeden dla sugestii i drugi do autouzupełniania. 

+ Element [widget autouzupełniania (interfejs użytkownika jQuery)](https://jqueryui.com/autocomplete/) jest używany w przykładzie sugestii. Można utworzyć pole wyszukiwania, a następnie odwołać się do niego w funkcji języka JavaScript korzystającej z widżetu Autouzupełnianie. Właściwości w widżecie ustaw źródło (funkcja autouzupełniania lub sugestii), minimalną długość znaków wejściowych przed akcją i pozycjonowanie.

+ [Wtyczka autouzupełniania XDSoft](https://xdsoft.net/jqplugins/autocomplete/) jest używana na przykład Autouzupełnianie.

Te biblioteki są używane do tworzenia pola wyszukiwania z obsługą obu sugestii i autouzupełniania. Dane wejściowe zebrane w polu wyszukiwania są sparowane z sugestiami i akcjami autouzupełniania.

## <a name="suggestions"></a>Sugestie

Ta sekcja przeprowadzi Cię przez implementację sugerowanych wyników, rozpoczynając od definicji pola wyszukiwania. Pokazano w nim także, jak i skrypt wywołujący pierwszą bibliotekę autouzupełniania kodu JavaScript, do której odwołuje się w tym artykule.

### <a name="create-a-search-box"></a>Utwórz pole wyszukiwania

Przy założeniu, że [Biblioteka funkcji Autouzupełnianie interfejsu użytkownika jQuery](https://jqueryui.com/autocomplete/) i projekt MVC w języku C#, można zdefiniować pole wyszukiwania przy użyciu języka JavaScript w pliku **index. cshtml** . Biblioteka dodaje interakcję typu wyszukiwanie jako typ do pola wyszukiwania przez wykonywanie wywołań asynchronicznych do kontrolera MVC w celu pobrania sugestii.

W **indeksie. cshtml** w folderze \Views\Home, wiersz służący do utworzenia pola wyszukiwania może być następujący:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Ten przykład to proste wejściowe pole tekstowe z klasą do ustawiania stylu, IDENTYFIKATORem, do którego odwołuje się kod JavaScript, oraz tekst zastępczy.  

W tym samym pliku Osadź kod JavaScript, który odwołuje się do pola wyszukiwania. Poniższa funkcja wywołuje interfejs API sugerowania, który żąda sugerowanych pasujących dokumentów na podstawie danych wejściowych częściowego terminu:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

`source`Informuje funkcję autouzupełniania interfejsu użytkownika jQuery, gdzie uzyskać listę elementów do wyświetlenia w polu wyszukiwania. Ponieważ ten projekt jest projektem MVC, wywołuje funkcję **Sugeruj** w **HomeController. cs** , która zawiera logikę do zwracania sugestii dotyczących zapytań. Ta funkcja przekazuje również kilka parametrów w celu kontrolowania świateł, dopasowywania rozmytego i warunku. Interfejs API autouzupełniania języka JavaScript dodaje parametr term.

`minLength: 3`Zapewnia, że zalecenia będą wyświetlane tylko wtedy, gdy w polu wyszukiwania znajdują się co najmniej trzy znaki.

### <a name="enable-fuzzy-matching"></a>Włącz dopasowywanie rozmyte

Wyszukiwanie rozmyte pozwala uzyskać wyniki na podstawie zbliżonych dopasowań nawet wtedy, gdy użytkownik błędnie napisze wyraz w polu wyszukiwania. Wartość Edytuj odległość wynosi 1, co oznacza, że może istnieć maksymalna rozbieżność jednego znaku między danymi wejściowymi użytkownika a dopasowaniem. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Włącz podświetlanie

Wyróżnianie stosuje styl czcionki do znaków w wyniku, który odpowiada dane wejściowe. Jeśli na przykład częściowe dane wejściowe to "mikro", wynik będzie wyglądał jako **Micro** Soft, **Micro** Scope i tak dalej. Wyróżnianie opiera się na parametrach HighlightPreTag i HighlightPostTag zdefiniowanych w tekście przy użyciu funkcji sugestii.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Funkcja Sugeruj

Jeśli używasz języka C# i aplikacji MVC, plik **HomeController. cs** w katalogu controllers jest miejscem, w którym można utworzyć klasę sugerowanych wyników. W programie .NET Funkcja Sugeruj jest oparta na [metodzie SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync). Aby uzyskać więcej informacji na temat zestawu .NET SDK, zobacz [jak używać platformy Azure wyszukiwanie poznawcze z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

`InitSearch`Metoda tworzy uwierzytelnionego klienta indeksu http w usłudze Azure wyszukiwanie poznawcze. Właściwości klasy [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) określają, które pola są przeszukiwane i zwracane w wynikach, liczbę dopasowań oraz czy użyto dopasowania rozmytego. 

W przypadku autouzupełniania dopasowywanie rozmyte jest ograniczone do jednej odległości edycji (jeden pominięty lub nieumieszczony znak). Należy zauważyć, że dopasowywanie rozmyte w zapytaniach autouzupełniania może czasami generować nieoczekiwane wyniki w zależności od rozmiaru indeksu i sposobu jego podzielonej na fragmenty. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące partycji i fragmentowania](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

Funkcja SuggestAsync przyjmuje dwa parametry, które określają, czy wyróżnienia trafień są zwracane, czy jako uzupełnienie danych wejściowych wyszukiwanego terminu użyto dopasowywania rozmytego. W sugerowanych wynikach można uwzględnić maksymalnie osiem dopasowań. Metoda tworzy [obiekt SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions), który następnie jest przenoszona do interfejsu API sugerowania. Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

## <a name="autocomplete"></a>Autouzupełnianie

Do tej pory kod środowiska użytkownika wyszukiwania został wyśrodkowany na sugestii. Następny blok kodu przedstawia Autouzupełnianie przy użyciu funkcji Autouzupełnianie interfejsu użytkownika XDSoft jQuery, przekazując żądanie do usługi Azure Wyszukiwanie poznawcze Autouzupełnianie. Podobnie jak w przypadku sugestii, w aplikacji języka C# kod, który obsługuje interakcję użytkownika, znajduje się w **indeksie. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funkcja Autouzupełnianie

Autouzupełnianie jest oparte na [metodzie AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Podobnie jak w przypadku sugestii, ten blok kodu przejdzie do pliku **HomeController. cs** .

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

Funkcja Autouzupełnianie pobiera dane wejściowe terminu wyszukiwania. Metoda tworzy [obiekt AutoCompleteParameters](/rest/api/searchservice/autocomplete). Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z tych linków, aby uzyskać szczegółowe instrukcje lub kod pokazujący, że oba środowiska wyszukiwania są w trakcie pisania. Oba przykłady kodu obejmują hybrydowe implementacje sugestii i funkcji Autouzupełnianie.

+ [Samouczek: Tworzenie pierwszej aplikacji w języku C# (Lekcja 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Przykładowy kod w języku C#: Azure-Search-dotnet-Samples/Create-First-App/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead)