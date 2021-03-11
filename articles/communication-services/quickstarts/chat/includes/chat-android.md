---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7472e899c0ef6c87b19c7bb94bce5f754247c627
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623507"
---
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
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.7'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.7'
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

#### <a name="add-a-maven-resource-in-root-buildgradle"></a>Dodawanie zasobu Maven w katalogu głównym Build. Gradle
```
allprojects {
    repositories {
        ...
        maven {
            url 'https://trouterpublicpackages.z13.web.core.windows.net'
        }
    }
```

Kliknij pozycję "Synchronizuj teraz" w Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>Różne Aby zainstalować biblioteki za poorednictwem Maven
Aby zaimportować bibliotekę do projektu przy użyciu systemu kompilacji [Maven](https://maven.apache.org/) , Dodaj go do `dependencies` sekcji `pom.xml` pliku aplikacji, określając jej identyfikator artefaktu i wersję, której chcesz użyć:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.7</version>
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
    private String second_user_id = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.7";
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
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient client = new ChatAsyncClient.Builder()
        .endpoint(endpoint)
        .credentialInterceptor(chain -> chain.proceed(chain.request()
                .newBuilder()
                .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                .build()))
        .build();
```

1. Użyj, `ChatAsyncClient.Builder` Aby skonfigurować i utworzyć wystąpienie `ChatAsyncClient` .
2. Zamień `<resource>` na zasób usług komunikacyjnych.
3. Zamień `<user_access_token>` na prawidłowy token dostępu usług komunikacyjnych.

## <a name="object-model"></a>Model obiektów
Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta czatu usługi Azure Communication Services dla języka JavaScript.

| Nazwa                                   | Opis                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient/ChatThreadAsyncClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClientu i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i odczytywania, subskrybowania zdarzeń rozmowy. |

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Będziemy używać naszego `ChatClient` do utworzenia nowego wątku z użytkownikiem początkowym.

Zastąp komentarz `<CREATE A CHAT THREAD>` następującym kodem:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
        .setCommunicationIdentifier(new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(id)))
        .setDisplayName(displayName)
);

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
  .setTopic(topic)
  .setParticipants(participants);

// optional, set a repeat request ID
final String repeatabilityRequestID = "";

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
      public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
              ChatThread chatThread = result.getChatThread();
              threadId = chatThread.getId();
              // take further action
              Log.i(TAG, "threadId: " + threadId);
      }

      public void onFailure(Throwable throwable, okhttp3.Response response) {
              // Handle error.
              Log.e(TAG, throwable.getMessage());
      }
});
```

Zamień `<user_id>` na prawidłowy identyfikator użytkownika usług komunikacyjnych. Użyjemy `threadId` od odpowiedzi zwróconej do procedury obsługi uzupełniania w dalszych krokach, więc Zastąp wartość `<thread_id>` w klasie `threadId` pobraną z tego żądania i ponownie uruchom aplikację.

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

Teraz, gdy utworzyliśmy wątek rozmowy, otrzymamy `ChatThreadClient` do wykonania operacji w wątku. Zastąp komentarz `<CREATE A CHAT THREAD CLIENT>` następującym kodem:

```
ChatThreadAsyncClient threadClient =
        new ChatThreadAsyncClient.Builder()
                .endpoint(endpoint)
                .credentialInterceptor(chain -> chain.proceed(chain.request()
                    .newBuilder()
                    .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                    .build()))
                .build();
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Upewnij się, że został zastąpiony `<thread_id>` prawidłowym identyfikatorem wątku, wyślemy teraz komunikat do tego wątku.

Zastąp komentarz `<SEND A MESSAGE>` następującym kodem:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
        .setType(ChatMessageType.TEXT)
        .setContent(content)
        .setSenderDisplayName(senderDisplayName);

threadClient.sendChatMessage(threadId, message, new Callback<String>() {
        @Override
        public void onSuccess(String messageId, okhttp3.Response response) {
                // A string is the response returned from sending a message, it is an id,
                // which is the unique ID of the message.
                chatMessageId = messageId;
                // Take further action.
                Log.i(TAG, "chatMessageId: " + chatMessageId);
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Po otrzymaniu `chatMessageId` tej metody można zastąpić poleceniem `<chat_message_id>` `chatMessageId` w celu użycia metod w przyszłości w szybkim samouczku i ponownym uruchomieniu aplikacji.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Zastąp komentarz `<ADD A USER>` następującym kodem:

```java
//  The list of ChatParticipant to be added to the thread.
participants = new ArrayList<>();
// The display name for the thread participant.
displayName = "a new participant";
participants.add(new ChatParticipant().setCommunicationIdentifier(
          new CommunicationIdentifierModel().setCommunicationUser(
              new CommunicationUserIdentifierModel().setId(second_user_id)
          )).setDisplayName(secondUserDisplayName));
// The model to pass to the add method.
AddChatParticipantsRequest addParticipantsRequest = new AddChatParticipantsRequest()
  .setParticipants(participants);

threadClient.addChatParticipants(threadId, addParticipantsRequest, new Callback<AddChatParticipantsResult>() {
        @Override
        public void onSuccess(AddChatParticipantsResult result, okhttp3.Response response) {
                // Take further action.
                Log.i(TAG, "add chat participants success");
        }
        
        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Zastąp wartość `<second_user_id>` w klasie identyfikatorem użytkownika usług komunikacyjnych, który ma zostać dodany. 

## <a name="list-users-in-a-thread"></a>Wyświetlanie listy użytkowników w wątku

Zastąp komentarz `<LIST USERS>` następującym kodem:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection,
            okhttp3.Response response) {
                // pageCollection enables enumerating list of chat participants.
                pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
                    @Override
                    public void onSuccess(Page<ChatParticipant> firstPage, okhttp3.Response response) {
                        for (ChatParticipant participant : firstPage.getItems()) {
                            // Take further action.
                            Log.i(TAG, "participant: " + participant.getDisplayName());
                        }
                        listChatParticipantsNext(firstPage.getNextPageId(), pageCollection);
                    }

                    @Override
                    public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                    }
                });

                @Override
                public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                }
                });
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Umieść następującą metodę pomocnika w klasie:

