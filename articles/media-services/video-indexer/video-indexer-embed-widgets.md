---
title: Osadź Video Indexer widżety w aplikacjach
titleSuffix: Azure Media Services
description: Dowiedz się, jak osadzić elementy widget Video Indexer w aplikacjach.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: b13086e11e1181bba91a3255e68e9f8a32e78450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98797785"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Osadź Video Indexer widżety w aplikacjach

W tym artykule przedstawiono sposób osadzania Video Indexer widżetów w aplikacjach. Usługa Video Indexer obsługuje osadzanie trzech typów widżetów w aplikacjach: *Szczegółowe informacje*, *Odtwarzacz* i *Edytor*.

Począwszy od wersji 2, podstawowy adres URL widżetu zawiera region określonego konta. Na przykład konto w regionie zachodnie stany USA generuje: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Typy widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Widżet Cognitive Insights (Szczegółowe informacje) zawiera wszystkie szczegóły wizualne wyodrębnione z filmu wideo w procesie indeksowania. Widżet szczegółowe informacje obsługuje następujące opcjonalne parametry adresu URL:

|Nazwa|Definicja|Opis|
|---|---|---|
|`widgets` | Ciągi rozdzielone przecinkami | Umożliwia kontrolowanie szczegółowych informacji, które mają być renderowane.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` renderuje tylko osoby i słowa kluczowe dotyczące interfejsu użytkownika.<br/>Dostępne opcje: ludzie, animatedCharacters, keywords, Labels, mową, emocji, tematy, ramki kluczowe, Transkrypcja, OCR, głośniki, sceny i namedEntities.|
|`controls`|Ciągi rozdzielone przecinkami|Umożliwia kontrolowanie formantów, które mają być renderowane.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` renderuje tylko opcję wyszukiwania i przycisk pobierania.<br/>Dostępne opcje: wyszukiwanie, pobieranie, ustawienia wstępne, język.|
|`language`|Kod w języku krótkim (nazwa języka)|Steruje językiem usługi Insights.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>oraz `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Kod w języku krótkim | Kontroluje język interfejsu użytkownika. Wartość domyślna to `en`. <br/>Przykład: `locale=de`.|
|`tab` | Domyślna wybrana karta | Steruje kartą usługi **Insights** , która jest renderowana domyślnie. <br/>Przykład: `tab=timeline` renderuje szczegółowych informacji przy użyciu karty **oś czasu** .|
|`location` ||`location`Parametr musi być uwzględniony w łączach osadzonych, zobacz [jak uzyskać nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, `trial` należy użyć wartości lokalizacji. `trial` jest wartością domyślną dla `location` parametru.| 

### <a name="player-widget"></a>Widżet Player

Możesz użyć widżetu odtwarzacza do przesyłania strumieniowego wideo przy użyciu adaptacyjnej szybkości transmisji bitów. Widżet odtwarzacza obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`t` | Sekund od początku | Sprawia, że gracz rozpocznie odtwarzanie od określonego momentu.<br/> Przykład: `t=60`. |
|`captions` | Kod języka | Pobiera podpis w określonym języku podczas ładowania elementu widget, aby był dostępny w menu **podpisy** .<br/> Przykład: `captions=en-US`. |
|`showCaptions` | Wartość logiczna | Powoduje załadowanie już włączonych napisów.<br/> Przykład: `showCaptions=true`. |
|`type`| | Aktywuje karnację odtwarzacza audio (część wideo jest usuwana).<br/> Przykład: `type=audio`. |
|`autoplay` | Wartość logiczna | Wskazuje, czy odtwarzacz powinien rozpocząć odtwarzanie wideo po załadowaniu. Wartość domyślna to `true`.<br/> Przykład: `autoplay=false`. |
|`language`/`locale` | Kod języka | Kontroluje język odtwarzacza. Wartość domyślna to `en-US`.<br/>Przykład: `language=de-DE`.|
|`location` ||`location`Parametr musi być uwzględniony w łączach osadzonych, zobacz [jak uzyskać nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, `trial` należy użyć wartości lokalizacji. `trial` jest wartością domyślną dla `location` parametru.| 

### <a name="editor-widget"></a>Widżet edytora

Za pomocą widżetu edytora można tworzyć nowe projekty i zarządzać szczegółowymi informacjami wideo. Widżet edytora obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`accessToken`<sup>*</sup> | Ciąg | Zapewnia dostęp do filmów wideo, które są tylko na koncie używanym do osadzenia widżetu.<br> Widżet edytora wymaga `accessToken` parametru. |
|`language` | Kod języka | Kontroluje język odtwarzacza. Wartość domyślna to `en-US`.<br/>Przykład: `language=de-DE`. |
|`locale` | Kod w języku krótkim | Steruje językiem usługi Insights. Wartość domyślna to `en`.<br/>Przykład: `language=de`. |
|`location` ||`location`Parametr musi być uwzględniony w łączach osadzonych, zobacz [jak uzyskać nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, `trial` należy użyć wartości lokalizacji. `trial` jest wartością domyślną dla `location` parametru.| 

<sup>*</sup>Właściciel powinien zapewnić `accessToken` ostrożność.

## <a name="embedding-videos"></a>Osadzanie filmów wideo

W tej sekcji omówiono osadzanie zawartości publicznej i prywatnej w aplikacjach.

`location`Parametr musi być uwzględniony w łączach osadzonych, zobacz [jak uzyskać nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, `trial` należy użyć wartości lokalizacji. `trial` jest wartością domyślną dla `location` parametru. Na przykład: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> Udostępnienie linku dla widżetu **Player** lub **szczegółowych** informacji spowoduje uwzględnienie tokenu dostępu i przyznanie uprawnień tylko do odczytu kontu.

### <a name="public-content"></a>Zawartość publiczna

1. Zaloguj się do witryny sieci Web [Video Indexer](https://www.videoindexer.ai/) .
1. Wybierz wideo, z którym chcesz korzystać, i naciśnij klawisz **Play**.
1. Wybierz odpowiedni typ widżetu (**poznawcze informacje**, **odtwarzacz** lub **Edytor**).
1. Kliknij przycisk **&lt; / &gt; Osadź**.
5. Skopiuj kod osadzania (jest wyświetlany w obszarze **Kopiuj osadzony kod** w oknie dialogowym **udział & Osadź** ).
6. Dodaj kod do aplikacji.

### <a name="private-content"></a>Zawartość prywatna

Aby osadzić prywatny film wideo, należy przekazać token dostępu w `src` atrybucie elementu iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Aby uzyskać zawartość widżetu analizy poznawczej, należy użyć jednej z następujących metod:

- Interfejs API usługi [Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- [Token uzyskiwania dostępu do wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Dodaj go jako parametr zapytania do adresu URL. Określ ten adres URL jako `src` wartość elementu iframe, jak pokazano wcześniej.

Aby zapewnić możliwości edytowania szczegółowych informacji w osadzonym elemencie widget, należy przekazać token dostępu, który obejmuje uprawnienia do edycji. Użyj [widżetu Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) lub [Uzyskaj token dostępu wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) przy użyciu `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interakcje z widżetami

