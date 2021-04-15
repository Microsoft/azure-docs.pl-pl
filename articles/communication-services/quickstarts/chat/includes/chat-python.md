---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386951"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, [zobacz Tworzenie bezpłatnego konta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalowanie języka [Python](https://www.python.org/downloads/)
- Utwórz zasób Azure Communication Services zasobów. Aby uzyskać szczegółowe informacje, zobacz Create an Azure Communication Resource (Tworzenie [zasobu komunikacji platformy Azure).](../../create-communication-resource.md) Na potrzeby tego przewodnika  Szybki start musisz zarejestrować punkt końcowy zasobu
- Token [dostępu użytkownika.](../../access-tokens.md) Pamiętaj, aby ustawić zakres na "czat" i zanotować ciąg tokenu, a także ciąg userId.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Otwórz terminal lub okno polecenia, aby utworzyć nowy katalog dla aplikacji, i przejdź do niego.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Użyj edytora tekstów, aby utworzyć plik o nazwie **start-chat.py** w katalogu głównym projektu i dodać strukturę programu, w tym podstawową obsługę wyjątków. W poniższych sekcjach dodasz cały kod źródłowy tego przewodnika Szybki start do tego pliku.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Instalowanie zestawu SDK

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu AZURE COMMUNICATION SERVICES Chat SDK dla języka Python.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ta klasa jest potrzebna w przypadku funkcji czatów. Tworzysz je przy użyciu informacji o subskrypcji i używasz ich do tworzenia, get i usuwania wątków. |
| ChatThreadClient | Ta klasa jest potrzebna dla funkcji wątku czatu. Uzyskujesz wystąpienie za pośrednictwem klasy ChatClient i używasz go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/uzyskiwania użytkowników, wysyłania powiadomień dotyczących wpisywania i odczytywania paragonów. |

## <a name="create-a-chat-client"></a>Tworzenie klienta czatu

Aby utworzyć klienta czatu, użyjesz punktu końcowego usługi Communications Service i wygenerowanego w ramach `Access Token` kroków wymagań wstępnych. Dowiedz się więcej o [tokenach dostępu użytkowników.](../../access-tokens.md)

