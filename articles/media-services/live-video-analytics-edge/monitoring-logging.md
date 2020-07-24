---
title: Monitorowanie i rejestrowanie — Azure
description: Ten artykuł zawiera omówienie analizy filmów wideo na żywo na IoT Edge monitorowania i rejestrowania.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 82e4a5879e4c88e462edcddb02866ec9b671d7fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060448"
---
# <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

W tym artykule opisano, jak można odbierać zdarzenia z analizy filmów wideo na żywo w module IoT Edge na potrzeby zdalnego monitorowania. 

Dowiesz się również, jak można kontrolować dzienniki generowane przez moduł.

## <a name="taxonomy-of-events"></a>Taksonomia zdarzeń

Usługa Analiza filmów wideo na żywo na IoT Edge emituje zdarzenia lub dane telemetryczne zgodnie z następującą taksonomią.

![Analiza wideo na żywo na IoT Edge schemacie telemetrii](./media/telemetry-schema/taxonomy.png)

* Działa: zdarzenia generowane jako część akcji wykonywanych przez użytkownika lub podczas wykonywania [grafu multimedialnego](media-graph-concept.md).
   
   * Wolumin: oczekiwano niskiego poziomu (kilka razy kilku minut, a nawet niższej szybkości).
   * Przykłady:

      Rozpoczęto nagrywanie (poniżej), rejestrowanie zostało zatrzymane
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostyka: zdarzenia, które pomagają zdiagnozować problemy i/lub problemy z wydajnością.

   * Wolumin: może być wysoka (kilka razy w minutę).
   * Przykłady:
   
      Informacje o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) protokołu RTSP (poniżej) lub luki w przychodzącej strumieniu wideo.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analiza: zdarzenia generowane w ramach analizy wideo.

   * Wolumin: może być wysoki (kilka razy więcej niż minutę).
   * Przykłady:
      
      Wykryto ruch (poniżej), wynik wnioskowania.
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
Zdarzenia emitowane przez moduł są wysyłane do [centrum IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)i z tego powodu mogą być kierowane do innych miejsc docelowych. 

## <a name="controlling-events"></a>Kontrolowanie zdarzeń

Można użyć następujących właściwości sznurka modułu, zgodnie z opisem w [schemacie JSON modułu](module-twin-configuration-schema.md), w celu kontrolowania zdarzeń operacyjnych i diagnostycznych opublikowanych przez usługę Live Video Analytics na IoT Edge module.

`diagnosticsEventsOutputName`— Dołącz i podaj wartość dla tej właściwości, aby pobrać zdarzenia diagnostyczne z modułu. Pomiń lub pozostaw to pole puste, aby zatrzymać publikowanie zdarzeń diagnostycznych przez moduł.
   
`operationalEventsOutputName`— Dołącz i podaj wartość dla tej właściwości w celu uzyskania zdarzeń operacyjnych z modułu. Pomiń lub pozostaw to pole puste, aby zatrzymać publikowanie zdarzeń operacyjnych przez moduł.
   
Zdarzenia analizy są generowane przez węzły takie jak procesor wykrywania ruchu lub procesor rozszerzenia HTTP, a ujścia usługi IoT Hub służy do wysyłania ich do centrum IoT Edge. 

