---
title: Samouczek języka C# do tworzenia pierwszej aplikacji
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć pierwszą aplikację wyszukiwania w języku C# krok po kroku. Samouczek zawiera link do działającej aplikacji w witrynie GitHub oraz kompletny proces tworzenia aplikacji od podstaw. Poznaj najważniejsze składniki Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 49dc551039e0fd82ddb4374713c59fca2f493b62
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397950"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Samouczek: Tworzenie pierwszej aplikacji wyszukiwania przy użyciu zestawu .NET SDK

Dowiedz się, jak utworzyć interfejs sieci Web, aby wykonywać zapytania i przedstawić wyniki wyszukiwania w indeksie przy użyciu usługi Azure Wyszukiwanie poznawcze. Ten samouczek rozpoczyna się od istniejącego, hostowanego indeksu, dzięki czemu możesz skupić się na tworzeniu strony wyszukiwania. Indeks zawiera fikcyjne dane hotelu. Gdy masz stronę podstawową, możesz ją ulepszyć w kolejnych lekcjach w celu uwzględnienia stronicowania, aspektów i środowiska z wyprzedzeniem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie środowiska deweloperskiego
> * Struktury danych modelu
> * Utwórz stronę sieci Web
> * Definiuj metody
> * Testowanie aplikacji

Dowiesz się również, jak proste jest wywołanie wyszukiwania. Najważniejsze instrukcje w kodzie, który utworzysz, są hermetyzowane w następujących kilku wierszach.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

To jedno wywołanie inicjuje wyszukiwanie danych platformy Azure i zwraca wyniki.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Wyszukiwanie * Pool *" border="false":::


## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[Zainstaluj program Visual Studio](https://visualstudio.microsoft.com/) , aby użyć go jako środowiska IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalowanie i uruchamianie projektu z usługi GitHub

1. Znajdź przykład w witrynie GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Wybierz opcję **Klonuj lub Pobierz** , aby utworzyć prywatną lokalną kopię projektu.
1. Korzystając z programu Visual Studio, przejdź do, a następnie otwórz rozwiązanie dla strony Wyszukiwanie podstawowe, a następnie wybierz pozycję **Uruchom bez debugowania** (lub naciśnij klawisz F5).
1. Wpisz słowa (na przykład "Wi-Fi", "widok", "słupek", "parking") i sprawdź wyniki.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Wyszukiwanie * Wi-Fi *" border="false":::

Miejmy nadzieję ten projekt będzie działać bezproblemowo i masz uruchomioną aplikację platformy Azure. Wiele najważniejszych składników dla bardziej złożonych wyszukiwań znajduje się w tej aplikacji, więc dobrym pomysłem jest przechodzenie przez nią i ponowne utworzenie jej krok po kroku.

Aby utworzyć ten projekt od podstaw i w związku z tym pomóc w zwiększeniu poziomu Wyszukiwanie poznawcze platformy Azure, wykonaj poniższe kroki.

## <a name="set-up-a-development-environment"></a>Konfigurowanie środowiska programistycznego

1. W programie Visual Studio 2017 lub nowszym wybierz pozycję **Nowy/projekt** , a następnie **ASP.NET Core aplikację sieci Web**. Nadaj projektowi nazwę, taką jak "FirstAzureSearchApp".

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Tworzenie projektu w chmurze" border="false":::

2. Po kliknięciu przycisku **OK** dla tego typu projektu zostanie nadany drugi zestaw opcji, które mają zastosowanie do tego projektu. Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Tworzenie projektu MVC":::

3. Następnie w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet** , a następnie **Zarządzaj pakietami NuGet dla rozwiązania.**... Istnieje jeden pakiet, który należy zainstalować. Wybierz kartę **Przeglądaj** , a następnie wpisz ciąg "Azure wyszukiwanie poznawcze" w polu wyszukiwania. Zainstaluj **program Microsoft. Azure. Search** , gdy zostanie wyświetlony na liście (w wersji 9.0.1 lub nowszej). Aby ukończyć instalację, należy kliknąć kilka dodatkowych okien dialogowych.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Dodawanie bibliotek platformy Azure przy użyciu narzędzia NuGet" border="false":::

### <a name="initialize-azure-cognitive-search"></a>Inicjowanie Wyszukiwanie poznawcze platformy Azure

Na potrzeby tego przykładu używamy publicznie dostępnych danych hotelowych. Te dane to arbitralna kolekcja nieprawidłowych nazw hotelów i opisów 50, utworzonych wyłącznie na potrzeby udostępniania danych demonstracyjnych. Aby uzyskać dostęp do tych danych, należy określić dla niej nazwę i klucz.

