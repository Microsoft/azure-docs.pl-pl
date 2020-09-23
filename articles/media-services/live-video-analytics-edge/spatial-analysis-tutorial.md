---
title: Analizowanie wideo na żywo przy użyciu przetwarzanie obrazów na potrzeby analizy przestrzennej — Azure
description: W tym samouczku pokazano, jak korzystać z analizy filmów wideo na żywo wraz z przetwarzanie obrazów funkcją AI do analizy przestrzennej z poziomu usługi Azure Cognitive Services w celu przeanalizowania na żywo kanału informacyjnego wideo z aparatu (symulowane).
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946728"
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

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem

Przeczytaj te artykuły przed rozpoczęciem:

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md)
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Samouczek: opracowywanie modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
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
 
Ten diagram przedstawia sposób przepływu sygnałów w tym samouczku. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer protokołu przesyłania strumieniowego w czasie rzeczywistym (RTSP). Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) . Ten procesor ogranicza szybkość klatek strumienia wideo, który dociera do węzła procesora MediaGraphCognitiveServicesVisionExtension.

Węzeł MediaGraphCognitiveServicesVisionExtension odgrywa rolę serwera proxy. Konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz za pośrednictwem **pamięci współdzielonej** do innego modułu brzegowego, który uruchamia operacje AI za punktem końcowym gRPC. W tym przykładzie modułem brzegowym jest moduł analizy przestrzennej. Węzeł procesora MediaGraphCognitiveServicesVisionExtension wykonuje dwie czynności:

