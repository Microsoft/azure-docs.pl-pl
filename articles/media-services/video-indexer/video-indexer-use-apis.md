---
title: Korzystanie z interfejsu API usługi Video Indexer
titleSuffix: Azure Media Services
description: W tym artykule opisano, jak rozpocząć pracę z Azure Media Services Video Indexer API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a445e9869b0cd9928d95364f39e60fc892214b9a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532462"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Samouczek: używanie interfejsu API usługi Video Indexer

Video Indexer integruje różne technologie sztucznej inteligencji (AI) audio i wideo oferowane przez firmę Microsoft w jednej zintegrowanej usłudze, co upraszcza opracowywanie rozwiązań. Interfejsy API zaprojektowano w celu umożliwienia deweloperom skoncentrowania się na technologii AI w zakresie multimediów bez martwienia się o skalę, globalny zasięg, dostępność i niezawodność platform w chmurze. Za pomocą interfejsu API można przekazywać pliki, uzyskać szczegółowe informacje o wideo, uzyskać adresy URL osadzanych szczegółowych informacji i widżetów odtwarzacza i nie tylko.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. W przypadku opcji płatnej tworzysz konto Video Indexer połączone z subskrypcją platformy Azure i konto Azure Media Services [usługi](connect-to-azure.md). Płacisz za minuty indeksowane. Aby uzyskać więcej informacji, [zobacz cennik Media Services minut.](https://azure.microsoft.com/pricing/details/media-services/)

W tym artykule pokazano, jak deweloperzy mogą korzystać z [interfejsu API usługi Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Subskrybowanie interfejsu API

1. Zaloguj się w [portalu dla deweloperów usługi Video Indexer](https://api-portal.videoindexer.ai/).

    Zapoznaj się z uwagami o wersji dotyczącymi [informacji logowania.](release-notes.md#october-2020)
    
     ![Zaloguj się do witryny Video Indexer Portal deweloperów](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Musisz użyć tego samego dostawcy, który został użyty podczas tworzenia konta w usłudze Video Indexer.
   > * Osobistych kont Google i Microsoft (Outlook/Live) można używać tylko w przypadku kont w wersji próbnej. Konta połączone z platformą Azure wymagają usługi Azure AD.
   > * W jednej wiadomości e-mail może być tylko jedno aktywne konto. Jeśli użytkownik spróbuje zalogować się w usłudze LinkedIn, a później w usłudze dla firmy Google, na drugiej stronie zostanie wyświetlana strona błędu z informacją, że użytkownik user@gmail.com user@gmail.com już istnieje.
2. Subskrybuj.

    Wybierz [kartę](https://api-portal.videoindexer.ai/products) Produkty. Następnie wybierz pozycję Autoryzacja i zasubskrybuj.
    
    ![Karta Products (Produkty) w Video Indexer Developer Portal](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > Nowi użytkownicy automatycznie subskrybują autoryzację.
    
    Po zasubskrybowania możesz znaleźć swoją subskrypcję w obszarze **Autoryzacja**  ->  **produktów.** Na stronie subskrypcji znajdują się klucze podstawowe i pomocnicze. Te klucze należy chronić. Powinno się ich używać tylko w kodzie serwera. Nie powinny być dostępne po stronie klienta (pliki js, html itd.).

    ![Subskrypcja i klucze w Video Indexer Portal deweloperów](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Usługa Video Indexer może używać klucza pojedynczej subskrypcji do łączenia się z wieloma kontami usługi Video Indexer. Te konta usługi Video Indexer można następnie połączyć z różnymi kontami usługi Media Services.

## <a name="obtain-access-token-using-the-authorization-api"></a>Uzyskiwanie tokenu dostępu przy użyciu interfejsu API autoryzacji

Po zasubskrybowania interfejsu API autoryzacji można uzyskać tokeny dostępu. Te tokeny dostępu są używane do uwierzytelniania w interfejsie API operacji.

Każde wywołanie interfejsu API operacji powinno być skojarzone z tokenem dostępu zgodnym z zakresem autoryzacji wywołania.

- Poziom użytkownika: tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na **poziomie** użytkownika. Na przykład uzyskiwanie informacji o skojarzonych kontach.
- Poziom konta: tokeny dostępu na poziomie konta umożliwiają wykonywanie operacji na **poziomie** konta lub **wideo.** Możesz na przykład przekazać wideo, wyświetlić listę wszystkich filmów wideo, uzyskać szczegółowe informacje o wideo itp.
- Poziom wideo: tokeny dostępu na poziomie wideo umożliwiają wykonywanie operacji na **określonym** wideo. Możesz na przykład pobierać szczegółowe informacje o wideo, pobierać napisy, pobierać widżety itp.

Możesz kontrolować, czy te tokeny są tylko do odczytu, czy też zezwalają na edycję, określając **wartość allowEdit=true/false.**

W przypadku większości scenariuszy serwer-serwer prawdopodobnie będziesz używać tego samego  **tokenu** konta, ponieważ obejmuje on zarówno operacje konta, jak i **operacje wideo.** Jeśli jednak planujesz wywołania po stronie klienta do usługi Video Indexer (na przykład z języka JavaScript), należy użyć tokenu dostępu do wideo, aby uniemożliwić klientom uzyskiwanie dostępu do całego konta.  Jest to również powód, dla którego podczas osadzania kodu klienta usługi Video Indexer  w kliencie (na przykład za  pomocą widżetu Pobierz szczegółowe informacje lub Pobierz widżet **odtwarzacza)** należy podać token dostępu do wideo.

Aby ułatwić sprawę, można uzyskać informacje o kontach za pomocą pozycji **Authorization** API (Interfejs API autoryzacji) > **GetAccounts** (Pobierz konta) bez wcześniejszego pobierania tokenu użytkownika. Można również zażądać informacji o kontach z ważnymi tokenami, co umożliwia pominięcie dodatkowego wywołania w celu pobrania tokenu konta.

Tokeny dostępu wygasają po godzinie. Przed skorzystaniem z interfejsu API operacji upewnij się, że token dostępu jest ważny. Jeśli wygaśnie, ponownie wywołaj interfejs API autoryzacji, aby uzyskać nowy token dostępu.

Wszystko jest gotowe do rozpoczęcia integracji z interfejsem API. Zapoznaj się ze [szczegółowymi opisami poszczególnych interfejsów API REST usługi Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Identyfikator konta

Identyfikator konta to parametr wymagany we wszystkich wywołaniach interfejsu API operacji. Identyfikator konta jest identyfikatorem GUID, który można uzyskać w jeden z następujących sposobów:

* Uzyskiwanie identyfikatora konta za pomocą **witryny internetowej usługi Video Indexer**:

    1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
    2. Przejdź do strony **Settings** (Ustawienia).
    3. Skopiuj identyfikator konta.

        ![Video Indexer i identyfikator konta](./media/video-indexer-use-apis/account-id.png)

* Uzyskiwanie identyfikatora konta za pomocą **portalu dla deweloperów usługi Video Indexer**.

    Użyj [interfejsu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) API Pobierz konto.

    > [!TIP]
    > Tokeny dostępu dla kont możesz wygenerować, definiując atrybut`generateAccessTokens=true`.

* Pobierz identyfikator konta z adresu URL strony odtwarzacza na swoim koncie.

    Podczas odtwarzania pliku wideo identyfikator jest podany po sekcji `accounts` i przed sekcją `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Zalecenia

W tej sekcji przedstawiono kilka zaleceń dotyczących korzystania z interfejsu API usługi Video Indexer.

- Jeśli planujesz przekazanie pliku wideo, zaleca się umieszczenie pliku w publicznie dostępnej lokalizacji sieciowej (na przykład na koncie Azure Blob Storage). Uzyskaj link do pliku wideo, a następnie podaj ten adres URL jako parametr przekazywania pliku.

    Adres URL przekazywany do usługi Video Indexer musi wskazywać plik multimedialny (audio lub wideo). Łatwą weryfikacją adresu URL (lub adresu URL sygnatury dostępu współdzielonego) jest wklejenie go w przeglądarce. Jeśli plik zacznie odtwarzać/pobierać, prawdopodobnie jest to dobry adres URL. Jeśli przeglądarka renderuje wizualizację, prawdopodobnie nie jest to link do pliku, ale do strony HTML.

- Wywołanie dla konkretnego pliku wideo interfejsu API, który pobiera szczegółowe informacje o wideo, powoduje pobranie w zawartości odpowiedzi szczegółowych danych wyjściowych JSON. [W tym temacie omówiono szczegółowo zwracane informacje JSON](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Przykład kodu

W poniższym fragmencie kodu języka C# pokazano używanie wszystkich interfejsów API usługi Video Indexer razem.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem usuń zasoby, których nie planujesz używać.

## <a name="see-also"></a>Zobacz też

- [Omówienie usługi Video Indexer](video-indexer-overview.md)
- [Regiony](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Następne kroki

- [Badanie szczegółów wyjściowego danych JSON](video-indexer-output-json-v2.md)
- Zapoznaj się z [przykładowym kodem,](https://github.com/Azure-Samples/media-services-video-indexer) który pokazuje ważny aspekt przekazywania i indeksowania wideo. Poniższy kod daje dobry pomysł na sposób użycia naszego interfejsu API dla podstawowych funkcji. Pamiętaj o przeczytaniu komentarzy w tekście i zwróć uwagę na nasze porady dotyczące najlepszych rozwiązań.

