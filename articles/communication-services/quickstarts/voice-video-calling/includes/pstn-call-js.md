---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: df8d3bb5986fd31f4a9079b6e6e077126867dbfe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104729502"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Numer telefonu uzyskany w zasobie usług komunikacyjnych. [jak uzyskać numer telefonu](../../telephony-sms/get-phone-number.md).
- A, `User Access Token` Aby włączyć klienta wywołania. Aby uzyskać więcej informacji na temat [uzyskiwania `User Access Token` ](../../access-tokens.md)


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
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
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

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential('<USER ACCESS TOKEN with PSTN scope>');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callPhoneButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>Rozpocznij wywołanie telefonu

Określ numer telefonu uzyskany w zasobie usług komunikacyjnych, który zostanie użyty do uruchomienia wywołania:
> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 12223334444)

Dodaj procedurę obsługi zdarzeń w celu zainicjowania wywołania numeru telefonu podanego po `callPhoneButton` kliknięciu:


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.startCall(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Zakończ wywołanie telefonu

Dodaj odbiornik zdarzeń, aby zakończyć bieżące wywołanie po `hangUpPhoneButton` kliknięciu:

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

`forEveryone`Właściwość przerywa wywołanie dla wszystkich uczestników wywołania.

## <a name="run-the-code"></a>Uruchamianie kodu

Użyj, `webpack-dev-server` Aby skompilować i uruchomić aplikację. Uruchom następujące polecenie, aby powiązać hosta aplikacji na lokalnym serwerze WebServer:


```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otwórz przeglądarkę i przejdź do `http://localhost:8080/` . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="../media/javascript/pstn-calling-javascript-app.png" alt-text="Zrzut ekranu ukończonej aplikacji JavaScript.":::

Możesz nawiązać połączenie z rzeczywistym numerem telefonu, podając numer telefonu w dodanym polu tekstowym, a następnie klikając przycisk **Rozpocznij połączenie telefoniczne** .

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 12223334444)
