---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 22cfe369561eab1ca334c7ff2450162dfae3e761
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92346874"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Utwórz nową aplikację Node.js

Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Uruchom `npm init -y` , aby utworzyć **package.jsw** pliku z ustawieniami domyślnymi.

```console
npm init -y
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Użyj `npm install` polecenia, aby zainstalować bibliotekę kliencką klienta usługi Azure Communications Services dla języka JavaScript.

```console

npm install @azure/communication-administration --save

```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

## <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz nowy plik tekstowy w edytorze kodu
1. Dodaj `require` wywołanie w celu załadowania `CommunicationIdentityClient`
1. Utwórz strukturę dla programu, w tym podstawową obsługę wyjątków

Użyj następującego kodu, aby rozpocząć:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Zapisz nowy plik jako **issue-token.js** w katalogu *User-Tokens-szybkiego startu* .

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie `CommunicationIdentityClient` z parametrami połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../create-communication-resource.md#store-your-connection-string).

Dodaj następujący kod do metody `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Tworzenie użytkownika

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `createUser` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Należy zachować mapowanie między użytkownikami aplikacji i wygenerowanymi tożsamościami usług komunikacyjnych (np. przechowując je w bazie danych serwera aplikacji).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Wystaw tokeny dostępu użytkowników

Użyj `issueToken` metody, aby wystawić token dostępu dla użytkownika usług komunikacyjnych. Jeśli nie podasz opcjonalnego `user` parametru, zostanie utworzony nowy użytkownik, który zostanie zwrócony z tokenem.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Tokeny dostępu użytkowników to krótkoterminowe poświadczenia, które należy ponownie wydać, aby uniemożliwić użytkownikom zakłócenia działania usługi. `expiresOn`Właściwość Response wskazuje okres istnienia tokenu.

## <a name="revoke-user-access-tokens"></a>Odwołaj tokeny dostępu użytkowników

W niektórych przypadkach może być konieczne jawne odwołanie tokenów dostępu użytkowników, na przykład gdy użytkownik zmieni hasło używane do uwierzytelniania w usłudze. Ta `revokeTokens` Metoda umożliwia unieważnienie wszystkich tokenów dostępu użytkownika.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="refresh-user-access-tokens"></a>Odśwież tokeny dostępu użytkowników

Aby odświeżyć token, użyj `CommunicationUser` obiektu do ponownego wystawienia:

```javascript  
let userResponse = new CommunicationUser(existingUserId);
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
```

## <a name="delete-a-user"></a>Usuwanie użytkownika

Usunięcie użytkownika odwołuje wszystkie aktywne tokeny i uniemożliwia wystawianie kolejnych tokenów dla tożsamości. Usuwa również całą zawartość utrwaloną skojarzoną z użytkownikiem.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Uruchamianie kodu

W wierszu konsoli przejdź do katalogu zawierającego plik *issue-token.js* , a następnie wykonaj następujące `node` polecenie, aby uruchomić aplikację.

```console
node ./issue-token.js
```
