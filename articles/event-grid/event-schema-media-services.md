---
title: Usługa Azure Media Services jako źródło siatki zdarzeń
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń usługi Media Services za pomocą usługi Azure Event Grid
services: media-services
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-grid
ms.workload: ''
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: spelluru
ms.openlocfilehash: d5d50bbde927efd4aee0cedd69486a52ab8c328b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394334"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Usługa Azure Media Services jako źródło siatki zdarzeń

Ten artykuł zawiera schematy i właściwości zdarzeń usługi Media Services.

## <a name="job-related-event-types"></a>Typy zdarzeń związanych z zadaniami

Usługa Media Services emituje typy zdarzeń związanych z **zadaniami** opisane poniżej. Istnieją dwie kategorie zdarzeń związanych z **zadaniami:** "Monitorowanie zmian stanu zadania" i "Monitorowanie zmian stanu wyjściowego zadania". 

Można zarejestrować dla wszystkich zdarzeń, subskrybując jobstatechange zdarzenia. Lub można subskrybować tylko określone zdarzenia (na przykład stanach końcowych, takich jak JobErrored, JobFinished i JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Monitorowanie zmian stanu zadania

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobStateZmiejeć| Pobierz zdarzenie dla wszystkich zmian stanu zadania. |
| Microsoft.Media.JobScheduled| Pobierz zdarzenie, gdy zadanie przechodzi do stanu zaplanowanego. |
| Microsoft.Media.JobProcessing| Pobierz zdarzenie, gdy zadanie przechodzi do stanu przetwarzania. |
| Microsoft.Media.JobCanceling| Pobierz zdarzenie, gdy przejście zadania do stanu anulowania. |
| Microsoft.Media.JobFinished| Pobierz zdarzenie, gdy zadanie przechodzi do stanu zakończonego. Jest to stan końcowy, który zawiera wyjścia zadania.|
| Microsoft.Media.JobCanceled| Pobierz zdarzenie, gdy przejście zadania do stanu anulowanego. Jest to stan końcowy, który zawiera wyjścia zadania.|
| Microsoft.Media.JobErrored| Pobierz zdarzenie, gdy zadanie przechodzi do stanu błędu. Jest to stan końcowy, który zawiera wyjścia zadania.|

Zobacz [przykłady schematu,](#event-schema-examples) które następują.

### <a name="monitoring-job-output-state-changes"></a>Monitorowanie zmian stanu wyjściowego zadania

Zadanie może zawierać wiele wyjść zadania (jeśli skonfigurowano transformację, aby mieć wiele wyjść zadania).) Jeśli chcesz śledzić szczegóły poszczególnych danych wyjściowych zadania, nasłuchuj zdarzenia zmiany danych wyjściowych zadania.

Każde **zadanie** będzie na wyższym poziomie niż **JobOutput**, w związku z tym zdarzenia wyjściowe zadania są uruchamiane wewnątrz odpowiedniego zadania. 

Komunikaty o `JobFinished` `JobCanceled`błędach w , , `JobError` dane wyjściowe zagregowane wyniki dla każdego zadania wyjściowego — po zakończeniu wszystkich z nich. Podczas gdy zdarzenia wyjściowe zadania są uruchamiane po zakończeniu każdego zadania. Na przykład jeśli masz dane wyjściowe kodowania, a następnie dane wyjściowe analizy wideo, można uzyskać dwa zdarzenia wypalania jako zdarzenia wyjściowe zadania przed ostatecznym JobFinished zdarzenia pożarów z zagregowanych danych.

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateZdaniezmienia| Pobierz zdarzenie dla wszystkich zmian stanu wyjścia zadania. |
| Witryna Microsoft.Media.JobOutputscheduled| Pobierz zdarzenie, gdy wyjście zadania przechodzi do stanu zaplanowanego. |
| Microsoft.Media.JobOutputProcessing| Pobierz zdarzenie, gdy wyjście zadania przechodzi do stanu przetwarzania. |
| Microsoft.Media.JobOutputCanceling| Pobierz zdarzenie, gdy przejście wyjściowe zadania do stanu anulowania.|
| Microsoft.Media.JobOutputKońkończone| Pobierz zdarzenie, gdy przejście wyjściowe zadania do stanu gotowego.|
| Microsoft.Media.JobOutputCanceled| Pobierz zdarzenie, gdy przejście wyjściowe zadania do stanu anulowanego.|
| Microsoft.Media.JobOutputErrored| Pobierz zdarzenie, gdy wyjście zadania przechodzi do stanu błędu.|

Zobacz [przykłady schematu,](#event-schema-examples) które następują.

### <a name="monitoring-job-output-progress"></a>Monitorowanie postępu produkcji zadania

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| To zdarzenie odzwierciedla postęp przetwarzania zadań, od 0% do 100%. Usługa próbuje wysłać zdarzenie, jeśli nastąpił 5% lub większy wzrost wartości postępu lub było więcej niż 30 sekund od ostatniego zdarzenia (pulsu). Wartość postępu nie jest gwarantowana, aby rozpocząć od 0%, lub osiągnąć 100%, ani nie jest gwarantowana do wzrostu w stałym tempie w czasie. To zdarzenie nie powinno być używane do określenia, że przetwarzanie zostało zakończone — zamiast tego należy użyć zdarzeń zmiany stanu.|

Zobacz [przykłady schematu,](#event-schema-examples) które następują.

## <a name="live-event-types"></a>Typy wydarzeń na żywo

Usługa Media Services emituje również typy zdarzeń **na żywo** opisane poniżej. Istnieją dwie kategorie dla wydarzeń **na żywo:** zdarzenia na poziomie strumienia i zdarzenia na poziomie ścieżki. 

### <a name="stream-level-events"></a>Zdarzenia na poziomie strumienia

Zdarzenia na poziomie strumienia są wywoływane na strumień lub połączenie. Każde zdarzenie `StreamId` ma parametr, który identyfikuje połączenie lub strumień. Każdy strumień lub połączenie ma jedną lub więcej ścieżek różnych typów. Na przykład jedno połączenie z kodera może mieć jedną ścieżkę audio i cztery ścieżki wideo. Typy zdarzeń strumienia to:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Plik Microsoft.Media.LiveEventConnectionZłożony | Próba połączenia kodera została odrzucona. |
| Microsoft.Media.LiveEventEncoderPołączone | Koder nawiązuje połączenie z wydarzeniem na żywo. |
| Microsoft.Media.LiveEventEncoderRozłączenie | Koder rozłącza się. |

Zobacz [przykłady schematu,](#event-schema-examples) które następują.

### <a name="track-level-events"></a>Zdarzenia na poziomie toru

Zdarzenia na poziomie toru są wywoływane na torze. 

> [!NOTE]
> Wszystkie zdarzenia na poziomie ścieżki są wywoływane po połączeniu kodera na żywo.

Typy zdarzeń na poziomie ścieżki to:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Program Microsoft.Media.LiveEventIncomingDataChunkDropped | Serwer multimediów porzuca fragment danych, ponieważ jest za późno lub ma nakładający się sygnatura czasowa (sygnatura czasowa nowego fragmentu danych jest mniejsza niż czas zakończenia poprzedniego fragmentu danych). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Serwer multimediów odbiera pierwszy fragment danych dla każdej ścieżki w strumieniu lub połączeniu. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Serwer multimediów wykrywa, że strumienie audio i wideo są niezsynchronizowane. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika może nie mieć wpływu. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Serwer multimediów wykrywa, że którykolwiek z dwóch strumieni wideo pochodzących z zewnętrznego kodera jest niezsynchronizowany. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika może nie mieć wpływu. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publikowane co 20 sekund dla każdej ścieżki, gdy wydarzenie na żywo jest uruchomione. Zawiera podsumowanie kondycji pozyskiwania.<br/><br/>Po łączeniu kodera zdarzenie pulsu nadal emituje co 20 sekund, czy koder jest nadal podłączony, czy nie. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Serwer multimediów wykrywa nieciągłość na ścieżce przychodzącej. |

Zobacz [przykłady schematu,](#event-schema-examples) które następują.

## <a name="event-schema-examples"></a>Przykłady schematów zdarzeń

### <a name="jobstatechange"></a>Zmiany w stanowisku pracy

W poniższym przykładzie przedstawiono schemat zdarzenia **JobStateChange:** 

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
| poprzedniPaństwo | ciąg | Stan zadania przed zdarzeniem. |
| state | ciąg | Nowy stan zadania, które jest powiadamiany w tym przypadku. Na przykład "Zaplanowane: zadanie jest gotowe do uruchomienia" lub "Zakończone: Zadanie jest gotowe" .|

Gdzie stan zadania może być jedną z wartości: *Kolejkowane*, *Zaplanowane,* *Przetwarzanie,* *Zakończone,* *Błąd*, *Anulowano*, *Anulowanie*

> [!NOTE]
> *W kolejce* będzie tylko w **previousState właściwości,** ale nie w **state** właściwości.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Dla każdej zmiany stanu zadania niekonktowego (na przykład JobScheduled, JobProcessing, JobCanceling), schemat przykładowy wygląda podobnie do następującego:

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

Dla każdej ostatecznej zmiany stanu zadania (takich jak JobFinished, JobCanceled, JobErrored), schemat przykładowy wygląda podobnie do następującego:

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
| Wyjść | Tablica | Pobiera wyjścia zadania.|

### <a name="joboutputstatechange"></a>JobOutputStateZdanie

W poniższym przykładzie przedstawiono schemat **zdarzenia JobOutputStateChange:**

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

Dla każdej zmiany stanu JobOutput schemat przykładowy wygląda podobnie do następującego:

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
### <a name="joboutputprogress"></a>JobOutputProgress (Ruch przychodzący na wynos)

Przykładowy schemat wygląda podobnie do następującego:

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

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionWysunięty

W poniższym przykładzie przedstawiono schemat **zdarzenia LiveEventConnectionSymected:** 

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
| streamId | ciąg | Identyfikator strumienia lub połączenia. Koder lub klient jest odpowiedzialny za dodanie tego identyfikatora w adresie URL pozyskiwania. |  
| połknieniaUrl | ciąg | Połknąc adres URL podany przez wydarzenie na żywo. |  
| koderIp | ciąg | adres IP kodera. |
| port embardy | ciąg | Port kodera, z którego nadchodzi ten strumień. |
| kod wynikowy | ciąg | Powód, dla którego połączenie zostało odrzucone. Kody wyników są wymienione w poniższej tabeli. |

Kody wyników błędu można znaleźć w [kodach błędów zdarzeń na żywo](../media-services/latest/live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderPołączone

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventEncoderConnected:** 

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
| streamId | ciąg | Identyfikator strumienia lub połączenia. Koder lub klient jest odpowiedzialny za podanie tego identyfikatora w adresie URL pozyskiwania. |
| połknieniaUrl | ciąg | Połknąc adres URL podany przez wydarzenie na żywo. |
| koderIp | ciąg | adres IP kodera. |
| port embardy | ciąg | Port kodera, z którego nadchodzi ten strumień. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderRozłączne

Poniższy przykład przedstawia schemat **zdarzenia LiveEventEncoderDisconnected:** 

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
| streamId | ciąg | Identyfikator strumienia lub połączenia. Koder lub klient jest odpowiedzialny za dodanie tego identyfikatora w adresie URL pozyskiwania. |  
| połknieniaUrl | ciąg | Połknąc adres URL podany przez wydarzenie na żywo. |  
| koderIp | ciąg | adres IP kodera. |
| port embardy | ciąg | Port kodera, z którego nadchodzi ten strumień. |
| kod wynikowy | ciąg | Przyczyną rozłączania kodera. Może to być wdzięczne rozłączenie lub błąd. Kody wyników są wymienione w poniższej tabeli. |

Kody wyników błędu można znaleźć w [kodach błędów zdarzeń na żywo](../media-services/latest/live-event-error-codes.md).

Wdzięczne kody wyników rozłączenia są następujące:

| Kod wyniku | Opis |
| ----------- | ----------- |
| S_OK | Koder rozłączony został pomyślnie odłączony. |
| MPE_CLIENT_TERMINATED_SESSION | Edykder odłączony (RTMP). |
| MPE_CLIENT_DISCONNECTED | Edykder odłączony (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Odebrano polecenie resetowania kanału. |
| MPI_REST_API_CHANNEL_STOP | Odebrano polecenie zatrzymania kanału. |
| MPI_REST_API_CHANNEL_STOP | Kanał poddawany konserwacji. |
| MPI_STREAM_HIT_EOF | Strumień EOF jest wysyłany przez koder. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

W poniższym przykładzie przedstawiono schemat **zdarzenia LiveEventIncomingDataChunkDropped:** 

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
| TrackType | ciąg | Typ utworu (Audio / Video). |
| nazwa utworu | ciąg | Nazwa utworu. |
| Bitrate | liczba całkowita | Szybkość transmisji bitów toru. |
| sygnatura czasowa | ciąg | Sygnatura czasowa fragmentu danych porzucona. |
| skala czasu | ciąg | Skala czasu sygnatury czasowej. |
| kod wynikowy | ciąg | Przyczyna spadku fragmentu danych. **FragmentDrop_OverlapTimestamp** lub **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Poniższy przykład przedstawia schemat **LiveEventIncomingStreamReceived** zdarzenia: 

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
| TrackType | ciąg | Typ utworu (Audio / Video). |
| nazwa utworu | ciąg | Nazwa ścieżki (dostarczona przez koder lub, w przypadku RTMP, serwer generuje w *formacie TrackType_Bitrate).* |
| Bitrate | liczba całkowita | Szybkość transmisji bitów toru. |
| połknieniaUrl | ciąg | Połknąc adres URL podany przez wydarzenie na żywo. |
| koderIp | ciąg  | adres IP kodera. |
| port embardy | ciąg | Port kodera, z którego nadchodzi ten strumień. |
| sygnatura czasowa | ciąg | Pierwszy sygnatura czasowa odebranego fragmentu danych. |
| skala czasu | ciąg | Skala czasu, w której sygnatura czasowa jest reprezentowana. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

W poniższym przykładzie przedstawiono schemat zdarzenia **LiveEventIncomingStreamsOutOfSync:** 

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
| minLastTimestamp (MinLastTimestamp) | ciąg | Minimum ostatniej sygnatury czasowej wśród wszystkich utworów (audio lub wideo). |
| typOfTrackWithMinLastTimestamp | ciąg | Typ ścieżki (audio lub wideo) z minimalną ostatnią sygnaturą czasowej. |
| maxLastTimestamp ( maxLastTimestamp ) | ciąg | Maksymalnie wszystkie sygnatury czasowe wśród wszystkich utworów (audio lub wideo). |
| typOfTrackWithMaxLastTimestamp | ciąg | Typ ścieżki (audio lub wideo) z maksymalną ostatnią sygnaturą czas. |
| sygnatura czasowaOfMinLastTimestamp| ciąg | Pobiera skalę czasu, w którym jest reprezentowana "MinLastTimestamp".|
| timescaleOfMaxLastTimestamp| ciąg | Pobiera skalę czasu, w którym jest reprezentowana "MaxLastTimestamp".|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

W poniższym przykładzie przedstawiono schemat **zdarzenia LiveEventIncomingVideoStreamsOutOfSync:** 

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
| pierwszyStamp czasu | ciąg | Sygnatura czasowa odebrana dla jednego z utworów / poziomów jakości typu wideo. |
| po pierwszeDuration | ciąg | Czas trwania fragmentu danych z pierwszą sygnaturą czasą. |
| secondTimestamp (sygnatura czasowy) | ciąg  | Sygnatura czasowa odebrana dla innego poziomu ścieżki/jakości typu wideo. |
| secondDuration | ciąg | Czas trwania fragmentu danych z drugim sygnaturą czasowej. |
| skala czasu | ciąg | Skala czasu sygnatur czasowych i czas trwania.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

W poniższym przykładzie przedstawiono schemat **zdarzenia LiveEventIngestHeartbeat:** 

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
| TrackType | ciąg | Typ utworu (Audio / Video). |
| nazwa utworu | ciąg | Nazwa ścieżki (dostarczona przez koder lub, w przypadku RTMP, serwer generuje w *formacie TrackType_Bitrate).* |
| Bitrate | liczba całkowita | Szybkość transmisji bitów toru. |
| przychodząceBitrate | liczba całkowita | Obliczona szybkość transmisji bitów na podstawie fragmentów danych pochodzących z kodera. |
| LastTimestamp (sygnatura czas-ostatni) | ciąg | Ostatni sygnatura czasowa odebrana dla toru w ciągu ostatnich 20 sekund. |
| skala czasu | ciąg | Skala czasu, w której są wyrażane sygnatury czasowe. |
| pokrywanie sięliczą | liczba całkowita | Liczba fragmentów danych miała nakładające się sygnatury czasowe w ciągu ostatnich 20 sekund. |
| nieciągłośćCount | liczba całkowita | Liczba nieciągłości zaobserwowanych w ciągu ostatnich 20 sekund. |
| inne niż IncreasingCount | liczba całkowita | Liczba fragmentów danych z sygnaturami czasowymi w przeszłości zostały odebrane w ciągu ostatnich 20 sekund. |
| unexpectedBitrate | bool | Jeśli oczekiwane i rzeczywiste szybkości transmisji bitów różnią się o więcej niż dozwolony limit w ciągu ostatnich 20 sekund. To prawda, jeśli i tylko wtedy, przychodząceBitrate >= 2* bitrate lub incomingBitrate <= bitrate/2 LUB IncomingBitrate = 0. |
| state | ciąg | Stan wydarzenia na żywo. |
| Zdrowe | bool | Wskazuje, czy połkowanie jest w dobrej kondycji na podstawie liczby i flagi. Zdrowy jest true if overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

W poniższym przykładzie przedstawiono schemat **zdarzenia LiveEventTrackDiscontinuityDetected:** 

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
| TrackType | ciąg | Typ utworu (Audio / Video). |
| nazwa utworu | ciąg | Nazwa ścieżki (dostarczona przez koder lub, w przypadku RTMP, serwer generuje w *formacie TrackType_Bitrate).* |
| Bitrate | liczba całkowita | Szybkość transmisji bitów toru. |
| poprzednistamp czasu | ciąg | Sygnatura czasowa poprzedniego fragmentu. |
| newTimestamp (sygnatura czasowego) | ciąg | Sygnatura czasowa bieżącego fragmentu. |
| nieciągłośćGap | ciąg | Odstęp między powyżej dwóch sygnatur czasowych. |
| skala czasu | ciąg | Skala czasu, w której są reprezentowane zarówno sygnatury czasowe, jak i nieciągłości. |

### <a name="common-event-properties"></a>Typowe właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Temat EventGrid. Ta właściwość ma identyfikator zasobu dla konta usługi Media Services. |
| Temat | ciąg | Ścieżka zasobu kanału usługi Media Services na koncie usługi Media Services. Łączenie tematu i tematu daje identyfikator zasobu dla zadania. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. Na przykład "Microsoft.Media.JobStateChange". |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzeń usługi Media Services. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

## <a name="next-steps"></a>Następne kroki

[Zarejestruj się, aby zdarzenia zmiany stanu zadania](../media-services/latest/job-state-events-cli-how-to.md)

## <a name="see-also"></a>Zobacz też

- [Zestaw SDK EventGrid .NET zawierający zdarzenia usługi Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definicje zdarzeń usługi Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Kody błędów zdarzeń na żywo](../media-services/latest/live-event-error-codes.md)
