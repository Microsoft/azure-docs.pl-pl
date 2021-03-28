---
title: Szybki Start — dodawanie telefonu VOIP do aplikacji internetowej przy użyciu usług Azure Communications Services
description: W tym samouczku dowiesz się, jak używać zestawu SDK wywołań usługi Azure Communications Services dla języka JavaScript
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7d7b62d6587a568b74d142a2ee6a93587941559d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645395"
---
W tym przewodniku szybki start dowiesz się, jak rozpocząć wywoływanie przy użyciu zestawu SDK wywołania usługi Azure Communication Services dla języka JavaScript.

> [!NOTE]
> Ten dokument używa wersji 1.0.0-beta. 10 wywołującego zestawu SDK.


## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Zasób aktywnych usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Token dostępu użytkownika do tworzenia wystąpienia klienta wywołania. Dowiedz się [, jak tworzyć tokeny dostępu użytkowników i zarządzać nimi](../../access-tokens.md).


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Oto kod:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Utwórz plik w katalogu głównym projektu o nazwie **client.js** , aby zawierał logikę aplikacji dla tego przewodnika Szybki Start. Dodaj następujący kod, aby zaimportować klienta wywołującego i uzyskać odwołania do elementów modelu DOM, dzięki czemu możemy dołączać logikę biznesową. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK wywołującego usługi Azure Communications Services:

| Nazwa                             | Opis                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient jest głównym punktem wejścia do wywołującego zestawu SDK.                                                                       |
| CallAgent                        | CallAgent jest używany do uruchamiania wywołań i zarządzania nimi.                                                                                            |
| AzureCommunicationTokenCredential | Klasa AzureCommunicationTokenCredential implementuje interfejs CommunicationTokenCredential, który jest używany do tworzenia wystąpienia CallAgent. |


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Musisz wprowadzić prawidłowy token dostępu użytkownika dla zasobu w polu tekstowym, a następnie kliknąć przycisk Prześlij. Jeśli nie masz jeszcze dostępnego tokenu, zapoznaj się z dokumentacją [tokenu dostępu użytkownika](../../access-tokens.md) . Przy użyciu `CallClient` , zainicjuj `CallAgent` wystąpienie z, `CommunicationTokenCredential` które umożliwi nam wykonywanie i odbieranie wywołań. Dodaj następujący kod do **client.js**:

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Rozpocznij wywołanie

Dodaj procedurę obsługi zdarzeń w celu zainicjowania wywołania po `callButton` kliknięciu:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Zakończenie wywołania

Dodaj odbiornik zdarzeń, aby zakończyć bieżące wywołanie po `hangUpButton` kliknięciu:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

`forEveryone`Właściwość przerywa wywołanie dla wszystkich uczestników wywołania.

## <a name="run-the-code"></a>Uruchamianie kodu

Użyj, `webpack-dev-server` Aby skompilować i uruchomić aplikację. Uruchom następujące polecenie, aby powiązać hosta aplikacji w lokalnym serwerze WebServer:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otwórz przeglądarkę i przejdź do http://localhost:8080/ . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Zrzut ekranu ukończonej aplikacji JavaScript.":::

Wychodzące wywołanie VOIP można utworzyć, podając identyfikator użytkownika w polu tekstowym i klikając przycisk **Rozpocznij połączenie** . Wywołanie `8:echo123` nawiązuje połączenie z botem ECHA. jest to doskonałe rozwiązanie do rozpoczęcia i sprawdzenia, czy urządzenia audio działają.
