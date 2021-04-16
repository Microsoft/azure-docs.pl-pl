---
title: Szybki start — dołączanie do spotkania w zespołów
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 49f9bac40ae803f980a22c19fd5d44d85fa99e9e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564445"
---
## <a name="joining-the-meeting-chat"></a>Dołączanie do czatu na spotkanie 

Po włączeniu współdziałania aplikacji Teams użytkownik Communication Services dołączyć do wywołania usługi Teams jako użytkownik zewnętrzny przy użyciu zestawu SDK wywoływania. Dołączenie do rozmowy spowoduje dodanie ich jako uczestnika do rozmowy na spotkanie, gdzie będą oni w stanie wysyłać i odbierać wiadomości z innymi użytkownikami podczas rozmowy. Użytkownik nie będzie miał dostępu do wiadomości czatu, które zostały wysłane przed dołączenia do połączenia. Aby dołączyć do spotkania i rozpocząć rozmowę, możesz wykonać następne kroki.

## <a name="install-the-chat-packages"></a>Instalowanie pakietów czatu

Użyj polecenia `npm install` , aby zainstalować niezbędne zestawy Communication Services SDK dla języka JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

Opcja `--save` wyświetla bibliotekę jako zależność w plikupackage.js **pliku.**

## <a name="add-the-teams-ui-controls"></a>Dodawanie kontrolek interfejsu użytkownika aplikacji Teams

Zastąp kod w index.html poniższym fragmentem kodu.
Pola tekstowe w górnej części strony będą używane do wprowadzania kontekstu spotkania aplikacji Teams i identyfikatora wątku spotkania. Przycisk "Dołącz do spotkania zespołów" będzie używany do dołączenia do określonego spotkania.
W dolnej części strony zostanie wyświetlone okno podręczne czatu. Może służyć do wysyłania komunikatów w wątku spotkania i będzie wyświetlana w czasie rzeczywistym wszystkie komunikaty wysyłane w wątku, gdy użytkownik ACS jest członkiem.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Włączanie kontrolek interfejsu użytkownika aplikacji Teams

Zastąp zawartość pliku client.js poniższym fragmentem kodu.

W tym fragmencie kodu zastąp 
- `SECRET CONNECTION STRING` z parametrów połączenia usługi Communication Service 
- `ENDPOINT URL` za pomocą adresu URL punktu końcowego usługi Communication Service

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Uzyskiwanie wątku rozmowy na spotkanie z zespołem dla Communication Services użytkownika

Link do spotkania i czat z zespołem można pobrać przy użyciu interfejsów API programu Graph, szczegółowo opisanego w [dokumentacji programu Graph.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) Wywołanie Communication Services SDK akceptuje pełny link spotkania usługi Teams. Ten link jest zwracany jako część zasobu i jest dostępny we właściwości Za pomocą interfejsów API graph można `onlineMeeting` również uzyskać [ `joinWebUrl` ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) element [](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) `threadId` . Odpowiedź będzie mieć obiekt `chatInfo` , który zawiera obiekt `threadID` . 

Wymagane informacje o spotkaniu i identyfikator wątku można również uzyskać z adresu **URL** dołączenia do spotkania w samym zaproszeniu na spotkanie w aplikacji Teams.
Link do spotkania z zespołem wygląda następująco: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . Będzie `threadId` gdzie `meeting_chat_thread_id` znajduje się w linku. Przed użyciem upewnij się, że nie `meeting_chat_thread_id` ma to zastosowania. Powinna ona mieć następujący format: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Uruchamianie kodu

Użytkownicy pakietów WebPack mogą używać programu `webpack-dev-server` do kompilowania i uruchamiania aplikacji. Uruchom następujące polecenie, aby po pakiecie hosta aplikacji na lokalnym serwerze internetowym:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otwórz przeglądarkę i przejdź do http://localhost:8080/ . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację JavaScript.":::

Wstaw link spotkania aplikacji Teams i identyfikator wątku do pól tekstowych. Naciśnij *przycisk Dołącz do spotkania zespołów,* aby dołączyć do spotkania teams. Gdy użytkownik ACS zostanie przyjęty na spotkanie, możesz porozmawiać z poziomu Communication Services aplikacji. Przejdź do pola w dolnej części strony, aby rozpocząć rozmowę.

> [!NOTE] 
> Obecnie tylko wysyłanie, odbieranie i edytowanie komunikatów jest obsługiwane w scenariuszach współdziałania z usługą Teams. Inne funkcje, takie jak wpisywanie wskaźników i Communication Services użytkowników dodających lub usuwających innych użytkowników ze spotkania w aplikacji Teams, nie są jeszcze obsługiwane.  
