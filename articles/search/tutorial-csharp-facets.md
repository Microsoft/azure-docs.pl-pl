---
title: Samouczek języka C# dotyczący używania aspektów do nawigacji
titleSuffix: Azure Cognitive Search
description: W tym samouczku przedstawiono projekt "wyniki wyszukiwania na stronie spisu — Wyszukiwanie poznawcze platformy Azure", aby dodać nawigację aspektu. Dowiedz się, jak można użyć aspektów, aby łatwo zawęzić wyszukiwanie.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77121567"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>Samouczek języka C#: używanie aspektów w celu ułatwienia nawigacji — Wyszukiwanie poznawcze platformy Azure

Zestawy reguł służą do ułatwienia nawigacji, dostarczając użytkownikowi zestaw linków umożliwiających skoncentrowanie się na wyszukiwaniu. Aspektami są atrybuty danych (takie jak kategoria lub określona funkcja w hotelu w naszych przykładowych danych).

Ten samouczek kompiluje się do projektu stronicowania utworzonego w [samouczku języka C#: wyniki wyszukiwania stronicowania — Samouczek platformy Azure wyszukiwanie poznawcze](tutorial-csharp-paging.md) .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Ustaw właściwości modelu jako _Isuderzający_
> * Dodawanie nawigacji aspektu do aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[Samouczek języka C#: Przeszukaj wyniki wyszukiwania stronicowania — Azure wyszukiwanie poznawcze](tutorial-csharp-paging.md) Project up i uruchomiona. Ten projekt może być własną wersją lub być instalowany z serwisu GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Ustaw właściwości modelu jako isuderzający

Aby właściwość modelu znajdowała się w wyszukiwaniu aspektów, należy ją oznaczyć przy użyciu właściwości **iskroju**.

1. Zapoznaj się z klasą **hotelu** . **Kategoria** i **Tagi**, na **przykład, są oznaczone jako**, ale **hotelname** i **Description** nie są. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. Nie zmienimy żadnych tagów w ramach tego samouczka, dlatego Zamknij plik hotel.cs bez zmian.

    > [!Note]
    > Wyszukiwanie aspektów spowoduje zgłoszenie błędu, jeśli pole wymagane w wyszukiwaniu nie zostanie odpowiednio oznakowane.


## <a name="add-facet-navigation-to-your-app"></a>Dodawanie nawigacji aspektu do aplikacji

Na potrzeby tego przykładu chcemy umożliwić użytkownikowi wybranie jednej kategorii hotelu lub jednej z części z list linków wyświetlanych na lewo od wyników. Użytkownik rozpoczyna od wprowadzenia tekstu wyszukiwania, a następnie może zawęzić wyniki wyszukiwania, wybierając kategorię i może zawęzić wyniki w dalszej kolejności, wybierając obszar terenowy lub klikając najpierw (zamówienie nie jest ważne).

Potrzebujemy kontrolera do przekazania list aspektów do widoku. Musimy zachować wybory użytkownika jako postęp wyszukiwania, a następnie używać magazynu tymczasowego jako mechanizmu zachowywania danych.

![Używanie nawigacji aspektu do zawężenia wyszukiwania "Pool"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Dodawanie ciągów filtru do modelu SearchData

1. Otwórz plik SearchData.cs i Dodaj właściwości ciągu do klasy **SearchData** , aby przechowywać ciągi filtru zestawu reguł.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Dodaj metodę akcji zestawu reguł

Kontroler główny wymaga jednej nowej akcji, **aspektu**i aktualizacji istniejącego **indeksu** oraz akcji **strony** , a także aktualizacji metody **RunQueryAsync** .

1. Otwórz plik kontrolera głównego i Dodaj instrukcję **using** , aby włączyć konstrukcję **ciągu&lt;&gt; list** .

    ```cs
    using System.Collections.Generic;
    ```

2. Zastąp metodę akcji **index (SearchData model)** .

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Zastąp metodę akcji **Page (SearchData model)** .

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
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

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Dodaj metodę akcji dla **aspektu (SearchData model)** , która ma zostać aktywowana, gdy użytkownik kliknie link aspekt. Model będzie zawierać filtr wyszukiwania kategorii lub filtr wyszukiwania na potrzeby przeszukiwania. Prawdopodobnie należy dodać go po akcji **strony** .

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Konfigurowanie filtru wyszukiwania

Gdy użytkownik wybierze określony zestaw reguł, na przykład klikają kategorię możliwości **i spa** , a następnie w wynikach zostaną zwrócone tylko Hotele określone jako Ta kategoria. Aby zawęzić wyszukiwanie w ten sposób, musimy skonfigurować _Filtr_.

1. Zastąp metodę **RunQueryAsync** poniższym kodem. Przede wszystkim Pobiera ciąg filtru kategorii i ciąg filtru rzeczy i ustawia parametr **filtru** **SearchParameters**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

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
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Do listy **wybranych** elementów, które mają zostać zwrócone, dodaliśmy właściwości **kategorii** i **znaczników** . To dodanie nie jest wymaganiem, aby Nawigacja aspektów działała, ale używamy tych informacji w celu sprawdzenia, czy filtrowanie odbywa się prawidłowo.

### <a name="add-lists-of-facet-links-to-the-view"></a>Dodawanie list linków aspektów do widoku

Widok będzie wymagał pewnych znaczących zmian. 

1. Zacznij od otwarcia pliku hoteli. CSS (w folderze wwwroot/CSS) i Dodaj następujące klasy.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. W przypadku widoku organizujemy dane wyjściowe w tabeli, aby starannie wyrównać listy aspektów po lewej stronie i wyniki po prawej stronie. Otwórz plik index. cshtml. Zastąp całą zawartość tagów treści &lt;&gt; HTML poniższym kodem.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
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

                                <td class="tdPage">
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
                                    <td class="tdPage">
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

                                <td class="tdPage">
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

                                <td class="tdPage">
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
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Zwróć uwagę na użycie wywołania **HTML. ActionLink** . To wywołanie komunikuje prawidłowe ciągi filtru z kontrolerem, gdy użytkownik kliknie link aspektu. 

### <a name="run-and-test-the-app"></a>Uruchamianie i testowanie aplikacji

Zaletą nawigacji aspektu dla użytkownika jest możliwość zawężenia wyszukiwania za pomocą jednego kliknięcia, które można wyświetlić w następującej kolejności.

1. Uruchom aplikację, wpisz "lotniska" jako tekst wyszukiwania. Sprawdź, czy lista aspektów pojawia się z widoczną z lewej strony. Te zestawy reguł są stosowane do hoteli, które mają "Lotnisko" w swoich danych tekstowych, wraz z liczbą częstotliwości ich występowania.

    ![Zawężanie wyszukiwania "lotniska" przy użyciu nawigacji aspektów](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Kliknij kategorię możliwości **i spa** . Sprawdź, czy wszystkie wyniki są w tej kategorii.

    ![Zawężanie wyszukiwania do "możliwości i spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Kliknij pozycję rekreacyjny **śniadanie** . Upewnij się, że wszystkie wyniki są nadal w kategorii "kurort i spa" z wybranym terenem.

    ![Zawężanie wyszukiwania do "śniadania kontynentalnego"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Spróbuj wybrać dowolną inną kategorię, a następnie jedną z nich i wyświetlić wąskie wyniki. Następnie Wypróbuj inne sposoby, jedną z nich, a następnie jedną kategorię.

    >[!Note]
    > Po dokonaniu wyboru jednego zaznaczenia na liście aspektów (takiej jak kategoria) zastąpi on poprzedni wybór na liście kategorii.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Koniecznie Oznacz każdą **Właściwość jako**isfacet, jeśli ma zostać uwzględniona w nawigacji aspektu.
* Nawigacja aspektów zapewnia użytkownikowi łatwy i intuicyjny sposób zawężania wyszukiwania.
* Nawigacja aspektów najlepiej dzieli się na sekcje (kategorie hotelu, walory hotelowe, zakresy cen, zakresy klasyfikacji itp.), Każda sekcja z odpowiednim nagłówkiem.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku będziemy przeglądać wyniki sortowania. Do tego momentu wyniki są uporządkowane po prostu w kolejności, w jakiej znajdują się w bazie danych.

> [!div class="nextstepaction"]
> [Samouczek języka C#: kolejność wyników — Wyszukiwanie poznawcze platformy Azure](tutorial-csharp-orders.md)
