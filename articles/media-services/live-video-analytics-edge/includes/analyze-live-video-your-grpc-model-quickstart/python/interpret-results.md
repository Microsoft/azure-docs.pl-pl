---
ms.openlocfilehash: fdc7360911e37babdb830b7d67ad7224ea84c774
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92038416"
---
Po uruchomieniu grafu multimediów wyniki z węzła procesora rozszerzenia HTTP przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty widoczne w oknie **danych wyjściowych** zawierają sekcję treść i sekcję applicationProperties. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści.

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Jeśli połączenie zakończy się pomyślnie, zostanie wydrukowane następujące zdarzenie. Typ zdarzenia to `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
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

W tym komunikacie Zwróć uwagę na następujące informacje:
* Komunikat jest zdarzeniem diagnostycznym. `MediaSessionEstablished` wskazuje, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału informacyjnego (symulowanego).
* W programie `applicationProperties` temat wskazuje, że komunikat został wygenerowany z węzła źródłowego RTSP na grafie nośnika.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wskazuje godzinę wystąpienia zdarzenia.
* Treść zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Zdarzenie wnioskowania

Węzeł procesora rozszerzenia gRPC odbiera wyniki wnioskowania z modułu lvaExtension. Następnie emituje wyniki przez węzeł ujścia IoT Hub jako zdarzenia wnioskowania.
W tych zdarzeniach typ jest ustawiany na wartość Jednostka, aby wskazać, że jest jednostką, na przykład samochodem lub ciężarówką. `eventTime`Wartość jest czasem UTC, gdy obiekt został wykryty.
W poniższym przykładzie zostały wykryte trzy samochody w tej samej klatce wideo z różnymi poziomami zaufania.

```
[IoTHubMonitor] [7:52:57 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143802500954716,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.86707145
          },
          "attributes": [],
          "box": {
            "l": 0.7294476,
            "t": 0.567829,
            "w": 0.031738576,
            "h": 0.027762715
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.8634398
          },
          "attributes": [],
          "box": {
            "l": 0.4765757,
            "t": 0.59559196,
            "w": 0.05597749,
            "h": 0.048316106
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.67120105
          },
          "attributes": [],
          "box": {
            "l": 0.7247387,
            "t": 0.49816906,
            "w": 0.032748587,
            "h": 0.030686663
          }
        },
        "extensions": {},
        "valueCase": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/grpcExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-19T02:52:57.174Z",
    "dataVersion": "1.0"
  }
}
```

W komunikatach Zwróć uwagę na następujące informacje:

* W programie `applicationProperties` podmiot odwołuje się do węzła w topologii grafu, z którego został wygenerowany komunikat.
* W programie element `applicationProperties` EventType wskazuje, że to zdarzenie jest zdarzeniem analizy.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, dlatego treść zawiera dane wnioskowania.
* `inferences`Sekcja wskazuje, że typem jest Entity. Ta sekcja zawiera dodatkowe dane dotyczące jednostki.