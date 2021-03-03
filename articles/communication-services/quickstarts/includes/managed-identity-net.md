---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657644"
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
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Wyślij wiadomość SMS z zarządzaną tożsamością

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi programu SMS z tożsamością zarządzaną, a następnie wysłać wiadomość SMS przy użyciu klienta:

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o uwierzytelnianiu](../concepts/authentication.md)

Możesz również chcieć:

- [Dowiedz się więcej o kontroli dostępu opartej na rolach na platformie Azure](../../../../articles/role-based-access-control/index.yml)
- [Dowiedz się więcej o bibliotece tożsamości platformy Azure dla platformy .NET](/dotnet/api/overview/azure/identity-readme)
- [Tworzenie tokenów dostępu użytkowników](../../quickstarts/access-tokens.md)
- [Wysyłanie wiadomości SMS](../telephony-sms/send.md)
- [Dowiedz się więcej o programie SMS](../../concepts/telephony-sms/concepts.md)
