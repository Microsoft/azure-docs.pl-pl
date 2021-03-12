---
ms.openlocfilehash: 8295849a7177eab774517816a239472677689434
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021015"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Dodawanie tożsamości zarządzanej do rozwiązania usług komunikacyjnych (.NET)

### <a name="install-the-client-library-packages"></a>Instalowanie pakietów biblioteki klienta

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Korzystanie z pakietów biblioteki klienta

Dodaj następujące `using` dyrektywy do kodu, aby użyć tożsamości platformy Azure i bibliotek klienckich usługi Azure Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Poniższe przykłady używają [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`i `AZURE_TENANT_ID` zmienne środowiskowe są konieczne do utworzenia `DefaultAzureCredential` obiektu. Aby utworzyć zarejestrowaną aplikację w środowisku deweloperskim i skonfigurować zmienne środowiskowe, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Tworzenie tożsamości i wystawianie tokenu z tożsamością zarządzaną

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi przy użyciu tokenów Azure Active Directory.

Następnie użyj klienta, aby wystawić token dla nowego użytkownika:

```csharp
     public async Task<Response<AccessToken>> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
          var identity = identityResponse.Value;

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Wyślij wiadomość SMS z zarządzaną tożsamością

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi programu SMS z tożsamością zarządzaną, a następnie wysłać wiadomość SMS przy użyciu klienta:

```csharp
     public async Task SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );
      }
```

