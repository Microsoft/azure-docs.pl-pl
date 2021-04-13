---
ms.openlocfilehash: d70514e81bc838b4193862e66b0c03440d006128
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307464"
---
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Utwórz nową aplikację Node.js

Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

Uruchom `npm init -y` , aby utworzyć **package.jsw** pliku z ustawieniami domyślnymi.

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>Instalowanie pakietów SDK

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>Tworzenie nowego pliku

Otwórz nowy plik z edytorem tekstu i Zapisz go jako `index.js` , umieszczamy kod w tym pliku.

### <a name="use-the-sdk-packages"></a>Korzystanie z pakietów SDK

Dodaj następujące `require` dyrektywy na początku, `index.js` Aby użyć tożsamości platformy Azure i zestawów SDK usługi Azure Storage.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>Utwórz DefaultAzureCredential

W tym przewodniku szybki start będziemy używać [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential) . To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych. Ponieważ jest to konieczne dla każdej operacji, utwórz ją w górnej części naszego `index.js` pliku. 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Tworzenie tożsamości i wystawianie tokenów z tożsamościami zarządzanymi

Następnie napiszemy funkcję, która tworzy nową tożsamość i wystawia token dla tej tożsamości. użyjemy tego później do testowania konfiguracji zarządzanej tożsamości.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-managed-identity"></a>Wyślij wiadomość SMS z zarządzaną tożsamością

Teraz program pozwala pisać funkcję, która używa tożsamości zarządzanych do wysyłania wiadomości SMS:

```JavaScript
async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}
```

## <a name="write-the-main-method"></a>Napisz metodę Main

Dzięki naszym funkcjom utworzone możemy teraz napisać główną funkcję w celu wywołania ich i zademonstrowania użycia zarządzanych tożsamości:
```JavaScript
async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

Ostatni `index.js` plik powinien wyglądać następująco:
```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");

const credential = new DefaultAzureCredential();

async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}

async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}

async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

## <a name="run-the-program"></a>Uruchom program

Po zakończeniu wszystkiego można uruchomić plik, wprowadzając go `node index.js` w katalogu projektu. Jeśli wszystko poszło dobrze, należy zobaczyć coś podobnego do poniższego.

```Bash
    $ node index.js
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
