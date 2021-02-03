---
title: Analizowanie wideo na żywo przy użyciu przetwarzanie obrazów na potrzeby analizy przestrzennej — Azure
description: W tym samouczku pokazano, jak korzystać z analizy filmów wideo na żywo wraz z przetwarzanie obrazów funkcją AI do analizy przestrzennej z poziomu usługi Azure Cognitive Services w celu przeanalizowania na żywo kanału informacyjnego wideo z aparatu (symulowane).
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: f8a828d27b69fedefe7ccbc2ad7290bf300afc16
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526792"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Analizowanie wideo na żywo przy użyciu przetwarzanie obrazów na potrzeby analizy przestrzennej (wersja zapoznawcza)

W tym samouczku pokazano, jak za pomocą usługi Cognitive Services Azure Video Analytics korzystać z [Przetwarzanie obrazów w celu](https://azure.microsoft.com/services/cognitive-services/computer-vision/) przeanalizowania na żywo kanału informacyjnego wideo z (symulowanej) kamery internetowej. Zobaczysz, jak ten serwer wnioskowania umożliwia analizowanie wideo przesyłania strumieniowego w celu zrozumienia relacji przestrzennych między ludźmi i przenoszeniem w miejscu fizycznym.  Podzbiór ramek w strumieniu wideo jest wysyłany do tego serwera wnioskowania, a wyniki są wysyłane do centrum IoT Edge, a w przypadku spełnienia pewnych warunków klipy wideo są rejestrowane i przechowywane jako zasoby Azure Media Services.

W tym samouczku wykonasz następujące instrukcje:

> [!div class="checklist"]
> * Skonfiguruj zasoby.
> * Przejrzyj kod.
> * Uruchom przykładowy kod.
> * Monitoruj zdarzenia.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > Potrzebna jest subskrypcja platformy Azure z uprawnieniami do tworzenia jednostek usługi (jest to **rola właściciela** ). Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta, aby przyznać Ci odpowiednie uprawnienia. 
## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem

Przeczytaj te artykuły przed rozpoczęciem:

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md)
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Samouczek: opracowywanie modułu IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Wdrażanie usługi Live Video Analytics na Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne dotyczące łączenia modułu analizy przestrzennej z modułem analiz wideo na żywo.

* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim. Upewnij się, że masz [rozszerzenie narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Upewnij się, że sieć, z którą jest połączony komputer deweloperski, zezwala na zaawansowaną kolejkowanie komunikatów przez port 5671. To ustawienie umożliwia usłudze Azure IoT Tools komunikowanie się z usługą Azure IoT Hub.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) z PRZYSPIESZENIEM procesora GPU.  
    Zalecamy używanie Azure Stack Edge z przyspieszeniem procesora GPU, ale kontener jest uruchamiany na dowolnym innym urządzeniu przy użyciu [procesora GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Usługa Azure poznawczej przetwarzanie obrazów kontener](https://azure.microsoft.com/services/cognitive-services/computer-vision/) na potrzeby analizy przestrzennej.  
    Aby można było użyć tego kontenera, musisz mieć zasób przetwarzanie obrazów, aby uzyskać skojarzony **klucz interfejsu API** i **Identyfikator URI punktu końcowego**. Klucz interfejsu API jest dostępny na stronach przeglądów przetwarzanie obrazów Azure Portal i kluczy. Klucz i punkt końcowy są wymagane do uruchomienia kontenera.

## <a name="overview"></a>Omówienie

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Analiza przestrzenna — Omówienie":::
 
Ten diagram przedstawia sposób przepływu sygnałów w tym samouczku. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer Real-Time Streaming Protocol (RTSP). Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do `MediaGraphCognitiveServicesVisionExtension` węzła procesora.

Węzeł MediaGraphCognitiveServicesVisionExtension odgrywa rolę serwera proxy. Konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz za pośrednictwem **pamięci współdzielonej** do innego modułu brzegowego, który uruchamia operacje AI za punktem końcowym gRPC. W tym przykładzie modułem brzegowym jest moduł analizy przestrzennej. Węzeł procesora MediaGraphCognitiveServicesVisionExtension wykonuje dwie czynności:

* Gromadzi wyniki i publikuje zdarzenia w węźle [ujścia IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). 
* Przechwytuje również 30-sekundowe klipy wideo ze źródła RTSP przy użyciu [procesora bramy sygnałów](media-graph-concept.md#signal-gate-processor) i zapisuje je jako Media Services zasoby.

## <a name="create-the-computer-vision-resource"></a>Tworzenie zasobu przetwarzanie obrazów

Należy utworzyć zasób platformy Azure typu przetwarzanie obrazów w [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) lub za pośrednictwem interfejsu wiersza polecenia platformy Azure. Można utworzyć zasób po zatwierdzeniu żądania dostępu do kontenera i zarejestrowaniu identyfikatora subskrypcji platformy Azure. Przejdź do, https://aka.ms/csgate Aby przesłać przypadek użycia i Identyfikator subskrypcji platformy Azure.  Musisz utworzyć zasób platformy Azure, korzystając z tej samej subskrypcji platformy Azure, która została podana w żądaniu dostępu do formularza.

### <a name="gathering-required-parameters"></a>Zbieranie wymaganych parametrów

Istnieją trzy podstawowe parametry dla wszystkich kontenerów Cognitive Services, które są wymagane, w tym kontener analizy przestrzennej. Umowa licencyjna użytkownika oprogramowania (EULA) musi być obecna z wartością akceptacji. Ponadto wymagany jest zarówno identyfikator URI punktu końcowego, jak i klucz interfejsu API.

### <a name="keys-and-endpoint-uri"></a>Klucze i identyfikator URI punktu końcowego

Klucz służy do uruchamiania kontenera analizy przestrzennej i jest dostępny na `Keys and Endpoint` stronie Azure Portal odpowiedniego zasobu usługi poznawczej. Przejdź do tej strony i Znajdź klucze oraz identyfikator URI punktu końcowego.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Identyfikator URI punktu końcowego":::

## <a name="set-up-azure-stack-edge"></a>Konfigurowanie Azure Stack Edge

Wykonaj następujące [kroki](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) , aby skonfigurować narzędzie Azure Stack Edge i kontynuować wykonywanie poniższych kroków w celu wdrożenia analiz wideo na żywo i modułów analizy przestrzennej.

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

1. Klonuj repozytorium z tej lokalizacji: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. W Visual Studio Code Otwórz folder, w którym repozytorium zostało pobrane.
1. W obszarze Visual Studio Code przejdź do folderu src/Cloud-to-Device-App. W tym miejscu Utwórz plik i nadaj mu nazwę appsettings.js. Ten plik będzie zawierać ustawienia, które są konieczne do uruchomienia programu.
1. Pobierz IotHubConnectionString z krawędzi Azure Stack, wykonując następujące czynności:

    * Przejdź do IoT Hub w Azure Portal i kliknij przycisk w `Shared access policies` lewym okienku nawigacji.
    * Kliknij pozycję `iothubowner` Pobierz klucze dostępu współdzielonego.
    * Skopiuj  `Connection String – primary key` i wklej go w polu wejściowym na programu vscode.
    
        Parametry połączenia będą wyglądać następująco: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Skopiuj poniżej zawartość do pliku. Upewnij się, że zastąpisz zmienne.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Przejdź do folderu src/Edge i Utwórz plik o nazwie. env.
1. Skopiuj zawartość pliku/CloudDrive/LVA-Sample/Edge-Deployment/.env. Tekst powinien wyglądać podobnie do poniższego kodu.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Konfigurowanie szablonu wdrożenia  

Wyszukaj plik wdrożenia w/src/Edge/deployment.spatialAnalysis.template.js. Szablon zawiera moduł lvaEdge module, moduł rtspsim i nasz moduł analizy przestrzennej.

Istnieje kilka rzeczy, do których należy zwrócić uwagę w pliku szablonu wdrożenia:

1. Ustaw powiązanie portu.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` w lvaEdge i module analizy przestrzennej elementu weboptions powinna być taka sama i ustawiona na wartość host.
1. Aby symulator był działał, upewnij się, że skonfigurowano granice woluminu. Aby uzyskać więcej informacji, zobacz [Instalowanie instalacji woluminów platformy Docker](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Połącz się z udziałem SMB](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) i skopiuj [przykładowy plik wideo Bulldozer](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) do udziału lokalnego.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Sprawdź, czy moduł rtspsim ma następującą konfigurację:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrożenia

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym. Definiuje również ustawienia konfiguracji dla tych modułów.

Wykonaj następujące kroki, aby wygenerować manifest z pliku szablonu, a następnie wdrożyć go na urządzeniu brzegowym.

1. Otwórz program Visual Studio Code.
1. Obok okienka AZURE IOT HUB wybierz ikonę Więcej akcji, aby ustawić parametry połączenia IoT Hub. Można skopiować ciąg z `src/cloud-to-device-console-app/appsettings.json` pliku.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Analiza przestrzenna: parametry połączenia":::
1. Kliknij prawym przyciskiem myszy `src/edge/deployment.spatialAnalysis.template.json` i wybierz polecenie generuj IoT Edge manifest wdrożenia.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Analiza przestrzenna: Deployment amd64 JSON":::
    
    Ta akcja powinna utworzyć plik manifestu o nazwie deployment.amd64.jsw folderze src/Edge/config.
1. Kliknij prawym przyciskiem myszy `src/edge/config/deployment.spatialAnalysis.amd64.json` , wybierz pozycję Utwórz wdrożenie dla pojedynczego urządzenia, a następnie wybierz nazwę urządzenia brzegowego.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Analiza przestrzenna: kod JSON szablonu wdrożenia":::   
1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz nazwę Azure Stack krawędzi z menu rozwijanego.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:
    
    * Analiza wideo na żywo na IoT Edge (Nazwa modułu: lvaEdge).
    * Symulator protokołu przesyłania strumieniowego Real-Time (RTSP) (Nazwa modułu rtspsim).
    * Analiza przestrzenna (Nazwa modułu spatialAnalysis).
    
W przypadku pomyślnego wdrożenia w danych wyjściowych zostanie wyświetlony komunikat podobny do tego:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Następnie można znaleźć `lvaEdge` , `rtspsim` `spatialAnalysis` i `rtspsim` moduły w obszarze urządzenia/moduły i ich stan powinien mieć wartość "uruchomiona".

## <a name="prepare-to-monitor-events"></a>Przygotowanie do monitorowania zdarzeń

Aby wyświetlić te zdarzenia, wykonaj następujące kroki:

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Pokaż pełny komunikat":::
1. Otwórz okienko Eksploratora i Znajdź IoT Hub platformy Azure w lewym dolnym rogu.
1. Rozwiń węzeł urządzenia.
1. Kliknij prawym przyciskiem myszy Azure Stack krawędź i wybierz pozycję Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Analiza przestrzenna: Rozpocznij monitorowanie":::
     
## <a name="run-the-program"></a>Uruchamianie programu

Istnieje element program.cs, który wywoła metody bezpośrednie w elemencie src/Cloud-to-Device-App/operations.jsw. Musimy skonfigurować operations.jsna i udostępnić topologię do użycia na potrzeby wykresów multimedialnych.  

W operations.jsna:

* Ustaw topologię w następujący sposób:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Utwórz wystąpienie grafu podobne do tego, ustaw parametry w topologii tutaj:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

>[!Note]
Zapoznaj się z tematem korzystanie z programu MediaGraphRealTimeComputerVisionExtension w celu nawiązania połączenia z modułem analizy przestrzennej. Ustaw wartość $ {grpcUrl} na **TCP://spatialAnalysis: <PORT_NUMBER>**, np. TCP://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Uruchom sesję debugowania i postępuj zgodnie z instrukcjami **terminalu** , ustawimy topologię, ustawisz wystąpienie grafu, Aktywuj wystąpienie grafu, a następnie usuń zasoby.

## <a name="interpret-results"></a>Interpretowanie wyników

Po utworzeniu wystąpienia grafu multimediów należy zobaczyć zdarzenie "MediaSessionEstablished" w tym [przykładzie przykładowe zdarzenie MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Moduł analizy przestrzennej wyśle także zdarzenia usługi AI Insights do usługi Analiza filmów wideo na żywo, a następnie IoTHub je w **danych wyjściowych**. JEDNOSTKA jest obiektem wykrywania, a zdarzenie to zdarzenia spaceanalytics. Te dane wyjściowe zostaną przesłane do analizy wideo na żywo.

Przykładowe dane wyjściowe dla personZoneEvent (z cognitiveservices. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Następne kroki

Wypróbuj różne operacje, które są `spatialAnalysis` oferowane przez moduł, takie jak **PersonCount** i **personDistance** , włączając flagę "Enabled" w węźle grafu pliku manifestu wdrożenia.
>[!Tip]
> Użyj [przykładowego pliku wideo](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) , który zawiera więcej niż jedną osobę w ramce.

> [!NOTE]
> Można uruchamiać tylko jedną operację naraz. Upewnij się, że tylko jedna flaga ma ustawioną **wartość true** , a pozostałe są ustawione na **wartość false**.