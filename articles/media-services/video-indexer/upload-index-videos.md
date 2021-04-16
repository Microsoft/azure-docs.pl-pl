---
title: Przekazywanie i indeksowanie plików wideo za pomocą usługi Video Indexer
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak przy użyciu interfejsów API przekazywać i indeksować pliki wideo za pomocą usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/04/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 90fca4342b1fe04adef97a1a4c1c2166ca7ec51e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532490"
---
# <a name="upload-and-index-your-videos"></a>Przekazywanie i indeksowanie plików wideo  

Po przesłaniu pliku wideo Video Indexer (opcjonalnie) zakodowane wideo (omówione w artykule). Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. W przypadku opcji płatnej utworzone zostaje konto usługi Video Indexer [połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services](connect-to-azure.md). Płacisz za minuty indeksowane. Aby uzyskać więcej informacji, [zobacz cennik Media Services minut.](https://azure.microsoft.com/pricing/details/media-services/)

Podczas przekazywania plików wideo za pomocą interfejsu API usługi Video Indexer dostępne są następujące opcje przekazywania: 

* przekazywanie pliku wideo z adresu URL (opcja preferowana),
* wysyłanie pliku wideo w postaci tablicy bajtów w treści żądania,
* używanie istniejącego elementu zawartości usługi Azure Media Services przez podanie [identyfikatora elementu zawartości](../latest/assets-concept.md) (obsługiwane tylko w przypadku płatnych kont).

W tym artykule pokazano, jak przekazywać i indeksować filmy wideo przy użyciu tych opcji:

* [Witryna internetowa usługi Video Indexer](#upload-and-index-a-video-using-the-video-indexer-website) 
* [Interfejsy API usługi Video Indexer](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Obsługiwane formaty plików dla Video Indexer

Listę formatów [plików, których](../latest/encode-media-encoder-standard-formats-reference.md) można używać z formatami plików, można znaleźć w artykule o formatach plików wejściowych Video Indexer.

## <a name="video-files-storage"></a>Magazyn plików wideo

- W przypadku Video Indexer konta Video Indexer konto usługi Video Indexer połączone z subskrypcją platformy Azure i kontem Azure Media Services subskrypcji. Aby uzyskać więcej informacji, zobacz [Create a Video Indexer account connected to Azure (Tworzenie](connect-to-azure.md)konta Video Indexer platformy Azure).
- Pliki wideo są przechowywane w usłudze Azure Storage przez Azure Media Services. Nie ma żadnych ograniczeń czasu.
- Zawsze możesz usunąć pliki wideo i audio, a także wszelkie metadane i szczegółowe informacje wyodrębnione z nich przez Video Indexer. Po usunięciu pliku z Video Indexer plik oraz jego metadane i szczegółowe informacje zostaną trwale usunięte z Video Indexer. Jeśli jednak zaimplementowano własne rozwiązanie do tworzenia kopii zapasowych w usłudze Azure Storage, plik pozostanie w magazynie platformy Azure.
- Trwałość filmu wideo jest identyczna, niezależnie od tego, czy przekazywanie odbywa się w witrynie Video Indexer internetowej, czy przy użyciu interfejsu API przekazywania.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Przekazywanie i indeksowanie wideo przy użyciu witryny Video Indexer internetowej

> [!NOTE]
> Nazwa filmu wideo nie może być dłuższa niż 80 znaków.

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
1. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Przekaż":::
1. Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Postęp przekazywania":::
1. Po Video Indexer, otrzymasz wiadomość e-mail z linkiem do filmu wideo i krótkim opisem tego, co znaleziono w filmie wideo. Na przykład: osoby, tematy i wyniki przetwarzania OCR.

## <a name="upload-and-index-with-api"></a>Przekazywanie i indeksowanie za pomocą interfejsu API

Interfejs API [przekazywania wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) umożliwia przekazywanie i indeksowanie wideo na podstawie adresu URL. Następujący przykład kodu zawiera kod z komentarzem, który pokazuje sposób przekazywania tablicy bajtów. 

### <a name="configurations-and-params"></a>Konfiguracje i parametry

W tej sekcji opisano niektóre parametry opcjonalne i wyjaśniono, kiedy należy je ustawić. Aby uzyskać najbardziej aktualne informacje o paramach, zobacz Interfejs API [przekazywania wideo.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

#### <a name="externalid"></a>externalID 

Ten parametr umożliwia określenie identyfikatora, który zostanie skojarzony z plikiem wideo. Ten identyfikator można zastosować do integracji z zewnętrznym systemem zarządzania zawartością wideo (VCM, Video Content Management). Pliki wideo znajdujące się w portalu usługi Video Indexer można wyszukiwać za pomocą tego określonego identyfikatora zewnętrznego.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Inne zagadnienia

- Usługa Video Indexer zwraca wszelkie istniejące parametry podane w oryginalnym adresie URL.
- Podany adres URL musi być zakodowany.

#### <a name="indexingpreset"></a>indexingPreset

Użyj tego parametru, aby zdefiniować pakiet AI, który chcesz zastosować do pliku audio lub wideo. Parametr ten służy do konfigurowania procesu indeksowania. Można określić następujące wartości:

- `AudioOnly` — Indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu tylko dźwięku (ignorowanie wideo).
- `VideoOnly` — Indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu tylko wideo (ignorowanie dźwięku).
- `Default` — Indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu zarówno dźwięku, jak i wideo.
- `DefaultWithNoiseReduction` — Indeksowanie i wyodrębnianie szczegółowych informacji zarówno z dźwięku, jak i wideo, przy jednoczesnym zastosowaniu algorytmów redukcji szumu w strumieniu audio.

    Wartość `DefaultWithNoiseReduction` jest teraz mapowana na domyślne ustawienie wstępne (przestarzałe).
- `BasicAudio` — Indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu tylko dźwięku (ignorowanie wideo), w tym tylko podstawowych funkcji audio (transkrypcja, tłumaczenie, formatowanie podpisów i napisów wyjściowych).
 - `AdvancedAudio` — Indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu tylko dźwięku (ignorowanie wideo), w tym zaawansowanych funkcji audio (wykrywanie zdarzeń audio) oprócz standardowej analizy dźwięku.

> [!NOTE]
> Video Indexer obejmuje maksymalnie dwie ścieżki audio. Jeśli w pliku znajduje się więcej ścieżek dźwiękowych, będą one traktowane jako jedna ścieżka.<br/>
Jeśli chcesz indeksować ścieżki oddzielnie, musisz wyodrębnić odpowiedni plik dźwiękowy i indeksować go jako `AudioOnly` .

Cena zależy od wybranej opcji indeksowania. Aby uzyskać więcej informacji, zobacz [Media Services cennika](https://azure.microsoft.com/pricing/details/media-services/).

#### <a name="priority"></a>priority

Usługa Video Indexer indeksuje filmy wideo zgodnie z ich priorytetem. Użyj parametru **priority**, aby określić priorytet indeksu. Prawidłowe są następujące wartości: **Low** (niski), **Normal** (normalny — wartość domyślna), **High** (wysoki).

Parametr **priority** jest obsługiwany tylko w przypadku płatnych kont.

#### <a name="streamingpreset"></a>streamingPreset

Po przekazaniu pliku wideo usługa Video Indexer opcjonalnie koduje ten plik. Następnie przechodzi do indeksowania i analizowania pliku wideo. Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z identyfikatorem pliku wideo.  

W przypadku korzystania z interfejsu API [przekazywania pliku wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) lub [ponownego indeksowania pliku wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) jednym z parametrów opcjonalnych jest `streamingPreset`. W razie ustawienia dla parametru `streamingPreset` wartości `Default`, `SingleBitrate` lub `AdaptiveBitrate` wywoływany jest proces kodowania. Po zakończeniu zadań indeksowania i kodowania plik wideo jest publikowany, aby można go było również przesyłać strumieniowo. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać plik wideo, musi mieć stan **Uruchomiony**.

W przypadku wartości SingleBitrate koszt kodera standardowego będzie mieć zastosowanie do danych wyjściowych. Jeśli wysokość wideo jest większa lub równa 720, Video Indexer koduje go jako 1280 x 720. W przeciwnym razie jako 640 x 468.
Ustawieniem domyślnym jest [kodowanie z zachowaniem zawartości](../latest/encode-content-aware-concept.md).

Aby można było uruchomić zadania indeksowania i kodowania, dla [konta usługi Azure Media Services połączonego z kontem usługi Video Indexer](connect-to-azure.md) wymagane są jednostki zarezerwowane. Aby uzyskać więcej informacji, zobacz [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Skalowanie przetwarzania multimediów). Ponieważ te zadania wymagają intensywnego przetwarzania, zdecydowanie zaleca się typ jednostki S3. Liczba jednostek zarezerwowanych określa maksymalną liczbę zadań, które mogą działać równolegle. Zalecenie dotyczące planu bazowego to 10 jednostek zarezerwowanych S3. 

Jeśli chcesz tylko zaindeksować plik wideo bez kodowania go, ustaw dla parametru `streamingPreset` wartość `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Adres URL pliku wideo/audio do zaindeksowania. Ten adres URL musi wskazywać plik multimedialny (strony HTML nie są obsługiwane). Plik może być chroniony przez token dostępu podany w ramach identyfikatora URI, a punkt końcowy obsługujący plik musi być zabezpieczony za pomocą protokołu TLS 1.2 lub nowszej wersji. Adres URL musi być zakodowany. 

Jeśli parametr `videoUrl` nie zostanie określony, usługa Video Indexer oczekuje przekazania pliku jako zawartości treści wieloczęściowej/formularza.

### <a name="code-sample"></a>Przykład kodu

W poniższym fragmencie kodu języka C# pokazano używanie wszystkich interfejsów API usługi Video Indexer razem.

**Instrukcje dotyczące uruchamiania następującego przykładowego kodu**

Po skopiowaniu tego kodu na platformę deweloperską należy podać dwa parametry: klucz uwierzytelniania API Management adres URL wideo.

* Klucz interfejsu API — klucz interfejsu API to osobisty klucz subskrypcji usługi API Management, który umożliwia uzyskanie tokenu dostępu w celu wykonywania operacji na Video Indexer api. 

    Aby uzyskać klucz interfejsu API, przejdź przez ten przepływ:

    * Przejdź do strony https://api-portal.videoindexer.ai/
    * Zaloguj się
    * Przejdź do **subskrypcji**  ->  **autoryzacji**  ->  **autoryzacji produktów**
    * Kopiowanie klucza **podstawowego**
* Adres URL wideo — adres URL pliku wideo/audio do indeksowania. Ten adres URL musi wskazywać plik multimedialny (strony HTML nie są obsługiwane). Plik może być chroniony przez token dostępu podany w ramach identyfikatora URI, a punkt końcowy obsługujący plik musi być zabezpieczony za pomocą protokołu TLS 1.2 lub nowszej wersji. Adres URL musi być zakodowany.

Wynik pomyślnego uruchomienia przykładowego kodu będzie zawierać adres URL widżetu szczegółowych informacji i adres URL widżetu odtwarzacza, który umożliwi przeanalizowanie przekazanych szczegółowych informacji i wideo. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Typowe błędy

Kody stanu wymienione w poniższej tabeli mogą być zwracane przez operację przekazywania.

|Kod stanu|ErrorType (w treści odpowiedzi)|Opis|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Ten sam plik wideo jest już w trakcie przetwarzania w ramach danego konta.|
|400|VIDEO_ALREADY_FAILED|Tego samego pliku wideo nie udało się przetworzyć w ramach danego konta mniej niż 2 godziny temu. Klienci interfejsu API powinni poczekać co najmniej 2 godziny przed ponownym przekazaniem pliku wideo.|
|429||Konta w wersji próbnej mogą przekazywać 5 danych na minutę. Konta płatne mają dozwolonych 50 przekazywania na minutę.|

## <a name="uploading-considerations-and-limitations"></a>Zagadnienia i ograniczenia dotyczące przekazywania
 
- Nazwa filmu wideo nie może być dłuższa niż 80 znaków.
- W przypadku przekazywania pliku wideo na podstawie adresu URL (opcja preferowana) punkt końcowy musi być zabezpieczony za pomocą protokołu TLS 1.2 (lub nowszej wersji).
- Rozmiar przekazywania z opcją adresu URL jest ograniczony do 30 GB.
- Długość adresu URL żądania jest ograniczona do 6144 znaków, a długość adresu URL ciągu zapytania jest ograniczona do 4096 znaków.
- Rozmiar przekazywania z opcją tablicy bajtów jest ograniczony do 2 GB.
- Opcja tablicy bajtów przekracza limit czasu po 30 minutach.
- Adres URL podany w `videoURL` param musi być zakodowany.
- Indeksowanie elementów zawartości usługi Media Services ma takie samo ograniczenie jak indeksowanie z adresu URL.
- W przypadku pojedynczego pliku usługa Video Indexer ma limit maksymalnego czasu trwania wynoszący 4 godziny.
- Adres URL musi być dostępny (na przykład być publicznym adresem URL). 

    Jeśli jest to prywatny adres URL, w żądaniu musi być podany token dostępu.
- Adres URL musi wskazać prawidłowy plik multimedialny, a nie stronę internetową, taką jak link do `www.youtube.com` strony.
- Na koncie płatnym można przekazywać do 50 filmów na minutę, a na koncie próbnym do 5 filmów na minutę.

> [!Tip]
> Zalecane jest korzystanie z platformy .NET w wersji 4.6.2. lub nowszej, ponieważ starsze wersje platformy .NET nie obsługują domyślnie protokołu TLS 1.2.
>
> Jeśli musisz użyć starszej platformy .NET, dodaj jeden wiersz do swojego kodu przed wprowadzeniem wywołania interfejsu API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>Następne kroki

[Badanie danych wyjściowych usługi Azure Video Indexer przez interfejs API](video-indexer-output-json-v2.md)
