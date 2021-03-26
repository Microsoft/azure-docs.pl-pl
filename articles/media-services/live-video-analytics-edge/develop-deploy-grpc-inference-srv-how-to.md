---
title: Opracowywanie i wdrażanie serwera gRPC wnioskowania — Azure
description: Ten artykuł zawiera wskazówki dotyczące tworzenia i wdrażania serwera wnioskowania gRPC.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034864"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Przewodnik — tworzenie i wdrażanie serwera wnioskowania gRPC

## <a name="overview"></a>Omówienie

W tym artykule opisano, jak można otoczyć wybrane modele AI w ramach serwera wnioskowania gRPC, dzięki czemu można zintegrować go z usługą Live Video Analytics (LVA) za pośrednictwem rozszerzenia Graf. 

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem

* [Rozszerzenia grafu multimediów](media-graph-extension-concept.md)
* [Protokół rozszerzenia gRPC](grpc-extension-protocol.md)
* [Schemat metadanych wnioskowania](inference-metadata-schema.md)
* [Wprowadzenie do gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [Przewodnik po języku proto3](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie z procesorem x86 lub ARM64, na którym działa jeden z [obsługiwanych systemów operacyjnych Linux](../../iot-edge/support.md#operating-systems) lub komputera z systemem Windows.
* [Zainstaluj platformę Docker](https://docs.docker.com/desktop/#download-and-install) na maszynie.
* Zainstaluj [IoT Edge środowiska uruchomieniowego](../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>kroki implementacji gRPC

Aby utworzyć serwer wnioskowania gRPC i wdrożyć go jako rozszerzenie za pomocą analizy wideo na żywo, zostaną użyte następujące kroki:

### <a name="setup"></a>Konfiguracja:

Wykonaj niezbędne kroki, aby [wdrożyć moduł analizy wideo na żywo i pracować na urządzeniu IoT Edge](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Kroki implementacji wysokiego poziomu:

1. Wybierz jedną z wielu języków obsługiwanych przez gRPC: C#, C++, dart, go, Java, Node, language-C, PHP, Python i Ruby.
1. Zaimplementuj serwer gRPC, który będzie komunikować się z usługą Live Video Analytics przy użyciu [plików proto3](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="Serwer gRPC, który będzie komunikować się z usługą Live Video Analytics przy użyciu plików proto3":::

    W ramach tej usługi:
    1. Obsługuje wymianę komunikatów opisu sesji między serwerem a klientem.
    1. Obsługuj [przykładowe wiadomości multimedialne](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) i zwracają wyniki.

        1. Wywołaj aparat inferencing, który korzysta z przeszkolonego modelu, aby wykonywać wnioskowania dotyczące komunikatów przychodzących.
        1. Odbieraj wyniki inferencing z aparatu, Pakuj je z powrotem jako próbka nośnika i przesyłaj z powrotem do analizy wideo na żywo przy użyciu pliku [inferencing. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) .

            Alternatywnie wywołaj funkcję przekształcania multimediów na przykład nośnika.
1. Wdróż implementację serwera gRPC. Istnieją dwa sposoby wykonania tej czynności:

    1. Wdrażanie jako lokalizacji modułu IoT przy użyciu modułu analizy wideo na żywo
    1. Wdróż jako moduł IoT w węźle dostępnym dla sieci (lokalnie lub w chmurze), który może wymieniać dane za pomocą modułu analizy wideo na żywo.
1. Skonfiguruj topologię grafu analizy wideo na żywo z modułem Live Video Analytics i wskaż ją na serwerze gRPC.

### <a name="recommendation"></a>Zaleca

Podczas kolokacji w tym samym węźle można użyć pamięci współużytkowanej w celu uzyskania najlepszej wydajności. Wymaga to używania możliwości pamięci udostępnionej systemu Linux udostępnianej przez język programowania/środowisko.

1. Otwórz uchwyt pamięci udostępnionej systemu Linux.
1. Po odebraniu ramki uzyskaj dostęp do przesunięcia adresu w pamięci współdzielonej.
1. Potwierdź zakończenie przetwarzania ramek, aby jego pamięć mogła zostać odczytana przez dynamiczną analizę wideo.

## <a name="create-a-grpc-inference-server"></a>Tworzenie serwera wnioskowania gRPC

Teraz utworzysz moduł IoT Edge (zewnętrzny AI), który akceptuje ramki wideo z usługi Live Video Analytics przy użyciu komunikatów [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) za pośrednictwem udostępnionej pamięci, klasyfikuje ramki jako "ciemne" lub "jasne" i zwraca wyniki wnioskowania z powrotem do ujścia komunikatów IoT Hub w usłudze Live Video Analytics przy użyciu [schematu metadanych wnioskowania](inference-metadata-schema.md).

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="Kompiluj moduł IoT Edge (zewnętrzny AI)":::

Ten serwer wnioskowania gRPC jest aplikacją konsolową .NET Core, która obsługuje komunikaty [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) wysyłane między na żywo i niestandardowym obrazie AI. Poniżej znajduje się przepływ komunikatów między analizą wideo na żywo a serwerem wnioskowania gRPC:

1. Usługa Analiza wideo na żywo wysyła deskryptor strumienia multimediów (zobacz [rozszerzenie. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)), który definiuje informacje o strumieniu nośnika, które będą wysyłane po nich przez ramki wideo do serwera jako komunikat [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) w sesji strumienia gRPC. 
1. Serwer sprawdza i potwierdza deskryptor strumienia i konfiguruje żądaną metodę transferu danych.
1. Usługa Live Video Analytics zaczyna wysyłać pliki MediaSample, które zawierają ramki wideo.
1. Serwer analizuje ramki wideo w miarę odbierania i rozpoczyna ich przetwarzanie przy użyciu procesora obrazów zdefiniowanego przez użytkownika.
1. Serwer następnie zwraca wyniki wnioskowania jako [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) komunikaty zaraz po ich udostępnieniu. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="Tworzenie serwera wnioskowania gRPC":::

Ramki wideo można przenieść za pomocą [pamięci współdzielonej](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) lub osadzić w komunikacie protobuf. Tryb transferu danych można skonfigurować w topologii grafu LVA, aby określić, jak ramki będą przesyłane. Jest to osiągane przez skonfigurowanie elementu **DataTransfer** właściwości MediaGraphGrpcExtension, jak pokazano poniżej:

Osadzić

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Pamięć udostępniona:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Podczas komunikacji za pomocą pamięci współdzielonej, wartość IpcMode powinna być ustawiona na możliwość **udostępniania** , a w module gRPC Server ustaw wartość IpcMode na **container: {CONTAINER_NAME}**. Te ustawienia należy wprowadzać w pliku manifestu wdrożenia, który jest używany do wdrażania modułów w usłudze Azure IoT Hub. Poniżej znajduje się przykład opcji kontenera do użycia podczas konfigurowania modułów IoT Edge.

Moduł analizy wideo na żywo:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

Moduł rozszerzenia gRPC:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Upewnij się, że możesz uzyskać dostęp do obszaru pamięci współdzielonej "Container: lvaEdge" w grpcExtension.

## <a name="sample-grpc-server"></a>Przykładowy serwer gRPC

Aby zrozumieć szczegóły dotyczące sposobu opracowania serwera gRPC, przejdźmy do naszego przykładu kodu.

1. Sklonuj repozytorium z linku w witrynie GitHub [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. Uruchom programu vscode i przejdź do folderu/src/edge/modules/grpcExtension.
1. Wykonajmy szybkie Instruktaż dotyczący plików:

    1. **Program. cs**: jest to punkt wejścia aplikacji. Jest on odpowiedzialny za Inicjowanie i zarządzanie serwerem gRPC, który będzie pełnić rolę hosta. W naszym przykładzie port do nasłuchiwania przychodzących komunikatów gRPC z klienta gRPC (na przykład usługi Live Video Analytics) jest określony przez element konfiguracji grpcBindings w AppConfig.jsna.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**: Ta klasa jest odpowiedzialna za obsługę komunikatów [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) . Odczyta ramkę w komunikacie, wywołaj ImageProcessor i napisz wyniki wnioskowania.
Teraz, po skonfigurowaniu i zainicjowaniu połączeń portów serwera gRPC, przyjrzyjmy się sposobom przetwarzania przychodzących komunikatów gRPC.

        * Po nawiązaniu sesji gRPC pierwszy komunikat, który serwer gRPC otrzyma od klienta (na żywo analizy wideo), jest MediaStreamDescriptor, który jest zdefiniowany w pliku [Extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) . 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * W naszej implementacji serwera Metoda `ProcessMediaStreamDescriptor` sprawdzi poprawność właściwości MediaDescriptor dla pliku wideo MediaStreamDescriptor, a następnie skonfiguruje tryb transferu danych (z użyciem pamięci współdzielonej lub w trybie osadzonego transferu ramki) w zależności od tego, co zostało określone w topologii i używanym pliku szablonu wdrożenia. 
        * Po odebraniu komunikatu i pomyślnym skonfigurowaniu trybu transferu danych serwer gRPC zwraca komunikat MediaStreamDescriptor z powrotem do klienta jako potwierdzenie i w związku z tym ustanawia połączenie między serwerem a klientem.    
        * Po otrzymaniu potwierdzenia na żywo usługa Video Analytics rozpocznie transfer strumienia multimediów do serwera gRPC. W naszej implementacji serwera Metoda `ProcessMediaStream` będzie przetwarzać przychodzące MediaStreamMessage. MediaStreamMessage jest również zdefiniowana w [rozszerzeniu proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * W zależności od wartości batchSize w Appconfig.jsw systemie, nasz serwer będzie przechowywać komunikaty i przechowywać na liście ramki wideo. Po osiągnięciu limitu batchSize funkcja będzie wywoływała funkcję lub plik, który będzie przetwarzać obraz. W naszym przypadku metoda wywołuje plik o nazwie BatchImageProcessor. cs
    1. **Processors\BatchImageProcessor.cs**: Ta klasa jest odpowiedzialna za przetwarzanie obrazów. W tym przykładzie użyto modelu klasyfikacji obrazów. Dla każdego obrazu, który zostanie przetworzony, używany algorytm jest następujący:

        1. Konwertuj obraz w tablicy bajtów do przetwarzania. Patrz metoda: `GetBytes(Bitmap image)`
        
            Używany procesor przykładowy obsługuje tylko ramki obrazu JPG kodowane i nie jest w formacie piksela. W przypadku, gdy niestandardowy procesor obsługuje inne kodowanie i/lub format, zaktualizuj `IsMediaFormatSupported` metodę klasy procesora.
        1. Korzystając z [klasy ColorMatrix](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1), Przekonwertuj obraz na szary Skala. Patrz metoda: `ToGrayScale(Image source)` .
        1. Po otrzymaniu obrazu szarej skali obliczamy średnią szarych bajtów skali.
        1. Jeśli średnia wartość < 127, klasyfikowanie obrazu jest klasyfikowane jako "ciemne", w przeciwnym razie klasyfikuje je jako "jasne" z wartością ufności równą 1,0. Patrz metoda: `ProcessImage(List<Image> images)` .

    Możesz dodać własną logikę procesora, modyfikując tę klasę lub dodając nową klasę i implementując metodę:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Po dodaniu nowej klasy należy zaktualizować MediaGraphExtensionService. cs, aby utworzyć wystąpienie klasy, a następnie wywoła na niej metodę ProcessImage w celu uruchomienia logiki przetwarzania. 

## <a name="connect-with-live-video-analytics-module"></a>Łączenie się z modułem analizy wideo na żywo

Po utworzeniu modułu rozszerzenia gRPC teraz utworzysz i wdrożono topologię programu Media Graph.

1. Korzystając z Visual Studio Code, postępuj zgodnie z [tymi instrukcjami](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) , aby zalogować się do platformy Docker.
1. W Visual Studio Code przejdź do pozycji src/Edge. Zobaczysz plik ENV i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego. Zawiera ona niektóre wartości zastępcze. Plik ENV zawiera wartości dla tych zmiennych.
    
    Następnie wybierz opcję Kompiluj i wypchnij IoT Edge rozwiązanie. Dla tego kroku Użyj elementu src/Edge/deployment.grpc.template.js.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Łączenie się z modułem analizy wideo na żywo":::
    
    Ta akcja powoduje utworzenie modułu serwera GRPC i wypchnięcie obrazu do Azure Container Registry.
    Sprawdź, czy istnieją zmienne środowiskowe CONTAINER_REGISTRY_USERNAME_myacr i CONTAINER_REGISTRY_PASSWORD_myacr zdefiniowane w pliku ENV.
1. Przejdź do folderu src/Cloud-to-Device-Console-App. Tutaj zobaczysz appsettings.jsw pliku i kilku innych plikach:

    * C2D-Console-App. csproj — plik projektu dla Visual Studio Code.
    * operations.jslistę operacji, które program ma uruchomić.
    * Program. cs — przykładowy kod programu. Ten kod:

        * Ładuje ustawienia aplikacji.
        * Wywołuje bezpośrednie metody, które są ujawniane w module IoT Edge na żywo. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md).
        * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie terminalu i przeanalizować zdarzenia, które zostały wygenerowane przez moduł w oknie danych wyjściowych.
        * Wywołuje bezpośrednie metody czyszczenia zasobów.
1. Edytuj operations.jsw pliku:

    * Zmień łącze do topologii grafu:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * W obszarze GraphInstanceSet Zmień nazwę topologii wykresu, aby odpowiadała wartości w poprzednim łączu:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * W obszarze GraphTopologyDelete Edytuj nazwę:<br/>`"name": "InferencingWithGrpcExtension"`

            Topologia (na przykład `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` ) musi definiować adres rozszerzenia:
    * Parametr adresu rozszerzenia

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Konfigurowanie

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym, oraz ustawienia konfiguracji dla tych modułów. Wykonaj następujące kroki, aby wygenerować manifest z pliku szablonu, a następnie wdróż go na urządzeniu brzegowym.
Ten krok powoduje utworzenie manifestu wdrażania IoT Edge przy użyciu src/Edge/config/deployment.grpc.amd64.jsw systemie. Kliknij prawym przyciskiem myszy ten plik i wybierz polecenie **Utwórz wdrożenie dla jednego urządzenia**.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Generowanie i wdrażanie manifestu wdrażania IoT Edge":::

Następnie Visual Studio Code prosi o wybranie urządzenia IoT Hubowego. Wybierz urządzenie IoT Edge, które powinno być LVA-Sample-Device.
Na tym etapie wdrożenie modułów brzegowych na urządzeniu IoT Edge zostało rozpoczęte. W ciągu około 30 sekund Odśwież IoT Hub platformy Azure w lewej dolnej części w Visual Studio Code. Powinieneś zobaczyć, że nowy moduł został wdrożony o nazwie lvaExtension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="Nowy moduł został wdrożony o nazwie lvaExtension":::

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z instrukcjami dotyczącymi **przygotowania do monitorowania zdarzeń** wymienionymi w sekcji [Analizowanie wideo na żywo z modelem](use-your-model-quickstart.md) Szybki Start, aby uruchomić przykład i zinterpretować wyniki. Ponadto zapoznaj się z naszymi przykładowymi topologiami gRPC: [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension i [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).