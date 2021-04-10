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
ms.openlocfilehash: 9bb4b5c27a114463b807ce9bba7f4d1887a40fb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726677"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zainstaluj język [Python](https://www.python.org/downloads/)
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../create-communication-resource.md). Musisz zarejestrować **punkt końcowy** zasobu dla tego przewodnika Szybki Start
- [Token dostępu użytkownika](../../access-tokens.md). Upewnij się, że ustawiono zakres "Rozmowa" i zanotuj ciąg tokenu, a także ciąg identyfikatora użytkownika.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Użyj edytora tekstów, aby utworzyć plik o nazwie **Start-Chat.py** w katalogu głównym projektu i dodać strukturę programu, w tym podstawową obsługę wyjątków. Do tego pliku zostanie dodany kod źródłowy dla tego przewodnika Szybki Start w poniższych sekcjach.

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

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu Azure Communications Services Chat SDK dla języka Python.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClient i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i otrzymywania potwierdzeń. |

## <a name="create-a-chat-client"></a>Tworzenie klienta czatu

Aby utworzyć klienta programu chat, należy użyć punktu końcowego usługi komunikacyjnej i `Access Token` wygenerowanego w ramach wstępnie wymaganych kroków. Dowiedz się więcej o [tokenach dostępu użytkowników](../../access-tokens.md).

Ten przewodnik Szybki Start nie obejmuje tworzenia warstwy usług w celu zarządzania tokenami dla aplikacji czatu, chociaż jest to zalecane. Więcej szczegółowych informacji o [architekturze rozmowy](../../../concepts/chat/concepts.md) można znaleźć w następującej dokumentacji

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `create_chat_thread` metody, aby utworzyć wątek rozmowy.

- Użyj, `topic` Aby nadać temat wątku; Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `update_thread` funkcji.
- Użyj `thread_participants` , aby wyświetlić listę, która ma `ChatThreadParticipant` zostać dodana do wątku rozmowy `ChatThreadParticipant` , `CommunicationUserIdentifier` Typ pobiera jako `user` , który jest po utworzeniu przez [utworzenie użytkownika](../../access-tokens.md#create-an-identity)

`CreateChatThreadResult` Czy wynikiem jest utworzenie wątku, można go użyć do pobrania `id` utworzonego wątku rozmowy. `id`Można go następnie użyć do pobrania `ChatThreadClient` obiektu za pomocą `get_chat_thread_client` metody. `ChatThreadClient` może służyć do wykonywania innych operacji czatu do tego wątku rozmowy.

```python
from azure.communication.chat import ChatThreadParticipant

topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy
`get_chat_thread_client`Metoda zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj uczestników, Wyślij wiadomość itp. thread_id jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

`ChatThreadClient` może służyć do wykonywania innych operacji czatu do tego wątku rozmowy.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Wyświetl wszystkie wątki rozmowy
`list_chat_threads`Metoda zwraca iterator typu `ChatThreadItem` . Może służyć do wyświetlania listy wszystkich wątków rozmowy.

- Użyj `start_time` , aby określić najwcześniejszy punkt w czasie, aby uzyskać wątki rozmowy do.
- Użyj `results_per_page` , aby określić maksymalną liczbę wątków czatu zwracanych na stronę.

Iterator `[ChatThreadItem]` jest odpowiedzią zwracaną z listy wątków

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `send_message` metody, aby wysłać wiadomość do wątku rozmowy, który właśnie został utworzony, identyfikowanego przez thread_id.

- Użyj, `content` Aby podać zawartość wiadomości czatu;
- Użyj, `chat_message_type` Aby określić typ zawartości komunikatu. Możliwe wartości to "text" i "HTML"; Jeśli nie zostanie określona wartość domyślna elementu "text".
- Użyj, `sender_display_name` Aby określić nazwę wyświetlaną nadawcy;

`SendChatMessageResult` czy odpowiedź zwrócona przez wysłanie komunikatu, zawiera identyfikator, który jest unikatowym IDENTYFIKATORem komunikatu.

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


## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie komunikatów rozmowy z wątku rozmowy

Wiadomości czatu można pobrać, sondowanie `list_messages` metody w określonych odstępach czasu.

- Użyj `results_per_page` , aby określić maksymalną liczbę komunikatów do zwrócenia na stronę.
- Użyj, `start_time` Aby określić najwcześniejszy punkt w czasie, w którym mają zostać pobrane komunikaty.

Iterator `[ChatMessage]` jest odpowiedzią zwracaną z listy komunikatów

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` zwraca najnowszą wersję komunikatu, w tym wszelkie edycje lub usunięcia, które wystąpiły w komunikacie przy użyciu `update_message` i `delete_message` . W przypadku usuniętych wiadomości `ChatMessage.deleted_on` zwraca wartość typu DateTime wskazującą, kiedy ten komunikat został usunięty. W przypadku edytowanych wiadomości `ChatMessage.edited_on` zwraca wartość typu DateTime wskazującą, kiedy wiadomość była edytowana. Można uzyskać dostęp do oryginalnego czasu tworzenia komunikatów przy użyciu programu `ChatMessage.created_on` , którego można użyć do porządkowania komunikatów.

`list_messages` zwraca różne typy komunikatów, które mogą być identyfikowane przez `ChatMessage.type` . 

Przeczytaj więcej o typach komunikatów tutaj: [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Wyślij potwierdzenie odczytania
`send_read_receipt`Metoda ta może służyć do ogłaszania zdarzenia odczytania do wątku w imieniu użytkownika.

- Użyj `message_id` , aby określić identyfikator najnowszego komunikatu odczytanego przez bieżącego użytkownika.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku rozmowy można z niego dodawać i usuwać użytkowników. Dodanie użytkowników daje im możliwość dostępu do wysyłania komunikatów do wątku rozmowy i dodawania/usuwania innych uczestników. Przed wywołaniem `add_participants` metody upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

Co najmniej jednego użytkownika można dodać do wątku rozmowy przy użyciu `add_participants` metody, podać nowy token dostępu i zidentyfikować jest dostępny dla wszystkich użytkowników.

`list(tuple(ChatThreadParticipant, CommunicationError))`Jest zwracany. Po pomyślnym dodaniu uczestnika jest oczekiwana pusta lista. W przypadku wystąpienia błędu podczas dodawania uczestnika lista zostanie wypełniona uczestnikom zakończonym niepowodzeniem wraz z napotkanym błędem.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
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
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
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


## <a name="list-thread-participants-in-a-chat-thread"></a>Wyświetlanie listy uczestników wątku w wątku rozmowy

Podobnie jak w przypadku dodawania uczestnika, możesz również wyświetlić listę uczestników z wątku.

Służy `list_participants` do pobierania uczestników wątku.
- Użycie `results_per_page` , opcjonalnie, Maksymalna liczba uczestników zwracanych na stronę.
- Użyj `skip` opcjonalne, aby pominąć uczestników do określonej pozycji w odpowiedzi.

Iterator `[ChatThreadParticipant]` jest odpowiedzią zwracaną z listy uczestników

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatThreadParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `python` polecenia.

```console
python start-chat.py
```