Widżet wglądu w szczegółowe dane może korzystać z filmu wideo w aplikacji. W tej sekcji pokazano, jak zrealizować taką interakcję.

![Widżet szczegółowe informacje o poznawczej Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Omówienie przepływu

Podczas edytowania transkrypcji następuje następujący przepływ:

1. Edytuj transkrypcję na osi czasu.
1. Video Indexer pobiera te aktualizacje i zapisuje je w ramach [edycji z transkrypcji](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) w modelu języka.
1. Napisy są aktualizowane:

    * Jeśli używasz widżetu Video Indexer odtwarzacza, zostanie on automatycznie zaktualizowany.
    * Jeśli używasz zewnętrznego odtwarzacza, otrzymujesz nowe podpisy, które **są wywoływane przez** użytkownika.

### <a name="cross-origin-communications"></a>Komunikacja między źródłami

Aby uzyskać Video Indexer widżety do komunikowania się z innymi składnikami, usługa Video Indexer:

- Używa metody komunikacji między źródłami HTML5 `postMessage` .
- weryfikuje komunikaty w źródle videoIndexer.ai.

Jeśli zaimplementowasz własny kod odtwarzacza i zintegrujesz go z widżetami poznawcze spostrzeżenia, odpowiadasz za potwierdzenie pochodzenia wiadomości, która pochodzi z VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Osadź widżety w aplikacji lub blogu (zalecane)

W tej sekcji przedstawiono sposób osiągnięcia interakcji między dwoma Video Indexer widżetami, dzięki czemu gdy użytkownik wybierze kontrolę wglądu w aplikację, gracz przejdzie do odpowiedniego momentu.

1. Skopiuj kod osadzania widżetu Player.
2. Skopiuj kod osadzania widżetu Cognitive Insights.
3. Dodaj [plik mediatora](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) do obsługi komunikacji między dwoma widżetami:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Teraz, gdy użytkownik wybierze kontrolę wglądu w aplikację, gracz przechodzi do odpowiedniego momentu.

Aby uzyskać więcej informacji, zobacz [Video Indexer — osadzanie obu elementów widget](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Osadzanie widżetu Cognitive Insights i odtwarzanie zawartości za pomocą usługi Azure Media Player

W tej sekcji przedstawiono sposób osiągnięcia interakcji między widżetem analizy poznawczej a wystąpieniem Azure Media Player za pomocą [wtyczki amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Dodaj wtyczkę Video Indexer dla odtwarzacza AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Tworzenie wystąpienia Azure Media Player za pomocą wtyczki Video Indexer.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Skopiuj kod osadzania widżetu Cognitive Insights.

Teraz można komunikować się z Azure Media Player.

Aby uzyskać więcej informacji, zobacz [demonstracja Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Osadź widżet Video Indexer poznawcze spostrzeżenia i użyj innego odtwarzacza wideo

Jeśli używasz odtwarzacza wideo innego niż Azure Media Player, musisz ręcznie manipulować odtwarzaczem wideo w celu osiągnięcia komunikacji.

1. Wstaw odtwarzacz wideo.

    Na przykład standardowy odtwarzacz HTML5:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Osadź widżet Cognitive Insights.
3. Zaimplementuj komunikację dla odtwarzacza z nasłuchiwaniem w oczekiwaniu na zdarzenie „message”. Na przykład:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Aby uzyskać więcej informacji, zobacz [demonstracja Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Dodawanie napisów

Jeśli osadzisz Video Indexer Insights przy użyciu własnych [Azure Media Player](https://aka.ms/azuremediaplayer), możesz użyć `GetVttUrl` metody, aby uzyskać napisy (napisy). Możesz również wywołać metodę JavaScript z wtyczki Video Indexer AMP `getSubtitlesUrl` (jak pokazano wcześniej).

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie osadzalnych widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Możesz wybrać typy szczegółowych informacji. W tym celu należy określić jako wartość następujący parametr adresu URL, który jest dodawany do kodu osadzania, który otrzymujesz (z interfejsu API lub z aplikacji sieci Web): `&widgets=<list of wanted widgets>` .

Możliwe wartości to:,,,,,,,,, `people` `animatedCharacters` `keywords` `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` , `speakers` , `scenes` , i `namedEntities` .

Na przykład jeśli chcesz osadzić widżet zawierający tylko osoby i szczegółowe informacje o słowach kluczowych, adres URL osadzania elementu iframe będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Tytuł okna elementu IFRAME można również dostosować, podając `&title=<YourTitle>` adres URL elementu iframe. (Dostosowuje `<title>` wartość html).
   
Na przykład jeśli chcesz nadać polu elementu IFRAME tytuł "MyInsights", adres URL będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Ta opcja ma zastosowanie tylko w przypadkach, gdy trzeba otworzyć szczegółowe informacje w nowym oknie.

### <a name="player-widget"></a>Widżet Player

W przypadku osadzania widżetu Player usługi Video Indexer można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Na przykład:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Domyślnie program Video Indexer Player automatycznie wygenerował napisy, które są oparte na transkrypcji filmu wideo. Transkrypcja jest wyodrębniana z filmu wideo z językiem źródłowym, który został wybrany podczas przekazywania wideo.

Jeśli chcesz osadzić w innym języku, możesz dodać `&captions=<Language Code>` do adresu URL osadzania odtwarzacza. Jeśli chcesz, aby podpisy były wyświetlane domyślnie, możesz przekazać &showCaptions = true.

Adres URL osadzania będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Autoodtwarzania

Domyślnie gracz rozpocznie odtwarzanie filmu wideo. Możesz zrezygnować z przekazywania `&autoplay=false` na poprzedni adres URL osadzania.

## <a name="code-samples"></a>Przykłady kodu

Zobacz repozytorium [przykłady kodu](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) zawierające przykłady Video Indexer interfejsu API i widżetów:

| Plik/folder                       | Opis                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Ładuj wideo indeksatora wideo w niestandardowym Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Osadź informacje o przeanalizie VI przy użyciu niestandardowego Azure Media Player.                             |
| `control-vi-embedded-player`      | Osadź odtwarzacz VI i kontroluj go z zewnątrz.                                    |
| `custom-index-location`           | Osadź informacje o VI z niestandardowej lokalizacji zewnętrznej (może to być odbiorca obiektu BLOB).     |
| `embed-both-insights`             | Podstawowe użycie programu VI Insights w usłudze Player i szczegółowych informacji.                            |
| `embed-insights-with-AMP`         | Osadź widżet analizy danych w usłudze VI z niestandardowym Azure Media Player.                      |
| `customize-the-widgets`           | Osadź elementy widget VI z dostosowanymi opcjami.                                     |
| `embed-both-widgets`              | Osadź program Player i szczegółowe informacje i Komunikuj się między nimi.                      |
| `url-generator`                   | Generuje niestandardowy adres URL osadzania widżetów na podstawie opcji określonych przez użytkownika.             |
| `html5-player`                    | Osadź plik VI Insights przy użyciu domyślnego odtwarzacza wideo HTML5.                           |

## <a name="supported-browsers"></a>Obsługiwane przeglądarki

Aby uzyskać więcej informacji, zobacz [obsługiwane przeglądarki](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat sposobu wyświetlania i edytowania Video Indexer szczegółowych informacji, zobacz [Wyświetlanie i edytowanie Video Indexer szczegółowych](video-indexer-view-edit.md)informacji.

Zapoznaj się również z [CodePen indeksatora wideo](https://codepen.io/videoindexer/pen/eGxebZ).
