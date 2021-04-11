---
title: Schematy Azure Event Grid dla zdarzeń Media Services
description: Dowiedz się więcej o właściwościach, które są dostępne dla zdarzeń Media Services z Azure Event Grid.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 6b9d3f6973c210e7bd91c088ee7a276bfbcc48ce
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121986"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Schematy Azure Event Grid dla zdarzeń Media Services

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Ten artykuł zawiera informacje o schematach i właściwościach zdarzeń Media Services.

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [Media Services źródło zdarzeń](../../../event-grid/event-schema-subscriptions.md).

## <a name="job-related-event-types"></a>Typy zdarzeń związanych z zadaniami

Media Services emituje typy zdarzeń powiązane z **zadaniami** opisane poniżej. Istnieją dwie kategorie zdarzeń związanych z **zadaniami** : "monitorowanie zmian stanu zadania" i "monitorowanie zmian stanu danych wyjściowych". 

Wszystkie zdarzenia można zarejestrować, subskrybując zdarzenie JobStateChange. Lub można subskrybować tylko określone zdarzenia (na przykład Stany końcowe, takie jak JobErrored, JobFinished i JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Monitorowanie zmian stanu zadania

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. Media. JobStateChange| Pobierz zdarzenie dla wszystkich zmian stanu zadania. |
| Microsoft. Media. JobScheduled| Pobierz zdarzenie, gdy zadanie przechodzi do stanu zaplanowanego. |
| Microsoft. Media. JobProcessing| Pobierz zdarzenie, gdy zadanie przechodzi do stanu przetwarzania. |
| Microsoft. Media. JobCanceling| Pobierz zdarzenie, gdy zadanie przechodzi do stanu anulowania. |
| Microsoft. Media. JobFinished| Pobierz zdarzenie, gdy zadanie przechodzi do stanu Gotowe. Jest to stan końcowy, który zawiera dane wyjściowe zadania.|
| Microsoft. Media. JobCanceled| Pobierz zdarzenie, gdy zadanie przechodzi do stanu anulowane. Jest to stan końcowy, który zawiera dane wyjściowe zadania.|
| Microsoft. Media. JobErrored| Pobierz zdarzenie, gdy zadanie przechodzi do stanu błędu. Jest to stan końcowy, który zawiera dane wyjściowe zadania.|

Zobacz poniższe [przykłady schematu](#event-schema-examples) .

### <a name="monitoring-job-output-state-changes"></a>Zmiany stanu danych wyjściowych zadania monitorowania

Zadanie może zawierać wiele danych wyjściowych zadania (jeśli przekształcenie zostało skonfigurowane tak, aby miało wiele danych wyjściowych zadania). Jeśli chcesz śledzić szczegóły poszczególnych danych wyjściowych zadania, nasłuchiwanie zdarzenia zmiany danych wyjściowych zadania.

Każde **zadanie** ma być na wyższym poziomie niż **JobOutput**, dzięki czemu zdarzenia wyjściowe zadania są wywoływane w ramach odpowiedniego zadania. 

Komunikaty o błędach w programie, `JobFinished` `JobCanceled` , `JobError` wyprowadza zagregowane wyniki dla każdego wyjścia zadania — po zakończeniu wszystkich z nich. W związku z tym zdarzenia wyjściowe zadania są wyzwalane po zakończeniu każdego zadania. Na przykład, jeśli masz dane wyjściowe kodowania, a następnie dane wyjściowe analizy wideo, otrzymasz dwa zdarzenia wyzwalane jako zdarzenia wyjściowe zadania przed wystąpieniem ostatecznego zdarzenia JobFinished z zagregowanymi danymi.

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. Media. JobOutputStateChange| Pobierz zdarzenie dla wszystkich zmian stanu danych wyjściowych zadania. |
| Microsoft. Media. JobOutputScheduled| Pobierz zdarzenie, gdy dane wyjściowe zadania przejścia do stanu zaplanowano. |
| Microsoft. Media. JobOutputProcessing| Pobierz zdarzenie, gdy dane wyjściowe zadania są przejściami do stanu przetwarzania. |
| Microsoft. Media. JobOutputCanceling| Pobierz zdarzenie, gdy dane wyjściowe zadania zostaną przejścia w stan anulowania.|
| Microsoft. Media. JobOutputFinished| Pobierz zdarzenie, gdy dane wyjściowe zadania przejścia do stanu Gotowe.|
| Microsoft. Media. JobOutputCanceled| Pobierz zdarzenie, gdy dane wyjściowe zadania przejścia do stanu anulowane.|
| Microsoft. Media. JobOutputErrored| Pobierz zdarzenie, gdy dane wyjściowe zadania przejścia do stanu błędu.|

Zobacz poniższe [przykłady schematu](#event-schema-examples) .

### <a name="monitoring-job-output-progress"></a>Postęp danych wyjściowych zadania monitorowania

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. Media. JobOutputProgress| To zdarzenie odzwierciedla postęp przetwarzania zadania, od 0% do 100%. Usługa podejmuje próbę wysłania zdarzenia, jeśli osiągnięto 5% lub większą podwyżkę wartości postępu lub została ona przekroczyć 30 sekund od ostatniego zdarzenia (pulsu). Wartość postępu nie gwarantuje, że zaczyna się o 0%, lub do osiągnięcia 100%, ani nie będzie można zwiększyć stałej stawki z upływem czasu. Tego zdarzenia nie należy używać do ustalenia, czy przetwarzanie zostało zakończone — zamiast tego należy użyć zdarzeń zmiany stanu.|

Zobacz poniższe [przykłady schematu](#event-schema-examples) .

## <a name="live-event-types"></a>Typy zdarzeń na żywo

Media Services również emituje typy zdarzeń na **żywo** opisane poniżej. Istnieją dwie kategorie zdarzeń na **żywo** : zdarzenia na poziomie strumienia i zdarzenia na poziomie śledzenia. 

### <a name="stream-level-events"></a>Zdarzenia na poziomie strumienia

Zdarzenia na poziomie strumienia są wywoływane na strumień lub połączenie. Każde zdarzenie ma `StreamId` parametr, który identyfikuje połączenie lub strumień. Każdy strumień lub połączenie ma jedną lub więcej ścieżek różnych typów. Na przykład jedno połączenie z kodera może mieć jedną ścieżkę audio i cztery ścieżki wideo. Typy zdarzeń strumienia to:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. Media. LiveEventConnectionRejected | Próba połączenia z koderem została odrzucona. |
| Microsoft. Media. LiveEventEncoderConnected | Koder nawiązuje połączenie z wydarzeniem na żywo. |
| Microsoft. Media. LiveEventEncoderDisconnected | Koder rozłącza się. |

Zobacz poniższe [przykłady schematu](#event-schema-examples) .

### <a name="track-level-events"></a>Zdarzenia na poziomie śledzenia

Zdarzenia na poziomie śledzenia są wywoływane na śledzenie. 

> [!NOTE]
> Wszystkie zdarzenia na poziomie śledzenia są wywoływane po podłączeniu kodera na żywo.

Typy zdarzeń na poziomie śledzenia są następujące:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. Media. LiveEventIncomingDataChunkDropped | Serwer multimediów odrzuca fragment danych, ponieważ jest zbyt późny lub ma nakładające się sygnaturę czasową (Sygnatura czasowa nowego fragmentu danych jest mniejsza niż godzina zakończenia poprzedniego fragmentu danych). |
| Microsoft. Media. LiveEventIncomingStreamReceived | Serwer multimediów otrzymuje pierwszy fragment danych dla każdej ścieżki w strumieniu lub połączeniu. |
| Microsoft. Media. LiveEventIncomingStreamsOutOfSync | Serwer multimediów wykrywa strumienie audio i wideo nie są zsynchronizowane. Użyj jako ostrzeżenia, ponieważ może to nie wpływać na środowisko użytkownika. |
| Microsoft. Media. LiveEventIncomingVideoStreamsOutOfSync | Serwer multimediów wykrywa wszystkie dwa strumienie wideo pochodzące z kodera zewnętrznego nie są zsynchronizowane. Użyj jako ostrzeżenia, ponieważ może to nie wpływać na środowisko użytkownika. |
| Microsoft. Media. LiveEventIngestHeartbeat | Opublikowano co 20 sekund dla każdej ścieżki, gdy działa zdarzenie na żywo. Zapewnia podsumowanie kondycji pozyskiwania.<br/><br/>Gdy koder został po raz pierwszy połączony, zdarzenie pulsu będzie nadal emitowane co 20 sekund, niezależnie od tego, czy koder jest nadal połączony, czy nie. |
| Microsoft. Media. LiveEventTrackDiscontinuityDetected | Serwer multimediów wykrywa nieciągłość w ścieżce przychodzącej. |

Zobacz poniższe [przykłady schematu](#event-schema-examples) .

## <a name="event-schema-examples"></a>Przykłady schematów zdarzeń

### <a name="jobstatechange"></a>JobStateChange

W poniższym przykładzie przedstawiono schemat zdarzenia **JobStateChange** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| previousState | ciąg | Stan zadania przed zdarzeniem. |
| stan | ciąg | Nowy stan zadania, które jest zgłaszane w tym zdarzeniu. Na przykład "zaplanowane: zadanie jest gotowe do rozpoczęcia" lub "zakończone: zadanie zostało zakończone".|

Gdzie stan zadania może być jedną z wartości: *kolejkowane*, *zaplanowane*, *przetwarzane*, *zakończone*, *błąd*, *anulowano*, *Anulowanie*

> [!NOTE]
> W *kolejce* jest obecna tylko we właściwości **previousState** , ale nie we właściwości **State** .

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Dla każdej zmiany stanu zadania poza ostateczną (na przykład JobScheduled, JobProcessing, JobCanceling), przykładowy schemat wygląda podobnie do poniższego:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Dla każdej ostatecznej zmiany stanu zadania (na przykład JobFinished, JobCanceled, JobErrored), przykładowy schemat wygląda podobnie do poniższego:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| wydajności | Tablica | Pobiera dane wyjściowe zadania.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

W poniższym przykładzie przedstawiono schemat zdarzenia **JobOutputStateChange** :

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Dla każdej zmiany stanu JobOutput Przykładowy schemat wygląda podobnie do poniższego:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Przykładowy schemat wygląda podobnie do poniższego:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventConnectionRejected** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Obecny streamid | ciąg | Identyfikator strumienia lub połączenia. Koder lub klient jest odpowiedzialny za dodanie tego identyfikatora w adresie URL pozyskiwania. |  
| ingestUrl | ciąg | Adres URL pozyskiwania podany przez wydarzenie na żywo. |  
| encoderIp | ciąg | Adres IP kodera. |
| encoderPort | ciąg | Port kodera, z którego pochodzi ten strumień. |
| resultCode | ciąg | Przyczyna odrzucenia połączenia. Kody wyników są wymienione w poniższej tabeli. |

Kody wyników błędów można znaleźć w [kodach błędów zdarzeń na żywo](../live-event-error-codes-reference.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventEncoderConnected** : 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Obecny streamid | ciąg | Identyfikator strumienia lub połączenia. Koder lub klient jest odpowiedzialny za podanie tego identyfikatora w adresie URL pozyskiwania. |
| ingestUrl | ciąg | Adres URL pozyskiwania podany przez wydarzenie na żywo. |
| encoderIp | ciąg | Adres IP kodera. |
| encoderPort | ciąg | Port kodera, z którego pochodzi ten strumień. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventEncoderDisconnected** : 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Obecny streamid | ciąg | Identyfikator strumienia lub połączenia. Koder lub klient jest odpowiedzialny za dodanie tego identyfikatora w adresie URL pozyskiwania. |  
| ingestUrl | ciąg | Adres URL pozyskiwania podany przez wydarzenie na żywo. |  
| encoderIp | ciąg | Adres IP kodera. |
| encoderPort | ciąg | Port kodera, z którego pochodzi ten strumień. |
| resultCode | ciąg | Przyczyna odłączenia kodera. Może to być bezpieczne rozłączenie lub błąd. Kody wyników są wymienione w poniższej tabeli. |

Kody wyników błędów można znaleźć w [kodach błędów zdarzeń na żywo](../live-event-error-codes-reference.md).

Kody wyniku bezpiecznego rozłączenia to:

| Kod wyniku | Opis |
| ----------- | ----------- |
| S_OK | Koder został rozłączony pomyślnie. |
| MPE_CLIENT_TERMINATED_SESSION | Koder odłączony (RTMP). |
| MPE_CLIENT_DISCONNECTED | Koder odłączony (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Odebrano polecenie resetowania kanału. |
| MPI_REST_API_CHANNEL_STOP | Odebrano polecenie zatrzymania kanału. |
| MPI_REST_API_CHANNEL_STOP | Kanał jest w trakcie konserwacji. |
| MPI_STREAM_HIT_EOF | Strumień EOF jest wysyłany przez koder. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventIncomingDataChunkDropped** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ścieżka śledzenia | ciąg | Typ ścieżki (audio/wideo). |
| ścieżka śledzenia | ciąg | Nazwa ścieżki. |
| Multimedia | liczba całkowita | Szybkość transmisji bitów ścieżki. |
| sygnatura czasowa | ciąg | Sygnatura czasowa fragmentu danych została porzucona. |
| Dział | ciąg | Skala czasu dla sygnatury czasowej. |
| resultCode | ciąg | Przyczyna usunięcia fragmentu danych. **FragmentDrop_OverlapTimestamp** lub **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventIncomingStreamReceived** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ścieżka śledzenia | ciąg | Typ ścieżki (audio/wideo). |
| ścieżka śledzenia | ciąg | Nazwa ścieżki (dostarczonej przez koder lub, w przypadku protokołu RTMP, serwer generuje w formacie *TrackType_Bitrate* ). |
| Multimedia | liczba całkowita | Szybkość transmisji bitów ścieżki. |
| ingestUrl | ciąg | Adres URL pozyskiwania podany przez wydarzenie na żywo. |
| encoderIp | ciąg  | Adres IP kodera. |
| encoderPort | ciąg | Port kodera, z którego pochodzi ten strumień. |
| sygnatura czasowa | ciąg | Odebrano pierwszą sygnaturę czasową fragmentu danych. |
| Dział | ciąg | Skala czasu, w której jest reprezentowana sygnatura czasowa. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventIncomingStreamsOutOfSync** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| minLastTimestamp | ciąg | Minimum ostatnich sygnatur czasowych między wszystkimi ścieżkami (audio lub wideo). |
| typeOfTrackWithMinLastTimestamp | ciąg | Typ ścieżki (audio lub wideo) z minimalną ostatnią sygnaturą czasową. |
| maxLastTimestamp | ciąg | Maksymalna liczba sygnatur czasowych między wszystkimi ścieżkami (audio lub wideo). |
| typeOfTrackWithMaxLastTimestamp | ciąg | Typ ścieżki (audio lub wideo) z maksymalną ostatnią sygnaturą czasową. |
| timescaleOfMinLastTimestamp| ciąg | Pobiera skalę czasu, w której reprezentowana jest wartość "MinLastTimestamp".|
| timescaleOfMaxLastTimestamp| ciąg | Pobiera skalę czasu, w której reprezentowana jest wartość "MaxLastTimestamp".|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventIncomingVideoStreamsOutOfSync** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| firstTimestamp | ciąg | Odebrano sygnaturę czasową dla jednego z poziomów śledzenia/jakości typu wideo. |
| firstDuration | ciąg | Czas trwania fragmentu danych z pierwszym znacznikiem czasu. |
| secondTimestamp | ciąg  | Odebrano sygnaturę czasową dla innego poziomu śledzenia/jakości filmu wideo typu. |
| secondDuration | ciąg | Czas trwania fragmentu danych z drugim znacznikiem czasu. |
| Dział | ciąg | Skala czasu dla sygnatur czasowych i czasu trwania.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventIngestHeartbeat** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ścieżka śledzenia | ciąg | Typ ścieżki (audio/wideo). |
| ścieżka śledzenia | ciąg | Nazwa ścieżki (dostarczonej przez koder lub, w przypadku protokołu RTMP, serwer generuje w formacie *TrackType_Bitrate* ). |
| Multimedia | liczba całkowita | Szybkość transmisji bitów ścieżki. |
| incomingBitrate | liczba całkowita | Obliczona szybkość transmisji bitów na podstawie fragmentów danych pochodzących z kodera. |
| lastTimestamp | ciąg | Odebrano ostatnią sygnaturę czasową dla ścieżki w ciągu ostatnich 20 sekund. |
| Dział | ciąg | Skala czasu, w której są wyrażane sygnatury czasowe. |
| overlapCount | liczba całkowita | Liczba fragmentów danych ma nakładające się sygnatury czasowe w ciągu ostatnich 20 sekund. |
| discontinuityCount | liczba całkowita | Liczba nietrzymań zaobserwowanych w ciągu ostatnich 20 sekund. |
| nonIncreasingCount | liczba całkowita | Liczba fragmentów danych z sygnaturami czasowymi w przeszłości została odebrana w ciągu ostatnich 20 sekund. |
| unexpectedBitrate | bool | Jeśli oczekiwane i rzeczywiste szybkości transmisji bitów różnią się o więcej niż dozwolony limit w ciągu ostatnich 20 sekund. Prawda, jeśli i tylko wtedy, gdy, incomingBitrate >= 2 * szybkość transmisji bitów lub incomingBitrate <= szybkość transmisji bitów/2 lub IncomingBitrate = 0. |
| stan | ciąg | Stan zdarzenia na żywo. |
| dobrej kondycji | bool | Wskazuje, czy pozyskiwanie jest w dobrej kondycji na podstawie liczby i flag. Dobra kondycja ma wartość true, jeśli overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventTrackDiscontinuityDetected** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ścieżka śledzenia | ciąg | Typ ścieżki (audio/wideo). |
| ścieżka śledzenia | ciąg | Nazwa ścieżki (dostarczonej przez koder lub, w przypadku protokołu RTMP, serwer generuje w formacie *TrackType_Bitrate* ). |
| Multimedia | liczba całkowita | Szybkość transmisji bitów ścieżki. |
| previousTimestamp | ciąg | Sygnatura czasowa poprzedniego fragmentu. |
| newTimestamp | ciąg | Sygnatura czasowa bieżącego fragmentu. |
| discontinuityGap | ciąg | Przerwa między powyżej dwóch sygnatur czasowych. |
| Dział | ciąg | Skala czasu, w której reprezentowane są obie sygnatury czasowe i przerwy w działaniu. |

### <a name="common-event-properties"></a>Właściwości typowego zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Temat EventGrid. Ta właściwość ma identyfikator zasobu dla konta Media Services. |
| subject | ciąg | Ścieżka zasobu dla Media Services kanału w ramach konta Media Services. Łączenie tematu i tematu zapewnia identyfikator zasobu dla zadania. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. Na przykład "Microsoft. Media. JobStateChange". |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Media Services dane zdarzenia. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

## <a name="next-steps"></a>Następne kroki

[Rejestrowanie zdarzeń zmiany stanu zadania](../job-state-events-cli-how-to.md)

## <a name="see-also"></a>Zobacz też

- [EventGrid .NET SDK zawierający zdarzenia usługi Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definicje zdarzeń Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Kody błędów zdarzeń na żywo](../live-event-error-codes-reference.md)
