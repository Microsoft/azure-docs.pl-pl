---
ms.openlocfilehash: c5d83cc709c334e15a1c13e64ba17ef24835fed0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657659"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Dodawanie tożsamości zarządzanej do rozwiązania usług komunikacyjnych (Java)

### <a name="install-the-client-library-packages"></a>Instalowanie pakietów biblioteki klienta
W pliku pom.xml Dodaj następujące elementy zależności do grupy zależności.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-client-library-packages"></a>Korzystanie z pakietów biblioteki klienta

Dodaj następujące `import` dyrektywy do kodu, aby użyć usług Azure Identity i Azure Communications Client Library.

```java
import com.azure.identity.*;
import com.azure.communication.sms.*;
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
```

Poniższe przykłady używają [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`i `AZURE_TENANT_ID` zmienne środowiskowe są konieczne do utworzenia `DefaultAzureCredential` obiektu. Aby utworzyć zarejestrowaną aplikację w środowisku deweloperskim i skonfigurować zmienne środowiskowe, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Tworzenie tożsamości i wystawianie tokenu z tożsamością zarządzaną

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tożsamością zarządzaną.
Następnie użyj klienta, aby wystawić token dla nowego użytkownika:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Wyślij wiadomość SMS z zarządzaną tożsamością

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tożsamością zarządzaną, a następnie wysłać wiadomość SMS przy użyciu klienta:

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          // Send the message and check the response for a message id
          SendSmsResponse response = smsClient.sendMessage(
               new PhoneNumberIdentifier("<leased-phone-number>"),
               to,
               "your message",
               options /* Optional */
          );
          return response;
    }
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o uwierzytelnianiu](../concepts/authentication.md)

Możesz również chcieć:

- [Dowiedz się więcej o kontroli dostępu opartej na rolach na platformie Azure](../../../../articles/role-based-access-control/index.yml)
- [Dowiedz się więcej o bibliotece tożsamości platformy Azure dla języka Java](/java/api/overview/azure/identity-readme)
- [Tworzenie tokenów dostępu użytkowników](../../quickstarts/access-tokens.md)
- [Wysyłanie wiadomości SMS](../../quickstarts/telephony-sms/send.md)
- [Dowiedz się więcej o programie SMS](../../concepts/telephony-sms/concepts.md)