[Routing wszystkich powyższych zdarzeń](../../iot-edge/module-composition.md#declare-routes) można kontrolować za pośrednictwem odpowiedniej właściwości sznurka modułu $edgeHub (w manifeście wdrożenia):

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

W powyższym przykładzie lvaEdge jest nazwą na żywo analizy wideo w module IoT Edge, a reguła routingu jest zgodna ze schematem zdefiniowanym w temacie [DECLARE Routes](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Aby zapewnić, że zdarzenia analizy docierają do centrum IoT Edge, musi to być węzeł ujścia usługi IoT Hub z dowolnego węzła procesora wykrywania ruchu i/lub dowolnego węzła procesora rozszerzenia HTTP.

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia pochodzą z urządzenia brzegowego i mogą być używane na brzegu lub w chmurze. Zdarzenia generowane przez usługę Azure Video Analytics na IoT Edge są zgodne ze [wzorcem komunikatów przesyłania strumieniowego](../../iot-hub/iot-hub-devguide-messages-construct.md) , który został ustanowiony przez IoT Hub na platformie Microsoft, z właściwościami systemu, właściwościami aplikacji i treścią.

### <a name="summary"></a>Podsumowanie

Każde zdarzenie, które jest zaobserwowane za pośrednictwem IoT Hub, będzie miało zestaw wspólnych właściwości, zgodnie z poniższym opisem.

|Właściwość   |Typ właściwości| Typ danych   |Opis|
|---|---|---|---|
|Identyfikator komunikatu |System |guid|  Unikatowy identyfikator zdarzenia.|
|temat| applicationProperty |ciąg|    Azure Resource Manager ścieżka do Media Services konta.|
|subject|   applicationProperty |ciąg|    Ścieżka podrzędna do jednostki emitującej zdarzenie.|
|eventTime| applicationProperty|    ciąg| Godzina wygenerowania zdarzenia.|
|eventType| applicationProperty |ciąg|    Identyfikator typu zdarzenia (patrz poniżej).|
|body|body  |object|    Dane określonego zdarzenia.|
|dataVersion    |applicationProperty|   ciąg  |{Główna}. Średni|

### <a name="properties"></a>Właściwości

#### <a name="message-id"></a>Identyfikator komunikatu

Unikatowy identyfikator globalny (GUID) zdarzenia

#### <a name="topic"></a>temat

Reprezentuje konto usługi Azure Media skojarzone z wykresem.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Jednostka, która emituje zdarzenie:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Właściwość subject umożliwia zamapowanie zdarzeń ogólnych na jego moduł generujący. Na przykład w przypadku nieprawidłowej nazwy użytkownika RTSP lub hasła wygenerowane zdarzenie będzie znajdować się `Microsoft.Media.Graph.Diagnostics.ProtocolError` w `/graphInstances/myGraph/sources/myRtspSource` węźle.

#### <a name="event-types"></a>Typy zdarzeń

Typy zdarzeń są przypisywane do przestrzeni nazw zgodnie z poniższym schematem:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Klasy zdarzeń

|Nazwa klasy|Opis|
|---|---|
|Analiza  |Zdarzenia generowane w ramach analizy zawartości.|
|Diagnostyka    |Zdarzenia, które ułatwiają diagnozowanie problemów i wydajności.|
|Działał    |Zdarzenia generowane jako część operacji zasobu.|

Typy zdarzeń są specyficzne dla każdej klasy zdarzeń.

Przykłady:

* Microsoft. Media. Graph. Analytics. wnioskowanie
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operations. GraphInstanceStarted

### <a name="event-time"></a>Czas zdarzenia

Czas zdarzenia jest opisany w ciągu ISO8601 i czas wystąpienia zdarzenia.

## <a name="logging"></a>Rejestrowanie

Podobnie jak w przypadku innych modułów IoT Edge, można również [przeanalizować dzienniki kontenerów](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) na urządzeniu brzegowym. Informacje zapisane w dziennikach mogą być kontrolowane przez [następujące właściwości sznurka modułu](module-twin-configuration-schema.md) :

* logLevel

   * Dozwolone wartości to pełne, informacje, ostrzeżenie, błąd, brak.
   * Wartość domyślna to informacje — dzienniki będą zawierać błąd, ostrzeżenie i informacje. komunikaty.
   * Jeśli ustawisz wartość na ostrzeżenie, dzienniki będą zawierać komunikaty o błędach i ostrzeżeniach
   * Jeśli wartość zostanie ustawiona na błąd, dzienniki będą zawierać tylko komunikaty o błędach.
   * Jeśli ustawisz wartość None, dzienniki nie zostaną wygenerowane (nie jest to zalecane).
   * Należy używać pełnych informacji tylko wtedy, gdy trzeba udostępnić dzienniki z pomocą techniczną platformy Azure w celu zdiagnozowania problemu.
* logCategories

   * Rozdzielana przecinkami lista co najmniej jednego z następujących elementów: Application, Events, MediaPipeline.
   * Domyślne: aplikacja, zdarzenia.
   * Aplikacja — są to informacje wysokiego poziomu z modułu, takie jak komunikaty uruchamiania modułu, błędy środowiska i wywołania metody bezpośredniej.
   * Zdarzenia — wszystkie zdarzenia, które zostały opisane wcześniej w tym artykule.
   * MediaPipeline — są to niektóre dzienniki niskiego poziomu, które mogą oferować informacje o rozwiązywaniu problemów, takich jak problemy z nawiązywaniem połączenia z kamerą z obsługą protokołu RTSP.
   
### <a name="generating-debug-logs"></a>Generowanie dzienników debugowania

W niektórych przypadkach może być konieczne wygenerowanie bardziej szczegółowych dzienników niż opisane powyżej, aby pomóc pomocy technicznej platformy Azure rozwiązać problem. Istnieją dwa kroki, które należy wykonać.

Najpierw należy [połączyć magazyn modułów z magazynem urządzeń](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) za pomocą funkcji. Jeśli przebadasz [szablon manifestu wdrożenia](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) z przewodnika Szybki Start, zobaczysz:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Powyżej umożliwia modułowi Edge zapisywanie dzienników na ścieżce magazynu (na urządzeniu) "/var/Local/MediaServices/". Jeśli do modułu zostanie dodana następująca żądana Właściwość:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Następnie moduł zapisze Dzienniki debugowania w formacie binarnym na ścieżce magazynu (na urządzeniu)/var/Local/MediaServices/debuglogs/, którą można udostępnić z pomocą techniczną platformy Azure.

## <a name="faq"></a>Często zadawane pytania

[Często zadawane pytania](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Następne kroki

[Ciągłe nagrywanie wideo](continuous-video-recording-tutorial.md)
