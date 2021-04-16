---
title: Osadzanie Video Indexer widget w aplikacjach
titleSuffix: Azure Media Services
description: Dowiedz się, jak osadzać Video Indexer widget w aplikacjach.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 822d50bca6bc1139e9b5f0554bcf9a56a8fcbd74
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532887"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Osadzanie Video Indexer widget w aplikacjach

W tym artykule pokazano, jak osadzać Video Indexer widgety w aplikacjach. Usługa Video Indexer obsługuje osadzanie trzech typów widżetów w aplikacjach: *Szczegółowe informacje*, *Odtwarzacz* i *Edytor*.

Począwszy od wersji 2, podstawowy adres URL widżetu zawiera region określonego konta. Na przykład konto w regionie Zachodnie stany USA generuje: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Typy widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Widżet Cognitive Insights (Szczegółowe informacje) zawiera wszystkie szczegóły wizualne wyodrębnione z filmu wideo w procesie indeksowania. Widżet Cognitive Insights obsługuje następujące opcjonalne parametry adresu URL:

|Nazwa|Definicja|Opis|
|---|---|---|
|`widgets` | Ciągi rozdzielone przecinkami | Umożliwia kontrolowanie szczegółowych informacji, które mają być renderowane.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` renderuje tylko szczegółowe informacje interfejsu użytkownika dotyczące osób i słów kluczowych.<br/>Dostępne opcje: people, animatedCharacters ,keywords, labels, sentiments, emotions, topics, keyframes, transcript, ocr, speakers, scenes, and namedEntities (osoby mówiące, słowa kluczowe, etykiety, emocje, tematy, ramki kluczowe, transkrypcja, ocr, osoby mówiące, sceny i nazwaneJednostki).|
|`controls`|Ciągi rozdzielone przecinkami|Umożliwia sterowanie kontrolkami, które mają być renderowane.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` renderuje tylko opcję wyszukiwania i przycisk pobierania.<br/>Dostępne opcje: wyszukiwanie, pobieranie, ustawienia wstępne, język.|
|`language`|Krótki kod języka (nazwa języka)|Kontroluje język szczegółowych informacji.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>Lub `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Krótki kod języka | Steruje językiem interfejsu użytkownika. Wartość domyślna to `en`. <br/>Przykład: `locale=de`.|
|`tab` | Domyślna wybrana karta | Steruje **kartą Szczegółowe** informacje, która jest domyślnie renderowana. <br/>Przykład: `tab=timeline` renderuje szczegółowe informacje z wybraną **kartą Oś** czasu.|
|`location` ||Parametr `location` musi znajdować się w osadzonych linkach. Zobacz, jak uzyskać [nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, dla wartości lokalizacji należy `trial` użyć wartości . `trial` jest wartością domyślną `location` parametru .| 

### <a name="player-widget"></a>Widżet Player

Widżet Player umożliwia przesyłanie strumieniowe wideo przy użyciu adaptacyjnej szybkości transmisji bitów. Widżet Player obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`t` | Sekundy od początku | Sprawia, że gracz rozpoczyna odtwarzanie od określonego punktu czasu.<br/> Przykład: `t=60`. |
|`captions` | Kod języka | Pobiera podpis w określonym języku podczas ładowania widżetu, aby był dostępny w menu **Napisy.**<br/> Przykład: `captions=en-US`. |
|`showCaptions` | Wartość logiczna | Powoduje załadowanie już włączonych napisów.<br/> Przykład: `showCaptions=true`. |
|`type`| | Aktywuje wygląd odtwarzacza audio (część wideo jest usuwana).<br/> Przykład: `type=audio`. |
|`autoplay` | Wartość logiczna | Wskazuje, czy odtwarzacz powinien rozpocząć odtwarzanie wideo po załadowaniu. Wartość domyślna to `true`.<br/> Przykład: `autoplay=false`. |
|`language`/`locale` | Kod języka | Steruje językiem odtwarzacza. Wartość domyślna to `en-US`.<br/>Przykład: `language=de-DE`.|
|`location` ||Parametr `location` musi znajdować się w osadzonych linkach. Zobacz, jak uzyskać [nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, dla wartości lokalizacji należy `trial` użyć wartości . `trial` jest wartością domyślną `location` parametru .| 

### <a name="editor-widget"></a>Widżet edytora

Widżet Edytor umożliwia tworzenie nowych projektów i zarządzanie analizami wideo. Widżet Edytor obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`accessToken`<sup>*</sup> | Ciąg | Zapewnia dostęp do filmów wideo, które znajdują się tylko na koncie używanym do osadzania widżetu.<br> Widżet Edytor wymaga `accessToken` parametru . |
|`language` | Kod języka | Steruje językiem odtwarzacza. Wartość domyślna to `en-US`.<br/>Przykład: `language=de-DE`. |
|`locale` | Krótki kod języka | Steruje językiem szczegółowych informacji. Wartość domyślna to `en`.<br/>Przykład: `language=de`. |
|`location` ||Parametr `location` musi znajdować się w linkach osadzonych. Zobacz, jak [uzyskać nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, jako wartości lokalizacji należy `trial` użyć wartości . `trial` jest wartością domyślną `location` parametru .| 

<sup>*</sup>Właściciel powinien zachować `accessToken` ostrożność.

## <a name="embed-videos"></a>Osadzanie wideo

W tej sekcji omówiono osadzanie filmów wideo przy [użyciu portalu](#the-portal-experience) lub przez ręczne składanie adresu [URL](#assemble-the-url-manually) w aplikacjach. 

Parametr `location` musi znajdować się w linkach osadzonych. Zobacz, jak [uzyskać nazwę regionu](regions.md). Jeśli Twoje konto jest w wersji zapoznawczej, jako wartości lokalizacji należy `trial` użyć wartości . `trial` jest wartością domyślną `location` parametru . Na przykład: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>Środowisko portalu

Aby osadzić film wideo, użyj portalu zgodnie z poniższym opisem:

1. Zaloguj się do witryny [Video Indexer](https://www.videoindexer.ai/) internetowej.
1. Wybierz film wideo, z którym chcesz pracować, a następnie naciśnij przycisk **Odtąd.**
1. Wybierz typ odpowiedniego widżetu **(Cognitive Insights,** **Player** lub **Editor).**
1. Kliknij pozycję **&lt; / &gt; Osadź.**
5. Skopiuj kod osadzania (jest wyświetlany w oknie dialogowym Kopiowanie **osadzonego** kodu & **osadzania).**
6. Dodaj kod do aplikacji.

> [!NOTE]
> Udostępnienie linku do **widżetu Player** lub **Insights** spowoduje dołączenie tokenu dostępu i przyznanie uprawnień tylko do odczytu do Twojego konta.

### <a name="assemble-the-url-manually"></a>Ręczne składanie adresu URL

#### <a name="public-videos"></a>Publiczne wideo

Możesz osadzić publiczne filmy wideo, aby utworzyć adres URL w następujący sposób:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Prywatne filmy wideo

Aby osadzić prywatny film wideo, musisz przekazać token dostępu (użyj właściwości [Pobierz token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) dostępu wideo w `src` atrybutze iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Zapewnianie możliwości edytowania szczegółowych informacji

Aby zapewnić możliwości edytowania szczegółowych informacji w osadzonym widżecie, musisz przekazać token dostępu, który obejmuje uprawnienia do edycji. Użyj funkcji [Pobierz token dostępu do wideo za](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) pomocą funkcji `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interakcje z widżetami