* Gromadzi wyniki i publikuje zdarzenia w węźle [ujścia IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub). 
* Przechwytuje również 30-sekundowe klipy wideo ze źródła RTSP przy użyciu [procesora bramy sygnałów](media-graph-concept.md#signal-gate-processor) i zapisuje je jako Media Services zasoby.

## <a name="create-the-computer-vision-resource"></a>Tworzenie zasobu przetwarzanie obrazów

Należy utworzyć zasób platformy Azure typu przetwarzanie obrazów w [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) lub za pośrednictwem interfejsu wiersza polecenia platformy Azure. Można utworzyć zasób po zatwierdzeniu żądania dostępu do kontenera i zarejestrowaniu identyfikatora subskrypcji platformy Azure. Przejdź do, https://aka.ms/csgate Aby przesłać przypadek użycia i Identyfikator subskrypcji platformy Azure.  Musisz utworzyć zasób platformy Azure, korzystając z tej samej subskrypcji platformy Azure, która została podana w żądaniu dostępu do formularza.

### <a name="gathering-required-parameters"></a>Zbieranie wymaganych parametrów

Istnieją trzy podstawowe parametry dla wszystkich kontenerów Cognitive Services, które są wymagane, w tym kontener analizy przestrzennej. Umowa licencyjna użytkownika oprogramowania (EULA) musi być obecna z wartością akceptacji. Ponadto wymagany jest adres URL punktu końcowego i klucz interfejsu API.

### <a name="endpoint-uri-endpoint_uri"></a>Identyfikator URI punktu końcowego {ENDPOINT_URI}

Wartość identyfikatora URI punktu końcowego jest dostępna na stronie Przegląd Azure Portal zasobu Cognitive Services. Przejdź do strony przegląd i Znajdź identyfikator URI punktu końcowego. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Klucze i punkt końcowy":::

### <a name="keys-api_key"></a>Klucze {API_KEY}

Ten klucz służy do uruchamiania kontenera analizy przestrzennej i jest dostępny na stronie klucze Azure Portal odpowiedniego zasobu usługi poznawczej. Przejdź do strony klucze i Znajdź klucze.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="Identyfikator URI punktu końcowego":::

## <a name="set-up-azure-stack-edge"></a>Konfigurowanie Azure Stack Edge

Wykonaj następujące [kroki](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) , aby skonfigurować narzędzie Azure Stack Edge i kontynuować wykonywanie poniższych kroków w celu wdrożenia analiz wideo na żywo i modułów analizy przestrzennej.

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
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
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
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Konfigurowanie szablonu wdrożenia  

Dodaj moduł analizy przestrzennej do/src/Edge/deployment.template.jsw systemie. Szablon zawiera moduł lvaEdge module, moduł rtspsim i nasz moduł analizy przestrzennej.

<p>
<details>
<summary>Rozwiń ten węzeł i przejrzyj przykładowy szablon wdrożenia.  
Skopiuj zawartość z tego miejsca i wklej ją do/src/Edge/deployment.template.jsna.
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
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
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

Istnieje kilka rzeczy, na które należy zwrócić uwagę:

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
1. IpcMode w lvaEdge i module analizy przestrzennej nie powinny być takie same i ustawione na wartość host.
1. Plik szablonu wdrożenia musi zawierać "wdrożenie" w nazwie pliku, w przeciwnym razie nie można go rozpoznać i wygenerować manifestu do wdrożenia.
1. Aby symulator był działał, upewnij się, że skonfigurowano granice woluminu. Aby uzyskać więcej informacji, zobacz [Instalowanie instalacji woluminów platformy Docker](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Połącz się z udziałem SMB](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share) i skopiuj [plik wideo](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) do udziału lokalnego.
    1. Sprawdź, czy moduł rtspsim ma następujące elementy:
        
        ```json
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
1. Obok okienka AZURE IOT HUB wybierz ikonę Więcej akcji, aby ustawić parametry połączenia IoT Hub. Możesz skopiować ten ciąg z pliku SRC/Cloud-to-Device-App/appsettings.jsna plik.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Analiza przestrzenna: parametry połączenia":::
1. Kliknij prawym przyciskiem myszy pozycję src/Edge/deployment.template.jsna i wybierz polecenie Generuj IoT Edge manifest wdrożenia.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Analiza przestrzenna: Deployment amd64 JSON":::
    
    Ta akcja powinna utworzyć plik manifestu o nazwie deployment.amd64.jsw folderze src/Edge/config.
1. Kliknij prawym przyciskiem myszy pozycję src/Edge/config/deployment.amd64.jsna, wybierz pozycję Utwórz wdrożenie dla pojedynczego urządzenia, a następnie wybierz nazwę urządzenia brzegowego.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Analiza przestrzenna: kod JSON szablonu wdrożenia":::   
1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz nazwę Azure Stack krawędzi z menu rozwijanego.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:
    
    * Analiza wideo na żywo na IoT Edge (Nazwa modułu: lvaEdge).
    * Symulator protokołu przesyłania strumieniowego w czasie rzeczywistym (RTSP) (Nazwa modułu rtspsim).
    * Analiza przestrzenna (Nazwa modułu spatialAnalysis).
    
W przypadku pomyślnego wdrożenia w danych wyjściowych zostanie wyświetlony komunikat podobny do tego:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Następnie można znaleźć `lvaEdge` , `rtspsim` `spatialAnalysis` i `rtspsim` moduły w obszarze urządzenia/moduły i ich stan powinien mieć wartość "uruchomiona".

## <a name="prepare-to-monitor-events"></a>Przygotowanie do monitorowania zdarzeń

Aby wyświetlić te zdarzenia, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Wyszukaj usługę Azure IoT Hub w lewym dolnym rogu.
1. Rozwiń węzeł urządzenia.
1. Kliknij prawym przyciskiem myszy Azure Stack krawędź i wybierz pozycję Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Analiza przestrzenna: Rozpocznij monitorowanie":::
     
## <a name="run-the-program"></a>Uruchamianie programu

Istnieje element program.cs, który wywoła metody bezpośrednie w elemencie src/Cloud-to-Device-App/operations.jsw. Musimy skonfigurować operations.jsna i udostępnić topologię do użycia na potrzeby wykresów multimedialnych.
W operations.jsna:

Ustaw topologię taką jak ta (topologyFile for Local Topology, topologyUrl for online Topology):

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

Utwórz wystąpienie grafu podobne do tego, ustaw parametry w topologii tutaj:

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

<p>
<details>
<summary>Rozwiń, aby wyświetlić nasz przykładowy plik topologii dla modułu spatialAnalysis:
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
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
          "inputs": [
            {
              "nodeName": "frameRateFilter"
            }
          ]
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


Użyj MediaGraphRealTimeComputerVisionExtension, aby nawiązać połączenie z modułem analizy przestrzennej. Ustaw wartość $ {grpcUrl} w tcp://spatialAnalysis: <PORT_NUMBER>, np. tcp://spatialAnalysis:50051

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
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

Uruchom sesję debugowania i postępuj zgodnie z instrukcjami terminalu, ustawimy topologię, ustawisz wystąpienie grafu, Aktywuj wystąpienie grafu, a następnie usuń zasoby.

## <a name="interpret-results"></a>Interpretowanie wyników

Po utworzeniu wystąpienia grafu multimediów należy zobaczyć zdarzenie "MediaSessionEstablished" w tym [przykładzie przykładowe zdarzenie MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Moduł analizy przestrzennej wyśle także zdarzenia usługi AI Insights do usługi Analiza filmów wideo na żywo, a następnie IoTHub je w danych wyjściowych. JEDNOSTKA jest obiektem wykrywania, a zdarzenie to zdarzenia spaceanalytics. Te dane wyjściowe zostaną przesłane do analizy wideo na żywo.

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
> Użyj [pliku wideo](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) , który zawiera więcej niż jedną osobę w ramce.

> [!NOTE]
> Można uruchamiać tylko jedną operację naraz. Upewnij się, że tylko jedna flaga ma ustawioną **wartość true** , a pozostałe są ustawione na **wartość false**.
