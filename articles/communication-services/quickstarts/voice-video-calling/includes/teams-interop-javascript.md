---
title: Szybki start — dołączanie do spotkania usługi Teams z aplikacji internetowej
description: Z tego samouczka dowiesz się, jak dołączyć do spotkania usługi Teams przy użyciu zestawu SDK wywoływania Azure Communication Services dla języka JavaScript
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564530"
---
W tym przewodniku Szybki start dowiesz się, jak dołączyć do spotkania usługi Teams przy użyciu zestawu SDK wywoływania Azure Communication Services dla języka JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne

- Działa aplikacja [Communication Services wywoływania aplikacji internetowej](../getting-started-with-calling.md).
- Wdrożenie [usługi Teams.](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls"></a>Dodawanie kontrolek interfejsu użytkownika aplikacji Teams

Zastąp kod w index.html poniższym fragmentem kodu.
Pole tekstowe zostanie użyte do wprowadzenia kontekstu spotkania w u użytkownika Teams, a przycisk zostanie użyty do dołączenia do określonego spotkania:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Włączanie kontrolek interfejsu użytkownika aplikacji Teams

Zastąp zawartość pliku client.js poniższym fragmentem kodu.

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Uzyskiwanie linku do spotkania z zespołem

Link do spotkania usługi Teams można pobrać przy użyciu interfejsów API programu Graph. Jest to szczegółowo opisane w [dokumentacji programu Graph.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
Wywołanie Communication Services SDK akceptuje pełny link spotkania usługi Teams. Ten link jest zwracany jako część zasobu i dostępny w ramach właściwości Wymagane informacje o spotkaniu można również uzyskać z adresu URL dołączenia do spotkania w ramach zaproszenia `onlineMeeting` na spotkanie w aplikacji Teams. [ `joinWebUrl` ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) 

## <a name="run-the-code"></a>Uruchamianie kodu

Użytkownicy pakietów WebPack mogą używać programu `webpack-dev-server` do kompilowania i uruchamiania aplikacji. Uruchom następujące polecenie, aby po pakiecie hosta aplikacji na lokalnym serwerze internetowym:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otwórz przeglądarkę i przejdź do http://localhost:8080/ . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację JavaScript.":::

Wstaw kontekst aplikacji Teams w polu tekstowym i naciśnij pozycję *Dołącz* do spotkania zespołów, aby dołączyć do spotkania aplikacji Teams z poziomu Communication Services aplikacji.
