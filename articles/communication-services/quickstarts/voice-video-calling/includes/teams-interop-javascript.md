---
title: Szybki Start — Dołącz do spotkania zespołów
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dd75e5e97e5dca898ba10e91528782861fb949ec
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114631"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Działająca [aplikacja wywołująca usługi komunikacyjne](../getting-started-with-calling.md).
- [Wdrożenie zespołów](https://docs.microsoft.com/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Włącz współdziałanie zespołów

Funkcja współdziałania zespołów jest obecnie w prywatnej wersji zapoznawczej. Aby włączyć tę funkcję dla zasobu usług komunikacyjnych, Wyślij wiadomość e-mail [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) na adres:

1. Identyfikator subskrypcji subskrypcji platformy Azure, która zawiera zasób usług komunikacyjnych.
2. Identyfikator dzierżawy zespołu. Najprostszym sposobem uzyskania takiego dostępu jest [uzyskanie i udostępnienie linku do zespołu](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

Aby korzystać z tej funkcji, użytkownik musi być członkiem organizacji będącej właścicielem.

## <a name="add-the-teams-ui-controls"></a>Dodawanie formantów interfejsu użytkownika zespołów

Dodaj nowe pole tekstowe i przycisk w kodzie HTML. Pole tekstowe zostanie użyte do wprowadzenia kontekstu spotkania zespołów, a przycisk zostanie użyty w celu dołączenia do określonego spotkania:

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
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Włączanie formantów interfejsu użytkownika zespołów

Teraz można powiązać przycisk **dołączania ze spotkaniem zespołu** z kodem, który jest dołączany do podanego zespołu:

```javascript
meetingButton.addEventListener("click", () => {
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Pobierz kontekst spotkania

Kontekst zespołów można pobrać przy użyciu interfejsów API programu Graph. Jest to szczegółowo opisany w [dokumentacji programu Graph](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http).

Wymagane informacje o spotkaniu można również uzyskać od adresu URL **spotkania sprzężenia** w samym zaproszeniu do spotkania.

## <a name="run-the-code"></a>Uruchamianie kodu

Użytkownicy pakietu WebPack mogą używać programu `webpack-dev-server` do kompilowania i uruchamiania aplikacji. Uruchom następujące polecenie, aby powiązać hosta aplikacji na lokalnym serwerze WebServer:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otwórz przeglądarkę i przejdź do http://localhost:8080/ . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Zrzut ekranu ukończonej aplikacji JavaScript.":::

Wstaw kontekst zespołów do pola tekstowego i naciśnij pozycję *Dołącz zespoły spotkanie* , aby dołączyć zespoły do spotkania z poziomu aplikacji usług komunikacyjnych.