Ten przewodnik Szybki start nie obejmuje tworzenia warstwy usługi do zarządzania tokenami dla aplikacji do czatów, chociaż jest to zalecane. Zapoznaj się z następującą dokumentacją, aby uzyskać więcej szczegółów na [temat architektury czatu](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Rozpoczynanie wątku czatu

Użyj metody `create_chat_thread` , aby utworzyć wątek czatu.

- Użyj `topic` , aby podać temat wątku; Temat można zaktualizować po utworzeniu wątku czatu przy użyciu `update_thread` funkcji .
- Użyj , aby wyświetlić listę, która ma zostać dodana do wątku czatu, typ przyjmuje wartość , czyli to, co masz po utworzeniu przez `thread_participants` `ChatParticipant` utworzenie `ChatParticipant` `CommunicationUserIdentifier` `user` [użytkownika](../../access-tokens.md#create-an-identity)

`CreateChatThreadResult` to wynik zwrócony podczas tworzenia wątku. Można go użyć do pobrania utworzonego wątku `id` czatu. Następnie `id` można go użyć do pobrania obiektu przy użyciu metody `ChatThreadClient` `get_chat_thread_client` . `ChatThreadClient` Może służyć do wykonywania innych operacji czatu w tym wątku czatu.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Uzyskiwanie klienta wątku czatu
Metoda `get_chat_thread_client` zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji na utworzonym wątku: dodawania uczestników, wysyłania wiadomości itp., thread_id to unikatowy identyfikator istniejącego wątku czatu.

`ChatThreadClient` Może służyć do wykonywania innych operacji czatu w tym wątku czatu.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Lista wszystkich wątków czatu
Metoda `list_chat_threads` zwraca iterator typu `ChatThreadItem` . Może służyć do listy wszystkich wątków czatu.

- Użyj `start_time` , aby określić najwcześniejszy punkt w czasie, aby uzyskać wątki czatu.
- Użyj `results_per_page` , aby określić maksymalną liczbę wątków czatu zwracanych na stronę.

Iteratorem jest `[ChatThreadItem]` odpowiedź zwrócona z listy wątków

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Wysyłanie wiadomości do wątku czatu

Użyj metody , aby wysłać wiadomość do właśnie utworzonego wątku czatu `send_message` identyfikowanego przez thread_id.

- Użyj `content` , aby udostępnić zawartość wiadomości czatu;
- Użyj `chat_message_type` , aby określić typ zawartości komunikatu. Możliwe wartości to "text" i "html". Jeśli nie zostanie określona, zostanie przypisana wartość domyślna "text".
- Użyj `sender_display_name` , aby określić nazwę wyświetlaną nadawcy;

`SendChatMessageResult` to odpowiedź zwrócona podczas wysyłania komunikatu, zawierająca identyfikator, który jest unikatowym identyfikatorem komunikatu.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie wiadomości czatu z wątku czatu

Wiadomości czatu można pobrać, sondując `list_messages` metodę w określonych odstępach czasu.

- Użyj `results_per_page` , aby określić maksymalną liczbę komunikatów do zwrócenia na stronę.
- Użyj `start_time` , aby określić najwcześniejszy punkt w czasie, do których mają być wysyłane komunikaty.

Iteratorem jest `[ChatMessage]` odpowiedź zwrócona z listy komunikatów

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` Polecenie zwraca najnowszą wersję komunikatu, w tym wszelkie zmiany lub usunięcia, które miały miejsce w komunikacie przy użyciu `update_message` i `delete_message` . W przypadku `ChatMessage.deleted_on` usuniętych komunikatów zwraca wartość daty/godziny wskazującą, kiedy komunikat został usunięty. W przypadku edytowanych komunikatów `ChatMessage.edited_on` zwraca datę/godzinę wskazującą, kiedy komunikat został edytowany. Dostęp do oryginalnego czasu tworzenia komunikatów można uzyskać przy użyciu funkcji , która może służyć do `ChatMessage.created_on` zamawiania komunikatów.

`list_messages`Zwraca różne typy komunikatów, które mogą być identyfikowane przez . `ChatMessage.type` 

Przeczytaj więcej na temat typów komunikatów tutaj: [Typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Wysyłanie potwierdzenia odczytu
Metoda może służyć do publikuje zdarzenie potwierdzenia odczytu `send_read_receipt` w wątku w imieniu użytkownika.

- Użyj `message_id` , aby określić identyfikator najnowszej wiadomości odczytane przez bieżącego użytkownika.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku czatu możesz dodawać i usuwać użytkowników. Dodając użytkowników, dajesz im dostęp do wysyłania wiadomości do wątku czatu oraz dodawania/usuwania innych uczestników. Przed wywołaniem metody upewnij się, że uzyskasz nowy token dostępu i tożsamość `add_participants` dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu do zainicjowania klienta czatu.

Co najmniej jednego użytkownika można dodać do wątku czatu przy użyciu metody , pod warunkiem, że nowy token dostępu i identyfikacja są `add_participants` dostępne dla wszystkich użytkowników.

Zwracany `list(tuple(ChatParticipant, CommunicationError))` jest A. Po pomyślnym dodaniu uczestnika oczekiwana jest pusta lista. W przypadku wystąpienia błędu podczas dodawania uczestnika lista jest wypełniana przy użyciu uczestników, których nie udało się napotkać, wraz z błędem, który wystąpił.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Lista uczestników wątków w wątku czatu

Podobnie jak w przypadku dodawania uczestnika, można również wyświetlić listę uczestników z wątku.

Użyj `list_participants` , aby pobrać uczestników wątku.
- Użyj `results_per_page` wartości , opcjonalnie: Maksymalna liczba uczestników do zwrócenia na stronę.
- Użyj `skip` parametru , opcjonalnie, aby pominąć uczestników do określonej pozycji w odpowiedzi.

Iteratorem jest `[ChatParticipant]` odpowiedź zwrócona z listy uczestników

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `python` polecenia .

```console
python start-chat.py
```
