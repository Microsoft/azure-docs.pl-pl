---
title: Analiza filmów wideo na żywo na IoT Edge często zadawane pytania — Azure
description: Ten temat zawiera odpowiedzi na żywo analizy wideo na IoT Edge często zadawanych pytań.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011519"
---
# <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

Ten temat zawiera odpowiedzi na żywo analizy wideo na IoT Edge często zadawanych pytań.

## <a name="general"></a>Ogólne

Jakie są zmienne systemowe, których można użyć w definicji topologii wykresu?

|Zmienna   |Opis|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Reprezentuje chwilę w czasie, zwykle wyrażoną jako datę i godzinę dnia.|
|System. GraphTopologyName   |Reprezentuje topologię grafu multimediów, która zawiera plan grafu.|
|System. GraphInstanceName|  Reprezentuje wystąpienie grafu nośnika, przechowuje wartości parametrów i odwołuje się do topologii.|

## <a name="configuration-and-deployment"></a>Konfiguracja i wdrażanie

Czy mogę wdrożyć moduł Media Edge na urządzeniu z systemem Windows 10?
    * Tak. Zapoznaj się z artykułem na temat [kontenerów systemu Linux w systemie Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Przechwyć z ustawień aparatu IP i RTSP

* Czy muszę użyć specjalnego zestawu SDK na urządzeniu, aby wysyłać strumień wideo?
    * Nie. Analiza filmów wideo na żywo na IoT Edge obsługuje przechwytywanie multimediów przy użyciu protokołu przesyłania strumieniowego RTSP wideo (który jest obsługiwany przez większość kamer IP).
* Czy można wypchnąć multimedia do analizy wideo na żywo na IoT Edge przy użyciu protokołu RTMP lub Wygładź (na przykład zdarzenia Media Services na żywo)?
    * Nie. LVA obsługują tylko protokół RTSP do przechwytywania wideo z kamer IP.
    * Wszystkie aparaty obsługujące przesyłanie strumieniowe RTSP za pośrednictwem protokołu TCP/HTTP powinny funkcjonować. 
* Czy mogę zresetować lub zaktualizować adres URL źródła RTSP w wystąpieniu grafu?
    * Tak, gdy wystąpienie grafu jest w stanie nieaktywnym.  
* Czy jest dostępny symulator RTSP do użycia podczas testowania i opracowywania?
    * Tak. Dostępny jest moduł brzegowy [symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do użycia w przewodnikach Szybki Start i samouczkach do obsługi procesu nauki. Ten moduł jest udostępniany w miarę możliwości i może nie być zawsze dostępny. Zdecydowanie zaleca się, aby nie używać go przez więcej niż kilka godzin. Przed utworzeniem planów dla wdrożenia produkcyjnego należy zainwestować w testy z rzeczywistym źródłem RTSP.
* Czy jest obsługiwane odnajdywanie kamer IP zgodne ze standardem ONVIF w sieci brzegowej?
    * Nie, odnajdywanie urządzeń w sieci brzegowej zgodne ze standardem ONVIF nie jest obsługiwane.

## <a name="streaming-and-playback"></a>Przesyłanie strumieniowe i odtwarzanie

* Czy zasoby mogą być rejestrowane w ramach AMS z brzegu, przy użyciu Media Services technologii przesyłania strumieniowego, takich jak HLS czy łącznik?
    * Tak. Zarejestrowane zasoby mogą być przesyłane strumieniowo jak każdy inny zasób w Azure Media Services. Aby przesłać strumieniowo zawartość, musisz mieć utworzony punkt końcowy przesyłania strumieniowego i w stanie uruchomienia. Użycie standardowego procesu tworzenia lokalizatora przesyłania strumieniowego zapewnia dostęp do manifestu HLS lub PAUZy na potrzeby przesyłania strumieniowego do dowolnych platform odtwarzacza. Aby uzyskać szczegółowe informacje na temat tworzenia HLS publikowanych lub manifestów PAUZy, zobacz [dynamiczne pakowanie](../latest/dynamic-packaging-overview.md).
* Czy można używać standardowych funkcji ochrony zawartości i technologii DRM Media Services na zarchiwizowanym elemencie zawartości?
    * Tak. Wszystkie standardowe funkcje dynamicznej ochrony zawartości i funkcji DRM są dostępne do użycia w zasobach zarejestrowanych z grafu multimedialnego.
* Jakich uczestników mogę używać do wyświetlania zawartości z zarejestrowanych zasobów?
   * Obsługiwane są wszystkie standardowe odtwarzacze obsługujące zgodną HTTP Live Streaming firmy Apple (HLS) w wersji 3 lub 4. Ponadto obsługiwany jest również każdy odtwarzacz, który jest w stanie zgodne z odtwarzaniem za PAUZą MPEG.
    Zalecane gracze do testowania obejmują:

    * [Azure Media Player](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Shaka Player](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Natywna HTTP Live Streaming firmy Apple](https://developer.apple.com/streaming/)
    * Przeglądarki Edge, Chrome lub Safari skompilowane w odtwarzaczu wideo HTML5
    * Gracze komercyjni obsługujące HLS lub odtwarzanie KRESek
* Jakie są limity przesyłania strumieniowego zasobów grafu multimediów?
    * Przesyłanie strumieniowe zawartości na żywo lub nagranie z grafu multimediów korzysta z tej samej infrastruktury o dużej skali i punktu końcowego przesyłania strumieniowego, który Media Services obsługuje na potrzeby przesyłania strumieniowego na żądanie i na żywo w przypadku multimediów & rozrywki, OTT i emisji. Oznacza to, że można szybko i łatwo włączyć Azure CDN, Verizon lub Akamai, aby dostarczyć zawartość do odbiorców jako mały jako kilku osób przeglądających lub do milionów w zależności od Twojego scenariusza.

    Zawartość można dostarczyć przy użyciu zarówno Apple HTTP Live Streaming (HLS), jak i MPEG-KRESKowego.

## <a name="monitoring-and-metrics"></a>Monitorowanie i metryki

* Czy mogę monitorować wykres multimedialny na krawędzi za pomocą Event Grid?
    * Nie. Obecnie Event Grid nie jest obsługiwana.
* Czy mogę używać Azure Monitor do wyświetlania kondycji, metryk i wydajności grafów multimediów w chmurze lub na granicy?
    * Nie.
* Czy istnieją jakieś narzędzia ułatwiające monitorowanie modułu Media Services IoT Edge?
    * Visual Studio Code obsługuje rozszerzenie "Azure IoT Tools", które pozwala łatwo monitorować punkty końcowe modułu LVAEdge. Za pomocą tego narzędzia można szybko rozpocząć monitorowanie IoT Hub wbudowanego punktu końcowego dla "events" i wyświetlanie komunikatów wnioskowania, które są kierowane z urządzenia brzegowego do chmury. 

    Ponadto można użyć tego rozszerzenia do edycji sznurka modułu dla modułu LVAEdge, aby zmodyfikować ustawienia wykresu multimedialnego.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [monitorowania i rejestrowania](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Rozliczenia i dostępność

* Jak LiveVideo analizę w przypadku IoT Edge rozliczania?
    * Aby uzyskać szczegółowe informacje, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Rozpoczynanie pracy — Analiza filmów wideo na żywo na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
