---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450473"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Dodaj zarządzaną tożsamość do rozwiązania usług komunikacyjnych

### <a name="install-the-sdk-packages"></a>Instalowanie pakietów SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>Korzystanie z pakietów SDK

Dodaj następujący `import` kod do kodu, aby użyć tożsamości platformy Azure.

```python
from azure.identity import DefaultAzureCredential
```

Poniższe przykłady używają [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

Aby łatwo przejść do korzystania z uwierzytelniania tożsamości zarządzanej, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](../managed-identity-from-cli.md)

Aby uzyskać bardziej szczegółowe informacje na temat sposobu działania obiektu DefaultAzureCredential oraz sposobu używania go w sposób nieokreślony w tym przewodniku Szybki Start, zobacz [Biblioteka klienta tożsamości platformy Azure dla języka Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

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

### <a name="list-all-your-purchased-phone-numbers"></a>Wystaw wszystkie zakupione numery telefonów

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi przy użyciu tożsamości zarządzanej przez platformę Azure, a następnie użyć klienta, aby wyświetlić wszystkie zakupione numery telefonów:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```