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
ms.openlocfilehash: 2d1c3d3be412f6f11f9d2e300b3a97cf5634f5e4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495458"
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
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Zainstaluj bibliotekę kliencką

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta programu Azure Communication Services Chat dla języka Python.

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
from azure.communication.chat import ChatClient
from azure.communication.identity._shared.user_credential import CommunicationTokenCredential
from azure.communication.chat._shared.user_token_refresh_options import CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `create_chat_thread` metody, aby utworzyć wątek rozmowy.

- Użyj, `topic` Aby nadać temat wątku; Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `update_thread` funkcji.
- Użyj `thread_participants` , aby wyświetlić listę, która ma `ChatThreadParticipant` zostać dodana do wątku rozmowy `ChatThreadParticipant` , `CommunicationUserIdentifier` Typ pobiera jako `user` , który jest po utworzeniu przez [utworzenie użytkownika](../../access-tokens.md#create-an-identity)
- Użyj `repeatability_request_id` , aby skierować żądanie do powtarzania. Klient może wielokrotnie wprowadzać żądanie z tym samym IDENTYFIKATORem żądania powtórzenia i uzyskać odpowiednią odpowiedź bez konieczności wielokrotnego wykonywania żądania przez serwer.

`CreateChatThreadResult` Czy wynikiem jest utworzenie wątku, można go użyć do pobrania `id` utworzonego wątku rozmowy. `id`Można go następnie użyć do pobrania `ChatThreadClient` obiektu za pomocą `get_chat_thread_client` metody. `ChatThreadClient` może służyć do wykonywania innych operacji czatu do tego wątku rozmowy.

#### <a name="without-repeatability-request-id"></a>Bez powtarzalności-ID żądania
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

#### <a name="with-repeatability-request-id"></a>Z powtarzalność-Request-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, 
                                                    thread_participants=participants, 
                                                    repeatability_request_id=repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy
`get_chat_thread_client`Metoda zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj uczestników, Wyślij wiadomość itp. thread_id jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

`ChatThreadClient` może służyć do wykonywania innych operacji czatu do tego wątku rozmowy.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="get-a-chat-thread"></a>Pobieranie wątku rozmowy

`get_chat_thread`Metoda use pobiera a `ChatThread` z usługi; `thread_id` jest unikatowym identyfikatorem wątku.
- Użyj `thread_id` , Required, aby określić unikatowy identyfikator wątku. 
```python
chat_thread = chat_client.get_chat_thread(thread_id=thread_id)
```

## <a name="list-all-chat-threads"></a>Wyświetl wszystkie wątki rozmowy
`list_chat_threads`Metoda zwraca iterator typu `ChatThreadInfo` . Może służyć do wyświetlania listy wszystkich wątków rozmowy.

- Użyj `start_time` , aby określić najwcześniejszy punkt w czasie, aby uzyskać wątki rozmowy do.
- Użyj `results_per_page` , aby określić maksymalną liczbę wątków czatu zwracanych na stronę.

Iterator `[ChatThreadInfo]` jest odpowiedzią zwracaną z listy wątków

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        print(chat_thread_info)
```

## <a name="delete-a-chat-thread"></a>Usuwanie wątku rozmowy
Służy `delete_chat_thread` do usuwania wątku rozmowy.

- Użyj, `thread_id` Aby określić thread_id istniejącego wątku rozmowy, który należy usunąć

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_client.delete_chat_thread(thread_id=thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `send_message` metody, aby wysłać wiadomość do wątku rozmowy, który właśnie został utworzony, identyfikowanego przez thread_id.

- Użyj, `content` Aby podać zawartość wiadomości czatu;
- Użyj, `chat_message_type` Aby określić typ zawartości komunikatu. Możliwe wartości to "text" i "HTML"; Jeśli nie zostanie określona wartość domyślna elementu "text".
- Użyj, `sender_display_name` Aby określić nazwę wyświetlaną nadawcy;

Odpowiedź jest identyfikatorem typu `str` , który jest unikatowym identyfikatorem tego komunikatu.

#### <a name="message-type-not-specified"></a>Nie określono typu komunikatu
```python
topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)

content='hello world'

send_message_result_id = chat_thread_client.send_message(content)
```

#### <a name="message-type-specified"></a>Określony typ komunikatu
```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_id_w_enum)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
print("Message sent: id: ", send_message_result_id_w_str)
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Pobierz konkretny komunikat rozmowy z wątku rozmowy
`get_message`Funkcja może służyć do pobierania określonego komunikatu identyfikowanego przez message_id

- Użyj, `message_id` Aby określić identyfikator wiadomości.

Odpowiedź typu `ChatMessage` zawiera wszystkie informacje związane z pojedynczym komunikatem.

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
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

`list_messages` zwraca różne typy komunikatów, które mogą być identyfikowane przez `ChatMessage.type` . Są to następujące typy:

- `ChatMessageType.TEXT`: Zwykły komunikat rozmowy Wysłany przez uczestnika wątku.

- `ChatMessageType.HTML`: Komunikat rozmowy HTML Wysłany przez uczestnika wątku.

- `ChatMessageType.TOPIC_UPDATED`: Komunikat systemowy wskazujący, że Zaktualizowano temat.