Widżet Cognitive Insights może wchodzić w interakcje z wideo w aplikacji. W tej sekcji pokazano, jak zrealizować taką interakcję.

![Widżet Cognitive Insights Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Omówienie przepływu

Podczas edytowania transkrypcji następuje następujący przepływ:

1. Transkrypcja jest edytowana na osi czasu.
1. Video Indexer pobiera te aktualizacje i zapisuje je w zapisie z [edycji transkrypcji](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) w modelu językowym.
1. Napisy są aktualizowane:

    * Jeśli używasz widżetu Video Indexer odtwarzacza — jest on automatycznie aktualizowany.
    * Jeśli używasz odtwarzacza zewnętrznego — użytkownik otrzyma nowy plik napisów w wywołaniu **Pobierz napisy** wideo.

### <a name="cross-origin-communications"></a>Komunikacja między źródłami

Aby Video Indexer widgety do komunikowania się z innymi składnikami, usługa Video Indexer service:

- Używa metody HTML5 komunikacji między `postMessage` źródłami.
- weryfikuje komunikaty w źródle videoIndexer.ai.

Jeśli zaim implementujesz własny kod odtwarzacza i integrujesz się z widżetami cognitive insights, Twoim zadaniem jest zweryfikowanie źródła komunikatu, który pochodzi z VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Osadzanie widżetów w aplikacji lub blogu (zalecane)

W tej sekcji pokazano, jak uzyskać interakcję między dwoma Video Indexer widgetami, dzięki czemu gdy użytkownik wybierze kontrolkę wglądu w szczegółowe dane w aplikacji, odtwarzacz przeskoczy do odpowiedniego momentu.

1. Skopiuj kod osadzania widżetu Player.
2. Skopiuj kod osadzania widżetu Cognitive Insights.
3. Dodaj [plik mediatora](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) do obsługi komunikacji między dwoma widżetami:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Teraz, gdy użytkownik wybierze kontrolkę szczegółowych informacji w aplikacji, odtwarzacz przeskoczy do odpowiedniego momentu.

Aby uzyskać więcej informacji, zobacz [pokaz Video Indexer — Osadź oba widżety.](https://codepen.io/videoindexer/pen/NzJeOb)

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Osadzanie widżetu Cognitive Insights i odtwarzanie zawartości za pomocą usługi Azure Media Player

W tej sekcji pokazano, jak uzyskać interakcję między widżetem cognitive insights i wystąpieniem Azure Media Player za pomocą [wtyczki AMP.](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)

1. Dodaj Video Indexer wtyczki dla odtwarzacza AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Za pomocą Azure Media Player Video Indexer wystąpienia.

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

Teraz możesz komunikować się z Azure Media Player.

Aby uzyskać więcej informacji, zobacz [pokaz Azure Media Player + VI Insights.](https://codepen.io/videoindexer/pen/rYONrO)

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Osadzanie Video Indexer Cognitive Insights i używanie innego odtwarzacza wideo

Jeśli używasz odtwarzacza wideo innego niż Azure Media Player, musisz ręcznie manipulować odtwarzaczem wideo, aby osiągnąć komunikację.

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

Aby uzyskać więcej informacji, zobacz [pokaz Azure Media Player + VI Insights.](https://codepen.io/videoindexer/pen/YEyPLd)

## <a name="adding-subtitles"></a>Dodawanie napisów

Jeśli osadzasz Video Indexer szczegółowych informacji przy użyciu własnego [Azure Media Player](https://aka.ms/azuremediaplayer), możesz użyć metody , aby uzyskać `GetVttUrl` napisy (napisy). Możesz również wywołać metodę JavaScript z Video Indexer AMP `getSubtitlesUrl` (jak pokazano wcześniej).

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie osadzalnych widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Możesz wybrać typy szczegółowych informacji. W tym celu określ je jako wartość następującego parametru adresu URL dodanego do kodu osadzania, który można pobrać (z interfejsu API lub aplikacji internetowej): `&widgets=<list of wanted widgets>` .

Możliwe wartości to: `people` , , , , , , , , `animatedCharacters` , , `keywords` , , `labels` i `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` `namedEntities` .

Jeśli na przykład chcesz osadzić widżet zawierający tylko szczegółowe informacje o osobach i słowach kluczowych, adres URL osadzania elementu iframe będzie wyglądać tak:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Tytuł okna iframe można również dostosować, podając `&title=<YourTitle>` adres URL ramki iframe. (Dostosowuje wartość `<title>` HTML).
   
Jeśli na przykład chcesz nadać oknie iframe tytuł "MyInsights", adres URL będzie wyglądać tak:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Ta opcja ma zastosowanie tylko w przypadkach, gdy trzeba otworzyć szczegółowe informacje w nowym oknie.

### <a name="player-widget"></a>Widżet Player

W przypadku osadzania widżetu Player usługi Video Indexer można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Na przykład:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Domyślnie Video Indexer ma automatycznie wygenerowane napisy, które są oparte na transkrypcji wideo. Transkrypcja jest wyodrębniona z wideo przy użyciu języka źródłowego wybranego podczas jego przesłania.

Jeśli chcesz osadzić przy użyciu innego języka, możesz dodać adres `&captions=<Language Code>` URL osadzania odtwarzacza. Jeśli chcesz, aby napisy były wyświetlane domyślnie, możesz przekazać &showCaptions=true.

Adres URL osadzania będzie wyglądać tak:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Autoodtwarzanie

Domyślnie odtwarzacz rozpocznie odtwarzanie wideo. Możesz z tego nie korzystać, przekazując `&autoplay=false` do poprzedniego adresu URL osadzania.

## <a name="code-samples"></a>Przykłady kodu

Zobacz [przykłady kodu,](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) które zawiera przykłady dla Video Indexer API i widżetów:

| Plik/folder                       | Opis                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Ładowanie wideo indeksatora wideo w niestandardowym Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Osadź szczegółowe informacje usługi VI za pomocą niestandardowego Azure Media Player.                             |
| `control-vi-embedded-player`      | Osadź odtwarzacz VI i kontroluj go z zewnątrz.                                    |
| `custom-index-location`           | Osadź szczegółowe informacje o interfejsie VI z niestandardowej lokalizacji zewnętrznej (może to być obiekt blob klienta).     |
| `embed-both-insights`             | Podstawowe użycie usługi VI Insights zarówno dla odtwarzacza, jak i szczegółowych informacji.                            |
| `embed-insights-with-AMP`         | Osadź widżet VI Insights przy użyciu niestandardowego Azure Media Player.                      |
| `customize-the-widgets`           | Osadź widżety VI z dostosowanymi opcjami.                                     |
| `embed-both-widgets`              | Osadź vi player i insights oraz komunikuj się między nimi.                      |
| `url-generator`                   | Generuje niestandardowy adres URL osadzania widżetów na podstawie opcji określonych przez użytkownika.             |
| `html5-player`                    | Osadź analizę VI za pomocą domyślnego odtwarzacza wideo HTML5.                           |

## <a name="supported-browsers"></a>Obsługiwane przeglądarki

Aby uzyskać więcej informacji, zobacz [obsługiwane przeglądarki.](video-indexer-overview.md#supported-browsers)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat wyświetlania i edytowania Video Indexer szczegółowych informacji, zobacz Wyświetlanie i edytowanie [Video Indexer szczegółowych informacji.](video-indexer-view-edit.md)

Zapoznaj się również z [plikiem CodePen indeksatora wideo.](https://codepen.io/videoindexer/pen/eGxebZ)
