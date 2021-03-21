---
title: Samouczek języka C# dotyczący porządkowania wyników
titleSuffix: Azure Cognitive Search
description: W tym samouczku w języku C# pokazano, jak zamówić wyniki wyszukiwania. Kompiluje on w poprzednim projekcie hoteli, sortując według właściwości podstawowej, właściwości pomocniczej i zawiera profil oceniania, aby dodać kryteria zwiększania wydajności.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786388"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Samouczek: kolejność wyników wyszukiwania przy użyciu zestawu .NET SDK

W tej serii samouczków wyniki zostały zwrócone i wyświetlone w [domyślnej kolejności](index-add-scoring-profiles.md#what-is-default-scoring). W tym samouczku dodasz podstawowe i pomocnicze kryteria sortowania. Zamiast określania kolejności na podstawie wartości liczbowych, ostatni przykład pokazuje, jak ustalić rangę wyników na podstawie niestandardowego profilu oceniania. Dodatkowo przejdziemy do wyświetlania _typów złożonych_.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Kolejność wyników na podstawie jednej właściwości
> * Kolejność wyników na podstawie wielu właściwości
> * Zamów wyniki w oparciu o profil oceniania

## <a name="overview"></a>Omówienie

Ten samouczek rozszerza nieskończony projekt przewijania utworzony w samouczku [Dodawanie stronicowania do wyników wyszukiwania](tutorial-csharp-paging.md) .

Gotowa wersja kodu w tym samouczku można znaleźć w następującym projekcie:

* [5 — wyniki zamówień (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Wymagania wstępne

* [2b — rozwiązanie Dodaj-nieograniczone-Scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll) . Ten projekt może być własną wersją utworzoną na podstawie poprzedniego samouczka lub kopii z usługi GitHub.

Ten samouczek został zaktualizowany do korzystania z pakietu [Azure.Search.Documents (wersja 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . Aby uzyskać wcześniejszą wersję zestawu .NET SDK, zobacz [przykład kodu Microsoft. Azure. Search (wersja 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Kolejność wyników na podstawie jednej właściwości

Gdy porządkuje się wyniki oparte na jednej właściwości, takiej jak w przypadku klasyfikacji hotelowej, nie tylko chcemy, aby kolejność uporządkowana była poprawna. Dodanie pola oceny do wyników pozwala nam potwierdzić, że wyniki są poprawnie sortowane.

W tym ćwiczeniu dodamy również nieco więcej informacji na temat wyświetlania wyników: najtańsza szybkość pokojowa i najbardziej kosztownej szybkości pokojowej dla każdego hotelu.

Nie ma potrzeby modyfikowania żadnego z modeli, aby umożliwić Określanie kolejności. Tylko widok i kontroler wymagają aktualizacji. Zacznij od otwarcia kontrolera macierzystego.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Dodaj właściwość OrderBy do parametrów wyszukiwania

1. W HomeController. cs Dodaj opcję **OrderBy** i Uwzględnij Właściwość Rating z malejącą kolejnością sortowania. W metodzie **index (SearchData model)** Dodaj następujący wiersz do parametrów wyszukiwania.

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > Kolejność domyślna to Ascending, ale można ją dodać `asc` do właściwości, aby to zrobić. Kolejność malejąca jest określana przez dodanie `desc` .

1. Teraz uruchom aplikację i wprowadź dowolny termin wyszukiwania. Wyniki mogą być w niewłaściwej kolejności lub mogą być niezgodne, ponieważ nie są one deweloperami, a nie użytkownikami, w łatwy sposób sprawdzają wyniki.

1. Wyczyśćmy, czy wyniki są uporządkowane według klasyfikacji. Najpierw Zastąp klasy **BOX1** i **box2** w pliku hoteli. css następującymi klasami (te klasy są wszystkie nowe, które są potrzebne w tym samouczku).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > Przeglądarki zwykle buforują pliki CSS i mogą prowadzić do używanego starego pliku CSS, a zmiany zostały zignorowane. Dobrym sposobem jest dodanie ciągu zapytania z parametrem Version do linku. Na przykład:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Zaktualizuj numer wersji, jeśli uważasz, że stary plik CSS jest używany przez przeglądarkę.

1. Dodaj właściwość **Rating** do parametru **SELECT** w metodzie **index (SearchData model)** , tak aby wyniki obejmowały następujące trzy pola:

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. Otwórz widok (index. cshtml) i Zastąp pętlę renderowania (**&lt; !--pokazać dane hotelu.-- &gt;**) z poniższym kodem.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Klasyfikacja musi być dostępna zarówno na pierwszej wyświetlanej stronie, jak i na kolejnych stronach, które są wywoływane przez nieskończone przewijanie. W przypadku tych dwóch sytuacji należy zaktualizować **następną** akcję w kontrolerze i **przewijaną** funkcję w widoku. Rozpoczynając od kontrolera, Zmień **następną** metodę na następujący kod. Ten kod tworzy i komunikuje tekst klasyfikacji.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Teraz zaktualizuj **przewijaną** funkcję w widoku, aby wyświetlić tekst klasyfikacji.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Teraz ponownie uruchom aplikację. Wyszukaj w dowolnym wspólnym terminie, takim jak "Wi-Fi", i sprawdź, czy wyniki są uporządkowane według malejącej kolejności klasyfikacji hotelowej.

    ![Porządkowanie na podstawie klasyfikacji](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Zobaczysz, że kilka hoteli ma identyczną klasyfikację, więc ich wygląd na ekranie jest ponownie kolejnością, w której znajdują się dane, co jest dowolne.

    Przed przystąpieniem do dodawania drugiego poziomu porządkumy dodamy kod umożliwiający wyświetlenie zakresu wielkości pomieszczeń. Dodajemy ten kod zarówno do pokazywania wyodrębniania danych z _typu złożonego_, jak również możemy omówić wyniki sortowania na podstawie ceny (najprawdopodobniej najtańszej pierwszej).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Dodaj zakres stawek pomieszczeń do widoku

1. Dodaj właściwości zawierające najtańszą i najbardziej kosztowną szybkość pokoju do modelu Hotel. cs.

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. Oblicz szybkość pomieszczeń na końcu akcji **indeks (SearchData model)** w kontrolerze głównym. Dodaj obliczenia po przechowywaniu danych tymczasowych.

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. Dodaj właściwość **pokoje** do parametru **SELECT** w metodzie Action **indeksu (SearchData model)** .

    ```cs
    options.Select.Add("Rooms");
    ```

1. Zmień pętlę renderowania w widoku, aby wyświetlić zakres stawki dla pierwszej strony wyników.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Zmień **następną** metodę na kontrolerze głównym, aby przekazywać zakres stawki dla kolejnych stron wyników.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Zaktualizuj **przewijaną** funkcję w widoku, aby obsłużyć tekst kursów pokojowych.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Uruchom aplikację i sprawdź, czy są wyświetlane zakresy szybkości pomieszczeń.

    ![Wyświetlanie zakresów szybkości pomieszczeń](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Właściwość **OrderBy** parametrów wyszukiwania nie akceptuje wpisu, takiego jak **sale. BaseRate** , aby zapewnić najtańszą szybkość pomieszczeń, nawet jeśli pokoje zostały już posortowane według stawki. W tym przypadku pokoje nie są sortowane według stawki. Aby wyświetlić Hotele w zestawie danych przykładowych, uporządkowane według szybkości pomieszczeń, należy posortować wyniki w kontrolerze głównym i wysłać te wyniki do widoku w odpowiedniej kolejności.

## <a name="order-results-based-on-multiple-values"></a>Kolejność wyników w oparciu o wiele wartości

Pytanie jest teraz sposobem rozróżniania hoteli z tą samą klasyfikacją. Jedno z metod może być kolejnością pomocniczą opartą na ostatnim czasie, w którym Hotel został Renovated, dzięki czemu bardziej ostatnio Renovated Hotele pojawiają się powyżej w wynikach.

1. Aby dodać drugi poziom sortowania, Dodaj **zapytaniu lastrenovationdate** do wyników wyszukiwania i do **OrderBy** w metodzie **index (model SearchData)** .

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > Na liście **OrderBy** można wprowadzić dowolną liczbę właściwości. Jeśli Hotele ma taką samą ocenę i datę odnawiania, można wprowadzić trzecią właściwość w celu rozróżnienia między nimi.

1. Ponownie musimy zobaczyć datę odnawiania w widoku, aby upewnić się, że kolejność jest poprawna. W przypadku takich czynności jako Renowacja prawdopodobnie wystarczy rok. Zmień pętlę renderowania w widoku na następujący kod.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Zmień **następną** metodę w kontrolerze głównym, aby przekazywać składnik roku ostatniego dnia odnawiania.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Zmień **przewijaną** funkcję w widoku, aby wyświetlić tekst odnawiania.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Uruchom aplikację. Wyszukaj wspólny termin, taki jak "Pool" lub "View", i sprawdź, czy Hotele o tej samej klasyfikacji są teraz wyświetlane w kolejności malejącej w dniu odnawiania.

    ![Porządkowanie według daty odnawiania](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>Zamów wyniki w oparciu o profil oceniania

W przykładach podanych w samouczku pokazano, jak zamówić wartości liczbowe (Klasyfikacja i Data odnawiania), zapewniając _dokładną_ sekwencję kolejności. Jednak niektóre wyszukiwania i niektóre dane nie zapewnią do takiego łatwego porównania między dwoma elementami danych. W przypadku zapytań wyszukiwania pełnotekstowego Wyszukiwanie poznawcze obejmuje koncepcję _klasyfikacji_. _Profile oceniania_ można określić, aby mieć wpływ na sposób pozycjonowania wyników, co zapewnia bardziej złożone i jakościowe porównania.

[Profile oceniania](index-add-scoring-profiles.md) są zdefiniowane w schemacie indeksu. Skonfigurowano kilka profilów oceniania dla danych hoteli. Spójrzmy na sposób definiowania profilu oceniania, a następnie spróbuj napisać kod, aby go wyszukać.

### <a name="how-scoring-profiles-are-defined"></a>Jak są definiowane profile oceniania

Profile oceniania są definiowane w indeksie wyszukiwania w czasie projektowania. Indeks hoteli tylko do odczytu, który jest hostowany przez firmę Microsoft, ma trzy profile oceniania. Ta sekcja zawiera informacje o profilach oceniania i pokazano, jak używać kodu w kodzie.

1. Poniżej znajduje się domyślny profil oceniania dla zestawu danych hoteli używany, gdy nie określisz żadnego parametru **OrderBy** lub **ScoringProfile** . Ten profil zwiększa _wynik_ hotelu, jeśli wyszukiwany tekst jest obecny w nazwie hotelu, opisie lub liście tagów (udogodnień). Zwróć uwagę na to, jak wagi oceniania preferują określone pola. Jeśli szukany tekst zostanie wyświetlony w innym polu, a nie na liście poniżej, będzie miał wagę 1. Oczywiście im wyższy wynik, tym większy jest wynik w widoku.

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. Następujący alternatywny profil oceniania zwiększa wynik znacząco, jeśli dostarczony parametr zawiera jedną lub więcej z listy tagów (w przypadku wywołania "udogodnień"). Kluczowym punktem tego profilu jest, że _należy_ podać parametr zawierający tekst. Jeśli parametr jest pusty lub nie zostanie podany, zostanie zgłoszony błąd.

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. W tym trzecim profilu Rating hotelowy daje znaczący wzrost do wyniku. Data ostatniej Renovated również zwiększy wynik, ale tylko wtedy, gdy dane te mieszczą się w przedziale od 730 dni (2 lata) bieżącej daty.

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    Teraz poinformuj nas, czy te profile działają zgodnie z oczekiwaniami.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Dodawanie kodu do widoku w celu porównania profilów

1. Otwórz plik index. cshtml i Zastąp &lt; &gt; sekcję Body poniższym kodem.

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
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Otwórz plik SearchData. cs i Zastąp klasę **SearchData** następującym kodem.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Otwórz plik hoteli. CSS i Dodaj następujące klasy HTML.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Dodaj kod do kontrolera, aby określić profil oceniania

1. Otwórz plik kontrolera głównego. Dodaj następującą instrukcję **using** (aby pomóc w tworzeniu list).

    ```cs
    using System.Linq;
    ```

1. Na potrzeby tego przykładu potrzebujemy początkowego wywołania **indeksu** , aby wykonać nieco więcej niż po prostu zwrócić widok początkowy. Metoda teraz wyszukuje nawet 20 walorów, które mają być wyświetlane w widoku.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Potrzebujemy dwóch metod prywatnych do zapisania aspektów w magazynie tymczasowym i odzyskania ich z magazynu tymczasowego i wypełnienia modelu.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. W razie potrzeby musimy ustawić parametry **OrderBy** i **ScoringProfile** . Zastąp istniejącą metodę **index (SearchData model)** następującym poleceniem.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
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
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Zapoznaj się z komentarzami dotyczącymi poszczególnych **przełączników** .

1. Nie musisz wprowadzać żadnych zmian w **następnej** akcji, Jeśli zakończysz dodatkowy kod dla poprzedniej sekcji w kolejności na podstawie wielu właściwości.

### <a name="run-and-test-the-app"></a>Uruchamianie i testowanie aplikacji

1. Uruchom aplikację. W widoku powinien zostać wyświetlony pełen zestaw udogodnień.

1. Wybranie pozycji "według klasyfikacji liczbowej" spowoduje nadanie liczbowej kolejności, która została już zaimplementowana w tym samouczku, z datą odnawiania, która decyduje o tym, jak Hotele są równej ocenie.

   ![Porządkowanie "sekwencje" na podstawie klasyfikacji](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Wypróbuj teraz profil "według udogodnień". Wybieraj różne rodzaje walorów i sprawdzaj, czy Hotele z tymi walorami zostały podwyższone do listy wyników.

   ![Porządkowanie "sekwencje" w oparciu o profil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Wypróbuj Renovated daty/klasyfikacji, aby zobaczyć, czy otrzymujesz oczekiwane dane. Tylko ostatnio Renovated Hotele powinny uzyskać zwiększenie _Aktualności_ .

### <a name="resources"></a>Zasoby

Aby uzyskać więcej informacji, zobacz następujące [Dodawanie profilów oceniania do indeksu wyszukiwanie poznawcze platformy Azure](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Użytkownicy będą oczekiwać, że wyniki wyszukiwania będą uporządkowane, najbardziej odpowiednie.
* Dane muszą mieć strukturę, dzięki czemu kolejność jest prosta. Nie udało się szybko sortować od "najtańszego", ponieważ dane nie są strukturalne, aby umożliwić porządkowanie bez dodatkowych kodów.
* Może istnieć wiele poziomów do uporządkowania, aby rozróżnić wyniki, które mają taką samą wartość na wyższym poziomie kolejności.
* Jest to naturalne dla niektórych wyników, które mają być uporządkowane w kolejności rosnącej (mówimy, odległość od punktu), a niektóre w kolejności malejącej (na przykład Ocena gościa).
* Profile oceniania można definiować, gdy porównania liczbowe są niedostępne lub nie są wystarczająco inteligentne dla zestawu danych. Ocenianie każdego wyniku może ułatwić uporządkowanie i wyświetlanie wyników.

## <a name="next-steps"></a>Następne kroki

Ta seria samouczków języka C# została ukończona — należy uzyskać cenne informacje dotyczące interfejsów API Wyszukiwanie poznawcze platformy Azure.

Aby uzyskać więcej informacji i samouczków, należy rozważyć przeglądanie [Microsoft Learn](/learn/browse/?products=azure)lub innych samouczków w [dokumentacji usługi Azure wyszukiwanie poznawcze](./index.yml).