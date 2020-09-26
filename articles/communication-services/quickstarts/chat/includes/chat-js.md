---
title: dołączanie pliku
description: dołączanie pliku
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376682"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Zainstaluj [Node.js](https://nodejs.org/en/download/) wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../create-communication-resource.md). Musisz zarejestrować **punkt końcowy** zasobu dla tego przewodnika Szybki Start.
- [Token dostępu użytkownika](../../access-tokens.md). Upewnij się, że ustawiono zakres "Rozmowa" i zanotuj ciąg tokenu, a także ciąg identyfikatora użytkownika.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-web-application"></a>Tworzenie nowej aplikacji sieci Web

Najpierw Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
Uruchom `npm init -y` , aby utworzyć **package.jsw** pliku z ustawieniami domyślnymi.

```console
npm init -y
```

Użyj edytora tekstów, aby utworzyć plik o nazwie **start-chat.js** w katalogu głównym projektu. Do tego pliku zostanie dodany kod źródłowy dla tego przewodnika Szybki Start w poniższych sekcjach.

### <a name="install-the-packages"></a>Zainstaluj pakiety

Użyj `npm install` polecenia, aby zainstalować poniższe biblioteki klienckie usług komunikacyjnych dla języka JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Ten przewodnik Szybki Start używa pakietu WebPack do łączenia zasobów aplikacji. Uruchom następujące polecenie, aby zainstalować pakiety WebPack, WebPack-CLI i WebPack — dev-Server npm i wyświetlić je jako zależności deweloperskie w **package.jsna**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Utwórz plik **index.html** w katalogu głównym projektu. Użyjemy tego pliku jako szablonu, aby dodać możliwość rozmowy przy użyciu biblioteki klienckiej rozmowy komunikacyjnej platformy Azure dla języka JavaScript.

Oto kod:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
Utwórz plik w katalogu głównym projektu o nazwie **client.js** , aby zawierał logikę aplikacji dla tego przewodnika Szybki Start. 

### <a name="create-a-chat-client"></a>Tworzenie klienta czatu

Aby utworzyć klienta programu chat w aplikacji sieci Web, należy użyć punktu końcowego usługi komunikacyjnej i tokenu dostępu, który został wygenerowany w ramach kroków wymagań wstępnych. Tokeny dostępu użytkowników umożliwiają tworzenie aplikacji klienckich, które bezpośrednio uwierzytelniają się w usłudze Azure Communications Services. Po wygenerowaniu tych tokenów na serwerze Przekaż je z powrotem do urządzenia klienckiego. Musisz użyć `AzureCommunicationUserCredential` klasy z, `Common client library` Aby przekazać token do klienta czatu.

Utwórz plik **client.js** w katalogu głównym projektu. Użyjemy tego pliku, aby dodać możliwość rozmowy przy użyciu biblioteki klienckiej usługi Azure Communication Chat dla języka JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
Zastąp **punkt końcowy** utworzony wcześniej na podstawie dokumentacji dotyczącej [tworzenia zasobów usługi Azure Communication](../../create-communication-resource.md) .
Zastąp **USER_ACCESS_TOKEN** tokenem wystawionym na podstawie dokumentacji [tokenu dostępu użytkownika](../../access-tokens.md) .
Dodaj ten kod do pliku **client.js**


### <a name="run-the-code"></a>Uruchamianie kodu
Użyj, `webpack-dev-server` Aby skompilować i uruchomić aplikację. Uruchom następujące polecenie, aby powiązać hosta aplikacji w lokalnym serwerze WebServer:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Otwórz przeglądarkę i przejdź do http://localhost:8080/ .
W konsoli narzędzia deweloperskie w przeglądarce powinny zostać wyświetlone następujące elementy:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Model obiektów 
Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta czatu usługi Azure Communication Services dla języka JavaScript.

| Nazwa                                   | Opis                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClientu i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i odczytywania, subskrybowania zdarzeń rozmowy. |


## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `createThread` metody, aby utworzyć wątek rozmowy.

`createThreadRequest` służy do opisywania żądania wątku:

- Użyj, `topic` Aby przekazać temat do tego rozmowy. Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `UpdateThread` funkcji. 
- Użyj, `members` Aby wyświetlić listę elementów członkowskich, które mają zostać dodane do wątku rozmowy.

Po rozwiązaniu `createChatThread` Metoda zwraca metodę, `threadId` która jest używana do wykonywania operacji na nowo utworzonym wątku rozmowy, takich jak dodawanie elementów członkowskich do wątku rozmowy, wysyłanie komunikatów, usuwanie wiadomości itd.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

Zastąp **USER_ID_FOR_JACK** i **USER_ID_FOR_GEETA** identyfikatorami użytkowników uzyskanymi w poprzednim kroku (tworzenie użytkowników i wystawianie [tokenów dostępu użytkowników](../../access-tokens.md))

Po odświeżeniu karty przeglądarki w konsoli programu powinny zostać wyświetlone następujące elementy:
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

`getChatThreadClient`Metoda zwraca `chatThreadClient` dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj członków, Wyślij wiadomość itp. threadId jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
Dodaj ten kod zamiast `<CREATE CHAT THREAD CLIENT>` komentarza w **client.js**, Odśwież kartę przeglądarki i sprawdź konsolę, powinny być widoczne następujące tematy:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `sendMessage` metody, aby wysłać wiadomość czatu do właśnie utworzonego wątku, identyfikowanego przez ThreadID.

`sendMessageRequest` opisuje wymagane pola żądania wiadomości czatu:

