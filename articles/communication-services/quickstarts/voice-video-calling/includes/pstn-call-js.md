---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 009bd57fdb82b8463352da8dc63c9aeebceab09b
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779787"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Numer telefonu uzyskany w zasobie usług komunikacyjnych. [jak uzyskać numer telefonu](../../telephony-sms/get-phone-number.md).
- A, `User Access Token` Aby włączyć klienta wywołania. Aby uzyskać więcej informacji na temat [uzyskiwania `User Access Token` ](../../access-tokens.md)
- Ukończ Przewodnik Szybki Start dotyczący [dodawania wywołania do aplikacji](../getting-started-with-calling.md)

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

- Aby wyświetlić numery telefonów skojarzone z zasobem usług komunikacyjnych, zaloguj się do [Azure Portal](https://portal.azure.com/), Znajdź zasób usługi komunikacyjnej i Otwórz kartę **numery telefonów** w okienku nawigacji po lewej stronie.
- Możesz skompilować i uruchomić swoją aplikację za pomocą usługi Azure Communications Services wywołującego bibliotekę kliencką dla języka JavaScript:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

## <a name="setting-up"></a>Konfigurowanie

### <a name="add-pstn-functionality-to-your-app"></a>Dodawanie funkcji PSTN do aplikacji

Rozszeranie układu przy użyciu kontrolek wybierania numeru telefonu.

Umieść ten kod na końcu `<body />` sekcji **index.html**przed `<script />` tagami:

```html
<input 
  id="callee-phone-input"
  type="text"
  placeholder="Phone number you would like to dial"
  style="margin-bottom:1em; width: 230px;"
/>
<div>
  <button id="call-phone-button" type="button">
    Start Phone Call
  </button>
  &nbsp;
  <button id="hang-up-phone-button" type="button" disabled="true">
    Hang Up Phone Call
  </button>
</div>
```

Rozwiń logikę aplikacji, korzystając z funkcji telefonii.

Dodaj następujący kod do **client.js**:

```javascript
const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");
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
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: '+18336528005'}
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
npx webpack-dev-server --entry ./client.js --output bundle.js
```

Otwórz przeglądarkę i przejdź do `http://localhost:8080/` . Powinien zostać wyświetlony następujący ekran:


![Zrzut ekranu ukończonej aplikacji JavaScript.](../media/javascript/pstn-calling-javascript-app.png)

Możesz nawiązać połączenie z rzeczywistym numerem telefonu, podając numer telefonu w dodanym polu tekstowym, a następnie klikając przycisk **Rozpocznij połączenie telefoniczne** .

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 12223334444)
