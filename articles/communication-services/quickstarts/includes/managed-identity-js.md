---
ms.openlocfilehash: 0fd97fe0eebb23130513409698b75d2ee7b98a6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657627"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Dodawanie tożsamości zarządzanej do rozwiązania usług komunikacyjnych (JS)

### <a name="install-the-client-library-packages"></a>Instalowanie pakietów biblioteki klienta

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>Korzystanie z pakietów biblioteki klienta

Dodaj następujące `import` dyrektywy do kodu, aby użyć tożsamości platformy Azure i bibliotek klienckich usługi Azure Storage.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient } from "@azure/communication-sms";
```

Poniższe przykłady używają [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

Aby zarejestrować aplikację w środowisku deweloperskim i skonfigurować zmienne środowiskowe, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Tworzenie tożsamości i wystawianie tokenu z tożsamością zarządzaną

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tożsamością zarządzaną, a następnie za pomocą klienta wydać token dla nowego użytkownika:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Wyślij wiadomość SMS z zarządzaną tożsamością

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tożsamością zarządzaną, a następnie wysłać wiadomość SMS przy użyciu klienta:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SendRequest = { 
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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o uwierzytelnianiu](../concepts/authentication.md)

Możesz również chcieć:

- [Dowiedz się więcej o kontroli dostępu opartej na rolach na platformie Azure](../../../../articles/role-based-access-control/index.yml)
- [Dowiedz się więcej o usłudze Azure Identity Library for JS](/javascript/api/overview/azure/identity-readme)
- [Tworzenie tokenów dostępu użytkowników](../../quickstarts/access-tokens.md)
- [Wysyłanie wiadomości SMS](../../quickstarts/telephony-sms/send.md)
- [Dowiedz się więcej o programie SMS](../../concepts/telephony-sms/concepts.md)

