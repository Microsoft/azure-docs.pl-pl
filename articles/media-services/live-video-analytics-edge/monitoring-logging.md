---
title: Monitorowanie i rejestrowanie — Azure
description: Ten artykuł zawiera omówienie monitorowania i rejestrowania w usłudze Live Video Analytics na IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 08b2f5cce80581d71ce73e97ab30900aa8957c77
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564487"
---
# <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

W tym artykule dowiesz się, jak odbierać zdarzenia dotyczące zdalnego monitorowania z analizy filmów wideo na żywo w module IoT Edge. 

Dowiesz się również, jak kontrolować dzienniki generowane przez moduł.

## <a name="taxonomy-of-events"></a>Taksonomia zdarzeń

Usługa Analiza filmów wideo na żywo na IoT Edge emituje zdarzenia lub dane telemetryczne zgodnie z następującą taksonomią:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagram przedstawiający taksonomię zdarzeń.":::

* Działa: zdarzenia generowane przez akcje użytkownika lub podczas wykonywania [grafu multimediów](media-graph-concept.md)
   
   * Wolumin: oczekiwano niskiego poziomu (kilka razy więcej niż minutę lub nawet mniej)
   * Przykłady:

      - Rozpoczęto nagrywanie (pokazane w poniższym przykładzie)
      - Zatrzymano nagrywanie
      
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
* Diagnostyka: zdarzenia, które pomagają zdiagnozować problemy z wydajnością

   * Wolumin: może być wysoka (kilka razy w minutę)
   * Przykłady:
   
      - Informacje o [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) protokołu RTSP (pokazane w poniższym przykładzie) 
      - Przerwy w przychodzącej strumieniu wideo

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
* Analiza: zdarzenia generowane w ramach analizy wideo

   * Wolumin: może być wysoki (kilka razy więcej niż minutę)
   * Przykłady:
      
      - Wykryto ruch (pokazany w poniższym przykładzie) 
      - Wynik wnioskowania

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

Zdarzenia emitowane przez moduł są wysyłane do [centrum IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). Mogą one być kierowane z innych miejsc docelowych. 

### <a name="timestamps-in-analytic-events"></a>Sygnatury czasowe w zdarzeniach analitycznych

