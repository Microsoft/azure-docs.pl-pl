---
title: gRPC rozszerzenia protokołu — Azure
description: W tym artykule dowiesz się, jak za pomocą protokołu rozszerzenia gRPC wysyłać wiadomości między modułem analizy wideo na żywo a rozszerzeniem niestandardowym AI lub CV.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f7b5be859702199b07dfa0d6a43a09ca8ff0c42f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455860"
---
# <a name="grpc-extension-protocol"></a>Protokół rozszerzenia gRPC

Funkcja analizy filmów wideo na żywo w systemie IoT Edge umożliwia rozszerzanie możliwości przetwarzania wykresów multimedialnych za pomocą [węzła rozszerzenia grafu](/azure/media-services/live-video-analytics-edge/media-graph-extension-concept). Jeśli używasz procesora rozszerzenia gRPC jako węzła rozszerzenia, komunikacja między modułem analizy wideo na żywo a modułem AI lub CV jest realizowana za pośrednictwem gRPC opartego na wysoce wydajnym protokole strukturalnym.

W tym artykule dowiesz się, jak za pomocą protokołu rozszerzenia gRPC wysyłać wiadomości między modułem analizy wideo na żywo a rozszerzeniem niestandardowym AI lub CV.

gRPC to nowoczesne środowisko RPC typu open source, które działa w dowolnym środowisku i obsługuje komunikację między platformami i między różnymi językami. Usługa transportowa gRPC używa dwukierunkowego przesyłania strumieniowego protokołu HTTP/2 między:

* Klient gRPC (Analiza wideo na żywo na IoT Edge module) i 
* serwer gRPC (rozszerzenie niestandardowe).

Sesja gRPC jest jednym połączeniem z klienta gRPC do serwera gRPC za pośrednictwem portu TCP/TLS. 

W pojedynczej sesji: klient wysyła do serwera deskryptor strumienia multimediów, a następnie ramki wideo z serwerem jako komunikat [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) za pośrednictwem sesji strumienia gRPC. Serwer sprawdza poprawność deskryptora strumienia, analizuje ramkę wideo i zwraca wyniki wnioskowania jako komunikat protobuf. 

Zdecydowanie zaleca się, aby odpowiedzi są zwracane przy użyciu prawidłowych dokumentów JSON, zgodnie ze wstępnie ustanowionym schematem zdefiniowanym zgodnie z [modelem obiektu schematu metadanych wnioskowania](/azure/media-services/live-video-analytics-edge/inference-metadata-schema). Pozwoli to lepiej zapewnić współdziałanie z innymi składnikami i możliwymi przyszłymi możliwościami dodanymi do modułu analizy wideo na żywo.

