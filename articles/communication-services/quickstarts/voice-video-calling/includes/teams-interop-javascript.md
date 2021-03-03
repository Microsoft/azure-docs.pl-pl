---
title: Szybki Start — Dołącz do spotkania zespołów z poziomu aplikacji sieci Web
description: W tym samouczku dowiesz się, jak przyłączyć się do spotkania zespołów przy użyciu biblioteki klienta wywołującego usługi Azure Communications Services dla języka JavaScript
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d022d90aa4558ef5a4973668d4966a30f477a1da
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660101"
---
W tym przewodniku szybki start dowiesz się, jak przyłączyć się do spotkania zespołów przy użyciu biblioteki klienta wywołującego usługi Azure Communications Services dla języka JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne

- Działająca [usługa komunikacyjna wywołująca aplikację sieci Web](../getting-started-with-calling.md).
- [Wdrożenie zespołów](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Dodawanie formantów interfejsu użytkownika zespołów

Zastąp kod w index.html następującym fragmentem kodu.
Pole tekstowe zostanie użyte do wprowadzenia kontekstu spotkania zespołów, a przycisk zostanie użyty w celu dołączenia do określonego spotkania:

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

## <a name="enable-the-teams-ui-controls"></a>Włączanie formantów interfejsu użytkownika zespołów

Zastąp zawartość pliku client.js następującym fragmentem kodu.

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

## <a name="get-the-teams-meeting-link"></a>Pobierz link do spotkania dla zespołów

Link do spotkania zespołów można pobrać przy użyciu interfejsów API programu Graph. Jest to szczegółowo opisany w [dokumentacji programu Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
Zestaw SDK wywoływania usług komunikacyjnych akceptuje łącze do spotkania z pełnymi zespołami. Ten link jest zwracany jako część `onlineMeeting` zasobu, który jest dostępny w ramach [ `joinWebUrl` Właściwości](/graph/api/resources/onlinemeeting?view=graph-rest-beta) , można także uzyskać wymagane informacje o spotkaniu z adresu URL **spotkania dołączania** w przypadku, gdy spotkanie zespołu się zaprosiło.

## <a name="run-the-code"></a>Uruchamianie kodu

Użytkownicy pakietu WebPack mogą używać programu `webpack-dev-server` do kompilowania i uruchamiania aplikacji. Uruchom następujące polecenie, aby powiązać hosta aplikacji na lokalnym serwerze WebServer:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otwórz przeglądarkę i przejdź do http://localhost:8080/ . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Zrzut ekranu ukończonej aplikacji JavaScript.":::

Wstaw kontekst zespołów do pola tekstowego i naciśnij pozycję *Dołącz zespoły spotkanie* , aby dołączyć zespoły do spotkania z poziomu aplikacji usług komunikacyjnych.
