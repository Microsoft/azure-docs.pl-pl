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
ms.openlocfilehash: 3cbed124963fe6e56d6721669d0feedc6e34ffc6
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107063"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/) w wersji 8 lub nowszej.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Wdrożony zasób usług komunikacyjnych i parametry połączenia. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- [Token dostępu użytkownika](../../access-tokens.md). Upewnij się, że ustawiono zakres "Rozmowa" i zanotuj ciąg tokenu, a także ciąg identyfikatora użytkownika.


## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-java-application"></a>Tworzenie nowej aplikacji Java

Otwórz terminal lub okno poleceń i przejdź do katalogu, w którym chcesz utworzyć aplikację Java. Uruchom poniższe polecenie, aby wygenerować projekt Java z szablonu Maven-Archetype-szybkiego startu.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Zauważ, że cel "Generuj" utworzył katalog o takiej samej nazwie jak artifactId. W tym katalogu `src/main/java directory` zawiera kod źródłowy projektu, `src/test/java` katalog zawiera źródło testu, a plik pom.xml jest modelem obiektu projektu projektu lub [pliku pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Zaktualizuj plik pliku pom aplikacji, aby używał języka Java 8 lub nowszego:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Dodaj odwołania do pakietu dla zestawu SDK usługi Chat

W pliku pliku pom odwołuje się do `azure-communication-chat` pakietu za pomocą interfejsów API rozmowy:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.4</version> 
</dependency>
```

W celu uwierzytelnienia klient musi odwołać się do `azure-communication-common` pakietu:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version> 
</dependency>
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu Azure Communications Services Chat SDK dla języka Java.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatAsyncClient | Ta klasa jest wymagana w przypadku funkcji rozmowy asynchronicznej. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClient i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i otrzymywania potwierdzeń. |
| ChatThreadAsyncClient | Ta klasa jest wymagana w przypadku asynchronicznych funkcji wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatAsyncClient i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i otrzymywania potwierdzeń. |

## <a name="create-a-chat-client"></a>Tworzenie klienta czatu
Aby utworzyć klienta programu chat, należy użyć punktu końcowego usługi komunikacyjnej i tokenu dostępu, który został wygenerowany w ramach kroków wymagań wstępnych. Tokeny dostępu użytkowników umożliwiają tworzenie aplikacji klienckich, które bezpośrednio uwierzytelniają się w usłudze Azure Communications Services. Po wygenerowaniu tych tokenów na serwerze Przekaż je z powrotem do urządzenia klienckiego. Aby przekazać token do klienta czatu, należy użyć klasy CommunicationTokenCredential ze wspólnego zestawu SDK. 

Dowiedz się więcej o [architekturze rozmowy](../../../concepts/chat/concepts.md)

Podczas dodawania instrukcji import należy pamiętać, aby tylko dodać importy z przestrzeni nazw com. Azure. Communications. chat i com. Azure. Communications. chat. Models, a nie z obszaru nazw com. Azure. Communications. chat. Implementation. W pliku App. Java, który został wygenerowany za pośrednictwem Maven, można użyć następującego kodu, aby rozpocząć od:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `createChatThread` metody, aby utworzyć wątek rozmowy.
`createChatThreadOptions` służy do opisywania żądania wątku.

- Użyj, `topic` Aby przekazać temat do tego rozmowy. Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `UpdateThread` funkcji.
- Użyj, `participants` Aby wyświetlić listę uczestników wątku, które mają zostać dodane do wątku. `ChatParticipant` Pobiera użytkownika utworzonego w ramach [tokenu dostępu użytkownika](../../access-tokens.md) — Szybki Start.

Odpowiedź `chatThreadClient` jest używana do wykonywania operacji na utworzonym wątku czatu: Dodawanie uczestników do wątku rozmowy, wysyłanie wiadomości, usuwanie wiadomości itd. Zawiera `chatThreadId` Właściwość, która jest unikatowym identyfikatorem wątku rozmowy. Właściwość jest dostępna dla metody publicznej. getChatThreadId ().

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");
    
ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setParticipants(participants);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Użyj `sendMessage` metody, aby wysłać wiadomość do właśnie utworzonego wątku, identyfikowanego przez chatThreadId.
`sendChatMessageOptions` służy do opisywania żądania komunikatu rozmowy.

- Użyj, `content` Aby podać zawartość wiadomości czatu.
- Służy `type` do określania typu zawartości wiadomości czatu, tekstu lub HTML.
- Użyj, `senderDisplayName` Aby określić nazwę wyświetlaną nadawcy.

Odpowiedź `sendChatMessageResult` zawiera `id` , który jest UNIKATOWYm identyfikatorem komunikatu.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

`getChatThreadClient`Metoda zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj uczestników, Wyślij wiadomość itd. `chatThreadId` jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie komunikatów rozmowy z wątku rozmowy

Komunikaty czatu można pobrać, sondowanie `listMessages` metody na kliencie wątku rozmowy w określonych odstępach czasu.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` zwraca najnowszą wersję komunikatu, w tym wszelkie edycje lub usunięcia, które wystąpiły w komunikacie przy użyciu. editMessage () i. deleteMessage (). W przypadku usuniętych komunikatów `chatMessage.getDeletedOn()` Funkcja zwraca wartość typu DateTime wskazującą, kiedy ten komunikat został usunięty. W przypadku edytowanych wiadomości `chatMessage.getEditedOn()` zwraca wartość typu DateTime wskazującą, kiedy wiadomość była edytowana. Pierwotny czas tworzenia wiadomości można uzyskać za pomocą polecenia `chatMessage.getCreatedOn()` i można go użyć do porządkowania komunikatów.

`listMessages` zwraca różne typy komunikatów, które mogą być identyfikowane przez `chatMessage.getType()` . Są to następujące typy:

- `text`: Zwykły komunikat rozmowy Wysłany przez uczestnika wątku.

- `html`: Komunikat rozmowy HTML Wysłany przez uczestnika wątku.

- `topicUpdated`: Komunikat systemowy wskazujący, że Zaktualizowano temat.

- `participantAdded`: Komunikat systemowy wskazujący, że co najmniej jeden uczestnik został dodany do wątku rozmowy.

- `participantRemoved`: Komunikat systemowy wskazujący, że uczestnik został usunięty z wątku rozmowy.

Aby uzyskać więcej informacji, zobacz [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku rozmowy można z niego dodawać i usuwać użytkowników. Przez dodanie użytkowników można udzielić im dostępu do wysyłania komunikatów do wątku rozmowy i dodawania/usuwania innych uczestników. Musisz zacząć od pobrania nowego tokenu dostępu i tożsamości dla tego użytkownika. Przed wywołaniem metody adduczestniks upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

Użyj `addParticipants` metody, aby dodać uczestników do wątku identyfikowanego przez ThreadID.

- Użyj, `listParticipants` Aby wyświetlić listę uczestników, którzy mają zostać dodani do wątku rozmowy.
- `communicationIdentifier`, wymagane, to CommunicationIdentifier, który został utworzony przez CommunicationIdentityClient w [tokenie dostępu użytkownika](../../access-tokens.md) — Szybki Start.
- `display_name`, opcjonalnie, jest nazwą wyświetlaną uczestnika wątku.
- `share_history_time`, opcjonalnie, to czas, po którym historia rozmowy jest udostępniana uczestnikowi. Aby udostępnić historię od momentu rozpoczęcia wątku rozmowy, należy ustawić tę właściwość na dowolną datę równą lub mniejszą niż godzina utworzenia wątku. Aby po dodaniu uczestnika nie była udostępniona żadna historia, ustaw ją na bieżącą datę. Aby udostępnić historię częściową, ustaw ją na wymaganą datę.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

AddChatParticipantsOptions addChatParticipantsOptions = new AddChatParticipantsOptions()
    .setParticipants(participants);
chatThreadClient.addParticipants(addChatParticipantsOptions);
```

## <a name="remove-participant-from-a-chat-thread"></a>Usuwanie uczestnika z wątku rozmowy

Podobnie jak w przypadku dodawania uczestnika do wątku, można usunąć uczestników z wątku rozmowy. W tym celu należy śledzić tożsamości dodanych uczestników.

Użyj `removeParticipant` , gdzie `identifier` to CommunicationIdentifier, który został utworzony.

```Java
chatThreadClient.removeParticipant(identity);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Przejdź do katalogu zawierającego plik *pom.xml* i skompiluj projekt przy użyciu następującego `mvn` polecenia.

```console
mvn compile
```

Następnie Skompiluj pakiet.

```console
mvn package
```

Uruchom następujące `mvn` polecenie, aby uruchomić aplikację.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```