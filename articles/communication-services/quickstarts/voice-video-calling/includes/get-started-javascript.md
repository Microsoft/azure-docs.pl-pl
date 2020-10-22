---
title: Szybki Start — dodawanie telefonu VOIP do aplikacji internetowej przy użyciu usług Azure Communications Services
description: W ramach tego samouczka nauczysz się korzystać z biblioteki klienta wywołującego usługę Azure Communications Services dla języka JavaScript
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 652566efda4d4f274dc5700d35bcf45c1ebfb9e2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347251"
---
W tym przewodniku szybki start dowiesz się, jak zacząć wywoływanie przy użyciu biblioteki klienta wywołania usługi Azure Communication Services dla języka JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Zasób aktywnych usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Token dostępu użytkownika do tworzenia wystąpienia klienta wywołania. Dowiedz się [, jak tworzyć tokeny dostępu użytkowników i zarządzać nimi](../../access-tokens.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Uruchom `npm init -y` , aby utworzyć **package.jsw** pliku z ustawieniami domyślnymi.

```console
npm init -y
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Użyj `npm install` polecenia, aby zainstalować bibliotekę klienta wywołującego usługi Azure Communications Services dla języka JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Następujące wersje programu WebPack są zalecane w ramach tego przewodnika Szybki Start:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Ten przewodnik Szybki Start używa pakietu WebPack do łączenia zasobów aplikacji. Uruchom następujące polecenie, aby zainstalować pakiety WebPack, WebPack-CLI i WebPack — dev-Server npm i wyświetlić je jako zależności deweloperskie w **package.jsna**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Utwórz plik **index.html** w katalogu głównym projektu. Ten plik zostanie użyty do skonfigurowania podstawowego układu, który umożliwi użytkownikowi nałożenie połączenia z usługą Azure Communications bot.

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
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
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
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta wywołującego usługi Azure Communications Services:

| Nazwa                             | Opis                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient jest głównym punktem wejścia do biblioteki wywołującej klienta.                                                                       |
| CallAgent                        | CallAgent jest używany do uruchamiania wywołań i zarządzania nimi.                                                                                            |
| AzureCommunicationUserCredential | Klasa AzureCommunicationUserCredential implementuje interfejs CommunicationUserCredential, który jest używany do tworzenia wystąpienia CallAgent. |


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Należy zastąpić `<USER_ACCESS_TOKEN>` prawidłowym tokenem dostępu użytkownika dla zasobu. Jeśli nie masz jeszcze dostępnego tokenu, zapoznaj się z dokumentacją [tokenu dostępu użytkownika](../../access-tokens.md) . Przy użyciu `CallClient` , zainicjuj `CallAgent` wystąpienie z, `CommunicationUserCredential` które umożliwi nam wykonywanie i odbieranie wywołań. Dodaj następujący kod do **client.js**:

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>Rozpocznij wywołanie

Dodaj procedurę obsługi zdarzeń w celu zainicjowania wywołania po `callButton` kliknięciu:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
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
