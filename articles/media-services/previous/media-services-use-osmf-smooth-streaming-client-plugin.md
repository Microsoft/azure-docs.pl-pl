---
title: Wtyczka Smooth Streaming dla platformy nośników typu open source
description: Dowiedz się, jak używać wtyczki Smooth Streaming Azure Media Services dla środowiska Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: e41e33b5f30cd861ae8715e0ad7f6694f8854198
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046933"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Jak używać wtyczki Microsoft Smooth Streaming dla środowiska multimedialnego firmy Adobe typu open source

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie
Wtyczka Microsoft Smooth Streaming dla programu Open Source Media Framework 2,0 (SS for OSMF) rozszerza domyślne możliwości OSMF i Smooth Streaming dodaje do nowych i istniejących graczy OSMF. Wtyczka dodaje również możliwości odtwarzania Smooth Streaming, aby przeprowadzić lampę odtwarzania multimediów (SMP).

SS dla OSMF obejmuje dwie wersje wtyczki:

* Wtyczka Smooth Streaming statyczna dla OSMF (. SWC)
* Wtyczka Smooth Streaming dynamicznego dla OSMF (. SWF)

W tym dokumencie przyjęto założenie, że czytelnik ma ogólną praktyczną wiedzę na temat wtyczek OSMF i OSMF. Aby uzyskać więcej informacji na temat OSMF, zapoznaj się z dokumentacją w oficjalnej witrynie OSMF.

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Wtyczka Smooth Streaming dla OSMF 2,0
Wtyczka obsługuje ładowanie i odtwarzanie zawartości Smooth Streaming na żądanie przy użyciu następujących funkcji:

* Odtwarzanie Smooth Streaming na żądanie (odtwarzanie, wstrzymywanie, wyszukiwanie, zatrzymywanie)
* Odtwarzanie na żywo Smooth Streaming (Odtwórz)
* Funkcje DVR na żywo (pauza, wyszukiwanie, odtwarzanie DVR, przechodzenie do trybu Live)
* Obsługa kodeków wideo-H. 264
* Obsługa kodeków audio — AAC
* Przełączanie wielu języków audio przy użyciu wbudowanych interfejsów API OSMF
* Maksymalna dobór jakości odtwarzania z wbudowanymi interfejsami API OSMF
* Napisy na przyczepach z wtyczką OSMF Captions
* Program Adobe &reg; Flash &reg; Player 11,4 lub nowszy.
* Ta wersja obsługuje tylko OSMF 2,0.

## <a name="supported-features-and-known-issues"></a>Obsługiwane funkcje i znane problemy
Aby zapoznać się z pełną listą obsługiwanych funkcji, nieobsługiwanymi funkcjami i znanymi problemami, zapoznaj się z [tym dokumentem](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Ładowanie wtyczki
Wtyczki OSMF można ładować statycznie (w czasie kompilacji) lub dynamicznie (w czasie wykonywania). Wtyczka Smooth Streaming do pobrania OSMF obejmuje wersje dynamiczne i statyczne.

* Ładowanie statyczne: Aby ładować statycznie, wymagany jest plik biblioteki statycznej (SWC). Wtyczki statyczne są dodawane jako odwołanie do projektów i scalane w końcowym pliku wyjściowym w czasie kompilacji.
* Ładowanie dynamiczne: do dynamicznego ładowania jest wymagany wstępnie skompilowany plik (SWF). Wtyczki dynamiczne są ładowane w czasie wykonywania i nie są uwzględniane w danych wyjściowych projektu. (Skompilowane dane wyjściowe) Wtyczki dynamiczne można ładować przy użyciu protokołów HTTP i plików.

Aby uzyskać więcej informacji na temat ładowania statycznego i dynamicznego, zapoznaj się z oficjalną [stroną wtyczki OSMF](https://www.ibm.com/support/knowledgecenter/SSLTBW_2.3.0/com.ibm.zos.v2r3.izua300/IZUHPINFO_PluginsPlanning.htm).

### <a name="ss-for-osmf-static-loading"></a>SS dla statycznego ładowania OSMF
Poniższy fragment kodu przedstawia sposób ładowania wtyczki SS dla OSMF statycznie i odtwarzania podstawowego wideo przy użyciu klasy OSMF MediaFactory. Przed dołączeniem do kodu OSMF, należy się upewnić, że odwołanie do projektu zawiera statyczną wtyczkę "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC". 

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS na potrzeby dynamicznego ładowania OSMF
Poniższy fragment kodu przedstawia sposób dynamicznego ładowania wtyczki SS dla OSMF i odtwarzania podstawowego wideo przy użyciu klasy OSMF MediaFactory. Przed dołączeniem kodu OSMF, skopiuj wtyczkę dynamiczną "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" do folderu projektu, jeśli chcesz załadować przy użyciu protokołu FILE Protocol lub skopiować ją do serwera sieci Web na potrzeby ładowania HTTP. Odwołania do projektu nie muszą zawierać "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC".

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Lampa błyskowa przy użyciu wtyczki dynamicznej ODMF SS
Smooth Streaming dla wtyczki dynamicznej OSMF jest zgodna z [lampą odtwarzania multimediów (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/). Aby dodać Smooth Streaming odtwarzanie zawartości do SMP, można użyć wtyczki SS for OSMF. Aby to zrobić, skopiuj plik "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" w ramach serwera sieci Web na potrzeby ładowania HTTP, wykonując następujące czynności:

1. Przejrzyj [stronę Konfiguracja ze społecznością](http://www.koopman.me/bob3/setup.html). 
2. Ustaw element src na źródło Smooth Streaming, (np. http: \/ /devplatem.VO.msecnd.NET/Sintel/Sintel_H264.ISM/manifest) 
3. Wprowadź żądane zmiany konfiguracji i kliknij przycisk Podgląd i zaktualizuj.
   
   **Uwaga** Serwer sieci Web zawartości wymaga prawidłowego crossdomain.xml. 
4. Skopiuj i wklej kod do prostej strony HTML przy użyciu ulubionego edytora tekstu, takiego jak w poniższym przykładzie:

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. Dodaj wtyczkę Smooth Streaming OSMF do kodu osadzania i Zapisz.
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. Zapisz stronę HTML i opublikuj ją na serwerze sieci Web. Przejdź do opublikowanej strony sieci Web przy użyciu ulubionej &reg; przeglądarki internetowej z włączonym programem Flash Player (Internet Explorer, Chrome, Firefox itd.).
3. Korzystaj z Smooth Streaming zawartości wewnątrz &reg; programu Adobe Flash &reg; Player.

Aby uzyskać więcej informacji na temat ogólnej OSMF projektowania, zobacz oficjalną [stronę programistyczną OSMF](https://www.adobe.com/devnet/video/articles1/osmf_overview.html).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Wtyczka do adaptacyjnego przesyłania strumieniowego firmy Microsoft dla OSMF Update](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

