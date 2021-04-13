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
ms.openlocfilehash: e5b5433be4a95a9df9d3b3527473c3004d24acac
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327590"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Zestaw Java Development Kit (JDK) w](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) wersji 8 lub nowszej.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Wdrożony zasób Communication Services parametrów połączenia. [Utwórz zasób Communication Services zasobów.](../../create-communication-resource.md)
- Token [dostępu użytkownika.](../../access-tokens.md) Pamiętaj, aby ustawić zakres na "chat" i zanotować ciąg tokenu, a także ciąg userId.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-java-application"></a>Tworzenie nowej aplikacji Java

Otwórz terminal lub okno polecenia i przejdź do katalogu, w którym chcesz utworzyć aplikację Java. Uruchom poniższe polecenie, aby wygenerować projekt Java na podstawie szablonu maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Zauważysz, że cel "wygeneruj" utworzył katalog o takiej samej nazwie jak artifactId. W tym katalogu plik zawiera kod źródłowy projektu, katalog zawiera źródło testowe, a plik pom.xml to model obiektów projektu `src/main/java directory` `src/test/java` [(POM) projektu](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Zaktualizuj plik POM aplikacji, aby używać języka Java 8 lub wyższego:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Dodawanie odwołań do pakietu dla zestawu SDK czatu

W pliku POM odwołuj się `azure-communication-chat` do pakietu za pomocą interfejsów API czatu:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

W przypadku uwierzytelniania klient musi odwoływać się do `azure-communication-common` pakietu:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu AZURE COMMUNICATION SERVICES Chat SDK dla języka Java.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ta klasa jest potrzebna w przypadku funkcji czatów. Tworzysz je przy użyciu informacji o subskrypcji i używasz ich do tworzenia, get i usuwania wątków. |
| ChatAsyncClient | Ta klasa jest potrzebna w przypadku funkcji asynchronicznego czatu. Tworzysz je przy użyciu informacji o subskrypcji i używasz ich do tworzenia, get i usuwania wątków. |
| ChatThreadClient | Ta klasa jest potrzebna dla funkcji wątku czatu. Uzyskujesz wystąpienie za pośrednictwem klasy ChatClient i używasz go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/uzyskiwania użytkowników, wysyłania powiadomień dotyczących wpisywania i odczytywania paragonów. |
| ChatThreadAsyncClient | Ta klasa jest potrzebna dla funkcji asynchronicznego wątku czatu. Uzyskujesz wystąpienie za pośrednictwem klasy ChatAsyncClient i używasz go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/uzyskiwania użytkowników, wysyłania powiadomień dotyczących wpisywania i odczytywania paragonów. |

## <a name="create-a-chat-client"></a>Tworzenie klienta czatu
Aby utworzyć klienta czatu, użyjesz punktu końcowego usługi Communications Service i tokenu dostępu, który został wygenerowany w ramach kroków wymagań wstępnych. Tokeny dostępu użytkownika umożliwiają tworzenie aplikacji klienckich, które bezpośrednio uwierzytelniają się Azure Communication Services. Po wygenerowaniu tych tokenów na serwerze przekaż je z powrotem do urządzenia klienckiego. Musisz użyć klasy CommunicationTokenCredential z wspólnego zestawu SDK, aby przekazać token do klienta czatu.

Dowiedz się więcej o [architekturze czatu](../../../concepts/chat/concepts.md)

Podczas dodawania instrukcji import należy dodawać tylko importy z przestrzeni nazw com.azure.communication.chat i com.azure.communication.chat.models, a nie z przestrzeni nazw com.azure.communication.chat.implementation. W pliku App.java wygenerowanym za pośrednictwem programu Maven możesz użyć następującego kodu, aby rozpocząć od:

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

## <a name="start-a-chat-thread"></a>Uruchamianie wątku czatu

Użyj metody `createChatThread` , aby utworzyć wątek czatu.
`createChatThreadOptions` Służy do opisywania żądania wątku.

- Użyj `topic` parametru konstruktora, aby przekazać temat do tego czatu; Temat można zaktualizować po utworzeniu wątku czatu przy użyciu `UpdateThread` funkcji .
- Użyj `participants` , aby wyświetlić listę uczestników wątków, które mają zostać dodane do wątku. `ChatParticipant` przyjmuje użytkownika utworzonego w [przewodniku Szybki start dotyczącym tokenu](../../access-tokens.md) dostępu użytkownika.

`CreateChatThreadResult` to odpowiedź zwrócona podczas tworzenia wątku czatu.
Zawiera on metodę , która zwraca obiekt , który może służyć do uzyskania klienta wątku, z którego można pobrać metodę do wykonywania operacji na utworzonym wątku: dodawanie uczestników, wysyłanie wiadomości `getChatThread()` `ChatThread` `ChatThreadClient` itp. Obiekt `ChatThread` zawiera również metodę , która pobiera unikatowy identyfikator `getId()` wątku.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Lista wątków czatu

Użyj metody `listChatThreads` , aby pobrać listę istniejących wątków czatu.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Uzyskiwanie klienta wątku czatu

Metoda `getChatThreadClient` zwraca klienta wątku dla wątku, który już istnieje. Może służyć do wykonywania operacji na utworzonym wątku: dodawania uczestników, wysyłania wiadomości itp. `chatThreadId` to unikatowy identyfikator istniejącego wątku czatu.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Wysyłanie wiadomości do wątku czatu

Użyj metody , aby wysłać komunikat do właśnie utworzonego wątku `sendMessage` identyfikowanego przez chatThreadId.
`sendChatMessageOptions` Służy do opisywania żądania wiadomości na czacie.

- Użyj `content` , aby udostępnić zawartość wiadomości czatu.
- Użyj `type` , aby określić typ zawartości wiadomości czatu, TEKST lub HTML.
- Użyj `senderDisplayName` , aby określić nazwę wyświetlaną nadawcy.

Odpowiedź zawiera `sendChatMessageResult` element , który jest `id` unikatowym identyfikatorem komunikatu.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Odbieranie wiadomości czatu z wątku czatu

Komunikaty czatu można pobrać, sondując metodę na kliencie wątku `listMessages` czatu w określonych odstępach czasu.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` Metoda zwraca najnowszą wersję komunikatu, w tym wszelkie zmiany lub usunięcia, które miały miejsce w komunikacie przy użyciu funkcji .editMessage() i .deleteMessage(). W przypadku usuniętych `chatMessage.getDeletedOn()` komunikatów zwraca wartość daty/godziny wskazującą, kiedy komunikat został usunięty. W przypadku edytowanych komunikatów `chatMessage.getEditedOn()` zwraca datę/godzinę wskazującą, kiedy komunikat został edytowany. Oryginalny czas tworzenia komunikatów można uzyskać za pomocą funkcji i można go użyć `chatMessage.getCreatedOn()` do zamawiania komunikatów.

Więcej informacji na temat typów komunikatów można uzyskać tutaj: [Typy komunikatów](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Wysyłanie potwierdzenia odczytu

Użyj metody , aby opublikować zdarzenie potwierdzenia odczytu w wątku czatu `sendReadReceipt` w imieniu użytkownika.
`chatMessageId` to unikatowy identyfikator odczytanych wiadomości czatu.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Lista uczestników czatu

Użyj `listParticipants` , aby pobrać stronicowane kolekcje zawierające uczestników wątku czatu identyfikowanego przez chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Po utworzeniu wątku czatu można dodawać i usuwać użytkowników. Dodając użytkowników, dajesz im dostęp do wysyłania wiadomości do wątku czatu i dodawania/usuwania innych uczestników. Musisz zacząć od uzyskania nowego tokenu dostępu i tożsamości dla tego użytkownika. Przed wywołaniem metody addParticipants upewnij się, że uzyskasz nowy token dostępu i tożsamość dla tego użytkownika. Użytkownik będzie potrzebować tego tokenu dostępu do zainicjowania klienta czatu.

Użyj metody `addParticipants` , aby dodać uczestników do wątku.

- `communicationIdentifier`, jest identyfikatorem CommunicationIdentifier utworzonym przez communicationIdentityClient w [przewodniku Szybki start dotyczącym tokenu](../../access-tokens.md) dostępu użytkownika.
- `displayName`, opcjonalnie, jest nazwą wyświetlaną uczestnika wątku.
- `shareHistoryTime`, opcjonalnie, to czas, od którego historia czatu jest udostępniana uczestnikowi. Aby udostępnić historię od momentu utworzenia wątku czatu, ustaw tę właściwość na dowolną datę równą lub mniejszą niż czas tworzenia wątku. Aby nie udostępniać żadnej historii wcześniej niż, gdy uczestnik został dodany, ustaw ją na bieżącą datę. Aby udostępnić częściową historię, ustaw ją na wymaganą datę.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Przejdź do katalogu zawierającego *pom.xml* i skompiluj projekt przy użyciu następującego `mvn` polecenia.

```console
mvn compile
```

Następnie skompilowanie pakietu.

```console
mvn package
```

Uruchom następujące `mvn` polecenie, aby wykonać aplikację.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
