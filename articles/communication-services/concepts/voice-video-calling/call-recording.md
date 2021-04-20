---
title: Azure Communication Services omówienie rejestrowania wywołań
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie funkcji rejestrowania wywołań i interfejsów API.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730610"
---
# <a name="calling-recording-overview"></a>Omówienie wywoływania nagrania

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Wiele krajów i stanów ma przepisy prawne, które mają zastosowanie do rejestrowania połączeń PSTN, połączeń głosowych i wideo, co często wymaga zgody użytkowników na nagrywanie ich wiadomości. To Ty odpowiadasz za korzystanie z funkcji rejestrowania połączeń zgodnie z prawem. Użytkownik musi uzyskać zgodę od stron zarejestrowanej komunikacji w sposób zgodny z przepisami prawa mającymi zastosowanie do każdego uczestnika.

> [!NOTE]
> Przepisy dotyczące obsługi danych osobowych wymagają możliwości eksportowania danych użytkowników. Aby zapewnić obsługę tych wymagań, pliki metadanych rejestrowania obejmują participantId dla każdego uczestnika wywołania w `participants` tablicy. Aby identyfikować uczestników wywołania, można odwoływać się do identyfikatorów MRI w tablicy za pomocą tożsamości użytkowników `participants` wewnętrznych. Poniżej przedstawiono przykład pliku metadanych rejestrowania.

Rejestrowanie wywołań udostępnia zestaw interfejsów API do uruchamiania, zatrzymywania, wstrzymywania i wznawiania rejestrowania. Dostęp do tych interfejsów API można uzyskać z logiki biznesowej po stronie serwera lub za pośrednictwem zdarzeń wyzwalanych przez akcje użytkownika. Zarejestrowane dane wyjściowe multimediów mają format, który jest taki sam jak format używany przez zespół `MP4 Audio+Video` Teams do nagrywania multimediów. Powiadomienia związane z nośnikami i metadanymi są emitowane za pośrednictwem Event Grid. Nagrania są przechowywane przez 48 godzin we wbudowanym magazynie tymczasowym w celu pobrania i przemieszczenia do wybranego rozwiązania do przechowywania długoterminowego. 

## <a name="run-time-control-apis"></a>Interfejsy API sterowania w czasie rzeczywistym
Interfejsy API sterowania w czasie działania mogą służyć do zarządzania rejestrowaniem za pośrednictwem wewnętrznych wyzwalaczy logiki biznesowej, takich jak aplikacja tworząca wywołanie grupy i rejestrowanie konwersacji, lub z akcji wyzwalanych przez użytkownika, która nakazuje aplikacji serwera rozpoczęcie rejestrowania. W każdym scenariuszu `<conversation-id>` program jest wymagany do nagrania określonego spotkania lub rozmowy. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Pobieranie identyfikatora konwersacji z wywołania zainicjowanego przez serwer

A `ConversationId` jest zwracany za pośrednictwem `Microsoft.Communication.CallLegStateChanged` zdarzenia. To powiadomienie o zdarzeniu jest emitowane po nawiązeniu wywołania. Można go znaleźć w `data.ConversationId` polu . Tej wartości można użyć bezpośrednio jako `{conversationId}` parametru w interfejsach API sterowania w czasie uruchamiania:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>Pobieranie identyfikatora konwersacji ze zdarzenia wyzwalanego przez użytkownika na kliencie

Z biblioteki języka JavaScript po ustanowieniu wywołania w celu uzyskania , a następnie zakodowania biblioteki `@azure/communication-calling` `let result = call.info.getConversationUrl()` `conversationUrl` **Base64Url `conversationUrl` `{conversationId}`** w celu uzyskania pliku do użycia w interfejsach API sterowania w czasie rzeczywistym. Kodowanie można wykonać na kliencie przed wysłaniem zdarzenia do serwera lub po stronie serwera.

Należy pamiętać, że obiekt musi być zakodowany w formacie Base64Url, a nie należy go mylić z kodowaniem `conversationUrl`  Base64 (tj. btoa).                                                            

### <a name="start-recording"></a>Rozpocznij nagrywanie

#### <a name="request"></a>Żądanie

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**Zestaw SDK języka C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Reakcja

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Uzyskiwanie stanu rejestrowania wywołań

#### <a name="request"></a>Żądanie

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**Zestaw SDK języka C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Reakcja

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Zatrzymaj nagrywanie
#### <a name="request"></a>Żądanie
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**Zestaw SDK języka C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Reakcja
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Wstrzymywanie rejestrowania
Wstrzymanie i wznowienie rejestrowania połączeń umożliwia pominięcie rejestrowania części rozmowy lub spotkania i wznowienie rejestrowania do pojedynczego pliku. 
#### <a name="request"></a>Żądanie
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**Zestaw SDK języka C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Reakcja
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Wznawianie rejestrowania
#### <a name="request"></a>Żądanie
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**Zestaw SDK języka C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Reakcja
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Typy danych wyjściowych multimediów
Rejestrowanie wywołań obsługuje obecnie mieszany format danych wyjściowych MP4 audio i wideo. Nośnik wyjściowy pasuje do nagrań spotkania wytwarzanych za pośrednictwem rejestrowania w aplikacji Microsoft Teams.

| Typ kanału | Format zawartości | Wideo | Dźwięk |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 1920 x 1080 8 wideo DOTYCZĄCE WSZYSTKICH uczestników domyślnego układu kafelków | Mieszany dźwięk 16 kHz dla wszystkich uczestników |

## <a name="event-grid-notifications"></a>Event Grid powiadomień
Powiadomienie Event Grid jest publikowane, gdy nagranie jest gotowe do pobrania, zazwyczaj 1–2 minuty po zakończeniu procesu rejestrowania (np. spotkanie zostało zakończone, nagranie `Microsoft.Communication.RecordingFileStatusUpdated` zostało zatrzymane). Powiadomienia o zdarzeniu rejestrowania obejmują identyfikator dokumentu, którego można użyć do pobrania zarówno zarejestrowanego nośnika, jak i pliku metadanych rejestrowania:

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Przykładowy kod do obsługi powiadomień usługi Event Grid oraz pobierania plików rejestrowania i metadanych można [znaleźć tutaj.](../../quickstarts/voice-video-calling/download-recording-file-sample.md) 

### <a name="notification-schema"></a>Schemat powiadomień
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Pobieranie pliku

> Azure Communication Services udostępnia krótkoterminowe magazyny multimediów do rejestrowania. **Wyeksportuj dowolną rekordową zawartość, którą chcesz zachować w ciągu 48 godzin.** Po upływie 48 godzin nagrania nie będą już dostępne.

### <a name="download-recording"></a>Pobieranie nagrania
#### <a name="request"></a>Żądanie
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Reakcja
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Pobieranie metadanych rejestrowania
#### <a name="request"></a>Żądanie
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Reakcja
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
