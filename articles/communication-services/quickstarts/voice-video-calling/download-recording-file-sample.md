---
title: Rejestrowanie i pobieranie połączeń za pomocą Event Grid — Azure Communication Services Szybki start
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku Szybki start dowiesz się, jak rejestrować i pobierać wywołania przy użyciu Event Grid.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730583"
---
# <a name="record-and-download-calls-with-event-grid"></a>Rejestrowanie i pobieranie wywołań za pomocą Event Grid

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Rozpoczynanie pracy z Azure Communication Services przez rejestrowanie wywołań Communication Services przy użyciu Azure Event Grid.

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aktywny zasób Communication Services zasobów. [Utwórz zasób Communication Services zasobów.](../create-communication-resource.md?pivots=platform-azp&tabs=windows)
- Pakiet [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) NuGet.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Tworzenie urządzenia webhook i subskrybowanie zdarzeń rejestrowania
Użyjemy elementy *webhook i* *zdarzeń, aby* ułatwić rejestrowanie wywołań i pobieranie plików multimedialnych. 

Najpierw utworzymy webhook. Zasób Communication Services użyje Event Grid, aby powiadomić ten webhook o wyzwoleniu zdarzenia, a następnie ponownie, gdy zarejestrowany nośnik będzie gotowy `recording` do pobrania.

Możesz napisać własny niestandardowy webhook, aby otrzymywać te powiadomienia o zdarzeniach. Ważne jest, aby ten element webhook odpowiadał na komunikaty przychodzące przy użyciu kodu walidacji, aby pomyślnie zasubskrybować element webhook do usługi zdarzeń.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


Powyższy kod zależy od pakietu `Microsoft.Azure.EventGrid` NuGet. Aby dowiedzieć się więcej na Event Grid weryfikacji punktu końcowego, zapoznaj się z [dokumentacją dotyczącą walidacji punktu końcowego](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation)

Następnie zasubskrybujesz zdarzenie dla tego `recording` webhook:

1. Wybierz `Events` blok z zasobu Azure Communication Services zasobów.
2. Wybierz `Event Subscription` pozycję , jak pokazano poniżej.
![Zrzut ekranu przedstawiający interfejs użytkownika siatki zdarzeń](./media/call-recording/image1-event-grid.png)
3. Skonfiguruj subskrypcję zdarzeń i wybierz `Call Recording File Status Update` pozycję `Event Type` jako . Wybierz `Webhook` pozycję `Endpoint type` jako .
![Tworzenie subskrypcji zdarzeń](./media/call-recording/image2-create-subscription.png)
4. Wprowadź adres URL swojego urządzenia webhook w pliku `Subscriber Endpoint` .
![Subskrybowanie zdarzenia](./media/call-recording/image3-subscribe-to-event.png)

Twój webhook będzie teraz powiadamiany za każdym razem, Communication Services zostanie użyty do nagrania wywołania.

## <a name="notification-schema"></a>Schemat powiadomień
Gdy rejestrowanie będzie dostępne do pobrania, Communication Services będzie emitować powiadomienie z następującym schematem zdarzeń. Identyfikatory dokumentów dla rejestrowania można pobrać z `documentId` pól każdego . `recordingChunk`

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>Pobieranie zarejestrowanych plików multimedialnych

Po pobraniu identyfikatora dokumentu dla pliku, który chcemy pobrać, wywołamy poniższe interfejsy API usługi Azure Communication Services, aby pobrać zarejestrowany nośnik i metadane przy użyciu uwierzytelniania HMAC.

Maksymalny rozmiar pliku rejestrowania wynosi 1,5 GB. Po przekroczeniu tego rozmiaru pliku rejestrator automatycznie podzieli zarejestrowany nośnik na wiele plików.

Klient powinien mieć możliwość pobrania wszystkich plików multimedialnych za pomocą jednego żądania. Jeśli występuje problem, klient może ponowić próbę przy użyciu nagłówka zakresu, aby uniknąć ponownego pobierania segmentów, które zostały już pobrane.

Aby pobrać zarejestrowany nośnik: 
- Metoda: `GET` 
- Adres url: https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Aby pobrać metadane zarejestrowanego nośnika: 
- Metoda: `GET` 
- Adres url: https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Authentication
Aby pobrać zarejestrowane nośniki i metadane, użyj uwierzytelniania HMAC w celu uwierzytelnienia żądania względem Azure Communication Services API.

Utwórz element `HttpClient` i dodaj wymagane nagłówki, korzystając z `HmacAuthenticationUtils` poniższych informacji:

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
Poniższe narzędzia mogą służyć do zarządzania przepływem pracy HMAC.

**Tworzenie skrótu zawartości**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**Dodawanie nagłówków HMAC**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli chcesz wyczyścić i usunąć subskrypcję Communication Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów. Dowiedz się więcej [na temat czyszczenia zasobów.](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources)


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z naszym [przykładowym wywołaniem internetowym](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Dowiedz się więcej o [możliwościach wywoływania zestawu SDK](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Dowiedz się więcej o [tym, jak działa wywoływanie](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
