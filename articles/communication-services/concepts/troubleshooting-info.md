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
ms.openlocfilehash: 775075765c8c8eaa94541c0f094c1f7743fe59d9
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886791"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Rozwiązywanie problemów w usłudze Azure Communications Services

Ten dokument pomoże Ci w rozwiązywaniu problemów, które mogą wystąpić w ramach rozwiązania usług komunikacyjnych. W przypadku rozwiązywania problemów z programem SMS można [włączyć raportowanie dostarczania z Event Grid](../quickstarts/telephony-sms/handle-sms-events.md) , aby przechwycić szczegóły dotyczące dostarczania wiadomości SMS.

## <a name="getting-help"></a>Uzyskiwanie pomocy

Zachęcamy deweloperów do przesyłania pytań, sugerujenia funkcji i zgłaszania problemów jako problemów w [repozytorium GitHub](https://github.com/Azure/communication)usług komunikacyjnych. Inne fora obejmują:

* [Pytania i odpowiedzi Microsoft](/answers/questions/topics/single/101418.html)
* [Witryna StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

W zależności od [planu pomocy technicznej](https://azure.microsoft.com/support/plans/) dotyczącej subskrypcji platformy Azure możesz przesłać bilet pomocy technicznej bezpośrednio za pomocą [Azure Portal](https://azure.microsoft.com/support/create-ticket/).

Aby pomóc w rozwiązywaniu niektórych typów problemów, może zostać wyświetlony monit o podanie następujących informacji:

* **Identyfikator MS-CV**: ten identyfikator jest używany do rozwiązywania problemów z wywołaniami i komunikatami. 
* **Identyfikator wywołania**: ten identyfikator jest używany do identyfikowania wywołań usług komunikacyjnych.
* **Identyfikator wiadomości SMS**: ten identyfikator jest używany do identyfikowania wiadomości SMS.
* **Dzienniki wywołań**: te dzienniki zawierają szczegółowe informacje, których można użyć do rozwiązywania problemów z wywoływaniem i siecią.


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

## <a name="enable-and-access-call-logs"></a>Włączanie i uzyskiwanie dostępu do dzienników wywołań




# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy kod może służyć do konfigurowania `AzureLogger` programu w celu wysyłania dzienników do konsoli przy użyciu biblioteki klienckiej języka JavaScript:

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

Podczas tworzenia aplikacji dla systemu iOS dzienniki są przechowywane w `.blog` plikach. Należy pamiętać, że nie można wyświetlić dzienników bezpośrednio, ponieważ są one zaszyfrowane.

Dostęp do nich można uzyskać, otwierając Xcode. Przejdź do urządzeń z systemem Windows > i symulatorów > urządzeń. Wybierz urządzenie. W obszarze zainstalowane aplikacje wybierz aplikację i kliknij pozycję "Pobierz kontener". 

Spowoduje to udostępnienie `xcappdata` pliku. Kliknij prawym przyciskiem myszy ten plik i wybierz polecenie "Pokaż zawartość pakietu". Następnie zobaczysz `.blog` pliki, które można następnie dołączyć do żądania pomocy technicznej platformy Azure.

# <a name="android"></a>[Android](#tab/android)

Podczas tworzenia aplikacji dla systemu Android dzienniki są przechowywane w `.blog` plikach. Należy pamiętać, że nie można wyświetlić dzienników bezpośrednio, ponieważ są one zaszyfrowane.

Na Android Studio przejdź do Eksploratora plików urządzenia, wybierając pozycję Wyświetl > narzędzia Windows > Eksplorator plików urządzenia z symulatora i urządzenia. `.blog`Plik zostanie umieszczony w katalogu aplikacji, który powinien wyglądać podobnie do tego `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` . Możesz dołączyć ten plik do żądania pomocy technicznej. 
   

---


## <a name="related-information"></a>Informacje pokrewne
- [Dzienniki i Diagnostyka](logging-and-diagnostics.md)
- [Metryki](metrics.md)