---
title: Smooth Streaming Plugin dla Open Source Media Framework
description: Dowiedz się, jak korzystać z wtyczki Azure Media Services Smooth Streaming dla programu Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 71d28a19316bf1b618ec9008a5e96a503687b202
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681967"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Jak korzystać z wtyczki Microsoft Smooth Streaming dla programu Adobe Open Source Media Framework  
## <a name="overview"></a>Omówienie
Wtyczka Microsoft Smooth Streaming dla open source media framework 2.0 (SS dla OSMF) rozszerza domyślne możliwości OSMF i dodaje odtwarzanie zawartości Microsoft Smooth Streaming do nowych i istniejących odtwarzaczy OSMF. Wtyczka dodaje również możliwości szybkiego odtwarzania strumieniowego do odtwarzania multimediów Strobe (SMP).

SS dla OSMF zawiera dwie wersje wtyczki:

* Static Smooth Streaming plugin dla OSMF (.swc)
* Wtyczka Dynamic Smooth Streaming dla OSMF (.swf)

W tym dokumencie przyjęto założenie, że czytnik posiada ogólną wiedzę roboczą na temat wtyczek OSMF i OSMF. Aby uzyskać więcej informacji na temat OSMF, zapoznaj się z dokumentacją na [oficjalnej stronie OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Wtyczka Smooth Streaming do OSMF 2.0
Wtyczka obsługuje ładowanie i odtwarzanie zawartości Smooth Streaming na żądanie z następującymi funkcjami:

* Płynne odtwarzanie na żądanie (Odtwarzanie, Pauza, Szukanie, Zatrzymywanie)
* Odtwarzanie płynnej transmisji strumieniowej na żywo (Odtwarzanie)
* Funkcje DVR na żywo (pauza, seek, odtwarzanie rejestratora, przejdź do na żywo)
* Obsługa kodeków wideo - H.264
* Obsługa kodeków audio - AAC
* Wielokrotne przełączanie języka audio z wbudowanymi interfejsami API OSMF
* Maksymalny wybór jakości odtwarzania dzięki wbudowanym interfejsom API OSMF
* Sidecar napisy z wtyczką podpisów OSMF
* Adobe&reg; &reg; Flash Player 11.4 lub nowszy.
* Ta wersja obsługuje tylko OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Obsługiwane funkcje i znane problemy
Pełna lista obsługiwanych funkcji, nieobsługiconych funkcji i znanych problemów można znaleźć w [tym dokumencie](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Ładowanie wtyczki
Wtyczki OSMF można ładować statycznie (w czasie kompilacji) lub dynamicznie (w czasie wykonywania). Wtyczka Smooth Streaming do pobrania OSMF zawiera zarówno wersje dynamiczne, jak i statyczne.

* Ładowanie statyczne: Aby załadować statycznie, wymagany jest plik biblioteki statycznej (SWC). Wtyczki statyczne są dodawane jako odniesienie do projektów i scalać wewnątrz pliku wyjściowego końcowego w czasie kompilacji.
* Ładowanie dynamiczne: Aby ładować się dynamicznie, wymagany jest plik wstępnie skompilowany (SWF). Wtyczki dynamiczne są ładowane w czasie wykonywania i nie są uwzględniane w danych wyjściowych projektu. (Skompilowane dane wyjściowe) Wtyczki dynamiczne można ładować za pomocą protokołów HTTP i FILE.

Aby uzyskać więcej informacji na temat ładowania statycznego i dynamicznego, zobacz oficjalną [stronę wtyczki OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS do ładowania statycznego OSMF
Poniższy fragment kodu pokazuje, jak załadować wtyczkę SS dla OSMF statycznie i odtworzyć podstawowy film przy użyciu klasy OSMF MediaFactory. Przed dołączeniem kodu SS dla OSMF upewnij się, że odwołanie do projektu zawiera statyczną wtyczkę "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
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


### <a name="ss-for-osmf-dynamic-loading"></a>SS do dynamicznego ładowania OSMF
Poniższy fragment kodu pokazuje, jak dynamicznie ładować wtyczkę SS dla OSMF i odtwarzać podstawowy film przy użyciu klasy OSMF MediaFactory. Przed dołączeniem kodu SS dla OSMF skopiuj wtyczkę dynamiczną "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" do folderu projektu, jeśli chcesz załadować za pomocą protokołu FILE, lub skopiować pod serwerem www do ładowania HTTP. Nie ma potrzeby dołączania "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" w odwołaniach do projektu.

pakiet {

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Odtwarzanie mediów stroboskopowych z wtyczką dynamiczną SS ODMF
Płynna transmisja strumieniowa dla osmf dynamiczna wtyczka jest kompatybilna z [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Możesz użyć wtyczki SS for OSMF, aby dodać płynne odtwarzanie zawartości do SMP. Aby to zrobić, skopiuj "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" w ramach serwera sieci Web dla obciążenia HTTP, wykonując następujące kroki:

1. Przejrzyj [stronę konfiguracji odtwarzania nośników strobe](http://osmf.org/dev/2.0gm/setup.html). 
2. Ustaw src na źródło płynnego przesyłania strumieniowego\/(np. http: /devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Wykonuj żądane zmiany konfiguracji i kliknij pozycję Podgląd i aktualizacja.
   
   **Uwaga** Serwer sieci web zawartości potrzebuje prawidłowego pliku crossdomain.xml. 
4. Skopiuj i wklej kod na prostą stronę HTML za pomocą ulubionego edytora tekstu, na przykład w poniższym przykładzie:

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



1. Dodaj wtyczkę Smooth Streaming OSMF do kodu osadzania i zapisz.
   
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
2. Zapisz stronę HTML i opublikuj na serwerze www. Przejdź do opublikowanej strony internetowej za&reg; pomocą ulubionej przeglądarki internetowej obsługującej program Flash Player (Internet Explorer, Chrome, Firefox, tak dalej).
3. Ciesz się płynną&reg; &reg; transmisją strumieniową w programie Adobe Flash Player.

Aby uzyskać więcej informacji na temat ogólnego rozwoju OSMF, zobacz oficjalną [stronę rozwoju OSMF](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Wtyczka Microsoft Adaptive Streaming do aktualizacji OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