- Użyj, `content` Aby podać zawartość wiadomości czatu;

`sendMessageOptions` opisuje opcjonalne pola żądania wiadomości czatu:

- Służy `priority` do określania poziomu priorytetu wiadomości czatu, takiego jak "normal" lub "High". Ta właściwość może być używana jako wskaźnik interfejsu użytkownika dla użytkownika odbiorcy w aplikacji, aby mógł zwrócić uwagę na komunikat lub wykonać niestandardową logikę biznesową.   
- Użyj, `senderDisplayName` Aby określić nazwę wyświetlaną nadawcy;

Odpowiedź `sendChatMessageResult` zawiera identyfikator, który jest unikatowym identyfikatorem tego komunikatu.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
Dodaj ten kod zamiast `<SEND MESSAGE TO A CHAT THREAD>` komentarza w **client.js**, Odśwież kartę przeglądarki i sprawdź konsolę.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie komunikatów rozmowy z wątku rozmowy

Dzięki sygnalizowaniu w czasie rzeczywistym można subskrybować nasłuchiwanie nowych wiadomości przychodzących i aktualizowanie bieżących komunikatów w pamięci. Usługi komunikacyjne platformy Azure obsługują [listę zdarzeń, które można subskrybować](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Dodaj ten kod zamiast `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` komentarza w **client.js**.
Odśwież kartę przeglądarki, która powinna zostać wyświetlona w konsoli programu `Notification chatMessageReceived` .

Alternatywnie można pobrać wiadomości czatu przez sondowanie `listMessages` metody w określonych odstępach czasu. 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
Dodaj ten kod zamiast `<LIST MESSAGES IN A CHAT THREAD>` komentarza w **client.js**.
Odśwież kartę, w konsoli powinna znajdować się lista komunikatów wysyłanych w tym wątku rozmowy.


`listMessages` zwraca najnowszą wersję komunikatu, w tym wszelkie edycje lub usunięcia, które wystąpiły w komunikacie przy użyciu `updateMessage` i `deleteMessage` .
W przypadku usuniętych wiadomości `chatMessage.deletedOn` zwraca wartość typu DateTime wskazującą, kiedy ten komunikat został usunięty. W przypadku edytowanych wiadomości `chatMessage.editedOn` zwraca wartość typu DateTime wskazującą, kiedy wiadomość była edytowana. Można uzyskać dostęp do oryginalnego czasu tworzenia komunikatów przy użyciu programu `chatMessage.createdOn` , którego można użyć do porządkowania komunikatów.

`listMessages` zwraca różne typy komunikatów, które mogą być identyfikowane przez `chatMessage.type` . Są to następujące typy:

- `Text`: Zwykły komunikat rozmowy Wysłany przez element członkowski wątku.

- `ThreadActivity/TopicUpdate`: Komunikat systemowy wskazujący, że Zaktualizowano temat.

- `ThreadActivity/AddMember`: Komunikat systemowy wskazujący, że co najmniej jeden element członkowski został dodany do wątku rozmowy.

- `ThreadActivity/RemoveMember`: Komunikat systemowy wskazujący, że element członkowski został usunięty z wątku rozmowy.

Aby uzyskać więcej informacji, zobacz [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Dodawanie użytkownika jako elementu członkowskiego do wątku rozmowy

Po utworzeniu wątku rozmowy można z niego dodawać i usuwać użytkowników. Dodanie użytkowników daje im dostęp do wysyłania komunikatów do wątku rozmowy i dodawania/usuwania innych członków. Przed wywołaniem `addMembers` metody upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

`addMembersRequest` opisuje obiekt request, który `members` zawiera listę elementów członkowskich, które mają zostać dodane do wątku rozmowy.
- `user`, wymagane, to użytkownik komunikacyjny, który ma zostać dodany do wątku rozmowy.
- `displayName`, opcjonalnie, jest nazwą wyświetlaną dla elementu członkowskiego wątku.
- `shareHistoryTime`, opcjonalnie, to czas, od którego historia rozmowy jest udostępniana członkowi. Aby udostępnić historię od momentu rozpoczęcia wątku rozmowy, należy ustawić tę właściwość na dowolną datę równą lub mniejszą niż godzina utworzenia wątku. Aby po dodaniu elementu członkowskiego nie była udostępniona żadna historia, ustaw ją na bieżącą datę. Aby udostępnić historię częściową, ustaw ją na wybraną datę.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
Zastąp **NEW_MEMBER_USER_ID** [nowym identyfikatorem użytkownika](../../access-tokens.md) Dodaj ten kod zamiast `<ADD NEW MEMBER TO THREAD>` komentarza w **client.js**

## <a name="list-users-in-a-chat-thread"></a>Wyświetlanie listy użytkowników w wątku rozmowy
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
Dodaj ten kod zamiast `<LIST MEMBERS IN A THREAD>` komentarza w **client.js**, Odśwież kartę przeglądarki i sprawdź konsolę, powinny być widoczne informacje o użytkownikach w wątku.

## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Podobnie jak w przypadku dodawania elementu członkowskiego, można usunąć członków z wątku rozmowy. Aby można było usunąć, należy śledzić identyfikatory dodanych elementów członkowskich.

Użyj `removeMember` metody, w której `member` użytkownik komunikacyjny ma zostać usunięty z wątku.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
Zastąp **MEMBER_ID** identyfikatorem użytkownika użytym w poprzednim kroku (<NEW_MEMBER_USER_ID>).
Dodaj ten kod zamiast `<REMOVE MEMBER FROM THREAD>` komentarza w **client.js**,
