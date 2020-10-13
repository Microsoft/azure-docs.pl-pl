---
title: Samouczek języka C# do tworzenia pierwszej aplikacji
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć pierwszą aplikację wyszukiwania w języku C# krok po kroku. Samouczek zawiera link do pobierania dla aplikacji działającej w usłudze GitHub oraz kompletny proces tworzenia aplikacji od podstaw.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec98762ac5918437e8fdb8426b54b79b1fb5b222
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939726"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Samouczek: Tworzenie pierwszej aplikacji wyszukiwania przy użyciu zestawu .NET SDK

W tym samouczku pokazano, jak utworzyć aplikację sieci Web, która wykonuje zapytania i zwraca wyniki z indeksu wyszukiwania przy użyciu usługi Azure Wyszukiwanie poznawcze i programu Visual Studio.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska programistycznego
> * Struktury danych modelu
> * Utwórz stronę sieci Web, aby zebrać dane wejściowe zapytania i wyświetlić wyniki
> * Zdefiniuj metodę wyszukiwania
> * Testowanie aplikacji

Dowiesz się również, jak proste jest wywołanie wyszukiwania. Najważniejsze instrukcje w kodzie, który utworzysz, są hermetyzowane w następujących kilku wierszach.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Tylko jedno wywołanie wysyła zapytanie do indeksu i zwraca wyniki.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Wyszukiwanie * Pool *" border="true":::

## <a name="overview"></a>Omówienie

W tym samouczku jest wykorzystywany istniejący, hostowany przykładowy indeks, dzięki czemu można skupić się na tworzeniu strony wyszukiwania, która zbiera ciąg zapytania dla żądania i zwraca wyniki. Indeks zawiera fikcyjne dane hotelu. Gdy masz stronę podstawową, możesz ją ulepszyć w kolejnych lekcjach w celu uwzględnienia stronicowania, aspektów i środowiska z wyprzedzeniem.

Gotowa wersja kodu w tym samouczku można znaleźć w następującym projekcie:

