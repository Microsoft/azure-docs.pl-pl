---
title: Samouczek języka C# Optymalizacja indeksowania przy użyciu interfejsu API wypychania
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak efektywnie indeksować dane za pomocą interfejsu API push w usłudze Azure Wyszukiwanie poznawcze. Ten samouczek i przykładowy kod są w języku C#.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 1/29/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f97a99bf2d055805ee665ab51aff8cff12dc5a69
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094196"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Samouczek: Optymalizowanie indeksowania przy użyciu interfejsu API wypychania

Usługa Azure Wyszukiwanie poznawcze obsługuje [dwa podstawowe podejścia](search-what-is-data-import.md) do importowania danych do indeksu wyszukiwania: *wypchnięcie* danych do indeksu programowo lub wskazanie [indeksatora wyszukiwanie poznawcze platformy Azure](search-indexer-overview.md) w obsługiwanym źródle danych w celu *ściągnięcia* danych.

W tym samouczku opisano sposób wydajnego indeksowania danych przy użyciu [modelu wypychania](search-what-is-data-import.md#pushing-data-to-an-index) przez przetwarzanie wsadowe żądań i użycie wykładniczej strategii ponawiania wycofywania. Możesz [pobrać i uruchomić przykładową aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). W tym artykule wyjaśniono kluczowe aspekty aplikacji i czynników, które należy wziąć pod uwagę podczas indeksowania danych.

Ten samouczek używa języka C# i [zestawu SDK platformy .NET](/dotnet/api/overview/azure/search) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie indeksu
> * Przetestuj różne rozmiary partii, aby określić najbardziej wydajny rozmiar
> * Indeksuj partie asynchronicznie
> * Użyj wielu wątków, aby zwiększyć szybkość indeksowania
> * Użyj strategii wycofywaniaego ponawiania prób w celu ponowienia nieudanych dokumentów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku są wymagane następujące usługi i narzędzia.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), dowolna wersja. Przykładowy kod i instrukcje zostały przetestowane w wersji bezpłatnej społeczności.

