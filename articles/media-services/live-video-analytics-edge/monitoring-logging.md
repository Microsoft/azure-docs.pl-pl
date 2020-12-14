---
title: Monitorowanie i rejestrowanie — Azure
description: Ten artykuł zawiera omówienie analizy filmów wideo na żywo na IoT Edge monitorowania i rejestrowania.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 8ae455a4157cd649f610620e486323ac2c0a5744
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401053"
---
# <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

W tym artykule opisano, jak można odbierać zdarzenia z analizy filmów wideo na żywo w module IoT Edge na potrzeby zdalnego monitorowania. 

Dowiesz się również, jak można kontrolować dzienniki generowane przez moduł.

## <a name="taxonomy-of-events"></a>Taksonomia zdarzeń

Usługa Analiza filmów wideo na żywo na IoT Edge emituje zdarzenia lub dane telemetryczne zgodnie z następującą taksonomią.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Taksonomia zdarzeń":::

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

### <a name="timestamps-in-analytic-events"></a>Sygnatury czasowe w zdarzeniach analitycznych

Jak wskazano powyżej, zdarzenia generowane w ramach analizy wideo mają skojarzone z nimi sygnaturę czasową. Po [zarejestrowaniu wideo na żywo](video-recording-concept.md) jako części topologii wykresu, Ta sygnatura czasowa ułatwia znalezienie miejsca w zarejestrowanym filmie wideo, które wystąpiło. Poniżej przedstawiono wskazówki dotyczące sposobu mapowania sygnatury czasowej w zdarzeniu analitycznym na oś czasu filmu wideo zarejestrowanego w [usłudze Azure Media Service](terminology.md#asset).

Najpierw Wyodrębnij `eventTime` wartość. Użyj tej wartości w [filtrze zakresu czasu](playback-recordings-how-to.md#time-range-filters) , aby pobrać odpowiednią część nagrania. Na przykład możesz chcieć pobrać wideo, które zaczyna 30 sekund `eventTime` , a następnie zakończy 30 sekund. W powyższym przykładzie, gdzie `eventTime` jest 2020-05-12T23:33:09.381 z, żądanie dotyczące manifestu HLS dla okna +/-30 s będzie wyglądać następująco:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Powyższy adres URL zwróci nazwę [głównej listy odtwarzania](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming), zawierającą adresy URL dla list odtwarzania multimediów. Lista odtwarzania multimediów zawiera następujące wpisy:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
W powyższym miejscu wpis informuje, że fragment wideo jest dostępny, który zaczyna się od wartości sygnatury czasowej `143039375031270` . `timestamp`Wartość w zdarzeniu analitycznym używa tego samego czasu, co w przypadku listy odtwarzania multimediów, i może służyć do identyfikowania odpowiedniego fragmentu wideo i przechodzenia do odpowiedniej ramki.

Aby uzyskać więcej informacji, możesz przeczytać jeden z wielu [artykułów](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) na temat dokładnego wyszukiwania w HLS.

## <a name="controlling-events"></a>Kontrolowanie zdarzeń

Można użyć następujących właściwości sznurka modułu, zgodnie z opisem w [schemacie JSON modułu](module-twin-configuration-schema.md), w celu kontrolowania zdarzeń operacyjnych i diagnostycznych opublikowanych przez usługę Live Video Analytics na IoT Edge module.

`diagnosticsEventsOutputName` — Dołącz i podaj wartość dla tej właściwości, aby pobrać zdarzenia diagnostyczne z modułu. Pomiń lub pozostaw to pole puste, aby zatrzymać publikowanie zdarzeń diagnostycznych przez moduł.
   
`operationalEventsOutputName` — Dołącz i podaj wartość dla tej właściwości w celu uzyskania zdarzeń operacyjnych z modułu. Pomiń lub pozostaw to pole puste, aby zatrzymać publikowanie zdarzeń operacyjnych przez moduł.
   
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
|temat| applicationProperty |string|    Azure Resource Manager ścieżka do Media Services konta.|
|subject|   applicationProperty |string|    Ścieżka podrzędna do jednostki emitującej zdarzenie.|
|eventTime| applicationProperty|    string| Godzina wygenerowania zdarzenia.|
|eventType| applicationProperty |string|    Identyfikator typu zdarzenia (patrz poniżej).|
|body|body  |object|    Dane określonego zdarzenia.|
|dataVersion    |applicationProperty|   string  |{Główna}. Średni|

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
|Operacyjne    |Zdarzenia generowane jako część operacji zasobu.|

Typy zdarzeń są specyficzne dla każdej klasy zdarzeń.

Przykłady:

* Microsoft. Media. Graph. Analytics. wnioskowanie
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operations. GraphInstanceStarted

### <a name="event-time"></a>Czas zdarzenia

Czas zdarzenia jest opisany w ciągu ISO8601 i czas wystąpienia zdarzenia.

### <a name="azure-monitor-collection-using-telegraf"></a>Zbieranie Azure Monitor przy użyciu telegraf

Te metryki będą raportowane na żywo analizy filmów wideo na IoT Edge module:  

|Nazwa metryki|Typ|Etykieta|Opis|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Miernik|iothub, edge_device, module_name, graph_topology|Łączna liczba aktywnych wykresów na topologię.|
|lva_received_bytes_total|Licznik|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Całkowita liczba bajtów odebranych przez węzeł. Obsługiwane tylko dla źródeł RTSP|
|lva_data_dropped_total|Licznik|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node data_kind|Licznik wszystkich porzuconych danych (zdarzeń, multimediów itp.)|

> [!NOTE]
> [Punkt końcowy Prometheus](https://prometheus.io/docs/practices/naming/) jest udostępniany na porcie **9600** kontenera. Jeśli nazwasz na żywo analiza filmów wideo w IoT Edge module "lvaEdge", będzie ona mogła uzyskiwać dostęp do metryk przez wysłanie żądania GET do http://lvaEdge:9600/metrics .   

Wykonaj następujące kroki, aby włączyć zbieranie metryk z poziomu usługi Live Video Analytics w IoT Edge module:

1. Utwórz folder na komputerze deweloperskim i przejdź do tego folderu

1. W tym folderze Utwórz `telegraf.toml` plik z następującą zawartością
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Upewnij się, że zastąpisz zmienne (oznaczone przez `{ }` ) w pliku zawartości

1. W tym folderze Utwórz `.dockerfile` za pomocą następującej zawartości
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Teraz przy użyciu polecenia CLI platformy Docker **Skompiluj plik platformy Docker** i opublikuj go w Azure Container Registry.
    1. Informacje na temat [wypychania i ściągania obrazów platformy Docker — Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).  Więcej informacji na Azure Container Registry (ACR) można znaleźć [tutaj](https://docs.microsoft.com/azure/container-registry/).


1. Po zakończeniu wypychania do ACR w pliku manifestu wdrożenia Dodaj następujący węzeł:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{ACR_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Upewnij się, że zastąpisz zmienne (oznaczone przez `{ }` ) w pliku zawartości


1. **Authentication**
    1. Azure Monitor może być [uwierzytelniany przez jednostkę usługi](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        1. Wtyczka Azure Monitor telegraf udostępnia [kilka metod uwierzytelniania](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). Następujące zmienne środowiskowe muszą być ustawione tak, aby używały uwierzytelniania nazwy głównej usługi.  
            • AZURE_TENANT_ID: określa dzierżawcę, do którego należy się uwierzytelnić.  
            • AZURE_CLIENT_ID: Określa identyfikator klienta aplikacji, który ma być używany.  
            • AZURE_CLIENT_SECRET: określa klucz tajny aplikacji do użycia.  
    >[!TIP]
    > Jednostka usługi może otrzymać rolę "**Wydawca metryk monitorowania**".

1. Po wdrożeniu modułów metryki będą wyświetlane w Azure Monitor w ramach pojedynczej przestrzeni nazw z nazwami metryk pasującymi do tych, które są emitowane przez Prometheus. 
    1. W takim przypadku w Azure Portal przejdź do IoT Hub i kliknij link "**metryki**" w okienku nawigacji po lewej stronie. W tym miejscu powinny zostać wyświetlone metryki.
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
