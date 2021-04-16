---
ms.openlocfilehash: ce29158c2a44f01fa06649e4b4497bf5c5076866
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510840"
---
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Otwórz terminal lub okno polecenia, aby utworzyć nowy katalog dla aplikacji, i przejdź do niego.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

Uruchom `npm init -y` , aby utworzyćpackage.js **pliku** z ustawieniami domyślnymi.

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>Instalowanie pakietów zestawu SDK

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>Tworzenie nowego pliku

Otwórz nowy plik za pomocą edytora tekstów i zapisz go jako plik . Kod zostanie umieszczany `index.js` wewnątrz tego pliku.

### <a name="use-the-sdk-packages"></a>Korzystanie z pakietów zestawu SDK

Dodaj następujące dyrektywy `require` w górnej części strony, `index.js` aby użyć zestawów SDK tożsamości platformy Azure i usługi Azure Storage.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>Tworzenie wartości domyślnejAzureCredential

W tym przewodniku Szybki start będziemy używać wartości [DefaultAzureCredential.](/javascript/api/@azure/identity/defaultazurecredential) To poświadczenie jest odpowiednie dla środowisk produkcyjnych i deweloperskich. Ponieważ jest to potrzebne dla każdej operacji, utwórzmy ją w górnej części `index.js` pliku. 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Tworzenie tożsamości i wydawanie tokenu przy użyciu tożsamości zarządzanych

Następnie napiszemy funkcję, która tworzy nową tożsamość i generuje token dla tej tożsamości. Użyjemy jej później do przetestowania konfiguracji tożsamości zarządzanej.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-managed-identity"></a>Wysyłanie wiadomości SMS z tożsamością zarządzaną

Teraz napiszmy funkcję, która używa tożsamości zarządzanych do wysyłania wiadomości SMS:

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

## <a name="write-the-main-function"></a>Pisanie funkcji main

Po utworzeniu funkcji możemy napisać funkcję main, aby je wywołać i zademonstrować użycie tożsamości zarządzanych:
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

Ostateczny plik `index.js` powinien wyglądać tak:
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

## <a name="run-the-program"></a>Uruchamianie programu

Po zakończeniu wszystkiego możesz uruchomić plik, wprowadzając go `node index.js` z katalogu projektu. Jeśli wszystko poszło dobrze, powinien zostać wyświetlony kod podobny do poniższego.

```Bash
    $ node index.js
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
