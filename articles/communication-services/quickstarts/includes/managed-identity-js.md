---
ms.openlocfilehash: 3ec650f82b95cdfabc923e92325959c63f2846de
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110760"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Dodawanie tożsamości zarządzanej do rozwiązania usług komunikacyjnych (JS)

### <a name="install-the-sdk-packages"></a>Instalowanie pakietów SDK

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>Korzystanie z pakietów SDK

Dodaj następujące `import` dyrektywy do kodu, aby użyć tożsamości platformy Azure i zestawów SDK usługi Azure Storage.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Poniższe przykłady używają [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

Aby zarejestrować aplikację w środowisku deweloperskim i skonfigurować zmienne środowiskowe, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Tworzenie tożsamości i wystawianie tokenu z tożsamością zarządzaną

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tożsamością zarządzaną, a następnie za pomocą klienta wydać token dla nowego użytkownika:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Wyślij wiadomość SMS z zarządzaną tożsamością

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tożsamością zarządzaną, a następnie wysłać wiadomość SMS przy użyciu klienta:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

