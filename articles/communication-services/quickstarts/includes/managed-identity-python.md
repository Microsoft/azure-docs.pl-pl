---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657642"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Dodaj zarządzaną tożsamość do rozwiązania usług komunikacyjnych

### <a name="install-the-client-library-packages"></a>Instalowanie pakietów biblioteki klienta

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Korzystanie z pakietów biblioteki klienta

Dodaj następujący `import` kod do kodu, aby użyć tożsamości platformy Azure.

```python
from azure.identity import DefaultAzureCredential
```

Poniższe przykłady używają [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

Aby zarejestrować aplikację w środowisku deweloperskim i skonfigurować zmienne środowiskowe, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Tworzenie tożsamości i wystawianie tokenu

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tożsamością zarządzaną, a następnie za pomocą klienta wydać token dla nowego użytkownika:

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Wyślij wiadomość SMS z tożsamością zarządzaną platformy Azure

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi przy użyciu tożsamości zarządzanej platformy Azure, a następnie wysłać wiadomość SMS przy użyciu klienta:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o uwierzytelnianiu](../../concepts/authentication.md)

Możesz również chcieć:

- [Dowiedz się więcej o kontroli dostępu opartej na rolach na platformie Azure](../../../../articles/role-based-access-control/index.yml)
- [Dowiedz się więcej o bibliotece tożsamości platformy Azure dla języka Python](/net/api/overview/azure/identity-readme)
- [Tworzenie tokenów dostępu użytkowników](../../quickstarts/access-tokens.md)
- [Wysyłanie wiadomości SMS](../../quickstarts/telephony-sms/send.md)
- [Dowiedz się więcej o programie SMS](../../concepts/telephony-sms/concepts.md)