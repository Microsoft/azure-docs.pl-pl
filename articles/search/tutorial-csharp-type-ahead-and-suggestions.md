---
title: Samouczek języka C# dotyczący funkcji Autouzupełnianie i sugestii
titleSuffix: Azure Cognitive Search
description: Dodawanie funkcji Autouzupełnianie i sugestii w celu zbierania danych wejściowych terminu wyszukiwania od użytkowników przy użyciu listy rozwijanej. Ten samouczek jest oparty na istniejącym projekcie hoteli.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/22/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 06c0b25bcf64cfce01b4144550ef69da8c96ee0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785857"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Samouczek: Dodawanie funkcji Autouzupełnianie i sugestii przy użyciu zestawu .NET SDK

Dowiedz się, jak zaimplementować Autouzupełnianie (zapytania typeahead i sugerowane wyniki), gdy użytkownik zacznie pisać w polu wyszukiwania. W tym samouczku pokazywane są zapytania AutoComplete i sugerowane wyniki oddzielnie, a następnie razem. Użytkownik może tylko wpisać dwa lub trzy znaki, aby znaleźć wszystkie dostępne wyniki.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Dodawanie sugestii
> * Dodawanie wyróżniania do sugestii
> * Dodaj Autouzupełnianie
> * Łączenie autouzupełniania i sugestii

## <a name="overview"></a>Omówienie

Ten samouczek zawiera instrukcje autouzupełniania i sugerowane wyniki do poprzedniej instrukcji [Dodaj stronicowanie do wyników wyszukiwania](tutorial-csharp-paging.md) .

Gotowa wersja kodu w tym samouczku można znaleźć w następującym projekcie:

