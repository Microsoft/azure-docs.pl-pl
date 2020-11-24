---
title: 'Samouczek: Moderowanie wideo i transkrypcji na platformie .NET — usługa Content Moderator'
titleSuffix: Azure Cognitive Services
description: W tym samouczku wyjaśniono, jak utworzyć pełne rozwiązanie z zakresu moderowania wideo i transkrypcji przez moderowanie wspomagane maszynowo oraz utworzenie przeglądu wymagającego udziału człowieka (human-in-the-loop).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: ad689c746a0f4d7232e7f61982fb8c4f735cbe34
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95737806"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Samouczek: Moderowanie wideo i transkrypcji

W ramach tego samouczka nauczysz się, jak utworzyć kompletne rozwiązanie do moderowania wideo i transkrypcji z integracją z pomocąą maszynową i analizą przez człowieka.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Kompresowanie danych wejściowych wideo, aby przyspieszyć przetwarzanie
> - Moderowanie wideo, aby pobrać zrzuty i ramki za pomocą usługi Insights
> - Korzystanie ze znaczników czasu ramek w celu utworzenia miniatur (obrazy)
> - Przesyłanie znaczników czasu i miniatur w celu utworzenia przeglądów wideo
> - Konwertowanie zamiany mowy na tekst w wideo (transkrypcja) za pomocą interfejsu API procesora Media Indexer
> - Moderowanie transkrypcji przy użyciu usługi moderowania tekstu
> - Dodawanie moderowanej transkrypcji do przeglądu wideo

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz konto w witrynie internetowej [Narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/) i utworzyć niestandardowe znaczniki dla funkcji, które chcesz zidentyfikować. Zobacz [Używanie tagów](./review-tool-user-guide/configure.md#tags) , jeśli potrzebujesz pomocy w tym kroku.

    ![zrzut ekranu tagów niestandardowych moderowania wideo](images/video-tutorial-custom-tags.png)
- Do uruchomienia przykładowej aplikacji jest potrzebne konto platformy Azure, zasób Azure Media Services, zasób usługi Azure Content Moderator i poświadczenia Azure Active Directory. Aby uzyskać instrukcje dotyczące sposobu uzyskania tych zasobów, zobacz Przewodnik po [interfejsie API moderowania wideo](video-moderation-api.md) .
- Pobierz [aplikację konsolową Przejrzyj wideo](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) z usługi GitHub.

## <a name="enter-credentials"></a>Wprowadź poświadczenia

Edytuj `App.config` plik i dodaj Active Directory nazwę dzierżawy, punkty końcowe usługi i klucze subskrypcji wskazane przez `#####` . Potrzebne są następujące informacje:

|Klucz|Opis|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Punkt końcowy interfejsu API platformy Azure Media Services (AMS)|
|`ClientSecret`|Klucz subskrypcji dla platformy Azure Media Services|
|`ClientId`|Identyfikator klienta dla platformy Azure Media Services|
|`AzureAdTenantName`|Nazwa dzierżawcy usługi Active Directory reprezentująca Twoją organizację|
|`ContentModeratorReviewApiSubscriptionKey`|Klucz subskrypcji dla interfejsu API przeglądu usługi Content Moderator|
|`ContentModeratorApiEndpoint`|Punkt końcowy dla interfejsu API usługi Content Moderator|
|`ContentModeratorTeamId`|Identyfikator zespołu dla usługi Content Moderator|

## <a name="examine-the-main-code"></a>Badanie kodu głównego

**Program** klasy w _program.cs_ jest głównym punktem wejścia do aplikacji do moderowania wideo.

### <a name="methods-of-program-class"></a>Metody klasy programu

|Metoda|Opis|
|-|-|
|`Main`|Analizuje wiersz polecenia, zbiera dane wejściowe użytkownika, a następnie uruchamia przetwarzanie.|
|`ProcessVideo`|Kompresuje, przekazuje, moderuje i tworzy przeglądy wideo.|
|`CreateVideoStreamingRequest`|Tworzy strumień do przekazywania plików wideo.|
|`GetUserInputs`|Zbiera dane wejściowe użytkownika; używana, gdy nie podano opcji wiersza polecenia.|
|`Initialize`|Inicjuje obiekty niezbędne do procesu moderowania.|

### <a name="the-main-method"></a>Metoda Main

Wykonywanie rozpoczyna się od metody `Main()`, dlatego stanowi ona punkt wyjścia do zapoznania się z procesem moderowania wideo.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

Metoda `Main()` obsługuje następujące argumenty wiersza polecenia:

- Ścieżka do katalogu zawierającego pliki wideo MPEG-4, które mają być przesłane do moderacji. Wszystkie pliki `*.mp4` w tym katalogu i jego podkatalogach są przesyłane do moderacji.
- Opcjonalnie, flaga logiczna (prawda/fałsz) wskazująca, czy do moderowania audio mają być generowane transkrypcje tekstu.

Jeśli nie argumentów wiersza polecenia, metoda `Main()` wywołuje `GetUserInputs()`. Ta metoda prosi użytkownika o wprowadzenie ścieżki do pojedynczego pliku wideo oraz określenie, czy ma zostać wygenerowana transkrypcja tekstu.

> [!NOTE]
> Aplikacja konsolowa używa [interfejsu API Azure Media Indexer](../../media-services/previous/legacy-components.md) , aby generować transkrypcje z przesłanej ścieżki audio wideo. Wyniki są podane w formacie WebVTT. Aby uzyskać więcej informacji na temat tego formatu, zobacz temat [Web Video Text Tracks Format](https://developer.mozilla.org/docs/Web/API/WebVTT_API) (Format ścieżek tekstowych wideo w sieci Web).

### <a name="initialize-and-processvideo-methods"></a>Inicjowanie metod ProcessVideo

Niezależnie od tego, czy opcje programu pochodzą z wiersza polecenia czy danych wejściowych użytkownika interakcyjnego, metoda `Main()` wywołuje następnie `Initialize()` w celu utworzenia następujących wystąpień:

|Klasa|Opis|
|-|-|
|`AMSComponent`|Kompresuje pliki wideo przed przesłaniem ich do moderacji.|
|`AMSconfigurations`|Interfejs do danych konfiguracji aplikacji znajdujący się w pliku `App.config`.|
|`VideoModerator`| Przekazywanie, kodowanie, szyfrowanie i moderowanie przy użyciu zestawu SDK platformy AMS.|
|`VideoReviewApi`|Zarządza przeglądami wideo w usłudze Content Moderator.|

Te klasy (z wyjątkiem `AMSConfigurations`, która jest prosta w użyciu) zostaną szczegółowo omówione w kolejnych częściach tego samouczka.

Na koniec pliki wideo są pojedynczo przetwarzane — dla każdego z nich jest wywoływana metoda `ProcessVideo()`.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

Metoda `ProcessVideo()` jest dość prosta w obsłudze. Wykonuje kolejno następujące operacje:

- Kompresowanie wideo
- Przekazywanie wideo do zasobu platformy Azure Media Services
- Tworzenie zadania platformy AMS do moderowania wideo
- Tworzenie przeglądu wideo w usłudze Content Moderator

W poniższych sekcjach omówiono bardziej szczegółowo niektóre procesy wywoływane przez metodę `ProcessVideo()`. 

## <a name="compress-the-video"></a>Kompresuj wideo

Aby zminimalizować ruch sieciowy, aplikacja konwertuje pliki wideo do formatu H.264 (MPEG-4 AVC) i skaluje je do maksymalnej szerokości 640 pikseli. Koder-dekoder H.264 jest zalecany ze względu na wysoką wydajność (współczynnik kompresji). Kompresja jest wykonywana przy użyciu bezpłatnego narzędzia wiersza polecenia `ffmpeg`, które zostało dołączone do folderu `Lib` usługi Visual Studio. Pliki wejściowe mogą być w dowolnym formacie obsługiwanym przez wiersz polecenia `ffmpeg`, który obsługuje najpopularniejsze formaty plików wideo oraz koderów-dekoderów.

> [!NOTE]
> W przypadku uruchamiania programu przy użyciu opcji wiersza polecenia należy podać katalog zawierający pliki wideo, które mają być przesłane do moderacji. Wszystkie pliki w tym katalogu mające rozszerzenie nazwy pliku `.mp4` zostaną przetworzone. Aby przetwarzać inne rozszerzenia nazw plików, zaktualizuj `Main()` metodę w _program.cs_ , aby uwzględnić wymagane rozszerzenia.

Kodem, który kompresuje pojedynczy plik wideo jest klasa `AmsComponent` w pliku `AMSComponent.cs`. Metodą odpowiedzialną za tę funkcjonalność jest `CompressVideo()`, jak przedstawiono poniżej.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

Ten kod wykonuje następujące kroki:

- Sprawdza, czy konfiguracja w pliku `App.config` zawiera wszystkie niezbędne dane.
- Sprawdza, czy są dane binarne `ffmpeg`.
- Tworzy nazwę pliku danych wyjściowych przez dołączenie `_c.mp4` do nazwy podstawowej pliku (np. `Example.mp4` -> `Example_c.mp4`).
- Tworzy ciąg wiersza polecenia w celu wykonania konwersji.
- Uruchamia proces `ffmpeg` przy użyciu wiersza polecenia.
- Czeka, aż wideo zostanie przetworzone.

> [!NOTE]
> Jeżeli wiesz, że wideo zostały już skompresowane przy użyciu H.264 i mają odpowiednie wymiary, możesz zapisać ponownie `CompressVideo()`, aby pominąć kompresję.

Metoda zwraca nazwę skompresowanego pliku wyjściowego.

## <a name="upload-and-moderate-the-video"></a>Przekazywanie i umiarkowane wideo

Wideo należy przechowywać na platformie Azure Media Services, zanim będzie je można przetworzyć przy użyciu usługi moderowania zawartości. Klasa **programu** w _program.cs_ ma krótką metodę `CreateVideoStreamingRequest()` , która zwraca obiekt reprezentujący żądanie przesyłania strumieniowego używany do przekazywania wideo.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

Uzyskany obiekt `UploadVideoStreamRequest` jest zdefiniowany w pliku `UploadVideoStreamRequest.cs` (a jego obiekt nadrzędny, `UploadVideoRequest`, w pliku `UploadVideoRequest.cs`). Tych klas tutaj nie przedstawiono; są krótkie, a ich rola ogranicza się tylko do przechowywania skompresowanych danych wideo oraz informacji na ich temat. Kolejna klasa zawierająca jedynie dane, `UploadAssetResult` (`UploadAssetResult.cs`), służy do przechowywania wyników procesu przekazywania. Teraz możemy zrozumieć linie w metodzie `ProcessVideo()`:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Te linie wykonują następujące zadania:

- Tworzą metodę `UploadVideoStreamRequest` do przekazania skompresowanego wideo.
- Ustawiają flagę `GenerateVTT` żądania, jeśli użytkownik zażądał transkrypcji tekstu.
- Wywołują metodę `CreateAzureMediaServicesJobToModerateVideo()`, aby wykonać przekazywanie i otrzymać wynik.

## <a name="examine-video-moderation-code"></a>Sprawdzanie kodu moderowania wideo

Metoda `CreateAzureMediaServicesJobToModerateVideo()` znajduje się w pliku `VideoModerator.cs`, który zawiera większą część kodu współpracującego z platformą Azure Media Services. Kod źródłowy metody został przedstawiony w następującym fragmencie.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Ten kod wykonuje następujące zadania:

- Tworzy zadanie platformy AMS do wykonania przetwarzania.
- Dodaje zadania kodowania pliku wideo, jego moderowania oraz generowania transkrypcji tekstu.
- Przesyła zadanie przekazując plik i rozpoczynając przetwarzanie.
- Pobiera wyniki moderacji, transkrypcję tekstu (jeśli jest to wymagane) oraz inne informacje.

## <a name="sample-video-moderation-output"></a>Przykładowe dane wyjściowe moderowania wideo

Wynikiem zadania moderowania wideo (zobacz [przewodnik Szybki start dotyczący moderowania wideo](video-moderation-api.md)) jest struktura danych JSON zawierająca wyniki moderowania. Te wyniki zawierają podział fragmentów (zrzuty) w ramach wideo, z których każdy zawiera zdarzenia (klipy) wraz z ramkami kluczy oflagowanymi na potrzeby przeglądu. Każda ramka klucza jest oceniana w zależności od tego, jakie jest prawdopodobieństwo, że zawiera treści dla dorosłych lub treści o charakterze erotycznym. W poniższym przykładzie przedstawiono odpowiedź w formacie JSON:

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Transkrypcja dźwięku z wideo jest generowana również wtedy, gdy została ustawiona flaga `GenerateVTT`.

> [!NOTE]
> Aplikacja konsolowa używa [interfejsu API Azure Media Indexer](../../media-services/previous/legacy-components.md) , aby generować transkrypcje z przesłanej ścieżki audio wideo. Wyniki są podane w formacie WebVTT. Aby uzyskać więcej informacji na temat tego formatu, zobacz temat [Web Video Text Tracks Format](https://developer.mozilla.org/docs/Web/API/WebVTT_API) (Format ścieżek tekstowych wideo w sieci Web).

## <a name="create-a-human-review"></a>Tworzenie przeglądu przez ludzi

Proces moderowania zwraca listę ramek kluczy z wideo oraz transkrypcję ścieżek audio. Następnym krokiem jest utworzenie przeglądu w narzędziu do przeglądu Content Moderator dla moderatorów ludzkich. Powracając do `ProcessVideo()` metody w _program.cs_, zobaczysz wywołanie `CreateVideoReviewInContentModerator()` metody. Ta metoda jest częścią klasy `videoReviewApi`, która znajduje się w pliku `VideoReviewAPI.cs` i została przedstawiona tutaj.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

Metoda `CreateVideoReviewInContentModerator()` wywołuje kilka innych metod wykonujących następujące zadania:

> [!NOTE]
> Aplikacja konsolowa używa biblioteki [FFmpeg](https://ffmpeg.org/) do generowania miniatur. Te miniatury (obrazy) odpowiadają znacznikom czasu ramek w danych wyjściowych moderowania wideo.

|Zadanie|Metody|Plik|
|-|-|-|
|Wyodrębnianie ramek kluczy z wideo i tworzenie z nich obrazów miniatur|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Skanowanie transkrypcji tekstu, jeśli jest dostępna, w celu zlokalizowania dźwięku przeznaczonego dla osób dorosłych lub o charakterze erotycznym|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Przygotowanie i przesłanie żądania przeglądu wideo do kontroli przez ludzi|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

Na poniższym ekranie przedstawiono wyniki poprzednich kroków.

![Widok domyślny przeglądu wideo](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Przetwarzanie transkrypcji

Do tej pory kod przedstawiony w tym samouczku dotyczył głównie zawartości wizualnej. Przegląd zawartości mowy to oddzielny i opcjonalny proces, w którym, jak wspomniano wcześniej, jest używana transkrypcja wygenerowana z dźwięku. Teraz warto przyjrzeć się, jak transkrypcje tekstu są tworzone i używane w procesie przeglądu. Zadanie generowania transkrypcji jest wykonywane w ramach usługi [Azure Media Indexer](../../media-services/previous/media-services-index-content.md).

Aplikacja wykonuje następujące zadania:

|Zadanie|Metody|Plik|
|-|-|-|
|Ustalenie, czy mają być generowane transkrypcje tekstu|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Jeśli tak, przesłanie zadania transkrypcji w ramach moderowania|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Pobieranie kopii lokalnej transkrypcji|`GenerateTranscript()`|`VideoModerator.cs`|
|Oflagowanie ramek wideo, które zawierają nieodpowiedni dźwięk|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Dodanie wyników do przeglądu|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfiguracja zadania

Przejdźmy od razu do przesyłania zadania transkrypcji. Metoda `CreateAzureMediaServicesJobToModerateVideo()` (została już opisana) wywołuje metodę `ConfigureTranscriptTask()`.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

Konfiguracja zadania transkrypcji jest odczytywana z pliku `MediaIndexerConfig.json` w folderze `Lib` rozwiązania. Elementy zawartości platformy AMS są tworzone dla pliku konfiguracji i danych wyjściowych procesu transkrypcji. Po uruchomieniu zadania platformy AMS to zadanie tworzy transkrypcję tekstu ze ścieżki audio pliku wideo.

> [!NOTE]
> Przykładowa aplikacja rozpoznaje tylko mowę w języku angielskim (USA).

### <a name="transcript-generation"></a>Generowanie transkrypcji

Transkrypcja jest publikowana jako element zawartości platformy AMS. Aby przeprowadzić skanowanie transkrypcji pod kątem niepożądanej zawartości, aplikacja pobiera element zawartości z platformy Azure Media Services. Metoda `CreateAzureMediaServicesJobToModerateVideo()` wywołuje metodę `GenerateTranscript()`, jak przedstawiono poniżej, aby pobrać plik.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Po niezbędnej konfiguracji platformy AMS pobieranie odbywa się przez wywołanie metody `DownloadAssetToLocal()`, funkcji rodzajowej, która kopiuje element zawartości platformy AMS do pliku lokalnego.

## <a name="moderate-the-transcript"></a>Umiarkowane transkrypcję

Ponieważ jest łatwy dostęp do transkrypcji, można ją przeskanować i użyć w przeglądzie. Tworzenie przeglądu wchodzi w zakres metody `CreateVideoReviewInContentModerator()`, która wywołuje metodę `GenerateTextScreenProfanity()` w celu wykonania zadania. Z kolei ta metoda wywołuje metodę `TextScreen()`, która obejmuje większość funkcjonalności.

Metoda `TextScreen()` wykonuje następujące zadania:

- Analizuje transkrypcję pod kątem znaczników czasu i napisów.
- Przesyła każdy napis do moderowania tekstu.
- Flaguje wszystkie ramki, które mogą zawierać niepożądaną zawartość mowy.

Przeanalizujmy dokładniej każde z tych zadań:

### <a name="initialize-the-code"></a>Inicjowanie kodu

Najpierw należy zainicjować wszystkie zmienne i kolekcje.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Analizowanie transkrypcji do napisów

Następnie należy przeanalizować transkrypcję w formacie VTT na potrzeby napisów i znaczników czasu. Narzędzie Recenzja wyświetla te podpisy na karcie transkrypcja na ekranie Recenzja wideo. Znaczniki czasu są używane do synchronizacji napisów z odpowiednimi ramkami wideo.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderowanie napisów przy użyciu usługi moderowania tekstu

Następnie należy zeskanować przeanalizowane napisy tekstowe za pomocą tekstowego interfejsu API usługi Content Moderator.

> [!NOTE]
> Klucz usługi Content Moderator ma limit szybkości wyrażany w żądaniach na sekundę (RPS). Po przekroczeniu tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Podział tekstu na moderowanie

`TextScreen()` jest metodą mającą istotne znaczenie, więc opiszmy ją dokładniej.

1. Najpierw metoda odczytuje plik transkrypcji linia po linii. Ignoruje linie puste i linie zawierające `NOTE` ze współczynnikiem ufności. Wyodrębnia znaczniki czasu i elementy tekstowe ze *wskaźników* w pliku. Wskaźnik zawiera tekst ze ścieżki audio oraz godziny rozpoczęcia i zakończenia. Wskaźnik zaczyna się od linii znacznika czasu parametrami z ciągiem `-->`. Po nim następuje przynajmniej jedna linia tekstu.

1. Wystąpienia elementu `CaptionScreentextResult` (zdefiniowane w pliku `TranscriptProfanity.cs`) służą do przechowywania przeanalizowanych informacji z poszczególnych wskaźników.  Po wykryciu nowej linii znacznika czasu lub osiągnięciu maksymalnej długości tekstu wynoszącej 1024 znaki nowy element `CaptionScreentextResult` zostanie dodany do `csrList`. 

1. Następnie metoda przesyła każdy wskaźnik do interfejsu API moderowania tekstu. Wywołuje zarówno `ContentModeratorClient.TextModeration.DetectLanguageAsync()` jak i `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, które zdefiniowano w wirtualnym pliku dziennika `Microsoft.Azure.CognitiveServices.ContentModerator`. Aby uniknąć ograniczeń nakładanych przez współczynnik, metoda zatrzymuje się na chwilę przed przesłaniem poszczególnych wskaźników.

1. Po otrzymaniu wyników z usługi moderowania tekstu metoda analizuje je, aby sprawdzić, czy nie wykraczają poza progi ufności. Wartości te można znaleźć w pliku `App.config` i są to `OffensiveTextThreshold`, `RacyTextThreshold` oraz `AdultTextThreshold`. Niepożądane warunki również są przechowywane. Wszystkie ramki w zakresie czasu wskaźnika zostają oflagowane jako zawierające tekst o charakterze obraźliwym, erotycznym i/lub przeznaczony dla osób dorosłych.

1. Metoda `TextScreen()` zwraca wystąpienie `TranscriptScreenTextResult`, które zawiera wynik moderowania tekstu z całego wideo. Ten obiekt zawiera flagi i wyniki dla różnych typów niepożądanej zawartości wraz z listą wszystkich niepożądanych warunków. Obiekt wywołujący, `CreateVideoReviewInContentModerator()`, wywołuje wystąpienie `UploadScreenTextResult()`, aby dołączyć tę informację do przeglądu, dzięki czemu będzie ona dostępna dla ludzi będących recenzentami.

Na poniższym ekranie przedstawiono wynik czynności wykonywanych w ramach generowania i moderowania transkrypcji.

![Widok transkrypcji moderowania wideo](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Dane wyjściowe programu

Następujące dane wyjściowe wiersza polecenia programu przedstawiają różne zadania podczas ich wykonywania. Wynik moderowania (w formacie JSON) i transkrypcja mowy są dostępne w tym samym katalogu co oryginalne pliki wideo.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie aplikacji, która pozwala na umiarkowaną zawartość wideo, &mdash; w tym transkrypcję zawartości &mdash; i tworzenie przeglądów w narzędziu do przeglądu. Następnie Dowiedz się więcej na temat szczegółów moderowania wideo.

> [!div class="nextstepaction"]
> [Moderowanie filmów wideo](./video-moderation-human-review.md)