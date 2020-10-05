---
ms.openlocfilehash: eff73888a449de20b2b460d519b36c0f03c4ea77
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691097"
---
Po uruchomieniu grafu multimediów wyniki z węzła procesora wykrywania ruchu przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty wyświetlane w oknie **dane wyjściowe** Visual Studio Code zawierają `body` sekcję i `applicationProperties` sekcję. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści.

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Jeśli połączenie zakończy się pomyślnie, zostanie wydrukowane następujące zdarzenie.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-04-09T16:42:18.1280000Z"  
    }  
}
```

W poprzednich danych wyjściowych: 

* Komunikat jest zdarzeniem diagnostycznym `MediaSessionEstablished` . Wskazuje on, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału na żywo (symulowanego).
* W `applicationProperties` programie `subject` , odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wartość wskazuje godzinę wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="motiondetection-event"></a>Zdarzenie MotionDetection

Po wykryciu ruchu usługa analizy filmów wideo na żywo w IoT Edge module wysyła zdarzenie wnioskowania. `type`Jest ustawiona na `motion` , aby wskazać, że jest to wynik z procesora wykrywania ruchu. `eventTime`Wartość informuje o tym, kiedy nastąpiło przeruch (w formacie UTC). 

Oto przykład tego komunikatu:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

W tym przykładzie: 

* W `applicationProperties` programie `subject` odwołuje się do węzła na grafie nośnika, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła procesora wykrywania ruchu.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem analizy.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Wartość to dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, dlatego treść zawiera `timestamp` i `inferences` dane.
* `inferences`Dane wskazują, że `type` jest to `motion` . Zawiera ona dodatkowe dane dotyczące tego `motion` zdarzenia.
* `box`Sekcja zawiera współrzędne dla ograniczonego pola wokół przenoszonego obiektu. Wartości są znormalizowane przez szerokość i wysokość wideo w pikselach. Na przykład szerokość to 1920, a wysokość to 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
