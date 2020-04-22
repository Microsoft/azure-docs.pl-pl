---
title: Dodawanie autouzupełniania i sugestii w polu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz akcje kwerendy typu search-as-you-type w usłudze Azure Cognitive Search, tworząc sugesty i formułując żądania, które automatycznie zapełniają pole wyszukiwania z gotowymi terminami lub frazami. Możesz również zwrócić sugerowane mecze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758105"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Dodawanie autouzupełniania i sugestii do aplikacji klienckich

Wyszukiwanie zgodnie z typem jest powszechną techniką poprawy wydajności zapytań inicjowanych przez użytkownika. W usłudze Azure Cognitive Search to środowisko jest obsługiwane przez *autouzupełnianie*, które kończy termin lub frazę na podstawie częściowego wprowadzania danych (ukończenie "mikro" z "microsoft"). Innym formularzem są *sugestie:* krótka lista pasujących dokumentów (zwracanie tytułów książek z identyfikatorem, dzięki czemu można utworzyć łącze do strony szczegółów). Zarówno autouzupełnienie, jak i sugestie są oparte na dopasowaniu w indeksie. Usługa nie będzie oferować kwerend, które zwracają zero wyników.

Aby zaimplementować te środowiska w usłudze Azure Cognitive Search, konieczne będzie:

+ *Sugest na* zapleczu.
+ *Kwerenda* określająca interfejs API [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) lub [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions) w żądaniu.
+ *Kontrolka interfejsu użytkownika* do obsługi interakcji wyszukiwania po typie w aplikacji klienckiej. Zalecamy użycie w tym celu istniejącej biblioteki JavaScript.

W usłudze Azure Cognitive Search automatycznie kompletowane kwerendy i sugerowane wyniki są pobierane z indeksu wyszukiwania z wybranych pól, które zostały zarejestrowane w suggesterze. Sugestywny jest częścią indeksu i określa, które pola będą dostarczać zawartość, która kończy kwerendę, sugeruje wynik lub wykonuje oba. Gdy indeks jest tworzony i ładowany, struktury danych sugestywny jest tworzony wewnętrznie do przechowywania prefiksów używanych do dopasowywania w kwerendach częściowych. W przypadku sugestii wybór odpowiednich pól, które są unikatowe lub przynajmniej nie powtarzalne, jest niezbędny do doświadczenia. Aby uzyskać więcej informacji, zobacz [Tworzenie sugestatora](index-add-suggesters.md).

Poniżej dalsza część artykułu Koncentruje się na kwerendach i kodzie klienta. Używa JavaScript i C# do zilustrowania kluczowych punktów. PRZYKŁADY INTERFEJSU API REST są używane do zwięzłego prezentowania każdej operacji. Aby uzyskać łącza do przykładów kodu end-to-end, zobacz [Następne kroki](#next-steps).

## <a name="set-up-a-request"></a>Konfigurowanie żądania

Elementy żądania obejmują jeden z interfejsów API wyszukiwania jako typu, kwerendy częściowej i sugestywny. Poniższy skrypt ilustruje składniki żądania, przy użyciu interfejsu API REST autouzupełnianego jako przykład.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**Nazwaspozycjowa** zawiera pola obsługujące sugestę używane do wypełniania terminów lub sugestii. W szczególności w przypadku sugestii lista pól powinna składać się z tych, które oferują jasne opcje wśród pasujących wyników. W witrynie, która sprzedaje gry komputerowe, pole może być tytuł gry.

Parametr **wyszukiwania** zawiera kwerendę częściową, w której znaki są podawane do żądania zapytania za pośrednictwem kontrolki jQuery Autouzupełnianie. W powyższym przykładzie "minecraf" jest statyczną ilustracją tego, co formant mógł przekazać.

Interfejsy API nie nakładają minimalnych wymagań dotyczących długości kwerendy częściowej; może to być zaledwie jeden znak. Jednak jQuery Autouzupełnianie zapewnia minimalną długość. Typowe są co najmniej dwa lub trzy znaki.

Dopasowania znajdują się na początku terminu w dowolnym miejscu ciągu wejściowego. Biorąc pod uwagę "szybki brązowy lis", zarówno autouzupełnienie, jak i sugestie będą pasować do częściowych wersji "", "szybkie", "brązowe" lub "lis", ale nie na częściowych terminach infix, takich jak "rown" lub "wół". Ponadto każde dopasowanie ustawia zakres rozszerzeń podrzędnych. Częściowe zapytanie "quick br" będzie pasować do "quick brown" lub "quick bread", ale ani "brązowy" lub "chleb" sam będzie pasował, chyba że "szybkie" poprzedza je.

