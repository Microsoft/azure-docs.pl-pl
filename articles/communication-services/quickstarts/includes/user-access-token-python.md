---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: e307265cc95815f426317cee69d64b210bcd67a9
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506275"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 lub nowszy.
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
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę kliencką Administracja usług Azure Communication Services dla pakietu Python za pomocą `pip install` polecenia.

```console
pip install azure-communication-administration
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

## <a name="create-an-identity"></a>Tworzenie tożsamości

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `create_user` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Przechowywanie otrzymanej tożsamości z mapowaniem do użytkowników aplikacji. Można na przykład przechowywać je w bazie danych serwera aplikacji. Tożsamość jest wymagana później, aby można było wystawiać tokeny dostępu.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier + ":")
```

## <a name="issue-access-tokens"></a>Wystawianie tokenów dostępu

Użyj `issue_token` metody, aby wystawić token dostępu dla istniejącej tożsamości usług komunikacyjnych. Parametr `scopes` definiuje zestaw elementów pierwotnych, który będzie autoryzować ten token dostępu. Zapoznaj się z [listą obsługiwanych akcji](../../concepts/authentication.md). Nowe wystąpienie parametru `communicationUser` można utworzyć na podstawie ciągu reprezentującego tożsamość usługi komunikacyjnej platformy Azure.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Tokeny dostępu to krótkoterminowe poświadczenia, które należy ponownie wydać. Nie może to spowodować zakłócenia środowiska użytkownika aplikacji. `expires_on`Właściwość Response wskazuje okres istnienia tokenu dostępu.

## <a name="refresh-access-tokens"></a>Odświeżenie tokenów dostępu

Aby odświeżyć token dostępu, użyj `CommunicationUser` obiektu, aby go ponownie wydać:

```python  
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUser(existingIdentity)
token_result = client.issue_token( identity, ["voip"])
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

W wierszu konsoli przejdź do katalogu zawierającego plik *Issue-Access-token.py* , a następnie wykonaj następujące `python` polecenie, aby uruchomić aplikację.

```console
python ./issue-access-token.py
```