1. Otwórz appsettings.jsw pliku w nowym projekcie i Zastąp wiersze domyślne następującymi nazwami i kluczami. Klucz interfejsu API przedstawiony w tym miejscu nie jest przykładem klucza, ale jest _dokładnie_ kluczem potrzebnym do uzyskania dostępu do danych hotelowych. appsettings.jsw pliku powinien teraz wyglądać następująco.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Nie zrobiono jeszcze tego pliku, wybierz właściwości tego pliku i zmień ustawienie **Kopiuj do katalogu wyjściowego** na **Kopiuj, jeśli nowszy**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Kopiowanie ustawień aplikacji do danych wyjściowych" border="false":::

## <a name="model-data-structures"></a>Struktury danych modelu

Modele (klasy C#) służą do przekazywania danych między klientem (widokiem), serwerem (kontrolerem), a także chmurą platformy Azure przy użyciu architektury MVC (model, widok, kontroler). Zwykle te modele odzwierciedlają strukturę danych, do których uzyskuje się dostęp. Ponadto potrzebujemy modelu do obsługi komunikacji z widokiem/kontrolerem.

1. Otwórz folder **modele** projektu przy użyciu Eksplorator rozwiązań i w tym miejscu zobaczysz jeden model domyślny: **ErrorViewModel.cs**.

2. Kliknij prawym przyciskiem myszy folder **modele** i wybierz polecenie **Dodaj** **nowy element**. Następnie w oknie dialogowym, które się pojawi, wybierz **ASP.NET Core** następnie pierwszej **klasy**opcji. Zmień nazwę pliku CS na Hotel.cs, a następnie kliknij przycisk **Dodaj**. Zastąp całą zawartość Hotel.cs następującym kodem. Zwróć uwagę na **adres** i składowe **pokoju** klasy, te pola są klasami, więc potrzebne są również modele.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. Postępuj zgodnie z tym samym procesem tworzenia modelu dla klasy **adresów** , z wyjątkiem Address.cs pliku. Zastąp zawartość poniższymi.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. I ponownie wykonaj ten sam proces, aby utworzyć klasę **pokojową** , nadając jej nazwę Room.cs. Ponownie Zastąp zawartość następującym poleceniem.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. Zestawy **hotelowe**i **adresowe**oraz klasy **pokoju** są znane na platformie Azure jako [_typy złożone_](search-howto-complex-data-types.md), ważną funkcję systemu Azure wyszukiwanie poznawcze. Typy złożone mogą mieć wiele poziomów na głębokości klas i podklas, a ponadto zapewniają znacznie bardziej złożone struktury danych, które mają być reprezentowane niż przy użyciu _typów prostych_ (Klasa zawierająca tylko pierwotne elementy członkowskie). Potrzebujemy jeszcze jednego modelu, więc przejdź przez proces tworzenia nowej klasy modelu ponownie, chociaż ten czas wywoła klasę SearchData.cs i zastąpi kod domyślny następującym.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Ta klasa zawiera dane wejściowe użytkownika (**tekstprzeszukiwany**) i wyniki wyszukiwania (**resultList**). Typ danych wyjściowych jest krytyczny, **DocumentSearchResult &lt; hotelu &gt; **, ponieważ ten typ dokładnie pasuje do wyników wyszukiwania i musi przekazać to odwołanie do widoku.



## <a name="create-a-web-page"></a>Utwórz stronę sieci Web

Projekt, który został utworzony, zostanie domyślnie utworzony w wielu widokach klienta. Dokładne widoki są zależne od używanej wersji platformy .NET Core (w tym przykładzie stosujemy 2,1). Znajdują się one w folderze **widoki** projektu. Wystarczy zmodyfikować plik index. cshtml (w **widokach/folderze głównym** ).

Usuń zawartość pliku index. cshtml w całości i Skompiluj ponownie plik w poniższych krokach.

1. Używamy dwóch małych obrazów w widoku. Możesz użyć własnych lub skopiować obrazy z projektu GitHub: azure-logo.png i search.png. Te dwa obrazy należy umieścić w folderze **wwwroot/images** .

2. Pierwszy wiersz index. cshtml powinien odwoływać się do modelu, który będzie używany do przekazywania danych między klientem (widokiem) a serwerem (kontrolerem), który jest modelem **SearchData** , który został utworzony. Dodaj ten wiersz do pliku index. cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Jest to standardowe rozwiązanie służące do wprowadzania tytułu widoku, dlatego kolejne wiersze powinny być następujące:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Po tytule wprowadź odwołanie do arkusza stylów HTML, który wkrótce utworzymy.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Teraz do mięsa z widoku. Należy pamiętać, że widok musi obsługiwać dwie sytuacje. Po pierwsze musi obsłużyć ekran, gdy aplikacja jest uruchamiana po raz pierwszy, a użytkownik nie wprowadził jeszcze żadnego tekstu wyszukiwania. Po drugie, musi obsługiwać wyświetlanie wyników, oprócz pola tekstowego wyszukiwanie, do wielokrotnego użycia przez użytkownika. Aby obsłużyć te dwie sytuacje, musimy sprawdzić, czy model podany w widoku ma wartość null, czy nie. Model o wartości null wskazuje, że mamy pierwszy z dwóch sytuacji (początkowy uruchomienie aplikacji). Dodaj następujący element do pliku index. cshtml i przeczytaj komentarz.

    ```cs
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
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Na koniec dodamy arkusz stylów. W programie Visual Studio, w menu **plik** wybierz polecenie **nowy/plik** , a następnie **arkusz stylów** (z wyróżnioną **ogólną** ). Zastąp domyślny kod następującym. Ten plik nie będzie bardziej szczegółowy, a style są standardowym kodem HTML.

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

7. Zapisz plik stylesheet jako Hotele. CSS w folderze wwwroot/CSS, obok domyślnego pliku. css.

To kończy nasz widok. Trwa przygotowywanie dobrego postępu. Modele i widoki są wykonywane, tylko kontroler pozostanie powiązany ze sobą.

## <a name="define-methods"></a>Definiuj metody

Musimy zmodyfikować zawartość jednego kontrolera (**kontrolera głównego**), który jest tworzony domyślnie.

1. Otwórz plik HomeController.cs i Zastąp instrukcje **using** poniższymi instrukcjami.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Dodawanie metod indeksu

Potrzebujemy dwóch metod **indeksu** , ale nie pobrały żadnych parametrów (w przypadku, gdy aplikacja jest otwarta po raz pierwszy) i jeden z nich przyjmuje model jako parametr (dla momentu, gdy użytkownik wprowadził tekst wyszukiwania). Pierwszy z tych metod jest tworzony domyślnie. 

1. Dodaj następującą metodę po domyślnym **indeksie ()** .

    ```cs
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

    Zwróć uwagę na deklarację **Async** metody oraz wywołanie **await** do **RunQueryAsync**. Te słowa kluczowe zadbają o uczynienie wywołań asynchronicznych i dlatego Unikaj blokowania wątków na serwerze.

    Blok **catch** używa modelu błędów, który został domyślnie utworzony dla nas.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Zwróć uwagę na obsługę błędów oraz inne widoki i metody domyślne

W zależności od używanej wersji platformy .NET Core domyślnie tworzone są nieco różne zestawy widoków domyślnych. W przypadku platformy .NET Core 2,1 widoki domyślne to index, about, Contact, privacy i Error. W przypadku platformy .NET Core 2,2 na przykład domyślne widoki to index, privacy i Error. W obu przypadkach można wyświetlić te domyślne strony podczas uruchamiania aplikacji i sprawdzić, jak są one obsługiwane w kontrolerze.

W tym samouczku zostanie przetestowany widok błędów dalej.

W przykładzie usługi GitHub Usunięto nieużywane widoki i powiązane z nimi działania.

### <a name="add-the-runqueryasync-method"></a>Dodawanie metody RunQueryAsync

Wywołanie Wyszukiwanie poznawcze platformy Azure jest hermetyzowane w naszej metodzie **RunQueryAsync** .

1. Najpierw Dodaj pewne zmienne statyczne, aby skonfigurować usługę platformy Azure, a następnie zadzwoń do ich zainicjowania.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Teraz Dodaj samą metodę **RunQueryAsync** .

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    W tej metodzie najpierw upewnimy się, że konfiguracja platformy Azure została zainicjowana, a następnie ustaw kilka parametrów wyszukiwania. Nazwy pól w **zaznaczeniu** parametru pasują dokładnie do nazw właściwości w klasie **hotelu** . Możliwe jest pozostawienie parametru **SELECT** , w tym przypadku zwracane są wszystkie właściwości. Ustawienie nie **wybieraj** żadnych parametrów jest jednak niewydajne, Jeśli interesuje Cię tylko podzbiór danych. Określając właściwości, których interesuje, są zwracane tylko te właściwości.

    Wywołanie asynchroniczne do wyszukiwania (**model. resultList = await _indexClient.Documents. SearchAsync &lt; Hotel &gt; (model. tekstprzeszukiwany, Parameters);**) to informacje o tym samouczku i aplikacji. Klasa **DocumentSearchResult** jest interesująca, a dobrym pomysłem (gdy aplikacja jest uruchomiona) polega na tym, że w tym miejscu należy ustawić punkt przerwania i użyć debugera do sprawdzenia zawartości **modelu. resultList**. Należy się przekonać, że jest intuicyjny, dostarczając dane, których dotyczy żądanie, a nie wiele innych.

Nadeszła chwila prawdy.

### <a name="test-the-app"></a>Testowanie aplikacji

Teraz sprawdźmy, czy aplikacja działa prawidłowo.

1. Wybierz opcję **Debuguj/Rozpocznij bez debugowania** lub naciśnij klawisz F5. Jeśli poprawnie zakodowano elementy, zostanie wyświetlony początkowy widok indeksu.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Otwieranie aplikacji" border="false":::

2. Wprowadź tekst, taki jak "sekwencje" (lub dowolny tekst, który ma uwadze), a następnie kliknij ikonę wyszukiwania. Należy uzyskać pewne wyniki.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Wyszukiwanie * sekwencje *" border="false":::

3. Spróbuj wprowadzić ciąg "pięć gwiazdek". Zwróć uwagę, jak nie otrzymujesz żadnych wyników. Bardziej zaawansowane wyszukiwanie będzie traktować "pięć gwiazdek" jako synonim dla "możliwość zaprojektowania" i zwracać te wyniki. Użycie synonimów jest dostępne na platformie Azure Wyszukiwanie poznawcze, ale nie zajmiemy się tym w pierwszym samouczku.
 
4. Spróbuj wprowadzić "gorąca" jako tekst wyszukiwania. _Nie zwracają wpisów_ ze słowem "Hotel". Nasze wyszukiwanie lokalizuje tylko całe wyrazy, ale zwracane są kilka wyników.

5. Wypróbuj inne słowa: "Pool", "słońca", "View" i dowolne. Zobaczysz, że platforma Azure Wyszukiwanie poznawcze działa na najprostszej, ale nadal będzie mieć przekonujący poziom.

## <a name="test-edge-conditions-and-errors"></a>Warunki i błędy krawędzi testu

Ważne jest, aby upewnić się, że nasze funkcje obsługi błędów działają w miarę potrzeby, nawet gdy działają doskonale. 

1. W metodzie **index** , po wykonaniu **próby {** Call, Wprowadź wiersz **throw new Exception ()**. Ten wyjątek spowoduje wymuszenie błędu podczas wyszukiwania tekstu.

2. Uruchom aplikację, wprowadź "bar" jako szukany tekst, a następnie kliknij ikonę wyszukiwania. Wyjątek powinien spowodować wyświetlenie błędu.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Wymuś błąd" border="false":::

    > [!Important]
    > Jest uznawany za zagrożenie bezpieczeństwa, aby zwracały wewnętrzne numery błędów na stronach błędów. Jeśli aplikacja jest przeznaczona do użytku ogólnego, należy zapoznać się z bezpiecznymi i najlepszymi rozwiązaniami dotyczącymi tego, co należy zwrócić w przypadku wystąpienia błędu.

3. Usuń **nowy wyjątek throw ()** , gdy zostanie osiągnięta odpowiednia obsługa błędów.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Wywołanie Wyszukiwanie poznawcze platformy Azure jest zwięzłe i jest łatwe do interpretowania wyników.
* Wywołania asynchroniczne powodują dodanie niewielkiej złożoności do kontrolera, ale najlepszym rozwiązaniem jest, jeśli zamierzasz opracowywać aplikacje wysokiej jakości.
* Ta aplikacja wykonała proste wyszukiwanie tekstowe zdefiniowane przez co to jest konfiguracja w **searchParameters**. Jednak tę jedną klasę można wypełnić wieloma elementami członkowskimi, które dodają złożoności do wyszukiwania. Aby ta aplikacja była znacznie bardziej wydajna, nie trzeba wykonywać wielu dodatkowych czynności.

## <a name="next-steps"></a>Następne kroki

Aby zapewnić najlepsze środowisko użytkownika przy użyciu usługi Azure Wyszukiwanie poznawcze, musimy dodać więcej funkcji, szczególnie stronicowanie (przy użyciu numerów stron lub nieskończoność przewijania) oraz Autouzupełnianie/sugestie. Należy również rozważyć bardziej zaawansowane parametry wyszukiwania (na przykład geograficzne wyszukiwanie w hotelach w określonym promieniu danego punktu i porządkowanie wyników wyszukiwania).

Te następne kroki są rozłączone w szereg samouczków. Zacznijmy od stronicowania.

> [!div class="nextstepaction"]
> [Samouczek języka C#: wyszukiwanie wyników wyszukiwania na platformie Azure Wyszukiwanie poznawcze](tutorial-csharp-paging.md)


