---
title: Samouczek języka C# dotyczący dzielenia na strony wyników wyszukiwania
titleSuffix: Azure Cognitive Search
description: Dodawanie przycisków podziału na strony i nawigacji do wyników wyszukiwania, kompilowanie na istniejącym projekcie hoteli w celu dodania przycisków pierwszy, następny, poprzedni, ostatni i numerowany. Drugi system stronicowania używa Nieskończonego przewijania wyzwalanego przez przeniesienie pionowego paska przewijania do jego dolnego limitu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: edae1edc076c99c025ff70c9b2493bc15e44102b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280747"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Samouczek: Dodawanie stronicowania do wyników wyszukiwania przy użyciu zestawu .NET SDK

Dowiedz się, jak zaimplementować dwa różne systemy stronicowania, pierwsze na podstawie numerów stron i drugi przy nieskończonym przewijaniu. Oba systemy stronicowania są szeroko używane i wybór jednego z nich zależy od środowiska użytkownika, które chcesz uzyskać w wyniku. W tym samouczku przedstawiono systemy stronicowania w projekcie utworzonym w [samouczku języka C#: Tworzenie pierwszej aplikacji — samouczek wyszukiwanie poznawcze platformy Azure](tutorial-csharp-create-first-app.md) .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Poszerzanie aplikacji za pomocą numerowanego stronicowania
> * Zwiększanie możliwości aplikacji dzięki nieskończonemu przewijaniu

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[Samouczek języka C#: Tworzenie pierwszej aplikacji — Azure wyszukiwanie poznawcze](tutorial-csharp-create-first-app.md) Project w górę i uruchomiona. Ten projekt może być własną wersją lub być instalowany z serwisu GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Poszerzanie aplikacji za pomocą numerowanego stronicowania

Numerowane stronicowanie to system stronicowania wyboru głównych aparatów wyszukiwania internetowego i większość innych witryn sieci Web wyszukiwania. Stronicowanie numerowane zwykle zawiera opcję "Next" i "Previous" oprócz zakresu rzeczywistych numerów stron. Dostępna jest również opcja "Pierwsza Strona" i "Ostatnia strona". Te opcje dają kontrolę nad przechodzeniem między wynikami opartymi na stronach.

Dodamy system zawierający pierwszy, poprzedni, następny i ostatni z nich, a także numery stron, które nie zaczynają się od 1, ale zamiast tego otaczają bieżącą stronę (na przykład, jeśli użytkownik przegląda stronę 10, prawdopodobnie numery stron 8, 9, 10, 11 i 12).

System będzie wystarczająco elastyczny, aby można było ustawić liczbę widocznych numerów stron w zmiennej globalnej.

System będzie traktować przyciski o największej i największej liczbie stron jako specjalne, co oznacza, że będą wyzwalać zmianę zakresu wyświetlanych numerów stron. Na przykład jeśli są wyświetlane numery stron 8, 9, 10, 11 i 12, a użytkownik kliknie 8, zakres numerów stron zostanie zmieniony na 6, 7, 8, 9 i 10. Podobnie jak w przypadku wybrania 12.

### <a name="add-paging-fields-to-the-model"></a>Dodawanie pól stronicowania do modelu

Ma otwarte rozwiązanie podstawowego strony wyszukiwania.

1. Otwórz plik modelu SearchData.cs.

2. Najpierw Dodaj pewne zmienne globalne. W MVC zmienne globalne są deklarowane we własnej klasie statycznej. **ResultsPerPage** ustawia liczbę wyników na stronę. **MaxPageRange** określa liczbę widocznych numerów stron w widoku. **PageRangeDelta** określa, ile stron z lewej lub prawej strony ma być przenoszonych, gdy zaznaczony jest numer strony od lewej lub do prawej. Zwykle ta druga liczba jest około połowy **MaxPageRange**. Dodaj następujący kod do przestrzeni nazw.

    ```cs
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

3. Dodaj właściwości stronicowania do klasy **SearchData** , powiedzmy po właściwości **tekstprzeszukiwany** .

    ```cs
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

    ```cs
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
                        @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
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
                            @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
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
                        @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
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

2. Dodaj klasy stronicowania do listy stylów HTML w pliku hoteli. css. Klasa **pageSelected** umożliwia zidentyfikowanie strony, która jest aktualnie oglądana przez użytkownika (przez włączenie pogrubienia liczby) na liście numerów stron.

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

1. Otwórz plik HomeController.cs i Dodaj akcję **strony** . Ta akcja odpowiada dowolnej wybranej opcji strony.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
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
    > Wywołania **TempData** przechowują wartość ( **obiekt**) w magazynie tymczasowym, chociaż ten magazyn utrzymuje _tylko_ jedno wywołanie. Jeśli przechowujemy coś w danych tymczasowych, będzie on dostępny dla następnego wywołania do akcji kontrolera, ale w przypadku, gdy zostanie ono utracone przez wywołanie po tym! Ze względu na ten krótki cykl życia przechowujemy w tymczasowym magazynie wszystkie właściwości tekstu i stronicowania, a każde wywołanie **strony**.

2. Akcja **indeks (model)** musi zostać zaktualizowana w celu przechowywania zmiennych tymczasowych i dodać do wywołania **RunQueryAsync** wartość z lewej strony.

    ```cs
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

3. Metoda **RunQueryAsync** wymaga znaczącej aktualizacji. Używamy pól **Skip**, **Top**i **IncludeTotalResultCount** klasy **SearchParameters** , aby żądać tylko jednej strony wyników, zaczynając od ustawienia **pomijania** . Musimy również obliczyć zmienne stronicowania dla tego widoku. Zastąp całą metodę poniższym kodem.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
                   SearchMode = SearchMode.All,

                   // Skip past results that have already been returned.
                   Skip = page * GlobalVariables.ResultsPerPage,

                   // Take only the next page worth of results.
                   Top = GlobalVariables.ResultsPerPage,

                   // Include the total number of results.
                   IncludeTotalResultCount = true,
               };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

4. Na koniec musimy wprowadzić małą zmianę w widoku. Zmienna **resultsList. results. Count** będzie teraz zawierać liczbę wyników zwróconych w jednej stronie (3 w naszym przykładzie), a nie łączną liczbę. Ze względu na to, że ustawimy **IncludeTotalResultCount** na true, zmienna **resultsList. Count** zawiera teraz łączną liczbę wyników. W tym celu zlokalizuj miejsce, w którym jest wyświetlana liczba wyników, a następnie zmień go na następujący kod.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Wystąpił problem z wydajnością, ale nie jest to zwykle wiele z nich, ustawiając **IncludeTotalResultCount** na true, ponieważ suma ta musi być obliczana przez wyszukiwanie poznawcze platformy Azure. W przypadku złożonych zestawów danych występuje ostrzeżenie, że zwracana wartość jest _przybliżeniem_. W przypadku naszych danych hotelowych będzie to dokładne.

### <a name="compile-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wybierz pozycję **Uruchom bez debugowania** (lub naciśnij klawisz F5).

1. Wyszukaj tekst, który będzie zawierać wiele wyników (na przykład "Wi-Fi"). Czy można w sposób starannie wykonać wyniki?

    ![Numerowane stronicowanie za poorednictwem wyników "Pool"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Spróbuj kliknąć prawym przyciskiem myszy, a później pozycję numery stron z lewej strony. Czy numery stron są odpowiednio dostosowywane w celu wyśrodkowania strony?

3. Czy opcje "First" i "Last" są przydatne? Niektóre popularne wyszukiwania w sieci Web używają tych opcji, a inne nie.

4. Przejdź do ostatniej strony wyników. Ostatnia strona jest jedyną stroną, która może zawierać mniej niż **ResultsPerPage** wyników.

    ![Badanie ostatniej strony "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Wpisz "miasto", a następnie kliknij przycisk Wyszukaj. Nie są wyświetlane żadne opcje stronicowania, jeśli istnieje mniej niż jedna strona wyników.

    ![Wyszukiwanie "miejscowości"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Teraz Zapisz ten projekt i Wypróbujmy alternatywę dla tej formy stronicowania.

## <a name="extend-your-app-with-infinite-scrolling"></a>Zwiększanie możliwości aplikacji dzięki nieskończonemu przewijaniu

Nieskończone przewijanie jest wyzwalane, gdy użytkownik przewija pionowy pasek przewijania do ostatniego wyświetlanego wyniku. W tym zdarzeniu wywołanie do serwera zostanie wykonane na następnej stronie wyników. Jeśli nie ma więcej wyników, nic nie zostanie zwrócone i pionowy pasek przewijania nie zmieni się. Jeśli jest więcej wyników, są one dołączane do bieżącej strony, a pasek przewijania zmieni się, aby pokazać, że więcej wyników jest dostępnych.

Ważnym punktem jest to, że wyświetlana strona nie jest zastępowana, ale dołączona do nowych wyników. Użytkownik może zawsze przewijać kopię zapasową do pierwszych wyników wyszukiwania.

Aby zaimplementować nieskończoność przewijania, Zacznijmy od projektu przed dodaniem elementów przewijania numeru strony. W razie potrzeby wykonaj kolejną kopię strony wyszukiwania podstawowego z witryny GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Dodawanie pól stronicowania do modelu

1. Najpierw Dodaj właściwość **stronicowania** do klasy **SearchData** (w pliku modelu SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Ta zmienna jest ciągiem zawierającym wartość "Next", jeśli Następna strona wyników powinna zostać wysłana lub ma wartość null dla pierwszej strony wyszukiwania.

2. W tym samym pliku i w przestrzeni nazw Dodaj globalną klasę zmiennej z jedną właściwością. W MVC zmienne globalne są deklarowane we własnej klasie statycznej. **ResultsPerPage** ustawia liczbę wyników na stronę. 

    ```cs
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

1. Zlokalizuj sekcję pliku index. cshtml, który wyświetla wyniki (zaczyna się od ** @if (model! = null)**).

2. Zastąp sekcję poniższym kodem. Nowa sekcja ** &lt; DIV &gt; ** znajduje się wokół obszaru, który powinien być przewijalny i dodaje zarówno atrybut **overflow-y** , jak i wywołanie funkcji **OnScroll** o nazwie "scrolled ()", tak jak to zrobić.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Bezpośrednio pod pętlą po &lt; &gt; tagu/DIV Dodaj funkcję **przewijania** .

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
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

    Instrukcja **if** w skrypcie powyżej testuje, aby zobaczyć, czy użytkownik przewinie się w dolnej części pionowego paska przewijania. Jeśli ma, wywołanie do kontrolera **głównego** jest wykonywane w ramach akcji o nazwie Next ( **dalej**). Kontroler nie potrzebuje innych informacji, zwróci następną stronę danych. Te dane są następnie formatowane przy użyciu identycznych stylów HTML jako oryginalnej strony. Jeśli żadne wyniki nie zostaną zwrócone, nic nie jest dołączane i pozostaną niezmienione.

### <a name="handle-the-next-action"></a>Obsługuj następną akcję

Istnieją tylko trzy akcje, które muszą zostać wysłane do kontrolera: pierwsze uruchomienie aplikacji, która wywołuje **indeks ()**, pierwsze wyszukiwanie przez użytkownika, które wywołuje **indeks (model)**, a następnie kolejne wywołania w celu uzyskania większej liczby wyników za pośrednictwem usługi **Next (model)**.

1. Otwórz plik kontrolera głównego i Usuń metodę **RunQueryAsync** z oryginalnego samouczka.

2. Zamień akcję **index (model)** na następujący kod. Teraz obsługuje pole **stronicowania** , gdy ma wartość null, lub ma wartość "Next" i obsługuje wywołanie do wyszukiwanie poznawcze platformy Azure.

    ```cs
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
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

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

    Podobnie jak w przypadku ponumerowanej metody stronicowania, użyjemy parametrów **Pomiń** i **najpopularniejsze** wyszukiwanie, aby zażądać tylko danych, które są potrzebne.

3. Dodaj **następną** akcję do kontrolera macierzystego. Zwróć uwagę, jak zwraca listę, każdy Hotel dodaje dwa elementy do listy: nazwę hotelu i opis hotelu. Ten format jest ustawiony tak, aby odpowiadał użyciu **przewijanej** funkcji w widoku.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Jeśli otrzymujesz błąd składniowy w ** &lt; ciągu &gt; listy**, Dodaj następującą dyrektywę **using** do nagłówka pliku kontrolera.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilowanie i uruchamianie projektu

Teraz wybierz pozycję **Uruchom bez debugowania** (lub naciśnij klawisz F5).

1. Wprowadź termin, który będzie zawierać wiele wyników (takich jak "Pula"), a następnie przetestuj pionowy pasek przewijania. Czy wyzwala nową stronę wyników?

    ![Nieskończone przewijanie w wyniku "puli"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Aby mieć pewność, że na pierwszej stronie pojawi się pasek przewijania, pierwsza strona wyników musi nieco przekroczyć wysokość obszaru, w którym są wyświetlane. W naszym przykładzie **. BOX1** ma wysokość 30 pikseli, **. box2** ma wysokość 100 pikseli _i_ dolny margines 24 pikseli. Dlatego każdy wpis używa 154 pikseli. Trzy wpisy zajmieją 3 x 154 = 462 pikseli. Aby mieć pewność, że zostanie wyświetlony pionowy pasek przewijania, Wysokość do obszaru wyświetlania musi być mniejsza niż 462 pikseli, nawet 461. Ten problem występuje tylko na pierwszej stronie, gdy pasek przewijania jest widoczny. Wierszem do zaktualizowania jest: ** &lt; DIV ID = "myDiv" Style = "width: 800px; Height: 450px; overflow-y: Scroll;" OnScroll = "scrolled () &gt; "**.

2. Przewiń w dół do końca wyników. Zwróć uwagę na to, jak wszystkie informacje są teraz na stronie jednego widoku. Można przewijać wszystko z powrotem do góry bez wyzwalania wywołań serwera.

Bardziej zaawansowane, nieskończone systemy przewijania mogą korzystać z kółka myszy lub podobnego innego mechanizmu, aby wyzwolić ładowanie nowej strony wyników. Nie zajmiemy jeszcze Nieskończonego przewijania w tych samouczkach, ale ma on pewien panel, ponieważ pozwala to uniknąć dodatkowych kliknięć myszą i warto dokładniej zbadać inne opcje.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Numerowane stronicowanie jest dobrym sposobem wyszukiwania, w którym kolejność wyników jest nieco arbitralna, co oznacza, że może to być przydatne dla użytkowników na dalszych stronach.
* Nieskończone przewijanie jest dobre, gdy kolejność wyników jest szczególnie ważna. Na przykład jeśli wyniki są uporządkowane na odległość od środka docelowego miasta.
* Numerowane stronicowanie pozwala uzyskać lepszą nawigację. Na przykład użytkownik może pamiętać, że interesujący wynik znajdował się na stronie 6, podczas gdy nie ma takiego prostego odwołania.
* Nieskończone przewijanie ma łatwy odatrakcyjność, przewijanie w górę i w dół bez Fussy numerów stron do kliknięcia.
* Kluczową funkcją Nieskończonego przewijania jest to, że wyniki są dołączane do istniejącej strony, a nie do zastępowania tej strony, co jest wydajne.
* Magazyn tymczasowy utrzymuje tylko jedno wywołanie i musi zostać zresetowany w celu przechowania dodatkowych wywołań.


## <a name="next-steps"></a>Następne kroki

Stronicowanie jest podstawą wyszukiwania w Internecie. Przy użyciu dobrze stronicowania, następnym krokiem jest dalsze ulepszanie środowiska użytkownika przez dodanie wyszukiwania typu z wyprzedzeniem.

> [!div class="nextstepaction"]
> [Samouczek języka C#: Dodawanie autouzupełniania i sugestii — Azure Wyszukiwanie poznawcze](tutorial-csharp-type-ahead-and-suggestions.md)
