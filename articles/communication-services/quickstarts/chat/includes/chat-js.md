---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 0805537fe0791a622eb1814cc233c04d914dbecd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612976"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zainstaluj [Node.js](https://nodejs.org/en/download/) wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../create-communication-resource.md). Musisz **zarejestrować punkt końcowy zasobu** dla tego przewodnika Szybki Start.
- Utwórz *trzech* użytkowników usługi ACS i wystawiaj im [token dostępu użytkownika](../../access-tokens.md)tokenu dostępu użytkownika. Pamiętaj, aby ustawić zakres do **rozmowy** i **zanotować ciąg tokenu oraz ciąg identyfikatora użytkownika**. Pełny pokaz tworzy wątek z dwoma początkowymi uczestnikami, a następnie dodaje trzeciego uczestnika do wątku.

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

### <a name="install-the-packages"></a>Zainstaluj pakiety

Użyj `npm install` polecenia, aby zainstalować poniższe biblioteki klienckie usług komunikacyjnych dla języka JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Ten przewodnik Szybki Start używa pakietu WebPack do łączenia zasobów aplikacji. Uruchom następujące polecenie, aby zainstalować pakiety WebPack, WebPack-CLI i WebPack — dev-Server npm i wyświetlić je jako zależności deweloperskie w **package.jsna**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Utwórz `webpack.config.js` plik w katalogu głównym. Skopiuj następującą konfigurację do tego pliku:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Dodaj `start` skrypt do programu `package.json` , który będzie używany do uruchamiania aplikacji. W `scripts` sekcji `package.json` Dodaj następujące elementy:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Utwórz plik **index.html** w katalogu głównym projektu. Użyjemy tego pliku jako szablonu, aby dodać możliwość rozmowy przy użyciu biblioteki klienckiej rozmowy komunikacyjnej platformy Azure dla języka JavaScript.

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

Aby utworzyć klienta programu chat w aplikacji sieci Web, należy użyć **punktu końcowego** usługi komunikacyjnej i **tokenu dostępu** , który został wygenerowany w ramach kroków wymagań wstępnych.

Tokeny dostępu użytkowników umożliwiają tworzenie aplikacji klienckich, które bezpośrednio uwierzytelniają się w usłudze Azure Communications Services. Ten przewodnik Szybki Start nie obejmuje tworzenia warstwy usług w celu zarządzania tokenami aplikacji czatu. Aby uzyskać więcej informacji o tokenach dostępu, zobacz [pojęcia dotyczące rozmowy](../../../concepts/chat/concepts.md) , aby uzyskać więcej informacji o architekturze rozmowy i [tokenach dostępu użytkowników](../../access-tokens.md) .

Wewnątrz **client.js** Użyj punktu końcowego i tokenu dostępu w poniższym kodzie, aby dodać możliwość rozmowy przy użyciu biblioteki klienckiej usługi Azure Communication Chat dla języka JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Zastąp **endpointUrl** w punkcie końcowym zasobów usług komunikacyjnych, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../create-communication-resource.md) , jeśli jeszcze tego nie zrobiono.
- Zamień **userAccessToken** na wystawiony token.


### <a name="run-the-code"></a>Uruchamianie kodu

Uruchom następujące polecenie, aby powiązać hosta aplikacji w lokalnym serwerze WebServer:
```console
npm run start
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

- Użyj `topic` , aby przekazać temat do tego rozmowy. Tematy można aktualizować po utworzeniu wątku rozmowy przy użyciu `UpdateThread` funkcji.
- Użyj, `participants` Aby wyświetlić listę uczestników, którzy mają zostać dodani do wątku rozmowy.

Po rozwiązaniu `createChatThread` Metoda zwraca `CreateChatThreadResult` . Ten model zawiera właściwość, w `chatThread` której można uzyskać dostęp do `id` nowo utworzonego wątku. Następnie można użyć, `id` Aby pobrać wystąpienie elementu `ChatThreadClient` . `ChatThreadClient`Można następnie użyć do wykonania operacji w ramach wątku, takiego jak wysyłanie komunikatów lub wyświetlanie listy uczestników.

```JavaScript
async function createChatThread() {
    let createThreadRequest = {
        topic: 'Preparation for London conference',
        participants: [{
                    id: { communicationUserId: '<USER_ID_FOR_JACK>' },
                    displayName: 'Jack'
                }, {
                    id: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                    displayName: 'Geeta'
                }]
    };
    let createChatThreadResult = await chatClient.createChatThread(createThreadRequest);
    let threadId = createChatThreadResult.chatThread.id;
    return threadId;
    }

createChatThread().then(async threadId => {
    console.log(`Thread created:${threadId}`);
    // PLACEHOLDERS
    // <CREATE CHAT THREAD CLIENT>
    // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
    // <SEND MESSAGE TO A CHAT THREAD>
    // <LIST MESSAGES IN A CHAT THREAD>
    // <ADD NEW PARTICIPANT TO THREAD>
    // <LIST PARTICIPANTS IN A THREAD>
    // <REMOVE PARTICIPANT FROM THREAD>
    });
