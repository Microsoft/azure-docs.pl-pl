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
ms.openlocfilehash: ce6d2c34c48a26f99f78c364db5f06f9931c9dd7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021659"
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
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.5
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje biblioteki klienta czatu usługi Azure Communication Services dla języka C#.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClientu i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania i pobierania uczestników, wysyłania powiadomień o wpisywaniu i otrzymywania potwierdzeń. |

## <a name="create-a-chat-client"></a>Tworzenie klienta czatu

Aby utworzyć klienta programu chat, należy użyć punktu końcowego usług komunikacyjnych i tokenu dostępu, który został wygenerowany w ramach kroków wymagań wstępnych. Musisz użyć `CommunicationIdentityClient` klasy z biblioteki klienta tożsamości, aby utworzyć użytkownika i wydać token do przekazania do klienta czatu.

Dowiedz się więcej o [tokenach dostępu użytkowników](../../access-tokens.md).

Ten przewodnik Szybki Start nie obejmuje tworzenia warstwy usług w celu zarządzania tokenami dla aplikacji czatu, chociaż jest to zalecane. Dowiedz się więcej o [architekturze rozmowy](../../../concepts/chat/concepts.md)

Skopiuj następujące fragmenty kodu i wklej je do pliku źródłowego: **program.cs**
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `createChatThread` metody z chatClient, aby utworzyć wątek rozmowy
- Użyj, `topic` Aby przekazać temat do tego rozmowy. Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `UpdateTopic` funkcji.
- Użyj `participants` właściwości, aby przekazać listę `ChatParticipant` obiektów do dodania do wątku rozmowy. `ChatParticipant`Obiekt jest inicjowany z `CommunicationIdentifier` obiektem. `CommunicationIdentifier` może być typu `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` lub `PhoneNumberIdentifier` . Na przykład aby uzyskać `CommunicationIdentifier` obiekt, należy przekazać identyfikator dostępu, który został utworzony przez następującą instrukcję, aby [utworzyć użytkownika](../../access-tokens.md#create-an-identity)

Obiekt Response z `createChatThread` metody zawiera `chatThread` szczegóły. Aby można było korzystać z operacji wątku rozmowy, takich jak dodawanie uczestników, wysyłanie komunikatu, usuwanie komunikatu itp., `chatThreadClient` wystąpienie klienta musi być tworzone przy użyciu `GetChatThreadClient` metody na `ChatClient` kliencie.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy
`GetChatThreadClient`Metoda zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj członków, Wyślij wiadomość itp. threadId jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `SendMessage` , aby wysłać komunikat do wątku.

- Użyj `content` , aby podać zawartość wiadomości, jest to wymagane.
- Użyj `type` dla typu zawartości komunikatu, takiego jak "text" lub "HTML". Jeśli nie zostanie określony, zostanie ustawiona wartość "text".
- Użyj, `senderDisplayName` Aby określić nazwę wyświetlaną nadawcy. Jeśli nie zostanie określony, zostanie ustawiony pusty ciąg.

```csharp
var messageId = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
```
## <a name="get-a-message"></a>Pobierz komunikat

Użyj `GetMessage` , aby pobrać komunikat z usługi.
`messageId` jest unikatowym IDENTYFIKATORem komunikatu.

`ChatMessage` czy odpowiedź zwrócona przez pobranie komunikatu, zawiera identyfikator, który jest unikatowym identyfikatorem komunikatu, między innymi polami. Zapoznaj się z tematem Azure. Communications. chat. ChatMessage

```csharp
ChatMessage chatMessage = await chatThreadClient.GetMessageAsync(messageId: messageId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie komunikatów rozmowy z wątku rozmowy

Komunikaty czatu można pobrać, sondowanie `GetMessages` metody na kliencie wątku rozmowy w określonych odstępach czasu.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` przyjmuje opcjonalny `DateTimeOffset` parametr. W przypadku określenia tego przesunięcia zostaną odebrane komunikaty, które zostały odebrane, zaktualizowane lub usunięte. Należy zauważyć, że komunikaty odebrane przed upływem czasu przesunięcia, ale edytowane lub usunięte po tym, również zostaną zwrócone.

`GetMessages` zwraca najnowszą wersję komunikatu, w tym wszelkie edycje lub usunięcia, które wystąpiły w komunikacie przy użyciu `UpdateMessage` i `DeleteMessage` . W przypadku usuniętych komunikatów `chatMessage.DeletedOn` Funkcja zwraca wartość typu DateTime wskazującą, kiedy ten komunikat został usunięty. W przypadku edytowanych wiadomości `chatMessage.EditedOn` zwraca wartość typu DateTime wskazującą, kiedy wiadomość była edytowana. Pierwotny czas tworzenia wiadomości można uzyskać za pomocą polecenia `chatMessage.CreatedOn` i można go użyć do porządkowania komunikatów.

`GetMessages` zwraca różne typy komunikatów, które mogą być identyfikowane przez `chatMessage.Type` . Są to następujące typy:

- `Text`: Zwykły komunikat rozmowy Wysłany przez element członkowski wątku.

- `Html`: Sformatowana wiadomość tekstowa. Należy zauważyć, że użytkownicy usług komunikacyjnych obecnie nie mogą wysyłać komunikatów o postaci tekstu sformatowanego. Ten typ komunikatu jest obsługiwany przez komunikaty wysyłane przez zespoły użytkowników do użytkowników usług komunikacyjnych w scenariuszach międzyoperacyjności zespołów.

- `TopicUpdated`: Komunikat systemowy wskazujący, że Zaktualizowano temat. trybie

- `ParticipantAdded`: Komunikat systemowy wskazujący, że co najmniej jeden uczestnik został dodany do wątku rozmowy. trybie

- `ParticipantRemoved`: Komunikat systemowy wskazujący, że uczestnik został usunięty z wątku rozmowy.

Aby uzyskać więcej informacji, zobacz [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Aktualizowanie wiadomości

Można zaktualizować komunikat, który został już wysłany przez wywołanie `UpdateMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(messageId: id, content: content);
```

## <a name="deleting-a-message"></a>Usuwanie wiadomości

Można usunąć komunikat, wywołując `DeleteMessage` na `ChatThreadClient` .

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(messageId: id);
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku można następnie dodawać i usuwać użytkowników. Dodanie użytkowników daje im możliwość dostępu do wysyłania komunikatów do wątku oraz dodawania/usuwania innego uczestnika. Przed wywołaniem `AddParticipants` upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

Służy `AddParticipants` do dodawania jednego lub kilku uczestników do wątku rozmowy. Obsługiwane są następujące atrybuty dla każdego uczestnika wątku:
- `communicationUser`, wymagane, jest tożsamością uczestnika wątku.
- `displayName`, opcjonalnie, jest nazwą wyświetlaną uczestnika wątku.
- `shareHistoryTime`, opcjonalnie, godzinę, z której historia rozmowy jest udostępniana uczestnikowi.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```
## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Podobnie jak w przypadku dodawania użytkownika do wątku, można usunąć użytkowników z wątku rozmowy. W tym celu należy śledzić tożsamość `CommunicationUser` dodanego uczestnika.

```csharp
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
await chatThreadClient.RemoveParticipantAsync(identifier: gloria);
```

## <a name="get-thread-participants"></a>Pobierz uczestników wątku

Służy `GetParticipants` do pobierania uczestników wątku rozmowy.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-typing-notification"></a>Wyślij powiadomienie o wpisaniu

Użyj `SendTypingNotification` , aby wskazać, że użytkownik pisze odpowiedź w wątku.

```csharp
await chatThreadClient.SendTypingNotificationAsync();
```

## <a name="send-read-receipt"></a>Wyślij potwierdzenie odczytania

Służy `SendReadReceipt` do powiadamiania innych uczestników, że wiadomość jest odczytywana przez użytkownika.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="get-read-receipts"></a>Pobierz potwierdzenia odczytu

Użyj `GetReadReceipts` do sprawdzenia stanu komunikatów, aby zobaczyć, które z nich są odczytywane przez innych uczestników wątku rozmowy.

```csharp
AsyncPageable<ChatMessageReadReceipt> allReadReceipts = chatThreadClient.GetReadReceiptsAsync();
await foreach (ChatMessageReadReceipt readReceipt in allReadReceipts)
{
    Console.WriteLine($"{readReceipt.ChatMessageId}:{((CommunicationUserIdentifier)readReceipt.Sender).Id}:{readReceipt.ReadOn}");
}
```
## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```console
dotnet run
```
