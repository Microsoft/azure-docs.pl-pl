---
title: Samouczek języka C# dotyczący używania aspektów do nawigacji
titleSuffix: Azure Cognitive Search
description: Kontynuuj od "wyników stronicowania", aby dodać nawigację aspektową. Dowiedz się, jak można użyć aspektów, aby łatwo zawęzić wyszukiwanie.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789806"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Samouczek: Dodawanie nawigacji aspektowej przy użyciu zestawu .NET SDK

Zestawy reguł umożliwiają samodzielne nawigowanie przez udostępnienie zestawu linków do filtrowania wyników. W tym samouczku struktura nawigacji aspektów zostanie umieszczona w lewej części strony, z etykietami i tekstami, które można kliknąć, aby przyciąć wyniki.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Ustaw właściwości modelu jako _Isuderzający_
> * Dodawanie nawigacji aspektu do aplikacji

## <a name="overview"></a>Omówienie

Zestawy reguł są oparte na polach w indeksie wyszukiwania. Żądanie zapytania, które zawiera element facet = [String], zawiera pole, według którego ma zostać Faseta. Często należy uwzględnić wiele aspektów, takich jak każdy z `&facet=category&facet=amenities` nich oddzielony znakiem handlowego "i" (&). Implementacja struktury nawigacji aspektowej wymaga określenia obu aspektów i filtrów. Filtr jest używany w zdarzeniu kliknięcia, aby zawęzić wyniki. Na przykład kliknięcie pozycji "budżet" filtruje wyniki na podstawie tych kryteriów.

Ten samouczek rozszerza projekt stronicowania utworzony w samouczku [Dodawanie stronicowania do wyników wyszukiwania](tutorial-csharp-paging.md) .

Gotowa wersja kodu w tym samouczku można znaleźć w następującym projekcie:

* [4 — Dodawanie aspektów nawigacji (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Wymagania wstępne

* [2a — rozwiązanie dodawania stronicowania (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) . Ten projekt może być własną wersją utworzoną na podstawie poprzedniego samouczka lub kopii z usługi GitHub.

Ten samouczek został zaktualizowany do korzystania z pakietu [Azure.Search.Documents (wersja 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . Aby uzyskać wcześniejszą wersję zestawu .NET SDK, zobacz [przykład kodu Microsoft. Azure. Search (wersja 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="set-model-properties-as-isfacetable"></a>Ustaw właściwości modelu jako isuderzający

Aby właściwość modelu znajdowała się w wyszukiwaniu aspektów, należy ją oznaczyć przy użyciu właściwości **iskroju**.

1. Zapoznaj się z klasą **hotelu** . **Kategoria** i **Tagi**, na **przykład, są oznaczone jako**, ale **hotelname** i **Description** nie są. 

    ```cs
    public partial class Hotel
    {
        [SimpleField(IsFilterable = true, IsKey = true)]
        public string HotelId { get; set; }

        [SearchableField(IsSortable = true)]
        public string HotelName { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public string Category { get; set; }

        [SearchableField(IsFilterable = true, IsFacetable = true)]
        public string[] Tags { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public bool? ParkingIncluded { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true)]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
    ```

1. Nie zmienimy żadnych tagów w ramach tego samouczka, dlatego Zamknij plik hotel.cs bez zmian.

    > [!Note]
    > Wyszukiwanie aspektów spowoduje zgłoszenie błędu, jeśli pole wymagane w wyszukiwaniu nie zostanie odpowiednio oznakowane.

## <a name="add-facet-navigation-to-your-app"></a>Dodawanie nawigacji aspektu do aplikacji

Na potrzeby tego przykładu chcemy umożliwić użytkownikowi wybranie jednej kategorii hotelu lub jednej z części z list linków wyświetlanych na lewo od wyników. Użytkownik rozpoczyna od wprowadzenia tekstu wyszukiwania, a następnie stopniowo zawęża wyniki wyszukiwania, wybierając kategorię lub teren.

Jest to zadanie kontrolera do przekazania list aspektów do widoku. Aby zachować wybory użytkownika w miarę postępu wyszukiwania, używamy magazynu tymczasowego jako mechanizmu zachowywania stanu.

![Używanie nawigacji aspektu do zawężenia wyszukiwania "Pool"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Dodawanie ciągów filtru do modelu SearchData

1. Otwórz plik SearchData.cs i Dodaj właściwości ciągu do klasy **SearchData** , aby przechowywać ciągi filtru zestawu reguł.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Dodaj metodę akcji zestawu reguł

Kontroler główny wymaga jednej nowej akcji, **aspektu** i aktualizacji istniejącego **indeksu** i akcji **strony** oraz do metody **RunQueryAsync** .

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Zastąp metodę akcji **index (SearchData model)** .

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
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Zastąp metodę akcji **PageAsync (model SearchData)** .

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
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

1. Dodaj metodę akcji **FacetAsync (model SearchData)** , która ma zostać aktywowana, gdy użytkownik kliknie link aspekt. Model będzie zawierać kategorię lub filtr wyszukiwania Stanów. Dodaj go po akcji **PageAsync** .

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
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
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
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

1. Zastąp metodę **RunQueryAsync** poniższym kodem. Przede wszystkim Pobiera ciąg filtru kategorii i ciąg filtru rzeczy i ustawia parametr **filtru** **SearchOptions zawierają**.

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

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

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

    Zauważ, że właściwości **Kategoria** i **Tagi** są dodawane do listy **wybranych** elementów do zwrócenia. To dodanie nie jest wymaganiem, aby Nawigacja aspektów działała, ale te informacje są używane do sprawdzenia, czy filtry działają prawidłowo.

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

1. W przypadku widoku Zorganizuj dane wyjściowe w tabeli, aby starannie wyrównać listy aspektów po lewej stronie i wyniki po prawej stronie. Otwórz plik index. cshtml. Zastąp całą zawartość &lt; tagów treści HTML &gt; poniższym kodem.

    ```html
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
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
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
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
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
                                        <td class="tdPage">
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

                                    <td class="tdPage">
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

                                    <td class="tdPage">
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

1. Kliknij kategorię możliwości **i spa** . Sprawdź, czy wszystkie wyniki są w tej kategorii.

    ![Zawężanie wyszukiwania do "możliwości i spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Kliknij pozycję rekreacyjny **śniadanie** . Upewnij się, że wszystkie wyniki są nadal w kategorii "kurort i spa" z wybranym terenem.

    ![Zawężanie wyszukiwania do "śniadania kontynentalnego"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Spróbuj wybrać dowolną inną kategorię, a następnie jedną z nich i wyświetlić wąskie wyniki. Następnie Wypróbuj inne sposoby, jedną z nich, a następnie jedną kategorię. Wyślij puste wyszukiwanie w celu zresetowania strony.

    >[!Note]
    > Po dokonaniu wyboru jednego zaznaczenia na liście aspektów (takiej jak kategoria) zastąpi on poprzedni wybór na liście kategorii.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Oznacza to, że należy oznaczyć każde pole kroju z właściwością **Isuderzającą** do włączenia w nawigacji aspektu.
* Zestawy reguł są łączone z filtrami w celu zmniejszenia wyników.
* Zestawy reguł są skumulowane, a każdy z nich jest tworzony na poprzednim, aby bardziej zawęzić wyniki.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku będziemy przeglądać wyniki sortowania. Do tego momentu wyniki są uporządkowane po prostu w kolejności, w jakiej znajdują się w bazie danych.

> [!div class="nextstepaction"]
> [Samouczek języka C#: kolejność wyników — Wyszukiwanie poznawcze platformy Azure](tutorial-csharp-orders.md)
