---
title: dołączanie pliku
description: dołączanie pliku
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: ecf9575f2ab469c9226ec18a4b3b4647e582d408
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376329"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że:
- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Zainstaluj [program Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../create-communication-resource.md). Musisz zarejestrować **punkt końcowy** zasobu dla tego przewodnika Szybki Start.
- [Token dostępu użytkownika](../../access-tokens.md). Upewnij się, że ustawiono zakres "Rozmowa" i zanotuj ciąg tokenu, a także ciąg identyfikatora użytkownika.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `ChatQuickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Zmień katalog na nowo utworzony folder aplikacji i Użyj `dotnet build` polecenia, aby skompilować aplikację.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Zainstaluj bibliotekę kliencką rozmowy komunikacyjnej platformy Azure dla platformy .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.1
``` 

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje biblioteki klienta czatu usługi Azure Communication Services dla języka C#.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClient i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i otrzymywania potwierdzeń. |

## <a name="create-a-chat-client"></a>Tworzenie klienta czatu

Aby utworzyć klienta programu chat, należy użyć punktu końcowego usług komunikacyjnych i tokenu dostępu, który został wygenerowany w ramach kroków wymagań wstępnych. Musisz użyć `CommunicationIdentityClient` klasy z `Administration` biblioteki klienta, aby utworzyć użytkownika i wydać token do przekazania do klienta czatu. Dowiedz się więcej o [tokenach dostępu użytkowników](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `createChatThread` metody, aby utworzyć wątek rozmowy.
- Użyj, `topic` Aby przekazać temat do tego rozmowy. Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `UpdateThread` funkcji.
- Użyj `members` właściwości, aby przekazać listę `ChatThreadMember` obiektów do dodania do wątku rozmowy. `ChatThreadMember`Obiekt jest inicjowany z `CommunicationUser` obiektem. Aby uzyskać `CommunicationUser` obiekt, należy przekazać identyfikator dostępu, który został utworzony przez następującą instrukcję, aby [utworzyć użytkownika](../../access-tokens.md#create-a-user)

Odpowiedź `chatThreadClient` jest używana do wykonywania operacji w utworzonym wątku czatu: Dodawanie członków do wątku rozmowy, wysyłanie wiadomości, usuwanie wiadomości itd. Zawiera atrybut, `Id` który jest unikatowym identyfikatorem wątku rozmowy. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy
`GetChatThreadClient`Metoda zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj członków, Wyślij wiadomość itp. threadId jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = await chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `SendMessage` metody, aby wysłać komunikat do wątku identyfikowanego przez ThreadID.

- Użyj `content` , aby podać zawartość wiadomości czatu, jest to wymagane.
- Użyj, `priority` Aby określić poziom priorytetu wiadomości, taki jak "normal" lub "High", jeśli nie zostanie określony, zostanie użyta wartość "normal".
- Użyj, `senderDisplayName` Aby określić nazwę wyświetlaną nadawcy, jeśli nie zostanie określony, zostanie użyta pusta nazwa.

`SendChatMessageResult` czy odpowiedź zwrócona przez wysłanie komunikatu, zawiera identyfikator, który jest unikatowym IDENTYFIKATORem komunikatu.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie komunikatów rozmowy z wątku rozmowy

Komunikaty czatu można pobrać, sondowanie `GetMessages` metody na kliencie wątku rozmowy w określonych odstępach czasu.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` przyjmuje opcjonalny `DateTimeOffset` parametr. W przypadku określenia tego przesunięcia zostaną odebrane komunikaty, które zostały odebrane, zaktualizowane lub usunięte. Należy zauważyć, że komunikaty odebrane przed upływem czasu przesunięcia, ale edytowane lub usunięte po tym, również zostaną zwrócone.

`GetMessages` zwraca najnowszą wersję komunikatu, w tym wszelkie edycje lub usunięcia, które wystąpiły w komunikacie przy użyciu `UpdateMessage` i `DeleteMessage` . W przypadku usuniętych komunikatów `chatMessage.DeletedOn` Funkcja zwraca wartość typu DateTime wskazującą, kiedy ten komunikat został usunięty. W przypadku edytowanych wiadomości `chatMessage.EditedOn` zwraca wartość typu DateTime wskazującą, kiedy wiadomość była edytowana. Pierwotny czas tworzenia wiadomości można uzyskać za pomocą polecenia `chatMessage.CreatedOn` i można go użyć do porządkowania komunikatów.

`GetMessages` zwraca różne typy komunikatów, które mogą być identyfikowane przez `chatMessage.Type` . Są to następujące typy:

- `Text`: Zwykły komunikat rozmowy Wysłany przez element członkowski wątku.

- `ThreadActivity/TopicUpdate`: Komunikat systemowy wskazujący, że Zaktualizowano temat.

- `ThreadActivity/AddMember`: Komunikat systemowy wskazujący, że co najmniej jeden element członkowski został dodany do wątku rozmowy.

- `ThreadActivity/DeleteMember`: Komunikat systemowy wskazujący, że element członkowski został usunięty z wątku rozmowy.

Aby uzyskać więcej informacji, zobacz [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Aktualizowanie wiadomości

Można zaktualizować komunikat, który został już wysłany przez wywołanie `UpdateMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Usuwanie wiadomości

Można usunąć komunikat, wywołując `DeleteMessage` na `ChatThreadClient` .

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Dodawanie użytkownika jako elementu członkowskiego do wątku rozmowy

Po utworzeniu wątku można następnie dodawać i usuwać użytkowników. Dodanie użytkowników daje im możliwość dostępu do wysyłania komunikatów do wątku oraz dodawania/usuwania innych członków. Przed wywołaniem `AddMembers` upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

Użyj `AddMembers` metody, aby dodać elementy członkowskie wątku do wątku identyfikowanego przez ThreadID.

 - Użyj, `members` Aby wyświetlić listę elementów członkowskich, które mają zostać dodane do wątku rozmowy.
 - `User`, wymagane, jest tożsamością uzyskaną dla nowego użytkownika.
 - `DisplayName`, opcjonalnie, jest nazwą wyświetlaną dla elementu członkowskiego wątku.
 - `ShareHistoryTime`, opcjonalnie, czas, od którego historia rozmowy jest udostępniana członkowi. Aby udostępnić historię od początku wątku rozmowy, ustaw dla niego wartość DateTime. MinValue. Aby udostępnić historię braku historii, poprzedni do momentu dodania elementu członkowskiego, ustaw go na bieżącą godzinę. Aby udostępnić historię częściową, ustaw ją na punkt w czasie między utworzeniem wątku a bieżącym czasem.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Podobnie jak w przypadku dodawania użytkownika do wątku, można usunąć użytkowników z wątku rozmowy. W tym celu należy śledzić tożsamość (CommunicationUser) dodanych członków.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```console
dotnet run
```
