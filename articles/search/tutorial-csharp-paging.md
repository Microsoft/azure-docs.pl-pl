---
title: Samouczek języka C# dotyczący dzielenia na strony wyników wyszukiwania
titleSuffix: Azure Cognitive Search
description: Dodawanie przycisków podziału na strony i nawigacji do wyników wyszukiwania, kompilowanie na istniejącym projekcie hoteli w celu dodania przycisków pierwszy, następny, poprzedni, ostatni i numerowany. Drugi system stronicowania używa Nieskończonego przewijania wyzwalanego przez przeniesienie pionowego paska przewijania do jego dolnego limitu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: bad4bc4d0016b2898b315bfb9799dc8972be7b12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785873"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Samouczek: Dodawanie stronicowania do wyników wyszukiwania przy użyciu zestawu .NET SDK

Dowiedz się, jak zaimplementować dwa różne systemy stronicowania, pierwsze na podstawie numerów stron i drugi przy nieskończonym przewijaniu. Oba systemy stronicowania są szeroko używane i wybór jednego z nich zależy od środowiska użytkownika, które chcesz uzyskać w wyniku. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Poszerzanie aplikacji za pomocą numerowanego stronicowania
> * Zwiększanie możliwości aplikacji dzięki nieskończonemu przewijaniu

## <a name="overview"></a>Omówienie

Ten samouczek nakłada system stronicowania na wcześniej utworzony projekt opisany w samouczku [Tworzenie pierwszej aplikacji wyszukiwania](tutorial-csharp-create-first-app.md) .

Gotowe wersje kodu, który będziesz opracowywać w tym samouczku, można znaleźć w następujących projektach:

