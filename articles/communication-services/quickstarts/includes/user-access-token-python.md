---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 913f2bc5569dc9bdd058c38902ad0c99acee5d48
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803315"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7 lub 3.6 +.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Użyj edytora tekstów, aby utworzyć plik o nazwie **Issue-Access-Tokens.py** w katalogu głównym projektu i dodać strukturę programu, w tym podstawową obsługę wyjątków. Do tego pliku zostanie dodany kod źródłowy dla tego przewodnika Szybki Start w poniższych sekcjach.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę klienta tożsamości usługi Azure Communication Services dla pakietu Python za pomocą `pip install` polecenia.

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie `CommunicationIdentityClient` z parametrami połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../create-communication-resource.md#store-your-connection-string).

Dodaj ten kod wewnątrz `try` bloku:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Alternatywnie, jeśli masz skonfigurowaną tożsamość zarządzaną, zobacz [Korzystanie z tożsamości zarządzanych](../managed-identity.md), ale także uwierzytelnianie przy użyciu tożsamości zarządzanej.
```python
const endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"];
var client = new CommunicationIdentityClient(endpoint, DefaultAzureCredential());
```

## <a name="create-an-identity"></a>Tworzenie tożsamości

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `create_user` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Przechowywanie otrzymanej tożsamości z mapowaniem do użytkowników aplikacji. Można na przykład przechowywać je w bazie danych serwera aplikacji. Tożsamość jest wymagana później, aby można było wystawiać tokeny dostępu.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>Wystawianie tokenów dostępu

Użyj `get_token` metody, aby wystawić token dostępu dla istniejącej tożsamości usług komunikacyjnych. Parametr `scopes` definiuje zestaw elementów pierwotnych, który będzie autoryzować ten token dostępu. Zapoznaj się z [listą obsługiwanych akcji](../../concepts/authentication.md). Nowe wystąpienie parametru `CommunicationUserIdentifier` można utworzyć na podstawie ciągu reprezentującego tożsamość usługi komunikacyjnej platformy Azure.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Tokeny dostępu to krótkoterminowe poświadczenia, które należy ponownie wydać. Nie może to spowodować zakłócenia środowiska użytkownika aplikacji. `expires_on`Właściwość Response wskazuje okres istnienia tokenu dostępu.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Tworzenie tożsamości i wystawianie tokenu dostępu w ramach tego samego żądania

Użyj `create_user_and_token` metody, aby utworzyć tożsamość usługi komunikacyjnej i wydać dla niej token dostępu. Parametr `scopes` definiuje zestaw elementów pierwotnych, który będzie autoryzować ten token dostępu. Zapoznaj się z [listą obsługiwanych akcji](../../concepts/authentication.md).

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Odświeżenie tokenów dostępu

Aby odświeżyć token dostępu, użyj `CommunicationUserIdentifier` obiektu, aby go ponownie wydać:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Odwołaj tokeny dostępu

W niektórych przypadkach można jawnie odwołać tokeny dostępu. Na przykład, gdy użytkownik aplikacji zmieni hasło używane do uwierzytelniania w usłudze. Metoda `revoke_tokens` unieważnia wszystkie aktywne tokeny dostępu, które zostały wystawione dla tożsamości.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Usuwanie tożsamości

Usunięcie tożsamości odwołuje wszystkie aktywne tokeny dostępu i uniemożliwia wystawianie tokenów dostępu dla tożsamości. Usuwa również całą zawartość utrwaloną skojarzoną z tożsamością.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Uruchamianie kodu

W wierszu konsoli przejdź do katalogu zawierającego plik *Issue-Access-Tokens.py* , a następnie wykonaj następujące `python` polecenie, aby uruchomić aplikację.

```console
python ./issue-access-tokens.py
```
