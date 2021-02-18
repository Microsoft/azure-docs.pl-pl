---
title: 'Szybki Start: Dodawanie uwierzytelniania do aplikacji sieci Web w węźle za pomocą węzła MSAL | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak zaimplementować uwierzytelnianie za pomocą aplikacji internetowej Node.js i biblioteki uwierzytelniania firmy Microsoft (MSAL) dla Node.js.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 5ca8c41dc1e6a05975227555abd91f5d6725285a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092184"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Szybki Start: Logowanie użytkowników i uzyskiwanie tokenu dostępu w aplikacji sieci Web Node przy użyciu przepływu kodu uwierzytelniania

W tym przewodniku szybki start pobrano i uruchomimy przykład kodu, który pokazuje, jak aplikacja sieci Web Node.js może zalogować użytkowników przy użyciu przepływu kodu autoryzacji. Przykład kodu demonstruje również sposób uzyskiwania tokenu dostępu do wywołania interfejsu API Microsoft Graph. 

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

Ten przewodnik Szybki Start używa biblioteki uwierzytelniania firmy Microsoft dla Node.js (węzeł MSAL) z przepływem kodu autoryzacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz bezpłatnie subskrypcję platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji. Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Ustaw wartość **identyfikatora URI przekierowania** na `http://localhost:3000/redirect` .
> 1. Wybierz pozycję **Zarejestruj**. 
> 1. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. W obszarze **Zarządzaj** wybierz pozycję **Certyfikaty &** wpisy tajne  >  **nowy klucz tajny klienta**.  Pozostaw opis pusty i domyślny czas wygaśnięcia, a następnie wybierz pozycję **Dodaj**.
> 1. Zwróć uwagę na **wartość** **klucza tajnego klienta** do późniejszego użycia.

#### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

> [!div renderon="docs"]
> Aby uruchomić projekt z serwerem sieci Web przy użyciu Node.js, [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt z serwerem sieci Web za pomocą Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Krok 3. Konfigurowanie aplikacji węzła
>
> Wyodrębnij projekt i Otwórz folder *MS-Identity-Node-Main*, a następnie otwórz plik *index.js* .
> Ustaw wartość `clientID` przy użyciu **identyfikatora aplikacji (klienta)**.
> Ustaw `clientSecret` wartość przy użyciu **wartości** **klucza tajnego klienta**.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Zmodyfikuj wartości w `config` sekcji zgodnie z opisem w tym miejscu:
>
> - `Enter_the_Application_Id_Here` to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.
> - `Enter_the_Client_Secret_Here` jest **wartością** **wpisu tajnego klienta** dla zarejestrowanej aplikacji.
>
> Wartość domyślna `authority` reprezentuje główną (globalną) chmurę platformy Azure:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Aby znaleźć wartość **identyfikatora aplikacji (klienta)**, przejdź do strony **Przegląd** rejestracji aplikacji w Azure Portal. Przejdź do pozycji **certyfikaty & wpisy tajne** , aby pobrać lub wygenerować nowy **klucz tajny klienta**.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Krok 4. uruchamianie projektu

Uruchom projekt przy użyciu Node.js:

1. Aby uruchomić serwer, uruchom następujące polecenia w katalogu projektu:
    ```console
    npm install
    npm start
    ```
1. Przejdź na stronę `http://localhost:3000/`.

1. Wybierz pozycję **Zaloguj** się, aby uruchomić proces logowania.

    Przy pierwszym logowaniu zostanie wyświetlony monit o podanie zgody na umożliwienie aplikacji dostępu do Twojego profilu oraz zalogowanie się. Po pomyślnym zalogowaniu zostanie wyświetlony komunikat dziennika w wierszu polecenia.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład

Przykład, gdy uruchamiany, hostuje serwer sieci Web na hoście lokalnym, port 3000. Gdy przeglądarka sieci Web uzyskuje dostęp do tej witryny, przykład natychmiast przekierowuje użytkownika do strony uwierzytelniania firmy Microsoft. Z tego powodu przykład nie zawiera żadnych elementów *HTML* ani Display. Pomyślne uwierzytelnienie — wyświetla komunikat "OK".

### <a name="msal-node"></a>Węzeł MSAL

Biblioteka węzłów MSAL umożliwia użytkownikom i żąda tokenów, które są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Najnowszą wersję można pobrać przy użyciu Menedżera pakietów Node.js (npm):

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie uwierzytelniania do istniejącej aplikacji sieci Web — przykładowego kodu usługi GitHub >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code)
