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
ms.openlocfilehash: 7fe50a6236cf67f1048dddecbf46fea836ec05c5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125962"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zestaw Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) w wersji 8 lub nowszej.
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
    <version>1.0.0</version>
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
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Użyj `createChatThread` metody, aby utworzyć wątek rozmowy.
`createChatThreadOptions` służy do opisywania żądania wątku.

- Użyj `topic` parametru konstruktora, aby przekazać temat do tego rozmowy. Temat można zaktualizować po utworzeniu wątku rozmowy przy użyciu `UpdateThread` funkcji.
- Użyj, `participants` Aby wyświetlić listę uczestników wątku, które mają zostać dodane do wątku. `ChatParticipant` Pobiera użytkownika utworzonego w ramach [tokenu dostępu użytkownika](../../access-tokens.md) — Szybki Start.

`CreateChatThreadResult` czy odpowiedź została zwrócona przez utworzenie wątku rozmowy.
Zawiera metodę, `getChatThread()` która zwraca `ChatThread` obiekt, którego można użyć w celu uzyskania klienta wątku, z którego można uzyskać `ChatThreadClient` wykonywanie operacji w utworzonym wątku: Dodaj uczestników, Wyślij wiadomość itd. `ChatThread` Obiekt zawiera również `getId()` metodę, która pobiera unikatowy identyfikator wątku.

```Java
ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Wyświetl listę wątków czatu

Użyj `listChatThreads` metody, aby pobrać listę istniejących wątków rozmowy.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

`getChatThreadClient`Metoda zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji w utworzonym wątku: Dodaj uczestników, Wyślij wiadomość itd. `chatThreadId` jest unikatowym IDENTYFIKATORem istniejącego wątku rozmowy.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
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

## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie komunikatów rozmowy z wątku rozmowy

Komunikaty czatu można pobrać, sondowanie `listMessages` metody na kliencie wątku rozmowy w określonych odstępach czasu.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` zwraca najnowszą wersję komunikatu, w tym wszelkie edycje lub usunięcia, które wystąpiły w komunikacie przy użyciu. editMessage () i. deleteMessage (). W przypadku usuniętych komunikatów `chatMessage.getDeletedOn()` Funkcja zwraca wartość typu DateTime wskazującą, kiedy ten komunikat został usunięty. W przypadku edytowanych wiadomości `chatMessage.getEditedOn()` zwraca wartość typu DateTime wskazującą, kiedy wiadomość była edytowana. Pierwotny czas tworzenia wiadomości można uzyskać za pomocą polecenia `chatMessage.getCreatedOn()` i można go użyć do porządkowania komunikatów.

Przeczytaj więcej o typach komunikatów tutaj: [typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Wyślij potwierdzenie odczytania

Użyj `sendReadReceipt` metody do publikowania zdarzenia odbioru do wątku rozmowy w imieniu użytkownika.
`chatMessageId` jest unikatowym IDENTYFIKATORem wiadomości czatu, która została odczytana.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Wyświetl listę uczestników rozmowy

Służy `listParticipants` do pobierania kolekcji stronicowanej zawierającej uczestników wątku rozmowy identyfikowanego przez chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku rozmowy można z niego dodawać i usuwać użytkowników. Przez dodanie użytkowników można udzielić im dostępu do wysyłania komunikatów do wątku rozmowy i dodawania/usuwania innych uczestników. Musisz zacząć od pobrania nowego tokenu dostępu i tożsamości dla tego użytkownika. Przed wywołaniem metody adduczestniks upewnij się, że uzyskano nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu, aby można było zainicjować klienta rozmowy.

Użyj `addParticipants` metody, aby dodać uczestników do wątku.

- `communicationIdentifier`, wymagane, to CommunicationIdentifier, który został utworzony przez CommunicationIdentityClient w [tokenie dostępu użytkownika](../../access-tokens.md) — Szybki Start.
- `displayName`, opcjonalnie, jest nazwą wyświetlaną uczestnika wątku.
- `shareHistoryTime`, opcjonalnie, to czas, po którym historia rozmowy jest udostępniana uczestnikowi. Aby udostępnić historię od momentu rozpoczęcia wątku rozmowy, należy ustawić tę właściwość na dowolną datę równą lub mniejszą niż godzina utworzenia wątku. Aby po dodaniu uczestnika nie była udostępniona żadna historia, ustaw ją na bieżącą datę. Aby udostępnić historię częściową, ustaw ją na wymaganą datę.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
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