- `ChatMessageType.PARTICIPANT_ADDED`: Komunikat systemowy wskazujący, że co najmniej jeden uczestnik został dodany do wątku rozmowy.

- `ChatMessageType.PARTICIPANT_REMOVED`: Komunikat systemowy wskazujący, że uczestnik został usunięty z wątku rozmowy.

Aby uzyskać więcej informacji, zobacz [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="update-topic-of-a-chat-thread"></a>Aktualizowanie tematu wątku rozmowy
Można zaktualizować temat wątku rozmowy przy użyciu `update_topic` metody

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)

chat_thread = chat_client.get_chat_thread(chat_thread_client.thread_id)
updated_topic = chat_thread.topic

print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Aktualizowanie wiadomości
Zawartość istniejącego komunikatu można zaktualizować przy użyciu `update_message` metody identyfikowanej przez message_id

- Użycie `message_id` , wymagane, jest unikatowym identyfikatorem komunikatu.
- Użycie `content` , opcjonalnie, jest treścią wiadomości do zaktualizowania; Jeśli nie zostanie określona, jest ona przypisana do pustego

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>Wyślij potwierdzenie odczytania dla wiadomości
`send_read_receipt`Metoda ta może służyć do ogłaszania zdarzenia odczytania do wątku w imieniu użytkownika.

- Użyj `message_id` , aby określić identyfikator najnowszego komunikatu odczytanego przez bieżącego użytkownika.

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Wyświetlanie listy przyjęć dla wątku rozmowy
`list_read_receipts`Metoda może służyć do uzyskania potwierdzeń odczytania wątku.

- Użyj `results_per_page` , aby określić maksymalną liczbę potwierdzeń odczytu komunikatów rozmowy na stronie.
- Użyj `skip` , aby określić pomijanie wiadomości czatu Odczytaj do określonej pozycji w odpowiedzi.

Iterator `[ChatMessageReadReceipt]` jest odpowiedzią zwracaną z listy potwierdzeń odczytu

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=5, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
        print('Sender', read_receipt.sender)
        print('Message Id', read_receipt.chat_message_id)
        print('Read On Timestamp', read_receipt.read_on)
```

## <a name="send-typing-notification"></a>Wyślij powiadomienie o wpisaniu
`send_typing_notification`Metoda może służyć do publikowania zdarzenia wpisywania do wątku w imieniu użytkownika.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Usuń komunikat
`delete_message`Metoda może służyć do usuwania wiadomości identyfikowanej przez message_id

- Użyj, `message_id` Aby określić message_id

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku rozmowy można z niego dodawać i usuwać użytkowników. Dodanie użytkowników daje im możliwość dostępu do wysyłania komunikatów do wątku rozmowy i dodawania/usuwania innych uczestników. Przed wywołaniem `add_participant` metody upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

Użyj `add_participant` metody, aby dodać uczestników wątku do wątku identyfikowanego przez thread_id.

- Użyj `thread_participant` , aby określić uczestnika, który ma zostać dodany do wątku rozmowy.
- `user`, wymagane, jest `CommunicationUserIdentifier` autorem utworzonym `CommunicationIdentityClient` przez [użytkownika](../../access-tokens.md#create-an-identity)
- `display_name`, opcjonalnie, jest nazwą wyświetlaną uczestnika wątku.
- `share_history_time`, opcjonalnie, to czas, po którym historia rozmowy jest udostępniana uczestnikowi. Aby udostępnić historię od momentu rozpoczęcia wątku rozmowy, należy ustawić tę właściwość na dowolną datę równą lub mniejszą niż godzina utworzenia wątku. Aby po dodaniu uczestnika nie była udostępniona żadna historia, ustaw ją na bieżącą datę. Aby udostępnić historię częściową, ustaw ją na datę pośrednią.

Po pomyślnym dodaniu uczestnika nie zostanie zgłoszony żaden błąd. W przypadku wystąpienia błędu podczas dodawania uczestnika `RuntimeError` jest zgłaszany

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create an user
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_user = identity_client.create_user()

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

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

participant = ChatThreadParticipant(
    user=new_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow())

try:
    chat_thread_client.add_participant(thread_participant=participant)
except RuntimeError as e:
    if e is not None and decide_to_retry(error=e):
        chat_thread_client.add_participant(thread_participant=participant)
```

Wielu użytkowników można również dodać do wątku rozmowy przy użyciu `add_participants` metody, podać nowy token dostępu i zidentyfikować jest dostępny dla wszystkich użytkowników.

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

response = chat_thread_client.add_participants(thread_participants=participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if len(retry) > 0:
    chat_thread_client.add_participants(retry)
```


## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Podobnie jak w przypadku dodawania uczestnika, można również usunąć uczestników z wątku. Aby można było usunąć, należy śledzić identyfikatory dodanych uczestników.

Użyj `remove_participant` metody, aby usunąć uczestnika wątku z wątku identyfikowanego przez ThreadID.
- `user` jest `CommunicationUserIdentifier` usuwany z wątku.

```python
chat_thread_client.remove_participant(user=new_user)

# # converesely you can also do the following; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
# 
# user_id = 'some user id'
# chat_thread_client.remove_participant(user=CommunincationUserIdentfier(new_user))
```

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `python` polecenia.

```console
python start-chat.py
```