### <a name="apis-for-search-as-you-type"></a>Interfejsy API wyszukiwania według typu

Skorzystaj z tych łączy dla stron referencyjnych REST i .NET SDK:

+ [Sugestie REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Interfejs API REST autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Sugestia z metodąHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Autouzupełnienie z metodąHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Ustrukturyzuj odpowiedź

Odpowiedzi na autouzupełnianie i sugestie są tym, czego można oczekiwać od wzorca: [Autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) zwraca listę [terminów, Sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) zwraca terminy oraz identyfikator dokumentu, dzięki czemu można pobrać dokument (użyj interfejsu API [dokumentu odnośnika,](https://docs.microsoft.com/rest/api/searchservice/lookup-document) aby pobrać określony dokument dla strony szczegółów).

Odpowiedzi są kształtowane przez parametry na żądanie. W przypadku autouzupełniania ustaw [**autouzupełnianieMode,**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) aby określić, czy uzupełnianie tekstu odbywa się na jednym lub dwóch terminach. W polu Sugestie wybrano pole określa zawartość odpowiedzi.

W przypadku sugestii należy dodatkowo uściślić odpowiedź, aby uniknąć duplikatów lub tego, co wydaje się być niepowiązanymi wynikami. Aby kontrolować wyniki, należy uwzględnić więcej parametrów na żądanie. Następujące parametry dotyczą zarówno autouzupełnienia, jak i sugestii, ale być może są bardziej potrzebne w przypadku sugestii, zwłaszcza gdy sugest zawiera wiele pól.

| Parametr | Sposób użycia |
|-----------|-------|
| **$select** | Jeśli w sugescie masz wiele **pól źródłowych,** użyj **$select,** aby`$select=GameTitle`wybrać, które pole współtworzy wartości ( ). |
| **pola wyszukiwania** | Ogranicz kwerendę do określonych pól. |
| **$filter** | Zastosuj kryteria dopasowania do`$filter=Category eq 'ActionAdventure'`zestawu wyników ( ). |
| **$top** | Ogranicz wyniki do`$top=5`określonej liczby ( ).|

## <a name="add-user-interaction-code"></a>Dodawanie kodu interakcji użytkownika

Automatyczne wypełnianie terminu zapytania lub upuszczanie listy pasujących łączy wymaga kodu interakcji użytkownika, zazwyczaj JavaScript, który może korzystać z żądań ze źródeł zewnętrznych, takich jak automatyczne uzupełnianie lub kwerendy sugestii względem indeksu usługi Azure Search Cognitive.

Chociaż można napisać ten kod natywnie, jest o wiele łatwiejsze w użyciu funkcji z istniejącej biblioteki JavaScript. W tym artykule przedstawiono dwa, jeden dla sugestii, a drugi dla autouzupełniania. 

+ [Widżet Autouzupełniaj (jQuery UI)](https://jqueryui.com/autocomplete/) jest używany w przykładzie sugestia. Można utworzyć pole wyszukiwania, a następnie odwołać się do niego w funkcji JavaScript, która używa widżetu Autouzupełnianie. Właściwości w widżetu ustawiają źródło (funkcja autouzupełniania lub sugestie), minimalną długość znaków wejściowych przed podjęciem akcji i pozycjonowanie.

+ [XDSoft Autouzupełniania plug-in](https://xdsoft.net/jqplugins/autocomplete/) jest używany autouzupełniania przykład.

Używamy tych bibliotek do tworzenia pola wyszukiwania obsługującego zarówno sugestie, jak i autouzupełnianie. Dane wejściowe zebrane w polu wyszukiwania są łączone z sugestiami i akcjami autouzupełniania.

## <a name="suggestions"></a>Sugestie

W tej sekcji znajdziesz implementację sugerowanych wyników, począwszy od definicji pola wyszukiwania. Pokazuje również, jak i skrypt, który wywołuje pierwszą bibliotekę autouzupełniania JavaScript, do których odwołuje się w tym artykule.

### <a name="create-a-search-box"></a>Tworzenie pola wyszukiwania

Zakładając, że [biblioteka autouzupełniania interfejsu użytkownika jQuery](https://jqueryui.com/autocomplete/) i projekt MVC w języku C#, można zdefiniować pole wyszukiwania przy użyciu języka JavaScript w pliku **Index.cshtml.** Biblioteka dodaje interakcji wyszukiwania po typie do pola wyszukiwania, wykonując asynchroniczne wywołania do kontrolera MVC, aby pobrać sugestie.

W **pliku Index.cshtml** w folderze \Views\Home wiersz utworzenia pola wyszukiwania może być następujący:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

W tym przykładzie jest proste pole tekstowe wprowadzania z klasy do stylizacji, identyfikator, do którego odwołuje się JavaScript i tekst zastępczy.  

W tym samym pliku osadź javascript, który odwołuje się do pola wyszukiwania. Następująca funkcja wywołuje interfejs API Sugestia, który żąda sugerowanych dokumentów pasujących na podstawie częściowych danych wejściowych:

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

Informuje `source` funkcję autouzupełniania interfejsu użytkownika jQuery, gdzie można uzyskać listę elementów do wyświetlenia w polu wyszukiwania. Ponieważ ten projekt jest projekt MVC, wywołuje **Suggest** funkcji w **HomeController.cs,** który zawiera logikę zwracania sugestie kwerendy. Ta funkcja przekazuje również kilka parametrów do kontrolowania podświetleń, dopasowywania rozmytego i terminu. Interfejs API autouzupełniania języka JavaScript dodaje parametr term.

Gwarantuje, `minLength: 3` że zalecenia będą wyświetlane tylko wtedy, gdy w polu wyszukiwania znajdują się co najmniej trzy znaki.

### <a name="enable-fuzzy-matching"></a>Włącz dopasowywanie rozmyte

Wyszukiwanie rozmyte pozwala uzyskać wyniki na podstawie zbliżonych dopasowań nawet wtedy, gdy użytkownik błędnie napisze wyraz w polu wyszukiwania. Odległość edycji wynosi 1, co oznacza, że może istnieć maksymalna rozbieżność jednego znaku między daneem wejściowym użytkownika a dopasowaniem. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Włącz wyróżnianie

Wyróżnianie stosuje styl czcionki do znaków w wyniku, które odpowiadają dane wejściowe. Na przykład, jeśli częściowe wejście jest "mikro", wynik będzie wyświetlany jako **mikro**miękki, **mikro**zakres i tak dalej. Wyróżnianie jest oparte na highlightPreTag i HighlightPostTag parametrów, zdefiniowane w linii z funkcji sugestia.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Zasugeruj funkcję

Jeśli używasz języka C# i aplikacji MVC, **HomeController.cs** plik w katalogu Kontrolery jest, gdzie można utworzyć klasę dla sugerowanych wyników. W .NET funkcja Sugestia jest oparta na [metodzie DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

Metoda `InitSearch` tworzy uwierzytelnionego klienta indeksu HTTP do usługi Azure Cognitive Search. Aby uzyskać więcej informacji na temat sdk .NET, zobacz [Jak korzystać z usługi Azure Cognitive Search z aplikacji .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funkcja Suggest przyjmuje dwa parametry, które określają, czy są zwracane wyróżnienia trafień oraz czy oprócz wprowadzonego terminu wyszukiwania jest stosowane dopasowywanie rozmyte. Metoda tworzy [SuggestParameters obiektu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), który jest następnie przekazywany do Suggest API. Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

## <a name="autocomplete"></a>Autouzupełnianie

Do tej pory kod UX wyszukiwania został wyśrodkowany na sugestie. Następny blok kodu pokazuje autouzupełnianie, przy użyciu funkcji autouzupełniania interfejsu użytkownika XDSoft jQuery, przekazując żądanie autouzupełniania usługi Azure Cognitive Search. Podobnie jak w przypadku sugestii, w aplikacji Języka C# kod, który obsługuje interakcję użytkownika idzie w **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
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

### <a name="autocomplete-function"></a>Funkcja autouzupełniania

Autouzupełnianie jest oparte na [metody DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Podobnie jak w sugestiach, ten blok kodu trafi w pliku **HomeController.cs.**

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funkcja Autouzupełniania pobiera dane wejściowe wyszukiwane. Metoda tworzy [obiekt Autouzupełniajparametry](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z tych łączy, aby uzyskać instrukcje end-to-end lub kod demonstrujący zarówno wyszukiwanie jako typ środowiska. Oba przykłady kodu obejmują hybrydowe implementacje sugestii i autouzupełnienie razem.

+ [Samouczek: Tworzenie pierwszej aplikacji w języku C# (lekcja 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Przykład kodu języka C#: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# i JavaScript z przykładowym kodem REST obok siebie](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)