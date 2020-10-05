---
title: plik dołączania
description: plik dołączania
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947613"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 lub nowszy.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Użyj edytora tekstów, aby utworzyć plik o nazwie **Issue-tokens.py** w katalogu głównym projektu i dodać strukturę programu, w tym podstawową obsługę wyjątków. Do tego pliku zostanie dodany kod źródłowy dla tego przewodnika Szybki Start w poniższych sekcjach.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
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

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

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

## <a name="create-a-user"></a>Tworzenie użytkownika

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `create_user` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Należy zachować mapowanie między użytkownikami aplikacji i wygenerowanymi tożsamościami usług komunikacyjnych (np. przechowując je w bazie danych serwera aplikacji).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Wystaw tokeny dostępu użytkowników

Użyj `issue_token` metody, aby wystawić token dostępu dla użytkownika usług komunikacyjnych. Jeśli nie podasz opcjonalnego `user` parametru, zostanie utworzony nowy użytkownik, który zostanie zwrócony z tokenem.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Tokeny dostępu użytkowników to krótkoterminowe poświadczenia, które należy ponownie wydać, aby uniemożliwić użytkownikom zakłócenia działania usługi. `expires_on`Właściwość Response wskazuje okres istnienia tokenu.

## <a name="revoke-user-access-tokens"></a>Odwołaj tokeny dostępu użytkowników

W niektórych przypadkach może być konieczne jawne odwołanie tokenów dostępu użytkowników, na przykład gdy użytkownik zmieni hasło używane do uwierzytelniania w usłudze. Ta funkcja jest dostępna za pośrednictwem biblioteki klienta Administracja usług Azure Communication Services.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Usuwanie użytkownika

Usunięcie tożsamości odwołuje wszystkie aktywne tokeny i uniemożliwia wystawianie kolejnych tokenów dla tożsamości. Usuwa również całą zawartość utrwaloną skojarzoną z użytkownikiem.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Uruchamianie kodu

W wierszu konsoli przejdź do katalogu zawierającego plik *Issue-token.py* , a następnie wykonaj następujące `python` polecenie, aby uruchomić aplikację.

```console
python ./issue-token.py
```
