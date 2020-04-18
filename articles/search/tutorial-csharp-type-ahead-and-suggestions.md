---
title: Autouzupełnienie i sugestie
titleSuffix: Azure Cognitive Search
description: W tym samouczku pokazano autouzupełnianie i sugestie jako sposób zbierania danych wejściowych wyszukiwanych haseł od użytkowników korzystających z listy rozwijanej. Opiera się na istniejącym projekcie hoteli.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 6b74c3bbb811c122950fd969a8797e87f8f77f86
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641073"
---
# <a name="c-tutorial-add-autocomplete-and-suggestions---azure-cognitive-search"></a>Samouczek C#: Dodawanie autouzupełniania i sugestii — Azure Cognitive Search

Dowiedz się, jak zaimplementować autouzupełnianie (zapytania typu naczołgowe i sugerowane dokumenty), gdy użytkownik zacznie wpisywać wpis w polu wyszukiwania. W tym samouczku pokażemy automatycznie kompletowane zapytania i wyniki sugestii oddzielnie, a następnie razem. Użytkownik może mieć tylko wpisać dwa lub trzy znaki, aby zlokalizować wszystkie wyniki, które są dostępne.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Dodawanie sugestii
> * Dodawanie wyróżniania do sugestii
> * Dodawanie autouzupełniania
> * Łączenie autouzupełniania i sugestii

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek jest częścią serii i opiera się na projekcie stronicowania utworzonym w [samouczku C#: Podział wyników wyszukiwania — Azure Cognitive Search](tutorial-csharp-paging.md).

Alternatywnie, można pobrać i uruchomić rozwiązanie dla tego konkretnego [samouczka: 3-add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Dodawanie sugestii

Zacznijmy od najprostszego przypadku oferowania alternatyw dla użytkownika: rozwijanej listy sugestii.

1. W pliku index.cshtml `@id` zmień instrukcję **TextBoxFor** na **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. Po tej instrukcji, po zamknięciu ** &lt;/div,&gt;** wprowadź ten skrypt. Ten skrypt wykorzystuje [widżet Autouzupełnianie](https://api.jqueryui.com/autocomplete/) z biblioteki interfejsu użytkownika jQuery typu open source, aby przedstawić listę rozwijaną sugerowanych wyników. 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    Identyfikator "azureautosuggest" łączy powyższy skrypt z polem wyszukiwania. Opcja źródłowa widżetu jest ustawiona na Sugestia metoda, która wywołuje Suggest API z dwóch parametrów kwerendy: **podkreśla** i **rozmyte,** zarówno ustawione na false w tym przypadku. Ponadto do wyzwolenia wyszukiwania potrzebne są co najmniej dwa znaki.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Dodawanie odwołań do skryptów jQuery do widoku

1. Aby uzyskać dostęp do biblioteki &lt;&gt; jQuery, zmień sekcję head pliku widoku na następujący kod:

    ```cs
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

2. Ponieważ wprowadzamy nowe odwołanie do jQuery, musimy również usunąć lub skomentować domyślne odwołanie do jQuery w pliku _Layout.cshtml (w folderze **Widoki/Udostępnione).** Znajdź następujące wiersze i skomentuj pierwszy wiersz skryptu, jak pokazano. Ta zmiana pozwala uniknąć kolizji odniesień do jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Teraz możemy użyć wstępnie zdefiniowanych funkcji autouzupełnianie jQuery.

### <a name="add-the-suggest-action-to-the-controller"></a>Dodaj akcję Zaproponuj do kontrolera

1. W kontrolerze domowym dodaj akcję **Sugeruj** (powiedzmy, po akcji **Strona).**

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    **Parametr Top** określa, ile wyników ma zwrócić (jeśli nieokreślone, wartość domyślna to 5). _Sugestator_ jest określony w indeksie platformy Azure, który odbywa się podczas konfigurowania danych, a nie przez aplikację kliencką, taką jak ten samouczek. W tym przypadku sugest jest nazywany "sg", i przeszukuje pole **HotelName** - nic więcej. 

    Rozmyte dopasowywanie pozwala na uwzględnienie "bliskich chybień" na wyjściu, do jednej odległości edycji. Jeśli parametr **highlights** jest ustawiony na true, do danych wyjściowych dodawane są pogrubione znaczniki HTML. W następnej sekcji ustawimy te dwa parametry na true.

2. Mogą pojawić się błędy składni. Jeśli tak, dodaj następujące dwa **instrukcje przy użyciu** górnej części pliku.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Uruchom aplikację. Czy masz szereg opcji po wprowadzeniu "po", na przykład? Teraz spróbuj "pa".

    ![Wpisanie "po" ujawnia dwie sugestie](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Należy zauważyć, że litery, które _wprowadzasz, muszą rozpoczynać_ słowo, a nie po prostu być zawarte w słowie.

4. W skrypcie widoku ustaw **&rozmyte** na true i uruchom aplikację ponownie. Teraz wpisz "po". Zauważ, że wyszukiwanie zakłada, że masz jedną literę źle!
 
    ![Wpisywanie "pa" z rozmytym zestawem do true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Jeśli jesteś zainteresowany, [składnia zapytania Lucene w usłudze Azure Cognitive Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) opisuje logikę używaną w wyszukiwaniach rozmytych szczegółowo.

## <a name="add-highlighting-to-the-suggestions"></a>Dodawanie wyróżniania do sugestii

Możemy poprawić wygląd sugestii dla użytkownika, ustawiając parametr **highlights** na true. Jednak najpierw musimy dodać kod do widoku, aby wyświetlić pogrubiony tekst.

1. W widoku (index.cshtml) dodaj następujący skrypt po **skryptie azureautosuggest,** który został wprowadzony powyżej.

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

2. Teraz zmień identyfikator pola tekstowego, aby było ono odczytywana w następujący sposób.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Uruchom aplikację ponownie, a wprowadzony tekst powinien być pogrubiony w sugestiach. Powiedz, spróbuj wpisać "pa".
 
    ![Wpisywanie "pa" z podświetleniem](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Logika używana w powyższym skrypcie wyróżniania nie jest niezawodna. Jeśli wprowadzisz termin, który pojawia się dwa razy w tej samej nazwie, pogrubione wyniki nie są takie, jakie chcesz. Spróbuj wpisać "mo".

    Jednym z pytań, na które deweloper musi odpowiedzieć, jest to, kiedy skrypt działa "wystarczająco dobrze", a kiedy należy zająć się jego dziwactwami. Nie będziemy brać podświetlanie dalej w tym samouczku, ale znalezienie dokładnego algorytmu jest coś do rozważenia, jeśli wyróżnianie nie jest skuteczne dla danych. Aby uzyskać więcej informacji, zobacz [Naciśnięcie podświetlania](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Dodawanie autouzupełniania

Inną odmianą, która różni się nieco od sugestii, jest autouzupełnianie (czasami nazywane "type-ahead"), które kończy termin kwerendy. Ponownie zaczniemy od najprostszej implementacji, zanim poprawimy środowisko użytkownika.

1. Wprowadź następujący skrypt do widoku, zgodnie z poprzednimi skryptami.

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

2. Teraz zmień identyfikator pola tekstowego, aby było ono następujące.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. W kontrolerze domowym musimy wprowadzić akcję **Autouzupełnienie,** powiedzmy, poniżej akcji **Zaproponuj.**

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Zauważ, że używamy tej samej funkcji *sugestywny,* o nazwie "sg", w wyszukiwaniu autouzupełniania, jak zrobiliśmy dla sugestii (więc staramy się tylko autouzupełniania nazw hoteli).

    Istnieje szereg ustawień **Autouzupełnianiamode,** a my używamy **OneTermWithContext**. Opis dodatkowych opcji można znaleźć w [interfejsie API autouzupełniania.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

4. Uruchom aplikację. Zwróć uwagę, że zakres opcji wyświetlanych na liście rozwijanej to pojedyncze słowa. Spróbuj wpisać słowa zaczynające się od "re". Zwróć uwagę, jak zmniejsza się liczba opcji w miarę wpisywania większej liczby liter.

    ![Pisanie z podstawowym autouzupełnianiem](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    W obecnej formie skrypt sugestii, który prowadziłeś wcześniej, jest prawdopodobnie bardziej pomocny niż ten skrypt autouzupełniania. Aby autouzupełnienie było bardziej przyjazne dla użytkownika, najlepiej jest je dodać do wyszukiwania sugestii.

## <a name="combine-autocompletion-and-suggestions"></a>Łączenie autouzupełniania i sugestii

Łączenie autouzupełniania i sugestii jest najbardziej złożonym z naszych opcji i prawdopodobnie zapewnia najlepsze wrażenia użytkownika. Co chcemy jest wyświetlanie, w tekście, który jest wpisywany, jest pierwszym wyborem usługi Azure Cognitive Search do automatycznego kompletowania tekstu. Ponadto chcemy szereg sugestii jako listy rozwijanej.

Istnieją biblioteki, które oferują tę funkcję — często nazywane "autouzupełnianiem wbudowanym" lub podobną nazwą. Jednak zamierzamy natywnie wdrożyć tę funkcję, dzięki czemu można zobaczyć, co się dzieje. Najpierw w tym przykładzie rozpoczniemy prace nad kontrolerem.

1. Musimy dodać akcję do kontrolera, który zwraca tylko jeden wynik autouzupełnienia, wraz z określoną liczbą sugestii. Będziemy nazywać tę akcję **AutouzupełniajandSuggest**. W kontrolerze domowym dodaj następującą akcję, wykonując inne nowe akcje.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    Jedna opcja autouzupełnienia jest zwracana u góry listy **wyników,** a następnie wszystkie sugestie.

2. W widoku najpierw implementujemy sztuczkę, aby jasnoszare słowo autouzupełniania było renderowane bezpośrednio pod odważniejszym tekstem wprowadzanym przez użytkownika. HTML obejmuje względne pozycjonowanie w tym celu. Zmień **Instrukcja TextBoxFor** (i otaczające ją &lt;instrukcje div)&gt; na następujące, zauważając, że drugie pole wyszukiwania zidentyfikowane jako znajdujące się pod **spodem** znajduje się tuż pod naszym normalnym polem wyszukiwania, wyciągając to pole wyszukiwania 39 pikseli poza jego domyślną lokalizacją!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Należy zauważyć, że zmieniamy identyfikator ponownie, na **azureautocomplete** w tym przypadku.

3. Również w widoku wprowadź następujący skrypt, po wszystkich skryptach, które do tej pory wprowadziłeś. Jest w tym całkiem sporo.

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
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
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

    Zwróć uwagę na sprytne użycie funkcji **interwału,** aby wyczyścić tekst podstawowy, gdy nie pasuje już do tego, co wpisuje użytkownik, a także ustawić tę samą literę (górną lub niższą), jak pisze użytkownik (jako "pa" pasuje do "PA", "pA", "Pa" podczas wyszukiwania), tak aby nałożony tekst był schludny.

    Przeczytaj komentarze w skrypcie, aby uzyskać pełniejsze zrozumienie.

4. Na koniec musimy dokonać drobnych korekt do dwóch klas HTML, aby były przejrzyste. Dodaj następujący wiersz do **searchBoxForm** i **searchBox** klasy, w pliku hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Teraz uruchom aplikację. Wpisz "pa" w polu wyszukiwania. Czy masz "pałac" jako sugestię autouzupełnienia, wraz z dwoma hotelami, które zawierają "pa"?

    ![Wpisywanie z wbudowanym autouzupełnianiem i sugestiami](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Spróbuj tabulatorzy, aby zaakceptować sugestię autouzupełniania, i spróbuj wybrać sugestie za pomocą klawiszy strzałek i klawisza tab, a następnie spróbuj ponownie użyć myszy i jednego kliknięcia. Sprawdź, czy skrypt obsługuje wszystkie te sytuacje starannie.

    Możesz zdecydować, że łatwiej jest załadować w bibliotece, która oferuje tę funkcję dla Ciebie, ale teraz wiesz, co najmniej jeden sposób, aby uzyskać wbudowane autouzupełnienie do pracy!

## <a name="takeaways"></a>Wnioski

Rozważmy następujące dania na wynos z tego projektu:

* Autouzupełnianie (znane również jako "typ z wyprzedzeniem") i sugestie mogą umożliwić użytkownikowi wpisanie tylko kilku klawiszy, aby zlokalizować dokładnie to, czego chce.
* Autouzupełnienie i sugestie współpracujące ze sobą mogą zapewnić bogate środowisko użytkownika.
* Zawsze testuj funkcje autouzupełniania ze wszystkimi formami wprowadzania.
* Za pomocą **setInterval** funkcja może być przydatne w weryfikacji i korygowania elementów interfejsu użytkownika.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku przyjrzymy się innemu sposóbowi poprawy środowiska użytkownika, używając aspektów do zawężenia wyszukiwania za pomocą jednego kliknięcia.

> [!div class="nextstepaction"]
> [Samouczek C#: używanie aspektów do pomocy w nawigacji — usługa Azure Cognitive Search](tutorial-csharp-facets.md)