```java
void listChatParticipantsNext(String nextLink, AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
                for (ChatParticipant participant : nextPage.getItems()) {
                    // Take further action.
                    Log.i(TAG, "participant: " + participant.getDisplayName());
                }
                if (nextPage.getPageId() != null) {
                    listChatParticipantsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}
```


## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Upewnij się, że zastąpisz `<second_user_id>` do prawidłowego identyfikatora użytkownika, a teraz usuniesz drugiego użytkownika z wątku.

Zastąp komentarz `<REMOVE A USER>` następującym kodem:

```java
CommunicationIdentifierModel communicationIdentifierModel = new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(second_user_id));
threadClient.removeChatParticipant(threadId, communicationIdentifierModel, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, okhttp3.Response response) {
        // Take further action.
        Log.i(TAG, "remove a user successfully");
    }

    @Override
    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-typing-notification"></a>Wyślij powiadomienie o wpisaniu

Zastąp komentarz `<SEND A TYPING NOTIFICATION>` następującym kodem:

```java
threadClient.sendTypingNotification(threadId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a typing notification successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-read-receipt"></a>Wyślij potwierdzenie odczytania

Pamiętaj, aby zastąpić `<chat_message_id>` identyfikatorem prawidłowym komunikatem czatu. teraz wyślemy do niego potwierdzenie odczytania dla tej wiadomości.

Zastąp komentarz `<SEND A READ RECEIPT>` następującym kodem:

```java
SendReadReceiptRequest readReceipt = new SendReadReceiptRequest()
    .setChatMessageId(chatMessageId);
threadClient.sendChatReadReceipt(threadId, readReceipt, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a read receipt successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="list-read-receipts"></a>Lista potwierdzeń odczytu

Zastąp komentarz `<READ RECEIPTS>` następującym kodem:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
threadClient.listChatReadReceiptsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection,
                              Response response) {
            // pageCollection enables enumerating list of chat participants.
            pageCollection.getFirstPage(new Callback<Page<ChatMessageReadReceipt>>() {
                @Override
                public void onSuccess(Page<ChatMessageReadReceipt> firstPage, Response response) {
                    for (ChatMessageReadReceipt receipt : firstPage.getItems()) {
                        Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                    }
                    listChatReadReceiptsNext(firstPage.getNextPageId(), pageCollection);
                }

                @Override
                public void onFailure(Throwable throwable, Response response) {
                    Log.e(TAG, throwable.getMessage());
                }
            });
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            Log.e(TAG, throwable.getMessage());
        }
});
```

Umieść następującą metodę pomocnika w klasie:
```java
void listChatReadReceiptsNext(String nextLink, AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatMessageReadReceipt>>() {
            @Override
            public void onSuccess(Page<ChatMessageReadReceipt> nextPage, Response response) {
                for (ChatMessageReadReceipt receipt : nextPage.getItems()) {
                    Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                }
                if (nextPage.getPageId() != null) {
                    listChatReadReceiptsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}

```


## <a name="run-the-code"></a>Uruchamianie kodu

W Android Studio naciśnij przycisk Run (Uruchom), aby skompilować i uruchomić projekt. W konsoli programu można wyświetlić dane wyjściowe z kodu i wyjścia rejestratora z ChatClient.
