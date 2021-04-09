---
ms.openlocfilehash: 8a46e93efbc4435fdc48648f658d13982981812a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958360"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Dodawanie tożsamości zarządzanej do rozwiązania usług komunikacyjnych (.NET)

### <a name="install-the-sdk-packages"></a>Instalowanie pakietów SDK

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0
dotnet add package Azure.Communication.Sms  --version 1.0.0
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>Korzystanie z pakietów SDK

Dodaj następujące `using` dyrektywy do kodu, aby użyć tożsamości platformy Azure i zestawów SDK usługi Azure Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Poniższe przykłady używają [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`i `AZURE_TENANT_ID` zmienne środowiskowe są konieczne do utworzenia `DefaultAzureCredential` obiektu. Aby utworzyć zarejestrowaną aplikację w środowisku deweloperskim i skonfigurować zmienne środowiskowe, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Tworzenie tożsamości i wystawianie tokenu z tożsamością zarządzaną

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi przy użyciu tokenów Azure Active Directory.

Następnie użyj klienta, aby wystawić token dla nowego użytkownika:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Wyślij wiadomość SMS z zarządzaną tożsamością

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi programu SMS z tożsamością zarządzaną, a następnie wysłać wiadomość SMS przy użyciu klienta:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