+ [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji.

<a name="get-service-info"></a>

## <a name="download-files"></a>Pobieranie plików

Kod źródłowy tego samouczka znajduje się w folderze [optimzize-Data-Indexes/v11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11) w repozytorium GitHub [Azure-Samples/Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) .

## <a name="key-considerations"></a>Najważniejsze zagadnienia

Podczas wypychania danych do indeksu istnieje kilka kluczowych zagadnień, które wpływają na szybkość indeksowania. Więcej informacji o tych czynnikach można znaleźć w [artykule indeksowanie dużych zestawów danych](search-howto-large-index.md).

Sześć kluczowych czynników, które należy wziąć pod uwagę:

+ **Warstwa usług i liczba partycji/replik** — Dodawanie partycji i zwiększanie warstwy spowoduje zwiększenie szybkości indeksowania.
+ **Indeksowanie schematu** — Dodawanie pól i Dodawanie dodatkowych właściwości do pól (takich jak *Wyszukiwanie*, dodanie lub *filtrowanie*) zmniejsza szybkość indeksowania.
+ **Rozmiar wsadu** — optymalny rozmiar wsadu zależy od schematu indeksu i zestawu danych.
+ **Liczba wątków/procesów roboczych** — pojedynczy wątek nie będzie całkowicie korzystał z szybkości indeksowania
+ **Strategia ponawiania** — w celu zoptymalizowania indeksowania należy użyć strategii ponawiania prób wycofywania.
+ **Szybkość transferu danych w sieci** — szybkość transferu danych może być czynnikiem ograniczającym. Indeksowanie danych z poziomu środowiska platformy Azure w celu zwiększenia szybkości transferu danych.


## <a name="1---create-azure-cognitive-search-service"></a>1 — Tworzenie usługi Azure Wyszukiwanie poznawcze

Do ukończenia tego samouczka potrzebna będzie usługa Wyszukiwanie poznawcze platformy Azure, którą można [utworzyć w portalu](search-create-service-portal.md). Zalecamy użycie tej samej warstwy, która ma być używana w środowisku produkcyjnym, aby można było dokładnie przetestować i zoptymalizować szybkość indeksowania.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz API i adres URL administratora dla usługi Azure Wyszukiwanie poznawcze

Wywołania interfejsu API wymagają adresu URL usługi i klucza dostępu. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   ![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-rest/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

## <a name="2---set-up-your-environment"></a>2 — Konfigurowanie środowiska

1. Uruchom program Visual Studio i Otwórz **OptimizeDataIndexing. sln**.
1. W Eksplorator rozwiązań Otwórz **appsettings.jsw** celu udostępnienia informacji o połączeniu.

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 — Eksplorowanie kodu

Po aktualizacji *appsettings.jsw* programie przykładowego programu w **OptimizeDataIndexing. sln** powinien być gotowy do kompilowania i uruchamiania.

Ten kod pochodzi od [przewodnika Szybki Start języka C#](search-get-started-dotnet.md). Bardziej szczegółowe informacje na temat pracy z zestawem SDK platformy .NET można znaleźć w tym artykule.

Ta prosta aplikacja konsolowa języka C#/.NET wykonuje następujące zadania:

+ Tworzy nowy indeks oparty na strukturze danych klasy hotelu języka C# (która odwołuje się również do klasy adresu).
+ Sprawdza różne rozmiary partii, aby określić najbardziej wydajny rozmiar
+ Asynchroniczne indeksowanie danych
    + Używanie wielu wątków w celu zwiększenia szybkości indeksowania
    + Użycie wykładniczej strategii ponawiania wycofywania do ponawiania prób elementów zakończonych niepowodzeniem

 Przed uruchomieniem programu Poświęć chwilę na zbadanie kodu i definicji indeksu dla tego przykładu. Odpowiedni kod znajduje się w kilku plikach:

  + **Hotel.cs** i **Address.cs** zawiera schemat definiujący indeks
  + **DataGenerator.cs** zawiera prostą klasę, aby ułatwić tworzenie dużych ilości danych hotelu
  + **ExponentialBackoff.cs** zawiera kod umożliwiający optymalizację procesu indeksowania zgodnie z poniższym opisem
  + **Program.cs** zawiera funkcje, które tworzą i usuwają indeks wyszukiwanie poznawcze platformy Azure, indeksuje partie danych i testuje różne rozmiary partii

### <a name="creating-the-index"></a>Tworzenie indeksu

Ten przykładowy program używa zestawu .NET SDK do definiowania i tworzenia indeksu Wyszukiwanie poznawcze platformy Azure. Wykorzystuje `FieldBuilder` klasę, aby wygenerować strukturę indeksu z klasy modelu danych języka C#.

Model danych jest definiowany przez klasę hotelu, która również zawiera odwołania do klasy Address. FieldBuilder przechodzi przez wiele definicji klas w celu wygenerowania złożonej struktury danych dla indeksu. Tagi metadanych są używane do definiowania atrybutów poszczególnych pól, na przykład czy można je przeszukiwać czy sortować.

Poniższe fragmenty kodu z pliku **Hotel.cs** pokazują, jak można określić jedno pole i odwołanie do innej klasy modelu danych.

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

W pliku **program.cs** indeks jest zdefiniowany przy użyciu nazwy i kolekcji pól wygenerowanej przez `FieldBuilder.Build(typeof(Hotel))` metodę, a następnie utworzony w następujący sposób:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="generating-data"></a>Generowanie danych

Prosta Klasa jest zaimplementowana w pliku **DataGenerator.cs** , aby generować dane do testowania. Jedynym celem tej klasy jest ułatwienie generowania dużej liczby dokumentów z unikatowym IDENTYFIKATORem indeksowania.

Aby uzyskać listę 100 000 hoteli z unikatowymi identyfikatorami, należy uruchomić następujące wiersze kodu:

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

Istnieją dwa rozmiary hoteli dostępnych do testowania w tym przykładzie: **małe** i  **duże**.

Schemat indeksu może mieć znaczny wpływ na szybkość indeksowania. Ze względu na ten wpływ Warto skonwertować tę klasę, aby generować dane pasujące do zamierzonego schematu indeksu po wykonaniu tego samouczka.

## <a name="4---test-batch-sizes"></a>4 — testowanie rozmiarów partii

Usługa Azure Wyszukiwanie poznawcze obsługuje następujące interfejsy API w celu załadowania jednego lub wielu dokumentów do indeksu:

+ [Dodawanie, aktualizowanie lub usuwanie dokumentów (interfejs API REST)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Klasa IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) lub [Klasa IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

Indeksowanie dokumentów w partiach znacznie poprawi wydajność indeksowania. Te partie mogą należeć do 1000 dokumentów lub maksymalnie 16 MB na partię.

Określenie optymalnego rozmiaru partii danych jest kluczowym elementem optymalizacji szybkości indeksowania. Dwa podstawowe czynniki wpływające na optymalny rozmiar partii to:

+ Schemat indeksu
+ Rozmiar danych

Ponieważ optymalny rozmiar wsadu zależy od indeksu i danych, najlepszym rozwiązaniem jest przetestowanie różnych rozmiarów partii, aby określić, jakie wyniki mają największą szybkość indeksowania dla danego scenariusza.

Poniższa funkcja przedstawia proste podejście do testowania rozmiarów partii.

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

Ponieważ nie wszystkie dokumenty mają taki sam rozmiar (choć są one w tym przykładzie), oceniamy rozmiar danych wysyłanych do usługi wyszukiwania. Wykonujemy to przy użyciu poniższej funkcji, która najpierw Konwertuje obiekt na kod JSON, a następnie określa jego rozmiar w bajtach. Ta technika pozwala określić, które rozmiary partii są najbardziej wydajne w zakresie szybkości indeksowania MB/s.

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

Funkcja wymaga `SearchClient` również liczby prób, które chcesz przetestować dla każdego rozmiaru partii. W miarę jak mogą wystąpić pewne zróżnicowanie czasów indeksowania dla każdej partii, każda partia jest domyślnie podejmowana trzy razy, aby wyniki były bardziej znaczące statystycznie.

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

Po uruchomieniu funkcji powinny zostać wyświetlone dane wyjściowe podobne do poniższego w konsoli programu:

   ![Dane wyjściowe funkcji rozmiaru partii testów](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Dane wyjściowe funkcji rozmiaru partii testów")

Określ, który rozmiar wsadu jest najbardziej wydajny, a następnie użyj tego rozmiaru partii w następnym kroku samouczka. W MB/s może zostać wyświetlona Plateau dla różnych rozmiarów partii.

## <a name="5---index-data"></a>5 — dane indeksu

Teraz, gdy został zidentyfikowany rozmiar wsadu, którego zamierzamy użyć, następnym krokiem jest rozpoczęcie indeksowania danych. Aby efektywnie indeksować dane, ten przykład:

+ Używa wielu wątków/procesów roboczych.
+ Implementuje strategię ponowień wycofywania wykładniczych.

### <a name="use-multiple-threadsworkers"></a>Korzystanie z wielu wątków/procesów roboczych

Aby w pełni wykorzystać szybkość indeksowania Wyszukiwanie poznawcze platformy Azure, prawdopodobnie trzeba będzie użyć wielu wątków, aby wysyłać żądania indeksowania wsadowego współbieżnie do usługi.  

Kilka z kluczowych zagadnień wymienionych powyżej ma wpływ na optymalną liczbę wątków. Możesz zmodyfikować ten przykład i przetestować z różnymi liczbami wątków, aby określić optymalną liczbę wątków dla danego scenariusza. Jednak o ile wiele wątków jest uruchomionych współbieżnie, powinno być możliwe korzystanie z większości korzyści z wydajności.

Podczas narastania żądań, które powodują przeszukanie usługi wyszukiwania, mogą wystąpić [kody stanu HTTP](/rest/api/searchservice/http-status-codes) wskazujące, że żądanie nie zostało w pełni zakończone. Podczas indeksowania dwa typowe kody stanu HTTP to:

+ **503 Usługa niedostępna** — ten błąd oznacza, że system jest mocno obciążony i nie można w tej chwili przetworzyć Twojego żądania.
+ **207 o wielu stanach** — ten błąd oznacza, że niektóre dokumenty zostały wykonane pomyślnie, ale co najmniej jeden z nich nie powiódł się.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Zaimplementuj strategię ponowień wycofywania wykładniczych

Jeśli wystąpi awaria, żądania powinny być ponawiane przy użyciu [strategii wycofywaniaego ponawiania prób](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Zestaw .NET SDK platformy Azure Wyszukiwanie poznawcze automatycznie ponawia próbę 503s i inne Nieudane żądania, ale musisz zaimplementować własną logikę, aby ponowić próbę 207s. Narzędzia typu open source, takie jak [Polly](https://github.com/App-vNext/Polly) , mogą również służyć do implementowania strategii ponawiania prób.

W tym przykładzie implementujemy własną strategię ponowień wykładniczych wycofywania. Aby zaimplementować tę strategię, Zacznijmy od definiowania niektórych zmiennych, w tym `maxRetryAttempts` i jako inicjału `delay` dla żądania zakończonego niepowodzeniem:

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Wyniki operacji indeksowania są przechowywane w zmiennej `IndexDocumentResult result` . Ta zmienna jest ważna, ponieważ pozwala sprawdzić, czy nie podano dokumentów w partii, jak pokazano poniżej. W przypadku awarii częściowej zostanie utworzona nowa partia oparta na IDENTYFIKATORze dokumentów zakończonych niepowodzeniem.

`RequestFailedException` wyjątki należy również przechwycić, ponieważ wskazują, że żądanie nie zostało całkowicie zakończone i należy również ponowić próbę.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

W tym miejscu zawijamy kod wykładniczy wycofywania do funkcji, aby można było ją łatwo wywołać.

Kolejna funkcja jest następnie tworzona w celu zarządzania aktywnymi wątkami. Dla uproszczenia ta funkcja nie jest uwzględniona w tym miejscu, ale można ją znaleźć w [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs). Funkcję można wywołać przy użyciu następującego polecenia `hotels` , gdzie to dane, które chcemy przekazać, `1000` to rozmiar wsadu i `8` Liczba współbieżnych wątków:

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

Po uruchomieniu funkcji powinny zostać wyświetlone dane wyjściowe podobne do poniższych:

![Dane wyjściowe funkcji indeksu](media/tutorial-optimize-data-indexing/index-data-start.png "Dane wyjściowe funkcji indeksu")

Gdy partia dokumentów nie powiedzie się, zostanie wydrukowany błąd wskazujący awarię i że trwa ponawianie próby wykonania partii:

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

Po zakończeniu działania funkcji można sprawdzić, czy wszystkie dokumenty zostały dodane do indeksu.

## <a name="6---explore-index"></a>6 — Eksplorowanie indeksu

Wypełniony indeks wyszukiwania można eksplorować po uruchomieniu programu programowo lub za pomocą [**Eksploratora wyszukiwania**](search-explorer.md) w portalu.

### <a name="programatically"></a>Programowo

Dostępne są dwie główne opcje sprawdzania liczby dokumentów w indeksie: [interfejs API zliczania dokumentów](/rest/api/searchservice/count-documents) i [interfejs API pobierania statystyk indeksu](/rest/api/searchservice/get-index-statistics). Obie ścieżki mogą wymagać dodatkowego czasu na aktualizację, dlatego nie jest to alarmne, jeśli liczba zwróconych dokumentów jest niższa niż oczekiwano.

#### <a name="count-documents"></a>Liczenie dokumentów

Operacja Count Documents Pobiera liczbę dokumentów w indeksie wyszukiwania:

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### <a name="get-index-statistics"></a>Pobierz statystyki indeksu

Operacja Pobierz statystyki indeksu zwraca liczbę dokumentów dla bieżącego indeksu oraz użycie magazynu. Aktualizacja statystyk indeksu będzie trwać dłużej niż liczba dokumentów do zaktualizowania.

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### <a name="azure-portal"></a>Witryna Azure Portal

W Azure Portal Otwórz stronę **Przegląd** usługi wyszukiwania i Znajdź indeks **Optymalizacja —** indeksowanie na liście **indeksów** .

  ![Lista indeksów usługi Azure Wyszukiwanie poznawcze](media/tutorial-optimize-data-indexing/portal-output.png "Lista indeksów usługi Azure Wyszukiwanie poznawcze")

*Liczba dokumentów* i *Rozmiar magazynu* są oparte na [interfejsie API pobierania statystyk indeksu](/rest/api/searchservice/get-index-statistics) i może potrwać kilka minut.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

W przypadku wczesnych eksperymentalnych etapów tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod dla tego samouczka sprawdza istniejące indeksy i usuwa je, aby można było ponownie uruchomić kod.

Możesz również użyć portalu, aby usunąć indeksy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już koncepcję pozyskiwania danych, przyjrzyjmy się bliżej architekturze zapytań Lucene i jak działa wyszukiwanie pełnotekstowe w usłudze Azure Wyszukiwanie poznawcze.

> [!div class="nextstepaction"]
> [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)