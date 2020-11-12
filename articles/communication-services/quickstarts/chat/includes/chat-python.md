---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523956"
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

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `create_chat_thread` metody, aby utworzyć wątek rozmowy.

- Użyj, `topic` Aby nadać temat wątku; Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `update_thread` funkcji.
- Użyj `members` , aby wyświetlić listę, która ma `ChatThreadMember` zostać dodana do wątku rozmowy `ChatThreadMember` , `CommunicationUser` Typ pobiera jako `user` , który jest po utworzeniu przez [utworzenie użytkownika](../../access-tokens.md#create-an-identity)

Odpowiedź `chat_thread_client` jest używana do wykonywania operacji na nowo utworzonym wątku rozmowy, takich jak dodawanie elementów członkowskich do wątku rozmowy, wysyłanie wiadomości, usuwanie wiadomości itd. Zawiera `thread_id` Właściwość, która jest unikatowym identyfikatorem wątku rozmowy.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy
Metoda get_chat_thread_client zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj członków, Wyślij wiadomość itp. thread_id jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `send_message` metody, aby wysłać wiadomość do wątku rozmowy, który właśnie został utworzony, identyfikowanego przez ThreadID.

- Użyj, `content` Aby podać zawartość wiadomości czatu;
- Służy `priority` do określania poziomu priorytetu wiadomości, takiego jak "normal" lub "High". Ta właściwość może być używana jako wskaźnik interfejsu użytkownika dla użytkownika odbiorcy w aplikacji, aby mógł zwrócić uwagę na komunikat lub wykonać niestandardową logikę biznesową.
- Użyj, `senderDisplayName` Aby określić nazwę wyświetlaną nadawcy;

Odpowiedź `SendChatMessageResult` zawiera identyfikator, który jest unikatowym identyfikatorem tego komunikatu.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie komunikatów rozmowy z wątku rozmowy

Wiadomości czatu można pobrać, sondowanie `list_messages` metody w określonych odstępach czasu.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` zwraca najnowszą wersję komunikatu, w tym wszelkie edycje lub usunięcia, które wystąpiły w komunikacie przy użyciu `update_message` i `delete_message` . W przypadku usuniętych wiadomości `ChatMessage.deleted_on` zwraca wartość typu DateTime wskazującą, kiedy ten komunikat został usunięty. W przypadku edytowanych wiadomości `ChatMessage.edited_on` zwraca wartość typu DateTime wskazującą, kiedy wiadomość była edytowana. Można uzyskać dostęp do oryginalnego czasu tworzenia komunikatów przy użyciu programu `ChatMessage.created_on` , którego można użyć do porządkowania komunikatów.

`list_messages` zwraca różne typy komunikatów, które mogą być identyfikowane przez `ChatMessage.type` . Są to następujące typy:

- `Text`: Zwykły komunikat rozmowy Wysłany przez element członkowski wątku.

- `ThreadActivity/TopicUpdate`: Komunikat systemowy wskazujący, że Zaktualizowano temat.

- `ThreadActivity/AddMember`: Komunikat systemowy wskazujący, że co najmniej jeden element członkowski został dodany do wątku rozmowy.

- `ThreadActivity/DeleteMember`: Komunikat systemowy wskazujący, że element członkowski został usunięty z wątku rozmowy.

Aby uzyskać więcej informacji, zobacz [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Dodawanie użytkownika jako elementu członkowskiego do wątku rozmowy

Po utworzeniu wątku rozmowy można z niego dodawać i usuwać użytkowników. Dodanie użytkowników daje im możliwość dostępu do wysyłania komunikatów do wątku rozmowy i dodawania/usuwania innych członków. Przed wywołaniem `add_members` metody upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

Użyj `add_members` metody, aby dodać elementy członkowskie wątku do wątku identyfikowanego przez ThreadID.

- Użyj, `members` Aby wyświetlić listę elementów członkowskich, które mają zostać dodane do wątku rozmowy.
- `user`, wymagane, jest `CommunicationUser` autorem utworzonym `CommunicationIdentityClient` przez [użytkownika](../../access-tokens.md#create-an-identity)
- `display_name`, opcjonalnie, jest nazwą wyświetlaną dla elementu członkowskiego wątku.
- `share_history_time`, opcjonalnie, to czas, od którego historia rozmowy jest udostępniana członkowi. Aby udostępnić historię od momentu rozpoczęcia wątku rozmowy, należy ustawić tę właściwość na dowolną datę równą lub mniejszą niż godzina utworzenia wątku. Aby po dodaniu elementu członkowskiego nie była udostępniona żadna historia, ustaw ją na bieżącą datę. Aby udostępnić historię częściową, ustaw ją na datę pośrednią.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Podobnie jak w przypadku dodawania elementu członkowskiego, można również usunąć członków z wątku. Aby można było usunąć, należy śledzić identyfikatory dodanych elementów członkowskich.

Użyj `remove_member` metody, aby usunąć element członkowski wątku z wątku identyfikowanego przez ThreadID.
- `user` to CommunicationUser, które mają zostać usunięte z wątku.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `python` polecenia.

```console
python start-chat.py
```
