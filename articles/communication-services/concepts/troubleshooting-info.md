---
title: Rozwiązywanie problemów w usłudze Azure Communications Services
description: Dowiedz się, jak zebrać informacje potrzebne do rozwiązywania problemów z rozwiązaniem usług komunikacyjnych.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754749"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Rozwiązywanie problemów w usłudze Azure Communications Services

Ten dokument pomoże Ci zebrać informacje potrzebne do rozwiązywania problemów z rozwiązaniem usług komunikacyjnych.

## <a name="getting-help"></a>Uzyskiwanie pomocy

Zachęcamy deweloperów do przesyłania pytań, sugerujenia funkcji i zgłaszania problemów jako problemów w [repozytorium GitHub](https://github.com/Azure/communication)usług komunikacyjnych. Inne fora obejmują:

* [Pytania i odpowiedzi Microsoft](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [Witryna StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

W zależności od [planu pomocy technicznej](https://azure.microsoft.com/support/plans/) dotyczącej subskrypcji platformy Azure możesz przesłać bilet pomocy technicznej bezpośrednio za pomocą [Azure Portal](https://azure.microsoft.com/support/create-ticket/).

Aby pomóc w rozwiązywaniu niektórych typów problemów, może zostać wyświetlony monit o podanie następujących informacji:

* **Identyfikator MS-CV** : ten identyfikator jest używany do rozwiązywania problemów z wywołaniami i komunikatami. 
* **Identyfikator wywołania** : ten identyfikator jest używany do identyfikowania wywołań usług komunikacyjnych.
* **Identyfikator wiadomości SMS** : ten identyfikator jest używany do identyfikowania wiadomości SMS.

## <a name="access-your-ms-cv-id"></a>Uzyskaj dostęp do identyfikatora MS-CV

Identyfikator MS-CV można uzyskać, konfigurując diagnostykę w `clientOptions` wystąpieniu obiektu podczas inicjowania bibliotek klienckich. Diagnostykę można skonfigurować dla dowolnej biblioteki klienta platformy Azure, w tym rozmowy, administrowania i VoIP.

### <a name="client-options-example"></a>Przykład opcji klienta

Poniższe fragmenty kodu przedstawiają konfigurację diagnostyki. Gdy biblioteki klienckie są używane z włączoną diagnostyką, szczegóły diagnostyczne będą wysyłane do skonfigurowanego odbiornika zdarzeń:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Dostęp do identyfikatora wywołania

W przypadku zgłoszenia żądania pomocy technicznej przez Azure Portal związane z problemami z wywoływaniem może zostać wyświetlony monit o podanie identyfikatora wywołania, do którego się odwołujesz. Dostęp do niego można uzyskać za pomocą biblioteki wywołującej klienta:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Uzyskaj dostęp do identyfikatora wiadomości SMS

W przypadku problemów z programem SMS można zebrać identyfikator wiadomości z obiektu Response.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Informacje pokrewne
- [Dzienniki i Diagnostyka](logging-and-diagnostics.md)
- [Metryki](metrics.md)
