---
title: 'Szybki Start: Azure Queue Storage Client Library V12 — Java'
description: Dowiedz się, jak utworzyć kolejkę i dodać do niej komunikaty przy użyciu V12 biblioteki klienta platformy Queue Storage Azure dla języka Java. Następnie Dowiedz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: twooley
ms.author: twooley
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 79b10e8281fe8189b490fe97dfe896179c8e64fc
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275876"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Szybki Start: usługa Azure Queue Storage Client Library V12 for Java

Rozpocznij pracę z biblioteką klienta usługi Azure Queue Storage V12 for Java. Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów do późniejszego pobrania i przetworzenia. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta Queue Storage platformy Azure dla języka Java, aby:

- Tworzenie kolejki
- Dodawanie komunikatów do kolejki
- Wgląd w wiadomości w kolejce
- Aktualizowanie komunikatu w kolejce
- Odbieranie i usuwanie komunikatów z kolejki
- Usuwanie kolejki

Dodatkowe zasoby:

- [Dokumentacja referencyjna interfejsu API](/java/api/overview/azure/storage-queue-readme)
- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Pakiet (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Samples](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/) w wersji 8 lub nowszej
- [Apache Maven](https://maven.apache.org/download.cgi)
- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Konto magazynu platformy Azure — [Tworzenie konta magazynu](../common/storage-account-create.md)

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z usługą Azure Queue Storage Client Library V12 for Java.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację Java o nazwie `queues-quickstart-v12` .

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj Maven, aby utworzyć nową aplikację konsolową o nazwie `queues-quickstart-v12` . Wpisz następujące `mvn` polecenie, aby utworzyć projekt Java "Hello World".

    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. Dane wyjściowe generowania projektu powinny wyglądać następująco:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Przejdź do nowo utworzonego `queues-quickstart-v12` katalogu.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz `pom.xml` plik w edytorze tekstu. Dodaj następujący element zależności do grupy zależności.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Przejdź do `/src/main/java/com/queues/quickstart` katalogu
1. Otwórz `App.java` plik w edytorze
1. Usuń `System.out.println("Hello, world");` instrukcję
1. Dodaj `import` dyrektywy

Oto kod:

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego. Queue Storage oferuje trzy typy zasobów:

- Konto magazynu
- Kolejka na koncie magazynu
- Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejki](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas języka Java do korzystania z tych zasobów:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): `QueueClientBuilder` Klasa konfiguruje i tworzy wystąpienie `QueueClient` obiektu.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): `QueueServiceClient` Umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): `QueueClient` Klasa umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz manipulowanie nimi.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): `QueueMessageItem` Klasa reprezentuje poszczególne obiekty zwrócone podczas wywoływania [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) kolejki.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności w bibliotece klienta Queue Storage platformy Azure dla języka Java:

- [Pobieranie parametrów połączenia](#get-the-connection-string)
- [Tworzenie kolejki](#create-a-queue)
- [Dodawanie komunikatów do kolejki](#add-messages-to-a-queue)
- [Wgląd w wiadomości w kolejce](#peek-at-messages-in-a-queue)
- [Aktualizowanie komunikatu w kolejce](#update-a-message-in-a-queue)
- [Odbieranie i usuwanie komunikatów z kolejki](#receive-and-delete-messages-from-a-queue)
- [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu. Parametry połączenia są przechowywane w zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj ten kod wewnątrz `main` metody:

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Określ nazwę nowej kolejki. Poniższy kod dołącza wartość identyfikatora GUID do nazwy kolejki, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki, a także muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również długość od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazewnictwa kolejek, zobacz [nazywanie kolejek i metadanych](/rest/api/storageservices/naming-queues-and-metadata).

Utwórz wystąpienie [`QueueClient`](/java/api/com.azure.storage.queue.queueclient) klasy. Następnie Wywołaj [`Create`](/java/api/com.azure.storage.queue.queueclient.create) metodę, aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na końcu `main` metody:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Dodawanie komunikatów do kolejki

Poniższy fragment kodu dodaje komunikaty do kolejki przez wywołanie [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) metody. Zapisuje również [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) zwrot z `sendMessage` wywołania. Ten wynik służy do aktualizowania komunikatu w dalszej części tego programu.

Dodaj ten kod na końcu `main` metody:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w wiadomości w kolejce

Wgląd w komunikaty w kolejce przez wywołanie [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) metody. Ta metoda pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

Dodaj ten kod na końcu `main` metody:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie komunikatu w kolejce

Zaktualizuj zawartość komunikatu, wywołując [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) metodę. Ta metoda może zmienić limit czasu i treść wiadomości. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8, który ma rozmiar do 64 KB. Wraz z nową zawartością wiadomości, należy przekazać identyfikator wiadomości i potwierdzenie pop przy użyciu `SendMessageResult` , który został zapisany wcześniej w kodzie. Identyfikator wiadomości i potwierdzenie wskazują, które wiadomości należy zaktualizować.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Odbieranie i usuwanie komunikatów z kolejki

Pobierz wcześniej dodane wiadomości, wywołując [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) metodę. Przykładowy kod usuwa również komunikaty z kolejki po ich odebraniu i przetworzeniu. W takim przypadku przetwarzanie właśnie wyświetla komunikat w konsoli programu.

Aplikacja wstrzymuje się do wprowadzania danych przez użytkownika, wywołując, `System.console().readLine();` zanim odbierze i usunie komunikaty. Przed usunięciem [Azure Portal](https://portal.azure.com) Sprawdź, czy zasoby zostały utworzone prawidłowo. Wszystkie komunikaty, które nie zostały jawnie usunięte, zostaną ostatecznie wyświetlone w kolejce w celu przetworzenia ich przez inną szansę.

Dodaj ten kod na końcu `main` metody:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) metody.

Dodaj ten kod na końcu `main` metody:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera i usuwa je przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu aplikacji, a następnie Skompiluj i uruchom aplikację.

```console
mvn compile
```

Następnie Skompiluj pakiet.

```console
mvn package
```

Użyj następującego `mvn` polecenia, aby uruchomić aplikację.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Gdy aplikacja jest wstrzymywana przed odebraniem wiadomości, Sprawdź konto magazynu w [Azure Portal](https://portal.azure.com). Sprawdź, czy w kolejce znajdują się komunikaty.

Naciśnij klawisz, `Enter` Aby odebrać i usunąć komunikaty. Po wyświetleniu monitu ponownie naciśnij klawisz, `Enter` Aby usunąć kolejkę i zakończyć demonstrację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia kolejki i dodawania do niej komunikatów przy użyciu kodu Java. Następnie nauczysz się wglądu, pobierania i usuwania komunikatów. Na koniec wiesz już, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, szybki start i inne dokumenty można znaleźć w temacie:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów rozwiązań w chmurze w języku Java](/azure/developer/java/)

- Aby uzyskać więcej przykładowych aplikacji platformy Azure Queue Storage, zobacz temat [Biblioteka kliencka usługi azure queue storage V12 for Java — przykłady](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
