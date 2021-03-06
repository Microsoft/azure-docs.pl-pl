---
title: Szybki start — dodawanie połączeń VOIP do aplikacji internetowej przy użyciu Azure Communication Services
description: Z tego samouczka dowiesz się, jak używać zestawu SDK wywoływania Azure Communication Services dla języka JavaScript
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: bd9a607b46c87ca590bcefef310f822d6f23483c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386926"
---
W tym przewodniku Szybki start dowiesz się, jak uruchomić wywołanie przy użyciu zestawu AZURE COMMUNICATION SERVICES SDK dla języka JavaScript.

> [!NOTE]
> W tym dokumencie jest używana wersja 1.0.0-beta.10 zestawu SDK wywołującego.


## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Wersje Active LTS i Maintenance LTS (zalecane wersje 8.11.1 i 10.14.1).
- Aktywny zasób Communication Services zasobów. [Utwórz zasób Communication Services zasobów.](../../create-communication-resource.md)
- Token dostępu użytkownika do wystąpienia klienta wywołania. Dowiedz się, jak [tworzyć tokeny dostępu użytkowników i zarządzać nimi.](../../access-tokens.md)


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

Utwórz plik w katalogu głównym projektu o nazwie **client.js,** który będzie zawierać logikę aplikacji dla tego przewodnika Szybki start. Dodaj następujący kod, aby zaimportować wywołującego klienta i pobrać odwołania do elementów DOM, aby można było dołączyć logikę biznesową. 

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

Następujące klasy i interfejsy obsługują niektóre główne funkcje zestawu AZURE COMMUNICATION SERVICES SDK:

| Nazwa                             | Opis                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient jest głównym punktem wejścia do wywołującego zestawu SDK.                                                                       |
| CallAgent                        | CallAgent służy do uruchamiania wywołań i zarządzania nimi.                                                                                            |
| AzureCommunicationTokenCredential | Klasa AzureCommunicationTokenCredential implementuje interfejs CommunicationTokenCredential, który służy do wystąpienia klasy CallAgent. |


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Musisz wprowadzić prawidłowy token dostępu użytkownika dla zasobu w polu tekstowym i kliknąć pozycję "Prześlij". Zapoznaj się z [dokumentacją tokenu dostępu](../../access-tokens.md) użytkownika, jeśli nie masz jeszcze dostępnego tokenu. Za pomocą obiektu zaimicjuj wystąpienie za pomocą obiektu , co `CallClient` umożliwi nam odbieranie i odbieranie `CallAgent` `CommunicationTokenCredential` wywołań. Dodaj następujący kod, aby **client.js**:

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

## <a name="start-a-call"></a>Uruchamianie wywołania

Dodaj procedurę obsługi zdarzeń w celu zainicjowania wywołania po `callButton` kliknięciu przycisku :

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

Dodaj odbiornik zdarzeń, aby zakończyć bieżące wywołanie po `hangUpButton` kliknięciu przycisku :

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

Właściwość `forEveryone` kończy wywołanie dla wszystkich uczestników połączeń.

## <a name="run-the-code"></a>Uruchamianie kodu

Użyj funkcji `webpack-dev-server` , aby skompilować i uruchomić aplikację. Uruchom następujące polecenie, aby do pakietu hosta aplikacji w programie na lokalnym serwerze internetowym:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otwórz przeglądarkę i przejdź do http://localhost:8080/ . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację JavaScript.":::

Możesz wykonać wychodzące wywołanie VOIP, podając prawidłowy token dostępu użytkownika i identyfikator użytkownika w odpowiednich polach tekstowych i klikając **przycisk Rozpocznij wywołanie.** Wywołanie umożliwia połączenie z echo botem. Jest to doskonałe miejsce do rozpoczęcia pracy i `8:echo123` sprawdzenia, czy urządzenia audio działają.

## <a name="sample-code"></a>Przykładowy kod

Przykładową aplikację możesz pobrać z usługi [Github.](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling)
