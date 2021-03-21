---
ms.openlocfilehash: 50707b46445803ee27118ee72b90a237a3e76200
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103021098"
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
from azure.communication.identity import CommunicationIdentityClient 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Wyślij wiadomość SMS z tożsamością zarządzaną platformy Azure

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi przy użyciu tożsamości zarządzanej platformy Azure, a następnie wysłać wiadomość SMS przy użyciu klienta:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```
