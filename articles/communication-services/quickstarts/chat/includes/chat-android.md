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
ms.openlocfilehash: b2c5237f3f7e949edbfb5486a3a17cc6e0a008a4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178395"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zainstaluj [Android Studio](https://developer.android.com/studio), będziemy używać Android Studio do tworzenia aplikacji systemu Android dla przewodnika Szybki Start, aby zainstalować zależności.
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../create-communication-resource.md). Musisz **zarejestrować punkt końcowy zasobu** dla tego przewodnika Szybki Start.
- Utwórz **dwóch** użytkowników usług komunikacyjnych i wystawiaj im [token dostępu użytkownika](../../access-tokens.md)tokenu dostępu użytkownika. Pamiętaj, aby ustawić zakres do **rozmowy** i **zanotować ciąg tokenu i ciąg identyfikatora użytkownika**. W tym przewodniku szybki start utworzymy wątek z początkowym uczestnikiem, a następnie dodamy drugiego uczestnika do wątku.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-android-application"></a>Tworzenie nowej aplikacji systemu Android

1. Otwórz Android Studio i wybierz opcję `Create a new project` . 
2. W następnym oknie Wybierz `Empty Activity` jako szablon projektu.
3. W przypadku wybrania opcji wprowadź `ChatQuickstart` nazwę projektu.
4. Kliknij przycisk Dalej i wybierz katalog, w którym ma zostać utworzony projekt.

### <a name="install-the-libraries"></a>Instalowanie bibliotek

Będziemy używać Gradle, aby zainstalować wymagane zależności usług komunikacyjnych. W wierszu polecenia przejdź do katalogu głównego `ChatQuickstart` projektu. Otwórz plik Build. Gradle aplikacji i Dodaj następujące zależności do `ChatQuickstart` obiektu docelowego:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Wyklucz meta pliki w opcjach pakowania w katalogu głównym Build. Gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>Różne Aby zainstalować biblioteki za poorednictwem Maven
Aby zaimportować bibliotekę do projektu przy użyciu systemu kompilacji [Maven](https://maven.apache.org/) , Dodaj go do `dependencies` sekcji `pom.xml` pliku aplikacji, określając jej identyfikator artefaktu i wersję, której chcesz użyć:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Skonfiguruj symbole zastępcze

Otwórz i edytuj plik `MainActivity.java` . W tym przewodniku szybki start dodamy nasz kod do i zobaczysz `MainActivity` dane wyjściowe w konsoli programu. Ten przewodnik Szybki Start nie rozsłuży do tworzenia interfejsu użytkownika. W górnej części pliku zaimportuj `Communication common` biblioteki i `Communication chat` :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Skopiuj następujący kod do pliku `MainActivity` :

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

W poniższych krokach zamienimy symbole zastępcze z przykładowym kodem przy użyciu biblioteki czatu usługi Azure Communication Services.


### <a name="create-a-chat-client"></a>Tworzenie klienta czatu

Zastąp komentarz `<CREATE A CHAT CLIENT>` następującym kodem (Umieść instrukcje importu u góry pliku):

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. Użyj, `ChatClientBuilder` Aby skonfigurować i utworzyć wystąpienie `ChatAsyncClient` .
2. Zamień `<resource>` na zasób usług komunikacyjnych.
3. Zamień `<user_access_token>` na prawidłowy token dostępu usług komunikacyjnych.

## <a name="object-model"></a>Model obiektów
Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu Azure Communications Services Chat SDK dla języka JavaScript.

| Nazwa                                   | Opis                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient/ChatThreadAsyncClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClientu i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i odczytywania, subskrybowania zdarzeń rozmowy. |

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Będziemy używać naszego `ChatAsyncClient` do utworzenia nowego wątku z użytkownikiem początkowym.

Zastąp komentarz `<CREATE A CHAT THREAD>` następującym kodem:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

Zamień `<user_id>` na prawidłowy identyfikator użytkownika usług komunikacyjnych. Użyjemy `threadId` od odpowiedzi zwróconej do procedury obsługi uzupełniania w dalszych krokach, więc Zastąp wartość `<thread_id>` w klasie `threadId` pobraną z tego żądania i ponownie uruchom aplikację.

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

Teraz, gdy utworzyliśmy wątek rozmowy, otrzymamy `ChatThreadAsyncClient` do wykonania operacji w wątku. Zastąp komentarz `<CREATE A CHAT THREAD CLIENT>` następującym kodem:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Upewnij się, że został zastąpiony `<thread_id>` prawidłowym identyfikatorem wątku, wyślemy teraz komunikat do tego wątku.

Zastąp komentarz `<SEND A MESSAGE>` następującym kodem:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

Po otrzymaniu `chatMessageId` tej metody można zastąpić poleceniem `<chat_message_id>` `chatMessageId` w celu użycia metod w przyszłości w szybkim samouczku i ponownym uruchomieniu aplikacji.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Zastąp komentarz `<ADD A USER>` następującym kodem:

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

Zastąp wartość `<second_user_id>` w klasie identyfikatorem użytkownika usług komunikacyjnych, który ma zostać dodany. 

## <a name="list-users-in-a-thread"></a>Wyświetlanie listy użytkowników w wątku

Zastąp komentarz `<LIST USERS>` następującym kodem:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Umieść następującą metodę pomocnika w klasie:

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Upewnij się, że zastąpisz `<second_user_id>` do prawidłowego identyfikatora użytkownika, a teraz usuniesz drugiego użytkownika z wątku.

Zastąp komentarz `<REMOVE A USER>` następującym kodem:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Wyślij powiadomienie o wpisaniu

Zastąp komentarz `<SEND A TYPING NOTIFICATION>` następującym kodem:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Wyślij potwierdzenie odczytania

Pamiętaj, aby zastąpić `<chat_message_id>` identyfikatorem prawidłowym komunikatem czatu. teraz wyślemy do niego potwierdzenie odczytania dla tej wiadomości.

Zastąp komentarz `<SEND A READ RECEIPT>` następującym kodem:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Lista potwierdzeń odczytu

Zastąp komentarz `<READ RECEIPTS>` następującym kodem:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Umieść następującą metodę pomocnika w klasie:
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>Uruchamianie kodu

W Android Studio naciśnij przycisk Run (Uruchom), aby skompilować i uruchomić projekt. W konsoli programu można wyświetlić dane wyjściowe z kodu i wyjścia rejestratora z ChatClient.