![kontrakt rozszerzenia gRPC](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>Implementowanie protokołu gRPC

### <a name="creating-a-grpc-connection"></a>Tworzenie połączenia gRPC

Rozszerzenie niestandardowe musi implementować następujące usługi gRPC:

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

Po wywołaniu spowoduje to otwarcie dwukierunkowego strumienia dla komunikatów do przepływu między rozszerzeniem gRPC i wykresem analizy wideo na żywo. Pierwszy komunikat wysłany w tym strumieniu przez każdą ze stron będzie zawierać MediaStreamDescriptor, który określa, jakie informacje będą wysyłane w następującej MediaSamples.

Na przykład rozszerzenie grafu może wysłać wiadomość (wyrażoną tutaj w formacie JSON), aby wskazać, że będzie wysyłać 416x416e ramki zakodowane RGB24 osadzone w komunikatach gRPC do niestandardowego rozszerzenia.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

Niestandardowe rozszerzenie w odpowiedzi wyśle następujący komunikat, aby wskazać, że zwraca tylko wnioskowania.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

Teraz, gdy obie strony mają wymieniane deskryptory multimediów, analiza wideo na żywo zacznie przeciążać ramki do rozszerzenia.

> [!NOTE]
> Implementację po stronie serwera gRPC można wykonać w wybranym języku programowania.
### <a name="sequence-numbers"></a>Numery sekwencyjne

Węzeł rozszerzenia gRPC i rozszerzenie niestandardowe utrzymują oddzielny zestaw numerów sekwencji przypisanych do swoich komunikatów. Te numery sekwencji powinny monotonicznie się zwiększyć od 1. `ack_sequence_number` można zignorować, jeśli nie zostanie potwierdzony komunikat, który może wystąpić podczas pierwszego wysłania wiadomości.

Żądanie musi zostać potwierdzone, gdy odpowiadający mu komunikat zostanie w pełni przetworzony. W przypadku transferu pamięci udostępnionej, to potwierdzenie wskazuje, że nadawca może zwolnić udostępnioną pamięć i że odbiorca nie ma już do niej dostępu. Nadawca musi przechowywać wszelkie udostępnione pamięci, dopóki nie zostanie potwierdzone.

### <a name="reading-embedded-content"></a>Odczytywanie zawartości osadzonej

Zawartość osadzona może zostać odczytana bezpośrednio z `MediaSample` wiadomości za pomocą `content_byte` pola s. Dane zostaną zakodowane zgodnie z `MediaDescriptor` .

### <a name="reading-content-from-shared-memory"></a>Odczytywanie zawartości z pamięci współdzielonej

Podczas transferowania zawartości za pomocą pamięci współdzielonej nadawca będzie zawierać `SharedMemoryBufferTransferProperties` w `MediaStreamDescriptor` momencie pierwszego ustanowienia sesji. Może to wyglądać w następujący sposób (wyrażony w formacie JSON):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Następnie odbiorca otworzy plik `/dev/shm/inference_client_share_memory_2146989006636459346` . Nadawca wyśle `MediaSample` komunikat, który zawiera `ContentReference` odwołanie do określonego miejsca w tym pliku.

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

Następnie odbiorca odczytuje dane z tej lokalizacji w pliku.

Aby w przypadku kontenera analizy wideo na żywo komunikować się za pośrednictwem pamięci współdzielonej, należy prawidłowo skonfigurować tryb IPC kontenera. Można to zrobić na wiele sposobów, ale poniżej przedstawiono kilka zalecanych konfiguracji.

* Podczas komunikowania się z aparatem inferencing gRPC uruchomionym na urządzeniu hosta należy ustawić tryb IPC na wartość host.
* Podczas komunikowania się z serwerem gRPC uruchomionym w innym module IoT Edge, tryb IPC powinien być ustawiony tak, aby można go było udostępnić dla modułu analizy wideo na żywo i `container:liveVideoAnalytics` dla niestandardowego rozszerzenia, gdzie `liveVideoAnalytics` jest nazwą modułu analizy wideo na żywo.

Oto, co może wyglądać w postaci sznurka urządzenia przy użyciu pierwszej opcji z powyżej.

```
"liveVideoAnalytics": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "host"
      }
  }
}
```

Aby uzyskać więcej informacji na temat trybów IPC, zobacz https://docs.docker.com/engine/reference/run/#ipc-settings---ipc .

## <a name="mediagraph-grpc-extension-contract-definitions"></a>Definicje kontraktu rozszerzenia MediaGraph gRPC

Ta sekcja definiuje kontrakt gRPC, który definiuje przepływ danych.

### <a name="protocol-messages"></a>Komunikaty protokołu

![Komunikaty protokołu](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>Uwierzytelnianie klienta

Implementacje rozszerzeń niestandardowych mogą weryfikować autentyczność przychodzących połączeń gRPC, aby upewnić się, że pochodzą one z węzła rozszerzenia gRPC. Węzeł dostarczy wpis w nagłówkach żądania, aby sprawdzić poprawność.

Do osiągnięcia tego celu można użyć poświadczeń nazwy użytkownika/hasła. Podczas tworzenia węzła rozszerzenia gRPC poświadczenia są podane poniżej:

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

Po wysłaniu żądania gRPC następujący nagłówek zostanie uwzględniony w metadanych żądania, naśladując uwierzytelniania podstawowego protokołu HTTP.

`x-ms-authentication: Basic (Base64 Encoded username:password)`


## <a name="configuring-inference-server-for-each-mediagraph-over-grpc-extension"></a>Konfigurowanie serwera wnioskowania dla każdego MediaGraph przez rozszerzenie gRPC
Podczas konfigurowania serwera wnioskowania nie trzeba ujawniać uwidocznienia węzła dla każdego modelu AI spakowanego na serwerze wnioskowania. Zamiast tego dla wystąpienia grafu można użyć `extensionConfiguration` właściwości `MediaGraphGrpcExtension` węzła i zdefiniować sposób wybierania modeli AI. Podczas wykonywania LVA przekaże ten ciąg do serwera inferencing, który może go użyć do wywołania żądanego modelu AI. Ta `extensionConfiguration` Właściwość jest opcjonalną właściwością i jest specyficzna dla serwera. Właściwość może być używana jak poniżej:
```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "5"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>Korzystanie z gRPC za pośrednictwem protokołu TLS

Połączenie gRPC używane dla inferencing może być zabezpieczone przez protokół TLS. Jest to przydatne w sytuacjach, gdy nie można zagwarantować bezpieczeństwa sieci między analizą wideo na żywo i aparatem inferencing. Protokół TLS szyfruje zawartość osadzoną w komunikatach gRPC, co powoduje dodatkowe obciążenie procesora podczas przesyłania klatek z dużą szybkością.

Opcje weryfikacji IgnoreHostname i IgnoreSignature nie są obsługiwane przez gRPC, więc certyfikat serwera, który przedstawia aparat inferencing, musi zawierać nazwę POSPOLITą zgodną z adresem IP/nazwą hosta w adresie URL punktu końcowego węzła rozszerzenia gRPC.

## <a name="next-steps"></a>Następne kroki

[Informacje o schemacie metadanych wnioskowania](inference-metadata-schema.md)