* [1 — podstawowe-strona wyszukiwania (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Ten samouczek został zaktualizowany do korzystania z pakietu Azure.Search.Documents (wersja 11). Aby uzyskać wcześniejszą wersję zestawu .NET SDK, zobacz [przykład kodu Microsoft. Azure. Search (wersja 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="prerequisites"></a>Wymagania wstępne

Ponieważ używasz publicznego indeksu wyszukiwania przykładowego hostowanego przez firmę Microsoft, nie potrzebujesz usługi Search ani konta platformy Azure dla tego samouczka.

* [Program Visual Studio](https://visualstudio.microsoft.com/)

* [Biblioteka kliencka usługi Azure Wyszukiwanie poznawcze (wersja 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Instalowanie i uruchamianie projektu z usługi GitHub

Jeśli chcesz przejść do działającej aplikacji, wykonaj poniższe kroki, aby pobrać i uruchomić gotowy kod.

1. Znajdź przykład w witrynie GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11).

1. W [folderze głównym](https://github.com/Azure-Samples/azure-search-dotnet-samples)wybierz pozycję **kod**, a następnie **Klonuj** lub **Pobierz plik zip** , aby utworzyć prywatną lokalną kopię projektu.

1. Korzystając z programu Visual Studio, przejdź do, a następnie otwórz rozwiązanie dla strony wyszukiwania podstawowego ("1-podstawowe-Search-Page") i wybierz pozycję **Uruchom bez debugowania** (lub naciśnij klawisz F5), aby skompilować i uruchomić program.

1. Jest to indeks hoteli, dlatego wpisz słowa, których można użyć do wyszukiwania hoteli (na przykład "Wi-Fi", "widok", "słupek", "Parkowanie") i przeanalizować wyniki.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Wyszukiwanie * Pool *" border="true":::

Miejmy nadzieję ten projekt będzie działać bezproblemowo i masz uruchomioną aplikację sieci Web. Wiele najważniejszych składników dla bardziej złożonych wyszukiwań znajduje się w tej aplikacji, więc dobrym pomysłem jest przechodzenie przez nią i ponowne utworzenie jej krok po kroku. W poniższych sekcjach opisano te kroki.

## <a name="set-up-a-development-environment"></a>Konfigurowanie środowiska programistycznego

Aby utworzyć projekt od podstaw i w ten sposób wzmocnić koncepcje Wyszukiwanie poznawcze platformy Azure, Zacznij od projektu programu Visual Studio.

1. W programie Visual Studio wybierz pozycję **Nowy**  >  **projekt**, a następnie **ASP.NET Core aplikację sieci Web**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Wyszukiwanie * Pool *" border="true":::

1. Nadaj projektowi nazwę, taką jak "FirstSearchApp", i Ustaw lokalizację. Wybierz przycisk **Utwórz**.

1. Wybierz szablon projektu **aplikacja sieci Web (Model-View-Controller)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Wyszukiwanie * Pool *" border="true":::

1. Zainstaluj bibliotekę kliencką. W obszarze **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **Zarządzaj pakietami NuGet dla rozwiązania...**, wybierz pozycję **Przeglądaj** , a następnie wyszukaj ciąg "azure.search.documents". Zainstaluj **Azure.Search.Documents** (wersja 11 lub nowsza), akceptując umowy licencyjne i zależności.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Wyszukiwanie * Pool *" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Inicjowanie Wyszukiwanie poznawcze platformy Azure

Na potrzeby tego przykładu są używane publicznie dostępne dane hotelowe. Te dane to arbitralna kolekcja nieprawidłowych nazw hotelów i opisów 50, utworzonych wyłącznie na potrzeby udostępniania danych demonstracyjnych. Aby uzyskać dostęp do tych danych, określ nazwę i klucz interfejsu API.

1. Otwórz **appsettings.jsna** i Zastąp wiersze domyślne następującymi nazwami i kluczami. Klucz interfejsu API przedstawiony w tym miejscu nie jest przykładem klucza, ale jest *dokładnie* kluczem potrzebnym do uzyskania dostępu do danych hotelowych. Plik powinien teraz wyglądać następująco.

    ```csharp
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

1. W Eksplorator rozwiązań wybierz plik, a w oknie właściwości Zmień ustawienie **Kopiuj do katalogu wyjściowego** na wartość Kopiuj, **jeśli nowszy**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Wyszukiwanie * Pool *" border="true":::

## <a name="model-data-structures"></a>Struktury danych modelu

Modele (klasy C#) służą do przekazywania danych między klientem (widokiem), serwerem (kontrolerem), a także chmurą platformy Azure przy użyciu architektury MVC (model, widok, kontroler). Zwykle te modele odzwierciedlają strukturę danych, do których uzyskuje się dostęp.

W tym kroku utworzysz strukturę danych indeksu wyszukiwania, a także ciąg wyszukiwania używany w komunikacji z widokiem/kontrolerem. W indeksie hoteli każdy Hotel ma wiele pokojów, a każdy Hotel ma adres wieloczęściowy. Całkowicie pełna reprezentacja hotelu jest hierarchiczną i zagnieżdżoną strukturą danych. Do utworzenia każdego składnika będą potrzebne trzy klasy.

Zestaw grup **hotelowych**, **adresów**i **pokojów** jest nazywany [*typami złożonymi*](search-howto-complex-data-types.md), ważną funkcją platformy Azure wyszukiwanie poznawcze. Typy złożone mogą mieć wiele poziomów na głębokości klas i podklas, a ponadto zapewniają znacznie bardziej złożone struktury danych, które mają być reprezentowane niż przy użyciu *typów prostych* (Klasa zawierająca tylko pierwotne elementy członkowskie).

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję **modele**  >  **Dodaj**  >  **nowy element**.

1. Wybierz**klasę** i nazwij element Hotel.cs. Zastąp całą zawartość Hotel.cs następującym kodem. Zwróć uwagę na **adres** i członków **pokoju** klasy, te pola są klasami, więc potrzebne są również modele.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

1. Powtórz ten sam proces tworzenia modelu dla klasy **Address** , nadając nazwę Address.cs pliku. Zastąp zawartość poniższymi.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. I ponownie wykonaj ten sam proces, aby utworzyć klasę **pokojową** , nadając jej nazwę Room.cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. Ostatni model, który utworzysz w tym samouczku, jest klasą o nazwie **SearchData** i reprezentuje on dane wejściowe użytkownika (**tekstprzeszukiwany**) oraz wynik wyszukiwania (**resultList**). Typ danych wyjściowych jest krytyczny, **SearchResults &lt; hotelu &gt; **, ponieważ ten typ dokładnie pasuje do wyników wyszukiwania i należy przekazać to odwołanie do widoku. Zastąp szablon domyślny poniższym kodem.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Utwórz stronę sieci Web

Szablony projektów są dostarczane z liczbą widoków klienta znajdujących się w folderze **widoki** . Dokładne widoki zależą od używanej wersji programu .NET Core (3,1). W tym samouczku zmodyfikujesz **index. cshtml** , aby uwzględnić elementy strony wyszukiwania.

Usuń zawartość pliku index. cshtml w całości i Skompiluj ponownie plik w poniższych krokach.

1. W tym samouczku w widoku są stosowane dwa małe obrazy: logo platformy Azure i ikona lupy wyszukiwania (azure-logo.png i search.png). Kopiuj obrazy z projektu GitHub do folderu **wwwroot/images** w projekcie.

1. Pierwszy wiersz index. cshtml powinien odwoływać się do modelu używanego do przekazywania danych między klientem (widokiem) i serwerem (kontrolerem), który jest wcześniej utworzonym modelem **SearchData** . Dodaj ten wiersz do pliku index. cshtml.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. Jest to standardowe rozwiązanie służące do wprowadzania tytułu widoku, dlatego kolejne wiersze powinny być następujące:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Po tytule wprowadź odwołanie do arkusza stylów HTML, który zostanie utworzony wkrótce.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. Treść widoku obsługuje dwa przypadki użycia. Najpierw należy podać pustą stronę przy pierwszym użyciu przed wprowadzeniem tekstu wyszukiwania. Po drugie, musi obsługiwać wyniki (oprócz pola tekstowego wyszukiwanie) dla powtarzających się zapytań.

   Aby obsługiwać oba przypadki, należy sprawdzić, czy model podany w widoku ma wartość null. Model o wartości null wskazuje pierwszy przypadek użycia (początkowy uruchomienie aplikacji). Dodaj następujący element do pliku index. cshtml i przeczytaj komentarz.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Dodaj arkusz stylów. W programie Visual Studio, w **pliku** >  **Nowy**  >  **plik**, wybierz pozycję **arkusz stylów** (z wyróżnioną **ogólną** ).

   Zastąp domyślny kod następującym. Ten plik nie będzie bardziej szczegółowy, a style są standardowym kodem HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. Zapisz plik stylesheet jako Hotele. CSS w folderze **wwwroot/CSS** , obok domyślnego pliku. css.

To kończy nasz widok. W tym momencie zarówno modele, jak i widoki zostaną wykonane. Tylko kontroler pozostały do powiązania wszystkiego.

## <a name="define-methods"></a>Definiuj metody

W tym kroku zmodyfikuj zawartość **kontrolera macierzystego**.

1. Otwórz plik HomeController.cs i Zastąp instrukcje **using** poniższymi instrukcjami.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Dodawanie metod indeksu

W aplikacji MVC Metoda **index ()** jest domyślną metodą akcji dla każdego kontrolera. Zostanie otwarta strona indeks HTML. Metoda domyślna, która nie przyjmuje parametrów, jest używana w tym samouczku w przypadku użycia aplikacji do uruchamiania w programie: renderowanie pustej strony wyszukiwania.

W tej sekcji rozszerzono metodę obsługi drugiego przypadku użycia: renderowanie strony, gdy użytkownik wprowadził tekst wyszukiwania. Aby zapewnić obsługę tego przypadku, Metoda index jest rozszerzona w celu przełączenia modelu jako parametru.

1. Dodaj następującą metodę po domyślnym **indeksie ()** .

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Zwróć uwagę na deklarację **Async** metody oraz wywołanie **await** do **RunQueryAsync**. Te słowa kluczowe zadbają o wykonywanie wywołań asynchronicznych i w ten sposób Unikaj blokowania wątków na serwerze.

    Blok **catch** używa modelu błędów, który został utworzony domyślnie.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Zwróć uwagę na obsługę błędów oraz inne widoki i metody domyślne

W zależności od używanej wersji platformy .NET Core domyślnie tworzone są nieco różne zestawy widoków domyślnych. W przypadku platformy .NET Core 3,1 widoki domyślne to index, privacy i Error. Można wyświetlić te domyślne strony podczas uruchamiania aplikacji i sprawdzić, jak są one obsługiwane w kontrolerze.

W tym samouczku zostanie przetestowany widok błędów później.

W przykładzie usługi GitHub usuwane są nieużywane widoki i powiązane z nimi działania.

### <a name="add-the-runqueryasync-method"></a>Dodawanie metody RunQueryAsync

Wywołanie Wyszukiwanie poznawcze platformy Azure jest hermetyzowane w naszej metodzie **RunQueryAsync** .

1. Najpierw Dodaj pewne zmienne statyczne, aby skonfigurować usługę platformy Azure, a następnie zadzwoń do ich zainicjowania.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Teraz Dodaj samą metodę **RunQueryAsync** .

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    W tej metodzie najpierw upewnij się, że konfiguracja platformy Azure została zainicjowana, a następnie ustaw kilka opcji wyszukiwania. Opcja **SELECT** określa, które pola mają być zwracane w wynikach, i w ten sposób dopasowuje nazwy właściwości w klasie **hotelu** . W przypadku pominięcia **opcji Select**wszystkie nieukryte pola są zwracane, co może być niewydajne, Jeśli interesuje Cię tylko podzbiór wszystkich możliwych pól.

    Wywołanie asynchroniczne do wyszukiwania ustala żądanie (modelowane jako **tekstprzeszukiwany**) i odpowiedź (modeluje jako **searchResult**). Jeśli debugujesz ten kod, Klasa **SearchResult** jest dobrym kandydatem do ustawiania punktu przerwania, jeśli trzeba przeanalizować zawartość **modelu. resultList**. Należy się przekonać, że jest intuicyjny, dostarczając dane, których dotyczy żądanie, a nie wiele innych.

### <a name="test-the-app"></a>Testowanie aplikacji

Teraz sprawdźmy, czy aplikacja działa prawidłowo.

1. Wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** lub naciśnij klawisz **F5**. Jeśli aplikacja działa zgodnie z oczekiwaniami, należy uzyskać początkowy widok indeksu.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Wyszukiwanie * Pool *" border="true":::

1. Wprowadź ciąg zapytania, taki jak "sekwencje" (lub dowolny tekst, który jest na myśli), a następnie kliknij ikonę wyszukiwania, aby wysłać żądanie.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Wyszukiwanie * Pool *" border="true":::

1. Spróbuj wprowadzić ciąg "pięć gwiazdek". Zwróć uwagę, że to zapytanie nie zwraca żadnych wyników. Bardziej zaawansowane wyszukiwanie będzie traktować "pięć gwiazdek" jako synonim dla "możliwość zaprojektowania" i zwracać te wyniki. Obsługa [synonimów](search-synonyms.md) jest dostępna w usłudze Azure wyszukiwanie poznawcze, ale nie jest objęta tą serią samouczków.

1. Spróbuj wprowadzić "gorąca" jako tekst wyszukiwania. _Nie zwracają wpisów_ ze słowem "Hotel". Nasze wyszukiwanie lokalizuje tylko całe wyrazy, ale zwracane są kilka wyników.

1. Wypróbuj inne słowa: "Pool", "słońca", "View" i dowolne. Zobaczysz, że platforma Azure Wyszukiwanie poznawcze działa na najprostszej, ale nadal będzie mieć przekonujący poziom.

## <a name="test-edge-conditions-and-errors"></a>Warunki i błędy krawędzi testu

Ważne jest, aby upewnić się, że nasze funkcje obsługi błędów działają w miarę potrzeby, nawet gdy działają doskonale. 

1. W metodzie **index** , po wykonaniu **próby {** Call, Wprowadź wiersz **throw new Exception ()**. Ten wyjątek spowoduje wymuszenie błędu podczas wyszukiwania tekstu.

2. Uruchom aplikację, wprowadź "bar" jako szukany tekst, a następnie kliknij ikonę wyszukiwania. Wyjątek powinien spowodować wyświetlenie błędu.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Wyszukiwanie * Pool *" border="true":::

    > [!Important]
    > Jest uznawany za zagrożenie bezpieczeństwa, aby zwracały wewnętrzne numery błędów na stronach błędów. Jeśli aplikacja jest przeznaczona do użytku ogólnego, należy zapoznać się z bezpiecznymi i najlepszymi rozwiązaniami dotyczącymi tego, co należy zwrócić w przypadku wystąpienia błędu.

3. Usuń **nowy wyjątek throw ()** , gdy zostanie osiągnięta odpowiednia obsługa błędów.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Wywołanie Wyszukiwanie poznawcze platformy Azure jest zwięzłe i jest łatwe do interpretowania wyników.
* Wywołania asynchroniczne powodują dodanie niewielkiej złożoności do kontrolera, ale najlepszym rozwiązaniem jest, jeśli zamierzasz opracowywać aplikacje wysokiej jakości.
* Ta aplikacja wykonała proste wyszukiwanie tekstowe zdefiniowane przez co to jest konfiguracja w **SearchOptions zawierają**. Jednak tę jedną klasę można wypełnić wieloma elementami członkowskimi, które dodają złożoności do wyszukiwania. Aby ta aplikacja była znacznie bardziej wydajna, nie trzeba wykonywać wielu dodatkowych czynności.

## <a name="next-steps"></a>Następne kroki

Aby ulepszyć środowisko użytkownika, Dodaj więcej funkcji, szczególnie stronicowanie (przy użyciu numerów stron lub Nieskończonego przewijania) oraz Autouzupełnianie/sugestie. Możesz również rozważyć bardziej zaawansowane opcje wyszukiwania (na przykład geograficzne wyszukiwanie w hotelach w określonym promieniu danego punktu i porządkowanie wyników wyszukiwania).

Te następne kroki są rozkierowane do pozostałych samouczków. Zacznijmy od stronicowania.

> [!div class="nextstepaction"]
> [Samouczek języka C#: wyszukiwanie wyników wyszukiwania na platformie Azure Wyszukiwanie poznawcze](tutorial-csharp-paging.md)