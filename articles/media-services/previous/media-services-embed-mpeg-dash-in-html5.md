---
title: Osadzanie wideo do adaptacyjnego przesyłania strumieniowego MPEG w aplikacji HTML5 przy użyciu DASH.js | Microsoft Docs
description: W tym temacie przedstawiono sposób osadzania wideo adaptacyjnego przesyłania strumieniowego MPEG w aplikacji HTML5 przy użyciu DASH.js.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 55c22a58ea76c268c40894c0ea64d43312b1ba27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269101"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Osadzanie plików wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z implementacją DASH.js

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

## <a name="overview"></a>Omówienie
MPEG-KRESKa to standard ISO do adaptacyjnego przesyłania strumieniowego zawartości wideo, który oferuje znaczące korzyści dla deweloperów, którzy chcą dostarczać wysokiej jakości dane wyjściowe przesyłania strumieniowego wideo. W przypadku używania standardu MPEG strumień wideo dostosowuje się automatycznie do niższej definicji, gdy sieć zostanie przeciążona. Pozwala to zmniejszyć prawdopodobieństwo wyświetlenia "wstrzymanego" filmu wideo, gdy gracz pobierze kolejne kilka sekund do odtworzenia (oznacza to, że zmniejsza prawdopodobieństwo buforowania). W miarę zmniejszania przeciążenia sieci odtwarzacz wideo powraca do strumienia o wyższej jakości. Możliwość dostosowania wymaganej przepustowości powoduje skrócenie czasu rozpoczęcia wideo. Oznacza to, że pierwsze kilka sekund można odtworzyć w szybkim, dynamicznym segmencie o niższej jakości, a następnie przejść do wyższej jakości, gdy wystarczająca zawartość jest buforowana.

Dash.js to odtwarzacz wideo "open source", który został zapisany w języku JavaScript. Celem jest zapewnienie niezawodnego, międzyplatformowego odtwarzacza, który można swobodnie ponownie wykorzystać w aplikacjach, które wymagają odtwarzania wideo. Zapewnia to odtwarzanie w formacie MPEG-PAUZy w dowolnej przeglądarce, która obsługuje rozszerzenia źródła nośników W3C (MSE), dzisiaj, które są Chrome, Microsoft Edge i IE11 (inne przeglądarki wskazują, że zamiar obsługi programu MSE). Aby uzyskać więcej informacji na temat DASH.js, js zapoznaj się z repozytorium dash.js GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Tworzenie odtwarzacza wideo przesyłania strumieniowego opartego na przeglądarce
Aby utworzyć prostą stronę sieci Web, która wyświetla odtwarzacz wideo z oczekiwanymi kontrolami, takimi jak odtwarzanie, wstrzymywanie, przewijanie do tyłu itp., należy wykonać następujące działania:

1. Tworzenie strony HTML
2. Dodaj tag wideo
3. Dodawanie dash.js odtwarzacza
4. Inicjowanie odtwarzacza
5. Dodawanie niektórych stylów CSS
6. Wyświetlanie wyników w przeglądarce, która implementuje MSE

Inicjowanie odtwarzacza można wykonać tylko w kilku wierszy kodu JavaScript. Korzystając z dash.js, jest to bardzo proste osadzanie wideo w formacie MPEG-KRESKowego w aplikacjach opartych na przeglądarce.

## <a name="creating-the-html-page"></a>Tworzenie strony HTML
Pierwszym krokiem jest utworzenie standardowej strony HTML zawierającej element **wideo** , Zapisz ten plik jako basicPlayer.html, jak pokazano na poniższym przykładzie:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Dodawanie DASH.js odtwarzacza
Aby dodać implementację odwołania dash.js do aplikacji, należy pobrać plik dash.all.js z najnowszej wersji programu dash.js Project. Powinno to być zapisane w folderze JavaScript aplikacji. Ten plik jest wygodnym plikiem, który ściąga wszystkie niezbędne dash.js kodu do pojedynczego pliku. Jeśli masz przyjrzeć się repozytorium dash.js, znajdziesz poszczególne pliki, kod testowy i wiele innych, ale jeśli wszystko, co chcesz zrobić, jest używane dash.js, wówczas plik dash.all.js jest tym, czego potrzebujesz.

Aby dodać odtwarzacz dash.js do aplikacji, Dodaj tag skryptu do sekcji nagłówka basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Następnie Utwórz funkcję, aby zainicjować odtwarzacz podczas ładowania strony. Dodaj następujący skrypt po wierszu, w którym załadowano dash.all.js:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Ta funkcja najpierw tworzy DashContext. Służy do konfigurowania aplikacji dla określonego środowiska uruchomieniowego. Z punktu widzenia technicznego definiuje klasy, które mają być używane przez platformę iniekcji zależności podczas konstruowania aplikacji. W większości przypadków używasz łącznika. di. DashContext.

Następnie Utwórz wystąpienie klasy podstawowej dash.js Framework MediaPlayer. Ta klasa zawiera podstawowe metody, takie jak Play i Pause, zarządza relacją z elementem wideo, a także zarządza interpretacją pliku opisu prezentacji multimediów (MPD), który opisuje wideo do odtworzenia.

Funkcja uruchamiania () klasy MediaPlayer jest wywoływana, aby upewnić się, że odtwarzacz jest gotowy do odtwarzania wideo. Funkcja ta zapewnia między innymi, że wszystkie niezbędne klasy (zgodnie z definicją kontekstu) zostały załadowane. Gdy gracz będzie gotowy, możesz dołączyć do niego element wideo przy użyciu funkcji attachView (). Funkcja uruchamiania umożliwia MediaPlayer, aby wstrzyknąć strumień wideo do elementu, a także kontrolować odtwarzanie w razie potrzeby.

Przekaż adres URL pliku MPD do MediaPlayer, tak aby wie o wideo, które ma być odtwarzane. Właśnie utworzona funkcja setupVideo () będzie musiała zostać wykonana po całkowitym załadowaniu strony. W tym celu należy użyć zdarzenia OnLoad elementu body. Zmień `<body>` element na:

```html
    <body onload="setupVideo()">
```

Na koniec Ustaw rozmiar elementu wideo przy użyciu CSS. W środowisku adaptacyjnego przesyłania strumieniowego jest to szczególnie ważne, ponieważ rozmiar odtwarzanego wideo może ulec zmianie w miarę odtwarzania dostosowuje się do zmieniających się warunków sieciowych. W tej prostej wersji demonstracyjnej wystarczy wymusić, aby element wideo miał wartość 80% dostępnego okna przeglądarki, dodając następujący kod CSS do sekcji nagłówka strony:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Odtwarzanie wideo
Aby odtworzyć wideo, wskaż przeglądarkę w pliku basicPlayback.html i kliknij opcję Odtwórz na wyświetlonym odtwarzaczu wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też

[Repozytorium dash.js GitHub](https://github.com/Dash-Industry-Forum/dash.js) 