```

Zastąp **USER_ID_FOR_JACK** i **USER_ID_FOR_GEETA** identyfikatorami użytkowników uzyskanymi od tworzenia użytkowników i tokenów ([tokeny dostępu użytkowników](../../access-tokens.md))

Po odświeżeniu karty przeglądarki powinny zostać wyświetlone następujące elementy w konsoli programu:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

`getChatThreadClient`Metoda zwraca `chatThreadClient` dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj uczestników, Wyślij wiadomość itp. threadId jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Dodaj ten kod zamiast `<CREATE CHAT THREAD CLIENT>` komentarza w **client.js**, Odśwież kartę przeglądarki i sprawdź konsolę, powinny być widoczne następujące tematy:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `sendMessage` metody, aby wysłać komunikat do wątku identyfikowanego przez ThreadID.

`sendMessageRequest` służy do opisywania żądania komunikatu:

- Użyj, `content` Aby podać zawartość wiadomości czatu;

`sendMessageOptions` służy do opisywania opcjonalnej parametry operacji:

- Użyj, `senderDisplayName` Aby określić nazwę wyświetlaną nadawcy;
- Użyj `type` , aby określić typ komunikatu, taki jak "text" lub "HTML";

`SendChatMessageResult` czy odpowiedź zwrócona przez wysłanie komunikatu, zawiera identyfikator, który jest unikatowym IDENTYFIKATORem komunikatu.

```JavaScript
let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    senderDisplayName : 'Jack',
    type: 'text'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
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

- `Text`: Zwykły komunikat rozmowy Wysłany przez uczestnika wątku.

- `ThreadActivity/TopicUpdate`: Komunikat systemowy wskazujący, że Zaktualizowano temat.

- `ThreadActivity/AddParticipant`: Komunikat systemowy wskazujący, że co najmniej jeden uczestnik został dodany do wątku rozmowy.

- `ThreadActivity/RemoveParticipant`: Komunikat systemowy wskazujący, że uczestnik został usunięty z wątku rozmowy.

Aby uzyskać więcej informacji, zobacz [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku rozmowy można z niego dodawać i usuwać użytkowników. Przez dodanie użytkowników można udzielić im dostępu do wysyłania komunikatów do wątku rozmowy i dodawania/usuwania innych uczestników.

Przed wywołaniem `addParticipants` metody upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

`addParticipantsRequest` opisuje obiekt request w `participants` sposób umożliwiający wyświetlenie listy uczestników, którzy mają zostać dodani do wątku rozmowy.
- `id`wymagany, to identyfikator komunikacji, który ma zostać dodany do wątku rozmowy.
- `displayName`, opcjonalnie, jest nazwą wyświetlaną uczestnika wątku.
- `shareHistoryTime`, opcjonalnie, to czas, po którym historia rozmowy jest udostępniana uczestnikowi. Aby udostępnić historię od momentu rozpoczęcia wątku rozmowy, należy ustawić tę właściwość na dowolną datę równą lub mniejszą niż godzina utworzenia wątku. Aby po dodaniu uczestnika nie była udostępniona żadna historia, ustaw ją na bieżącą datę. Aby udostępnić historię częściową, ustaw ją na wybraną datę.

```JavaScript

let addParticipantsRequest =
{
    participants: [
        {
            id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Zastąp **NEW_PARTICIPANT_USER_ID** [nowym identyfikatorem użytkownika](../../access-tokens.md) Dodaj ten kod zamiast `<ADD NEW PARTICIPANT TO THREAD>` komentarza w **client.js**

## <a name="list-users-in-a-chat-thread"></a>Wyświetlanie listy użytkowników w wątku rozmowy
```JavaScript
async function listParticipants() {
   let pagedAsyncIterableIterator = await chatThreadClient.listParticipants();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listParticipants();
```
Dodaj ten kod zamiast `<LIST PARTICIPANTS IN A THREAD>` komentarza w **client.js**, Odśwież kartę przeglądarki i sprawdź konsolę, powinny być widoczne informacje o użytkownikach w wątku.

## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Podobnie jak w przypadku dodawania uczestnika, można usunąć uczestników z wątku rozmowy. Aby można było usunąć, należy śledzić identyfikatory dodanych uczestników.

Użyj `removeParticipant` metody, w której `participant` użytkownik komunikacyjny ma zostać usunięty z wątku.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Zastąp **PARTICIPANT_ID** identyfikatorem użytkownika użytym w poprzednim kroku (<NEW_PARTICIPANT_USER_ID>).
Dodaj ten kod zamiast `<REMOVE PARTICIPANT FROM THREAD>` komentarza w **client.js**,