* [2a — Dodawanie stronicowania (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2b — Dodawanie-nieograniczone — przewijanie (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>Wymagania wstępne

* [1 — projekt na stronie Wyszukiwanie podstawowe (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) . Ten projekt może być własną wersją utworzoną na podstawie poprzedniego samouczka lub kopii z usługi GitHub.

Ten samouczek został zaktualizowany do korzystania z pakietu [Azure.Search.Documents (wersja 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . Aby uzyskać wcześniejszą wersję zestawu .NET SDK, zobacz [przykład kodu Microsoft. Azure. Search (wersja 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="extend-your-app-with-numbered-paging"></a>Poszerzanie aplikacji za pomocą numerowanego stronicowania

Numerowane stronicowanie to wybrany system stronicowania dla głównych aparatów wyszukiwania w sieci Web oraz wielu innych witryn wyszukiwania. Stronicowanie numerowane zwykle zawiera opcję "Next" i "Previous" oprócz zakresu rzeczywistych numerów stron. Dostępna jest również opcja "Pierwsza Strona" i "Ostatnia strona". Te opcje dają kontrolę nad przechodzeniem między wynikami opartymi na stronach.

W tym samouczku dodasz system zawierający pierwszy, poprzedni, następny i ostatni z nich, a także numery stron, które nie zaczynają się od 1, ale zamiast tego otaczają bieżącą stronę, na przykład, jeśli użytkownik przegląda stronę 10, mogą być wyświetlane numery stron 8, 9, 10, 11 i 12.

System będzie wystarczająco elastyczny, aby można było ustawić liczbę widocznych numerów stron w zmiennej globalnej.

System będzie traktować przyciski o największej i największej liczbie stron jako specjalne, co oznacza, że będą wyzwalać zmianę zakresu wyświetlanych numerów stron. Na przykład jeśli są wyświetlane numery stron 8, 9, 10, 11 i 12, a użytkownik kliknie 8, zakres numerów stron zostanie zmieniony na 6, 7, 8, 9 i 10. Podobnie jak w przypadku wybrania 12.

### <a name="add-paging-fields-to-the-model"></a>Dodawanie pól stronicowania do modelu

Ma otwarte rozwiązanie podstawowego strony wyszukiwania.

1. Otwórz plik modelu SearchData. cs.

1. Dodaj zmienne globalne do obsługi stronicowania. W MVC zmienne globalne są deklarowane we własnej klasie statycznej. **ResultsPerPage** ustawia liczbę wyników na stronę. **MaxPageRange** określa liczbę widocznych numerów stron w widoku. **PageRangeDelta** określa liczbę stron, które mają zostać przesunięte w lewo lub w prawo, gdy zaznaczony jest numer strony od lewej lub do prawej. Zwykle ta druga liczba jest około połowy **MaxPageRange**. Dodaj następujący kod do przestrzeni nazw.

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Jeśli ten projekt jest uruchamiany na urządzeniu o mniejszym ekranie, na przykład na laptopie, Rozważ zmianę **ResultsPerPage** na 2.

1. Dodaj właściwości stronicowania do klasy **SearchData** , po właściwości **tekstprzeszukiwany** .

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Dodawanie tabeli opcji stronicowania do widoku

1. Otwórz plik index. cshtml i Dodaj poniższy kod bezpośrednio przed tagiem zamykającym &lt; /Body &gt; . Ten nowy kod przedstawia tabelę opcji stronicowania: pierwszy, poprzedni, 1, 2, 3, 4, 5, następny, ostatni.

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&lt;</p>
                }
            </td>

            @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
            {
                <td>
                    @if (Model.currentPage == pn)
                    {
                        // Convert displayed page numbers to 1-based and not 0-based.
                        <p class="pageSelected">@(pn + 1)</p>
                    }
                    else
                    {
                        <p class="pageButton">
                            @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;|</p>
                }
            </td>
        </tr>
    </table>
    }
    ```

    Używamy tabeli HTML do ujednolicenia elementów. Jednak wszystkie akcje pochodzą z @Html.ActionLink instrukcji, z których każdy wywołuje kontroler z **nowym** modelem utworzonym z różnymi wpisami do właściwości **stronicowania** , która została dodana wcześniej.

    Opcje pierwszej i ostatniej strony nie wysyłają ciągów, takich jak "First" i "Last", ale zamiast tego wysyłają poprawne numery stron.

1. Dodaj klasy stronicowania do listy stylów HTML w pliku hoteli. css. Klasa **pageSelected** ma identyfikować bieżącą stronę (stosując format pogrubiony do numeru strony) na liście numerów stron.

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Dodawanie akcji strony do kontrolera

1. Otwórz plik HomeController. cs i Dodaj akcję **PageAsync** . Ta akcja odpowiada dowolnej wybranej opcji strony.

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    Metoda **RunQueryAsync** będzie teraz zawierać błąd składniowy, z powodu trzeciego parametru, który zostanie w bitach.

    > [!Note]
    > Wywołania **TempData** przechowują wartość ( **obiekt**) w magazynie tymczasowym, chociaż ten magazyn utrzymuje _tylko_ jedno wywołanie. Jeśli przechowujemy coś w danych tymczasowych, będzie on dostępny dla następnego wywołania do akcji kontrolera, ale najpóźniej zostanie utracone przez wywołanie. Ze względu na ten krótki cykl życia przechowujemy w tymczasowym magazynie wszystkie właściwości tekstu i stronicowania, a każde wywołanie do **PageAsync**.

1. Zaktualizuj akcję **indeks (model)** , aby przechowywać zmienne tymczasowe i dodać do wywołania **RunQueryAsync** wartość z lewej strony.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. Metoda **RunQueryAsync** wprowadzona w poprzedniej lekcji wymaga modyfikacji w celu rozpoznania błędu składni. Używamy pól **Skip**, **size** i **IncludeTotalCount** klasy [**SearchOptions zawierają**](/dotnet/api/azure.search.documents.searchoptions) , aby żądać tylko jednej strony wyników, zaczynając od ustawienia **pomijania** . Musimy również obliczyć zmienne stronicowania dla tego widoku. Zastąp całą metodę poniższym kodem.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        return View("Index", model);
    }
    ```

1. Na koniec wprowadź małą zmianę w widoku. Zmienna **resultList. results. TotalCount** będzie teraz zawierać liczbę wyników zwróconych w jednej stronie (3 w naszym przykładzie), a nie łączną liczbę. Ze względu na to, że ustawimy **IncludeTotalCount** na true, zmienna **resultList. TotalCount** zawiera teraz łączną liczbę wyników. W tym celu zlokalizuj miejsce, w którym jest wyświetlana liczba wyników, a następnie zmień go na następujący kod.

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > Podczas ustawiania **IncludeTotalCount** na wartość true występuje drobna wydajność, ponieważ ta suma musi być obliczana przez wyszukiwanie poznawcze platformy Azure. W przypadku złożonych zestawów danych występuje ostrzeżenie, że zwracana wartość jest _przybliżeniem_. Ponieważ korpus wyszukiwania hotelowego jest mały, będzie to dokładne.

### <a name="compile-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wybierz pozycję **Uruchom bez debugowania** (lub naciśnij klawisz F5).

1. Wyszukaj ciąg, który zwraca wiele wyników (na przykład "Wi-Fi"). Czy można w sposób starannie wykonać wyniki?

    ![Numerowane stronicowanie za poorednictwem wyników "Pool"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. Spróbuj kliknąć prawym przyciskiem myszy, a później pozycję numery stron z lewej strony. Czy numery stron są odpowiednio dostosowywane w celu wyśrodkowania strony?

1. Czy opcje "First" i "Last" są przydatne? W przypadku niektórych komercyjnych aparatów wyszukiwania są używane te opcje, a inne nie.

1. Przejdź do ostatniej strony wyników. Ostatnia strona jest jedyną stroną, która może zawierać mniej niż **ResultsPerPage** wyników.

    ![Badanie ostatniej strony "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. Wpisz "miasto", a następnie kliknij przycisk Wyszukaj. Jeśli wyniki są mniejsze niż jedna strona, nie są wyświetlane żadne opcje stronicowania.

    ![Wyszukiwanie "miejscowości"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Zapisz ten projekt i przejdź do następnej sekcji, aby zapoznać się z alternatywną formą stronicowania.

## <a name="extend-your-app-with-infinite-scrolling"></a>Zwiększanie możliwości aplikacji dzięki nieskończonemu przewijaniu

Nieskończone przewijanie jest wyzwalane, gdy użytkownik przewija pionowy pasek przewijania do ostatniego wyświetlanego wyniku. W tym zdarzeniu zostanie wykonane wywołanie usługi Search dla następnej strony wyników. Jeśli nie ma więcej wyników, nic nie zostanie zwrócone i pionowy pasek przewijania nie zmieni się. Jeśli jest więcej wyników, są one dołączane do bieżącej strony, a pasek przewijania zmieni się, aby pokazać, że więcej wyników jest dostępnych.

Ważną kwestią jest to, że bieżąca strona nie jest zastępowana, ale natomiast rozszerzona w celu wyświetlenia dodatkowych wyników. Użytkownik może zawsze przewijać kopię zapasową do pierwszych wyników wyszukiwania.

Aby zaimplementować nieskończoność przewijania, Zacznijmy od projektu przed dodaniem elementów przewijania numeru strony. W witrynie GitHub jest to rozwiązanie [FirstAzureSearchApp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) .

### <a name="add-paging-fields-to-the-model"></a>Dodawanie pól stronicowania do modelu

1. Najpierw Dodaj właściwość **stronicowania** do klasy **SearchData** (w pliku modelu SearchData. cs).

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    Ta zmienna jest ciągiem zawierającym wartość "Next", jeśli Następna strona wyników powinna zostać wysłana lub ma wartość null dla pierwszej strony wyszukiwania.

1. W tym samym pliku i w przestrzeni nazw Dodaj globalną klasę zmiennej z jedną właściwością. W MVC zmienne globalne są deklarowane we własnej klasie statycznej. **ResultsPerPage** ustawia liczbę wyników na stronę. 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Dodaj pionowy pasek przewijania do widoku

1. Zlokalizuj sekcję pliku index. cshtml, który wyświetla wyniki (zaczyna się od **@if (model! = null)**).

1. Zastąp sekcję poniższym kodem. Nowa sekcja **&lt; DIV &gt;** znajduje się wokół obszaru, który powinien być przewijalny i dodaje zarówno atrybut **overflow-y** , jak i wywołanie funkcji **OnScroll** o nazwie "scrolled ()", tak jak to zrobić.

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. Bezpośrednio pod pętlą po &lt; &gt; tagu/DIV Dodaj funkcję **przewijania** .

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    Instrukcja **if** w powyższym skrypcie testuje, czy użytkownik przewinie się w dolnej części pionowego paska przewijania. Jeśli mają, wywołanie do kontrolera **macierzystego** jest wykonywane do akcji o nazwie **NextAsync**. Kontroler nie potrzebuje innych informacji, zwróci następną stronę danych. Te dane są następnie formatowane przy użyciu identycznych stylów HTML jako oryginalnej strony. Jeśli żadne wyniki nie zostaną zwrócone, nic nie jest dołączane i pozostaną niezmienione.

### <a name="handle-the-next-action"></a>Obsługuj następną akcję

Istnieją tylko trzy akcje, które muszą zostać wysłane do kontrolera: pierwsze uruchomienie aplikacji, która wywołuje **indeks ()**, pierwsze wyszukiwanie przez użytkownika, które wywołuje **indeks (model)**, a następnie kolejne wywołania w celu uzyskania większej liczby wyników za pośrednictwem usługi **Next (model)**.

1. Otwórz plik kontrolera głównego i Usuń metodę **RunQueryAsync** z oryginalnego samouczka.

1. Zamień akcję **index (model)** na następujący kod. Teraz obsługuje pole **stronicowania** , gdy ma wartość null, lub ma wartość "Next" i obsługuje wywołanie do wyszukiwanie poznawcze platformy Azure.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Podobnie jak w przypadku numerowanej metody stronicowania, użyjemy ustawień wyszukiwania **Pomiń** i **rozmiar** , aby zażądać tylko potrzebnych danych.

1. Dodaj akcję **NextAsync** do kontrolera macierzystego. Zwróć uwagę, jak zwraca listę, każdy Hotel dodaje dwa elementy do listy: nazwę hotelu i opis hotelu. Ten format jest ustawiony tak, aby odpowiadał użyciu **przewijanej** funkcji w widoku.

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Jeśli wystąpi błąd składniowy w **&lt; ciągu &gt; listy**, Dodaj następującą dyrektywę **using** do nagłówka pliku kontrolera.

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilowanie i uruchamianie projektu

Teraz wybierz pozycję **Uruchom bez debugowania** (lub naciśnij klawisz F5).

1. Wprowadź termin, który będzie zawierać wiele wyników (takich jak "Pula"), a następnie przetestuj pionowy pasek przewijania. Czy wyzwala nową stronę wyników?

    ![Nieskończone przewijanie w wyniku "puli"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Aby mieć pewność, że na pierwszej stronie pojawi się pasek przewijania, pierwsza strona wyników musi nieco przekroczyć wysokość obszaru, w którym są wyświetlane. W naszym przykładzie **. BOX1** ma wysokość 30 pikseli, **. box2** ma wysokość 100 pikseli _i_ dolny margines 24 pikseli. Dlatego każdy wpis używa 154 pikseli. Trzy wpisy zajmieją 3 x 154 = 462 pikseli. Aby mieć pewność, że zostanie wyświetlony pionowy pasek przewijania, Wysokość do obszaru wyświetlania musi być mniejsza niż 462 pikseli, nawet 461. Ten problem występuje tylko na pierwszej stronie, gdy pasek przewijania jest widoczny. Wierszem do zaktualizowania jest: **&lt; DIV ID = "myDiv" Style = "width: 800px; Height: 450px; overflow-y: Scroll;" OnScroll = "scrolled () &gt; "**.

1. Przewiń w dół do końca wyników. Zwróć uwagę na to, jak wszystkie informacje są teraz na stronie jednego widoku. Można przewijać wszystko z powrotem do góry bez wyzwalania wywołań serwera.

Bardziej zaawansowane, nieskończone systemy przewijania mogą korzystać z kółka myszy lub podobnego innego mechanizmu, aby wyzwolić ładowanie nowej strony wyników. Nie zajmiemy jeszcze Nieskończonego przewijania w tych samouczkach, ale ma on pewien panel, ponieważ pozwala to uniknąć dodatkowych kliknięć myszą i można dokładniej zbadać inne opcje.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Numerowane stronicowanie jest przydatne w przypadku wyszukiwań, w których kolejność wyników jest nieco arbitralna, co oznacza, że może to być coś istotnego dla użytkowników na dalszych stronach.
* Przewijanie nieskończone jest przydatne, gdy kolejność wyników jest szczególnie ważna. Na przykład jeśli wyniki są uporządkowane na odległość od środka docelowego miasta.
* Numerowane stronicowanie umożliwia lepsze nawigowanie. Na przykład użytkownik może pamiętać, że interesujący wynik znajdował się na stronie 6, podczas gdy nie ma takiego prostego odwołania.
* Nieskończone przewijanie ma łatwy odatrakcyjność, przewijanie w górę i w dół bez numerów stron do kliknięcia.
* Kluczową funkcją Nieskończonego przewijania jest to, że wyniki są dołączane do istniejącej strony, a nie do zastępowania tej strony, co jest wydajne.
* Magazyn tymczasowy utrzymuje tylko jedno wywołanie i musi zostać zresetowany w celu przechowania dodatkowych wywołań.

## <a name="next-steps"></a>Następne kroki

Stronicowanie ma podstawowe znaczenie dla środowiska wyszukiwania. Przy użyciu dobrze stronicowania, następnym krokiem jest dalsze ulepszanie środowiska użytkownika przez dodanie wyszukiwania z wyprzedzeniem typu.

> [!div class="nextstepaction"]
> [Samouczek języka C#: Dodawanie autouzupełniania i sugestii — Azure Wyszukiwanie poznawcze](tutorial-csharp-type-ahead-and-suggestions.md)