* [3-Add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Wymagania wstępne

* [2a — rozwiązanie dodawania stronicowania (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) . Ten projekt może być własną wersją utworzoną na podstawie poprzedniego samouczka lub kopii z usługi GitHub.

Ten samouczek został zaktualizowany do korzystania z pakietu [Azure.Search.Documents (wersja 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . Aby uzyskać wcześniejszą wersję zestawu .NET SDK, zobacz [przykład kodu Microsoft. Azure. Search (wersja 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="add-suggestions"></a>Dodawanie sugestii

Zacznijmy od najprostszego przypadku zaoferowania użytkownikowi alternatywy: listy rozwijanej sugerowanych wyników.

1. W pliku index. cshtml Zmień `@id` instrukcję **TextBoxFor** na **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Po **&lt; &gt; zamknięciu tej zasady należy wprowadzić** ten skrypt. Ten skrypt korzysta z [widżetu Autouzupełnianie](https://api.jqueryui.com/autocomplete/) z biblioteki interfejsu użytkownika Open-Source w celu wyświetlenia listy rozwijanej sugerowanych wyników.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    Identyfikator `"azureautosuggest"` nawiązuje połączenie powyższego skryptu z polem wyszukiwania. Opcja Source elementu widget jest ustawiona na metodę sugerowania, która wywołuje interfejs API sugerowania z dwoma parametrami zapytania: **wyróżnione** i **rozmyte**, oba ustawione na wartość false w tym wystąpieniu. Ponadto do wyzwolenia wyszukiwania są niezbędne co najmniej dwa znaki.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Dodawanie odwołań do skryptów jQuery do widoku

1. Aby uzyskać dostęp do biblioteki jQuery, Zmień &lt; sekcję nagłówkową &gt; pliku widoku na następujący kod:

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Ze względu na to, że wprowadzamy nowe odwołanie jQuery, należy również usunąć lub dodać komentarz do domyślnego odwołania jQuery w pliku _Layout. cshtml (w **widokach/folderze udostępnionym** ). Znajdź poniższe wiersze i Skomentuj pierwszy wiersz skryptu, jak pokazano. Ta zmiana pozwala uniknąć konfliktów odwołań do jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Teraz możemy używać wstępnie zdefiniowanych funkcji Autouzupełnianie jQuery.

### <a name="add-the-suggest-action-to-the-controller"></a>Dodaj akcję Sugeruj do kontrolera

1. Na kontrolerze głównym Dodaj akcję **SuggestAsync** (po akcji **PageAsync** ).

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
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

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    Parametr **size** określa liczbę wyników do zwrócenia (jeśli nie zostanie określony, wartością domyślną jest 5). W indeksie wyszukiwania jest określany element _sugerujący_ , gdy indeks zostanie utworzony. W przykładowym indeksie hoteli hostowanym przez firmę Microsoft nazwa Sugerowana to "SG" i wyszukuje sugerowane dopasowania wyłącznie w polu **hotelname** .

    Dopasowanie rozmyte umożliwia uwzględnienie "blisko chybień" w danych wyjściowych, aż do jednej odległości edycji. Jeśli dla parametru **wyróżnienia** określono wartość true, znaczniki HTML pogrubione są dodawane do danych wyjściowych. W następnej sekcji ustawimy oba parametry na wartość true.

2. Mogą wystąpić błędy składniowe. Jeśli tak, Dodaj następujące dwie instrukcje **using** na początku pliku.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Uruchom aplikację. Czy po wprowadzeniu "ZZ" uzyskasz różne opcje? Teraz spróbuj "PA".

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="Wpisanie * po * ujawnia dwie sugestie" border="false":::

    Zauważ, że wprowadzane litery _muszą_ rozpoczynać się od słowa, a nie tylko w tym słowie.

4. W skrypcie widoku Ustaw **&rozmyte** na true, a następnie ponownie uruchom aplikację. Teraz wprowadź "po". Zwróć uwagę na to, że wyszukiwanie założono, że podano jedną literę.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="Wpisanie * PA * z rozmytym ustawieniem true" border="false":::

    Jeśli interesuje Cię, [Składnia zapytania Lucene na platformie Azure wyszukiwanie poznawcze](./query-lucene-syntax.md) opisuje logikę używaną w przypadku wyszukiwania rozmytego.

## <a name="add-highlighting-to-the-suggestions"></a>Dodawanie wyróżniania do sugestii

Możemy ulepszyć wygląd sugestii użytkownika, ustawiając dla parametru " **podświetli** " wartość true. Należy jednak najpierw dodać kod do widoku, aby wyświetlić tekst pogrubiony.

1. W widoku (index. cshtml) Dodaj następujący skrypt po `"azureautosuggest"` opisanej wcześniej skrypcie.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. Teraz zmień identyfikator pola tekstowego, tak aby odczytał się w następujący sposób.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Uruchom aplikację ponownie i zobaczysz wpisany tekst pogrubiony w sugestiach. Spróbuj wpisać "PA".
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="Wpisywanie * PA * z wyróżnieniem" border="false":::

   Logika użyta w powyższym skrypcie wyróżniania nie jest foolproof. Jeśli wprowadzisz termin, który występuje dwa razy pod tą samą nazwą, pogrubione wyniki nie będą całkiem wykonywane. Spróbuj wpisać "mo".

   Jednym z pytań, na które musi odpowiadać Deweloper, jest to, gdy skrypt działa prawidłowo, a kiedy należy osobliwości. W tym samouczku nie zajmiemy się wyróżnieniem, ale znalezienie dokładnego algorytmu ma na celu rozważenie, czy wyróżnianie nie obowiązuje w przypadku danych. Aby uzyskać więcej informacji, zobacz [wyróżnianie trafień](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Dodaj Autouzupełnianie

Inna odmiana, nieco inna od sugestii, jest autouzupełniania (czasami nazywana "typu"), która kończy termin zapytania. Ponownie zaczniemy od najprostszej implementacji przed ulepszeniem środowiska użytkownika.

1. Wprowadź następujący skrypt do widoku, wykonując poprzednie skrypty.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. Teraz zmień identyfikator pola tekstowego, aby odczytał się w następujący sposób.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Na kontrolerze głównym wprowadź akcję **AutocompleteAsync** po akcji **SuggestAsync** .

    ```cs
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

    Zwróć uwagę, że używamy tej samej funkcji *sugerującej* o nazwie "SG" w wyszukiwaniu autouzupełniania zgodnie z sugestiami (w związku z czym próbujemy tylko Autouzupełnianie nazw hotelów).

    Istnieje wiele ustawień opcji **AutoComplete** i **OneTermWithContext**. Zapoznaj się z [interfejsem API autouzupełniania](/rest/api/searchservice/autocomplete) , aby uzyskać opis dodatkowych opcji.

1. Uruchom aplikację. Zwróć uwagę na to, jak zakres opcji wyświetlanych na liście rozwijanej to pojedyncze słowa. Spróbuj wpisać słowa zaczynające się od "re". Zauważ, że liczba opcji jest mniejsza, jeśli wpisano więcej liter.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="Wpisywanie przy użyciu podstawowego autouzupełniania" border="false":::

    Jak to się dzieje, skrypt sugestii, który był uruchamiany wcześniej jest prawdopodobnie bardziej użyteczny niż ten skrypt autouzupełniania. Aby zapewnić Autouzupełnianie bardziej przyjazny dla użytkownika, rozważ użycie jej z sugerowanymi wynikami.

## <a name="combine-autocompletion-and-suggestions"></a>Łączenie autouzupełniania i sugestii

Łączenie autouzupełniania i sugestii jest najbardziej skomplikowane dla naszych opcji i prawdopodobnie zapewnia najlepsze środowisko użytkownika. Elementy, które chcemy wyświetlać, są wbudowane przy wpisywaniu tekstu, to pierwszy wybór Wyszukiwanie poznawcze platformy Azure do autouzupełniania tekstu. Ponadto chcemy mieć zakres sugestii jako listę rozwijaną.

Istnieją biblioteki, które oferują tę funkcję — często nazywane "wbudowanym autouzupełnianiem" lub podobną nazwą. Jednak chcemy natywnie zaimplementować tę funkcję, aby poznać interfejsy API. Zaczniemy najpierw korzystać z kontrolera w tym przykładzie.

1. Dodaj akcję do kontrolera, która zwraca tylko jeden wynik autouzupełniania wraz z określoną liczbą sugestii. Wywołamy tę akcję **AutoCompleteAndSuggestAsync**. Na kontrolerze głównym Dodaj następującą akcję, postępując zgodnie z innymi nowymi akcjami.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    Jedna opcja autouzupełniania jest zwracana u góry listy **wyników** , a następnie wszystkie sugestie.

1. W widoku najpierw implementujemy lewę, aby jasne, szare słowo uzupełniające jest renderowane bezpośrednio w obszarze tekstu pogrubionego przez użytkownika. Język HTML zawiera Pozycjonowanie względne do tego celu. Zmień instrukcję **TextBoxFor** (i jej otaczające &lt; &gt; instrukcje DIV) na następujące polecenie, zwracając uwagę, że drugie pole wyszukiwania identyfikowane  jako część jest bezpośrednio pod naszym normalnym polem wyszukiwania, pobierając to pole wyszukiwania 39 pikseli z lokalizacji domyślnej.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Zauważ, że zmienimy identyfikator ponownie, aby **azureautocomplete** w tym przypadku.

1. Ponadto w widoku wprowadź następujący skrypt, po wykonaniu wszystkich skryptów wprowadzonych do tej pory. Skrypt jest długi i skomplikowany ze względu na różnorodność zachowań wejściowych, które obsługuje.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Zwróć uwagę, jak funkcja **interwału** służy do czyszczenia tekstu podstawowego, gdy nie jest już zgodny z wpisanym przez użytkownika, a także do ustawiania tego samego przypadku (wielkie lub małe litery), gdy użytkownik pisze tekst ("PA" pasuje do "PA", "PA", "PA" podczas wyszukiwania), tak aby należąca do nałożonego tekstu jest przejrzysty.

    Zapoznaj się z komentarzami w skrypcie, aby uzyskać pełniejsze informacje.

1. Na koniec musimy wprowadzić drobne dopasowanie do dwóch klas HTML, aby były one przezroczyste. Dodaj następujący wiersz do klas **searchBoxForm** i **searchBox** w pliku hoteli. css.

    ```html
    background: rgba(0,0,0,0);
    ```

1. Teraz uruchom aplikację. Wprowadź wartość "PA" w polu wyszukiwania. Czy otrzymujesz "Palace" jako sugestię autouzupełniania wraz z dwiema hotelimi zawierającymi "PA"?

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="Pisanie przy użyciu wbudowanego autouzupełniania i sugestii" border="false":::

1. Spróbuj użyć tabulacji w celu zaakceptowania sugestii autouzupełniania i spróbuj wybrać sugestie przy użyciu klawiszy strzałek i klawisza Tab, a następnie spróbuj ponownie, używając myszy i jednego kliknięcia. Sprawdź, czy skrypt obsługuje wszystkie te sytuacje w sposób starannie.

    Możesz zdecydować, że jest to prostsze do załadowania w bibliotece, która oferuje tę funkcję, ale teraz wiesz, co najmniej jeden sposób na uzyskanie wbudowanego autouzupełniania.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Funkcja autouzupełniania (znana także jako "typu") i sugestie mogą umożliwić użytkownikowi wpisanie tylko kilku kluczy, aby znaleźć dokładnie to, czego chcą.
* Autouzupełnianie i sugestie pracujące razem mogą zapewnić bogate środowisko użytkownika.
* Zawsze Testuj funkcje autouzupełniania przy użyciu wszystkich form danych wejściowych.
* Użycie funkcji **setInterval** może być przydatne podczas sprawdzania i poprawiania elementów interfejsu użytkownika.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku Przyjrzyjmy się innemu sposobowi ulepszania środowiska użytkownika przy użyciu aspektów, aby zawęzić wyszukiwanie za pomocą jednego kliknięcia.

> [!div class="nextstepaction"]
> [Samouczek języka C#: używanie aspektów w celu ułatwienia nawigacji — Wyszukiwanie poznawcze platformy Azure](tutorial-csharp-facets.md)