Jak wskazano wcześniej, zdarzenia generowane jako część analizy wideo mają sygnatury czasowe skojarzone z nimi. Jeśli [zarejestrowano wideo na żywo](video-recording-concept.md) jako część topologii wykresu, te sygnatury czasowe ułatwiają znalezienie miejsca w zarejestrowanym filmie wideo, w którym wystąpiło określone zdarzenie. Poniżej znajdują się wskazówki dotyczące sposobu mapowania sygnatury czasowej w zdarzeniu analitycznym na oś czasu filmu wideo zarejestrowanego w [Azure Media Services element zawartości](terminology.md#asset).

Najpierw Wyodrębnij `eventTime` wartość. Użyj tej wartości w [filtrze zakresu czasu](playback-recordings-how-to.md#time-range-filters) , aby pobrać odpowiednią część nagrania. Na przykład możesz chcieć pobrać wideo, które zaczyna 30 sekund `eventTime` , a następnie zakończy 30 sekund od. W poprzednim przykładzie, gdzie `eventTime` to 2020-05-12T23:33:09.381 z, żądanie dotyczące manifestu HLS przez 30 sekund przed i po będzie wyglądać następująco `eventTime` :

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Powyższy adres URL zwróci [główną listę odtwarzania](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) zawierającą adresy URL dla list odtwarzania multimediów. Lista odtwarzania multimediów zawiera następujące wpisy:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Poprzedni wpis zawiera raporty o dostępności fragmentu wideo, który zaczyna się od `timestamp` wartości `143039375031270` . `timestamp`Wartość w zdarzeniu analitycznym używa tego samego czasu, co w przypadku listy odtwarzania multimediów. Można go użyć do zidentyfikowania odpowiedniego fragmentu wideo i przeszukania poprawnej ramki.

Aby uzyskać więcej informacji, zobacz następujące [artykuły na temat dokładnego wyszukiwania](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) w HLS.

## <a name="controlling-events"></a>Kontrolowanie zdarzeń

Można użyć następujących właściwości sznurów modułu do kontrolowania zdarzeń operacyjnych i diagnostycznych opublikowanych przez usługę Live Video Analytics na IoT Edge module. Te właściwości są udokumentowane w [schemacie JSON modułu](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName`: Aby pobrać zdarzenia diagnostyczne z modułu, należy uwzględnić tę właściwość i podać dla niej dowolną wartość. Pomiń lub pozostaw to pole puste, aby zatrzymać publikowanie zdarzeń diagnostycznych przez moduł.
   
- `operationalEventsOutputName`: Aby uzyskać zdarzenia operacyjne z modułu, należy uwzględnić tę właściwość i podać dla niej dowolną wartość. Pomiń lub pozostaw to pole puste, aby zatrzymać publikowanie zdarzeń operacyjnych przez moduł.
   
Zdarzenia analizy są generowane przez węzły takie jak procesor wykrywania ruchu lub procesor rozszerzenia HTTP. Obiekt sink Centrum IoT Hub służy do wysyłania ich do centrum IoT Edge. 

[Routing wszystkich powyższych zdarzeń](../../iot-edge/module-composition.md#declare-routes) można kontrolować za pomocą `desired` właściwości `$edgeHub` sznurka modułu w manifeście wdrożenia:

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

W powyższym kodzie JSON `lvaEdge` jest nazwą usługi Live Video Analytics na IoT Edge module. Reguła routingu jest zgodna ze schematem zdefiniowanym w [zadeklarować trasy](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Aby zapewnić, że zdarzenia analizy docierają do centrum IoT Edge, należy mieć węzeł ujścia Centrum IoT Hub z dowolnego węzła procesora wykrywania ruchu i/lub dowolnego węzła procesora rozszerzenia HTTP.

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia pochodzą z urządzenia brzegowego i mogą być używane na brzegu lub w chmurze. Zdarzenia generowane przez usługę analiza filmów wideo na żywo w IoT Edge są zgodne ze [wzorcem przesyłania strumieniowego](../../iot-hub/iot-hub-devguide-messages-construct.md) w usłudze Azure IoT Hub. Wzorzec składa się z właściwości systemu, właściwości aplikacji oraz treści.

### <a name="summary"></a>Podsumowanie

Każde zdarzenie, które jest zaobserwowane za pośrednictwem IoT Hub, ma zestaw wspólnych właściwości:

|Właściwość   |Typ właściwości| Typ danych   |Opis|
|---|---|---|---|
|`message-id`   |System |guid|  Unikatowy identyfikator zdarzenia.|
|`topic`|   applicationProperty |ciąg|    Azure Resource Manager ścieżka do Azure Media Services konta.|
|`subject`| applicationProperty |ciąg|    Ścieżka podrzędna jednostki emitującej zdarzenie.|
|`eventTime`|   applicationProperty|    ciąg| Godzina wygenerowania zdarzenia.|
|`eventType`|   applicationProperty |ciąg|    Identyfikator typu zdarzenia. (Zobacz poniższą sekcję).|
|`body`|body    |object|    Dane określonego zdarzenia.|
|`dataVersion`  |applicationProperty|   ciąg  |{Główna}. Średni|

### <a name="properties"></a>Właściwości

#### <a name="message-id"></a>Identyfikator komunikatu

Unikatowy identyfikator globalny (GUID) dla zdarzenia.

#### <a name="topic"></a>temat

Reprezentuje konto Azure Media Services skojarzone z wykresem.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Jednostka, która emituje zdarzenie:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject`Właściwość umożliwia mapowanie zdarzeń ogólnych do modułu generującego. Na przykład dla nieprawidłowej nazwy użytkownika lub hasła protokołu RTSP wygenerowane zdarzenie będzie znajdować się `Microsoft.Media.Graph.Diagnostics.ProtocolError` w `/graphInstances/myGraph/sources/myRtspSource` węźle.

#### <a name="event-types"></a>Typy zdarzeń

Typy zdarzeń są przypisywane do przestrzeni nazw zgodnie z tym schematem:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Klasy zdarzeń

|Nazwa klasy|Opis|
|---|---|
|Analiza  |Zdarzenia generowane w ramach analizy zawartości.|
|Diagnostyka    |Zdarzenia, które pomagają w diagnostyce problemów i wydajności.|
|Operacyjne    |Zdarzenia generowane jako część operacji zasobu.|

Typy zdarzeń są specyficzne dla każdej klasy zdarzeń.

Przykłady:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Czas zdarzenia

Godzina zdarzenia jest formatowana w ciągu ISO 8601. Przedstawia godzinę wystąpienia zdarzenia.

### <a name="azure-monitor-collection-via-telegraf"></a>Zbieranie Azure Monitor za pośrednictwem telegraf

Te metryki będą raportowane z poziomu usługi Live Video Analytics w IoT Edge module:  

|Nazwa metryki|Typ|Etykieta|Opis|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Miernik|iothub, edge_device, module_name, graph_topology|Łączna liczba aktywnych wykresów na topologię.|
|lva_received_bytes_total|Licznik|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Łączna liczba bajtów odebranych przez węzeł. Obsługiwane tylko dla źródeł RTSP.|
|lva_data_dropped_total|Licznik|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node data_kind|Licznik wszystkich porzuconych danych (zdarzeń, nośników itd.).|

> [!NOTE]
> [Punkt końcowy Prometheus](https://prometheus.io/docs/practices/naming/) jest udostępniany na porcie 9600 kontenera. Jeśli nazwasz na żywo analiza filmów wideo w IoT Edge module "lvaEdge", będą oni mogli uzyskiwać dostęp do metryk przez wysłanie żądania GET do http://lvaEdge:9600/metrics .   

Wykonaj następujące kroki, aby włączyć zbieranie metryk z poziomu usługi Live Video Analytics w IoT Edge module:

1. Utwórz folder na komputerze deweloperskim i przejdź do tego folderu.

1. W folderze Utwórz `telegraf.toml` plik zawierający następujące konfiguracje:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Pamiętaj, aby zastąpić zmienne w pliku. toml. Zmienne są oznaczane nawiasami klamrowymi ( `{}` ).

1. W tym samym folderze Utwórz element pliku dockerfile, który zawiera następujące polecenia:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Użyj poleceń interfejsu wiersza polecenia platformy Docker, aby skompilować plik platformy Docker i opublikować go w usłudze Azure Container Registry.
    
   Aby uzyskać więcej informacji o używaniu interfejsu wiersza polecenia platformy Docker do wypchnięcia do rejestru kontenerów, zobacz [wypychanie i ściąganie obrazów platformy Docker](../../container-registry/container-registry-get-started-docker-cli.md). Aby uzyskać więcej informacji na temat Azure Container Registry, zapoznaj się z [dokumentacją](../../container-registry/index.yml).


1. Po zakończeniu wypychania do Azure Container Registry Dodaj następujący węzeł do pliku manifestu wdrożenia:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
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
    > Pamiętaj, aby zastąpić zmienne w pliku manifestu. Zmienne są oznaczane nawiasami klamrowymi ( `{}` ).


   Azure Monitor można [uwierzytelniać za pomocą nazwy głównej usługi](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Wtyczka Azure Monitor telegraf udostępnia [kilka metod uwierzytelniania](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Aby użyć uwierzytelniania jednostki usługi, ustaw następujące zmienne środowiskowe:  
     `AZURE_TENANT_ID`: Określa dzierżawcę do uwierzytelnienia.  
     `AZURE_CLIENT_ID`: Określa identyfikator klienta aplikacji, który ma być używany.  
     `AZURE_CLIENT_SECRET`: Określa klucz tajny aplikacji do użycia.  
     
     >[!TIP]
     > Można przyznać jednostce usługi rolę **wydawcy metryk monitorowania** . Postępuj zgodnie z instrukcjami w temacie **[Tworzenie nazwy głównej usługi](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** , aby utworzyć jednostkę usługi i przypisać rolę.

1. Po wdrożeniu modułów metryki będą wyświetlane w Azure Monitor w ramach pojedynczej przestrzeni nazw. Nazwy metryk będą zgodne z tymi, które są emitowane przez Prometheus. 

   W takim przypadku w Azure Portal przejdź do centrum IoT Hub, a następnie wybierz pozycję **metryki** w okienku po lewej stronie. W tym miejscu powinny zostać wyświetlone metryki.

### <a name="log-analytics-metrics-collection"></a>Log Analytics zbieranie metryk
Za pomocą [punktu końcowego Prometheus](https://prometheus.io/docs/practices/naming/) wraz z [log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)można generować i [monitorować metryki](../../azure-monitor/essentials/metrics-supported.md) , takie jak używane CPUPercent, MemoryUsedPercent itd.   

> [!NOTE]
> Poniższa konfiguracja nie zbiera dzienników, a **jedynie metryki**. Istnieje możliwość rozbudowania modułu zbierającego, aby również zbierać i ładować dzienniki.

[![Diagram przedstawiający zbieranie metryk przy użyciu log Analytics.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Dowiedz się, jak [zbierać metryki](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. Użyj poleceń interfejsu wiersza polecenia platformy Docker, aby skompilować [plik platformy Docker](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) i opublikować go w usłudze Azure Container Registry.
    
   Aby uzyskać więcej informacji o używaniu interfejsu wiersza polecenia platformy Docker do wypchnięcia do rejestru kontenerów, zobacz [wypychanie i ściąganie obrazów platformy Docker](../../container-registry/container-registry-get-started-docker-cli.md). Aby uzyskać więcej informacji na temat Azure Container Registry, zapoznaj się z [dokumentacją](../../container-registry/index.yml).

1. Po zakończeniu wypychania do Azure Container Registry do manifestu wdrożenia zostanie wstawione następujące polecenie:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Moduły `edgeHub` `edgeAgent` i `lvaEdge` są nazwami modułów zdefiniowanych w pliku manifestu wdrożenia. Upewnij się, że nazwy modułów pasują do siebie.   

    Możesz uzyskać swoje `LogAnalyticsWorkspaceId` i wartości, wykonując `LogAnalyticsSharedKey` następujące czynności:
    1. Przejdź do Azure Portal
    1. Wyszukaj Twoje obszary robocze Log Analytics
    1. Po znalezieniu obszaru roboczego Log Analytics przejdź do `Agents management` opcji w okienku nawigacji po lewej stronie.
    1. Będziesz w stanie znaleźć identyfikator obszaru roboczego i klucze tajne, których można użyć.

1. Następnie Utwórz skoroszyt, klikając `Workbooks` kartę w okienku nawigacji po lewej stronie.
1. Korzystając z języka zapytań Kusto, możesz pisać zapytania jako poniżej i uzyskać procent procesora CPU używany przez moduły IoT Edge.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Diagram pokazujący metryki przy użyciu zapytania Kusto.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Rejestrowanie

Podobnie jak w przypadku innych modułów IoT Edge, można również [przeanalizować dzienniki kontenerów](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) na urządzeniu brzegowym. Można skonfigurować informacje zapisane w dziennikach przy użyciu [następujących właściwości sznurka modułu](module-twin-configuration-schema.md) :

* `logLevel`

   * Dozwolone wartości to `Verbose` , `Information` , `Warning` , `Error` i `None` .
   * Wartość domyślna to `Information`. Dzienniki będą zawierać błędy, ostrzeżenia i komunikaty informacyjne.
   * Jeśli wartość zostanie ustawiona na `Warning` , dzienniki będą zawierać komunikaty o błędach i ostrzeżeniach.
   * Jeśli ustawisz wartość na `Error` , dzienniki będą zawierać tylko komunikaty o błędach.
   * W przypadku ustawienia wartości na wartość `None` , dzienniki nie będą generowane. (Ta konfiguracja nie jest zalecana).
   * Używaj `Verbose` tylko wtedy, gdy musisz udostępnić dzienniki z pomocą techniczną platformy Azure, aby zdiagnozować problem.

* `logCategories`

   * Rozdzielana przecinkami lista co najmniej jednej z następujących wartości: `Application` , `Events` , `MediaPipeline` .
   * Wartość domyślna to `Application, Events`.
   * `Application`: Ogólne informacje z modułu, takie jak komunikaty uruchamiania modułu, błędy środowiska i wywołania metody bezpośredniej.
   * `Events`: Wszystkie zdarzenia, które zostały opisane wcześniej w tym artykule.
   * `MediaPipeline`: Dzienniki niskiego poziomu, które mogą oferować szczegółowe informacje w przypadku rozwiązywania problemów, takich jak problemy z nawiązywaniem połączenia z kamerą z obsługą protokołu RTSP.
   
### <a name="generating-debug-logs"></a>Generowanie dzienników debugowania

W niektórych przypadkach, aby pomóc pomocy technicznej platformy Azure rozwiązać problem, może być konieczne wygenerowanie bardziej szczegółowych dzienników niż opisane wcześniej. Aby wygenerować te dzienniki:

1. [Połącz magazyn modułów z magazynem urządzeń](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) za pośrednictwem programu `createOptions` . Jeśli zobaczysz [szablon manifestu wdrożenia](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) z przewodników Szybki Start, zobaczysz następujący kod:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Ten kod umożliwia zapisanie dzienników w module Edge w ścieżce magazynu urządzeń `/var/local/mediaservices/` . 

 1. Dodaj następującą `desired` Właściwość do modułu:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Moduł będzie teraz zapisywać dzienniki debugowania w formacie binarnym w ścieżce magazynu urządzeń `/var/local/mediaservices/debuglogs/` . Te dzienniki można udostępnić za pomocą pomocy technicznej systemu Azure.

## <a name="faq"></a>Często zadawane pytania

Jeśli masz pytania, zapoznaj się z tematem [monitorowanie i metryki — często zadawane pytania](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Następne kroki

[Ciągłe nagrywanie wideo](continuous-video-recording-tutorial.md)