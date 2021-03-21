---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88682243"
---
Po uruchomieniu grafu multimediów wyniki z węzła procesora wykrywania ruchu przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty wyświetlane w oknie **dane wyjściowe** Visual Studio Code zawierają `body` sekcję i `applicationProperties` sekcję. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści.

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Po pomyślnym nawiązaniu połączenia zostanie wydrukowane następujące zdarzenie.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

W poprzednich danych wyjściowych: 

* Komunikat jest zdarzeniem diagnostycznym `MediaSessionEstablished` . Wskazuje, że węzeł źródłowy RTSP (podmiot) ustanowił połączenie z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału informacyjnego (symulowanego).
* W `applicationProperties` programie `subject` odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="recordingstarted-event"></a>Zdarzenie RecordingStarted

Po wykryciu ruchu jest uaktywniany węzeł procesora bramy sygnalizujący, a węzeł ujścia pliku na grafie multimedialnym zaczyna zapisywać plik MP4. Węzeł ujścia plików wysyła zdarzenie operacyjne. `type`Jest ustawiona na `motion` , aby wskazać, że jest to wynik z procesora wykrywania ruchu. `eventTime`Wartość to czas UTC, w którym wystąpiło ruch. Aby uzyskać więcej informacji o tym procesie, zobacz sekcję [Przegląd](#overview) w tym przewodniku Szybki Start.

Oto przykład tego komunikatu:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

W powyższym komunikacie: 

* W `applicationProperties` programie `subject` odwołuje się do węzła na grafie nośnika, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła ujścia plików.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie działa.
* `eventTime`Wartość to godzina wystąpienia zdarzenia. Ten czas wynosi od 5 do 6 sekund po przejściu `MediaSessionEstablished` wideo i po nim. Ten czas odpowiada znakowi 5-do-6-sekundowym, gdy [samochód rozpoczął przechodzenie](#review-the-sample-video) do partii parkingowej.
* `body`Sekcja zawiera dane dotyczące zdarzenia operacyjnego. W takim przypadku dane obejmują `outputType` i `outputLocation` .
* `outputType`Zmienna wskazuje, że te informacje dotyczą ścieżki do pliku.
* `outputLocation`Wartość jest lokalizacją pliku MP4 w module Edge.

### <a name="recordingstopped-and-recordingavailable-events"></a>Zdarzenia RecordingStopped i RecordingAvailable

Jeśli przebadasz właściwości węzła procesor bramy sygnałów w [topologii wykresu](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), zobaczysz, że czasy aktywacji są ustawione na 5 sekund. Przez około 5 sekund od momentu `RecordingStarted` odebrania zdarzenia otrzymujesz następujące informacje:

* `RecordingStopped`Zdarzenie wskazujące, że nagranie zostało zatrzymane.
* `RecordingAvailable`Zdarzenie wskazujące, że plik MP4 może być teraz używany do wyświetlania.

Dwa zdarzenia są zwykle emitowane w ciągu sekund od siebie.
