---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: af5af26a8970409b07eda6195b0853c3fa931b3f
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506257"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Utwórz nową aplikację Node.js

Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
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
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Zapisz nowy plik jako **issue-access-token.js** w katalogu *tokeny dostępu — szybki start* .

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie `CommunicationIdentityClient` z parametrami połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../create-communication-resource.md#store-your-connection-string).

Dodaj następujący kod do metody `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Tworzenie tożsamości

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `createUser` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Przechowywanie otrzymanej tożsamości z mapowaniem do użytkowników aplikacji. Można na przykład przechowywać je w bazie danych serwera aplikacji. Tożsamość jest wymagana później, aby można było wystawiać tokeny dostępu.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Wystawianie tokenów dostępu

Użyj `issueToken` metody, aby wystawić token dostępu dla istniejącej tożsamości usług komunikacyjnych. Parametr `scopes` definiuje zestaw elementów pierwotnych, który będzie autoryzować ten token dostępu. Zapoznaj się z [listą obsługiwanych akcji](../../concepts/authentication.md). Nowe wystąpienie parametru `communicationUser` można utworzyć na podstawie ciągu reprezentującego tożsamość usługi komunikacyjnej platformy Azure.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Tokeny dostępu to krótkoterminowe poświadczenia, które należy ponownie wydać. Nie może to spowodować zakłócenia środowiska użytkownika aplikacji. `expiresOn`Właściwość Response wskazuje okres istnienia tokenu dostępu.


## <a name="refresh-access-tokens"></a>Odświeżenie tokenów dostępu

Aby odświeżyć token dostępu, użyj `CommunicationUser` obiektu, aby go ponownie wydać:

```javascript  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identityResponse = new CommunicationUser(existingIdentity);
tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Odwołaj tokeny dostępu

W niektórych przypadkach można jawnie odwołać tokeny dostępu. Na przykład, gdy użytkownik aplikacji zmieni hasło używane do uwierzytelniania w usłudze. Metoda `revokeTokens` unieważnia wszystkie aktywne tokeny dostępu, które zostały wystawione dla tożsamości.

```javascript  
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with Id: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Usuwanie tożsamości

Usunięcie tożsamości odwołuje wszystkie aktywne tokeny dostępu i uniemożliwia wystawianie tokenów dostępu dla tożsamości. Usuwa również całą zawartość utrwaloną skojarzoną z tożsamością.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with Id: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Uruchamianie kodu

W wierszu konsoli przejdź do katalogu zawierającego plik *issue-access-token.js* , a następnie wykonaj następujące `node` polecenie, aby uruchomić aplikację.

```console
node ./issue-access-token.js
```
