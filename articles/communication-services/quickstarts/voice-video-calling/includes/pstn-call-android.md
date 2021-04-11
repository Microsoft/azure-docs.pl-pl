---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 96f5fc868d4fa090848096174eb6fcdd34ef8388
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104729485"
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

## <a name="setting-up"></a>Konfigurowanie

### <a name="add-pstn-functionality-to-your-app"></a>Dodawanie funkcji PSTN do aplikacji

Dodaj `PhoneNumber` Typ do swojej aplikacji, modyfikując **Main. Java**:


```java
import com.azure.android.communication.common.PhoneNumberIdentifier;
```

<!--
> [!TBD]
> Namespace based on input from Komivi Agbakpem. But it does not correlates with other use namespaces in Calling Quickstart. E.g: "com.azure.communication.calling.CommunicationUserIdentifier" or "com.azure.communication.common.client.CommunicationTokenCredential". Double-chek this.
-->

## <a name="start-a-call-to-phone"></a>Rozpocznij wywołanie telefonu

Określ numer telefonu uzyskany w ramach zasobu usług komunikacyjnych. Ta wartość zostanie użyta do rozpoczęcia wywołania:

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 12223334444)

Zmodyfikuj `startCall()` procedurę obsługi zdarzeń w środowisku **MAINS. Java**, aby obsługiwać połączenia telefoniczne:

```java
    private void startCall() {
        EditText calleePhoneView = findViewById(R.id.callee_id);
        String calleePhone = calleePhoneView.getText().toString();
        PhoneNumberIdentifier callerPhone = new PhoneNumberIdentifier("+12223334444");
        StartCallOptions options = new StartCallOptions();
        options.setAlternateCallerId(callerPhone);
        options.setVideoOptions(new VideoOptions(null));
        call = agent.startCall(
                getApplicationContext(),
                new PhoneNumberIdentifier[] {new PhoneNumberIdentifier(calleePhone)},
                options);
    }
```

## <a name="launch-the-app-and-call-the-echo-bot"></a>Uruchom aplikację i Wywołaj echo bot

Aplikację można teraz uruchomić przy użyciu przycisku "Uruchom aplikację" na pasku narzędzi (Shift + F10). Możesz nawiązać połączenie telefoniczne, podając numer telefonu w dodanym polu tekstowym, a następnie klikając przycisk **Wywołaj** .
> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 12223334444)

![Zrzut ekranu przedstawiający ukończoną aplikację.](../media/android/quickstart-android-call-pstn.png)
