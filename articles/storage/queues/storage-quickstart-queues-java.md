---
title: 'Szybki start: Azure Queue Storage klienta w wersji 12 — Java'
description: Dowiedz się, jak za pomocą Azure Queue Storage klienta w wersji 12 dla języka Java utworzyć kolejkę i dodać do niego komunikaty. Następnie dowiedz się, jak odczytywać i usuwać komunikaty z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: twooley
ms.author: twooley
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: f4e33cac6ba00be56b0f63cf5a10b2dce32e1be7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534454"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Szybki start: Azure Queue Storage klienta w wersji 12 dla języka Java

Wprowadzenie do biblioteki Azure Queue Storage klienta w wersji 12 dla języka Java. Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów do późniejszego pobierania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki Azure Queue Storage klienta w wersji 12 dla języka Java, aby:

- Tworzenie kolejki
- Dodawanie komunikatów do kolejki
- Wgląd w komunikaty w kolejce
- Aktualizowanie komunikatu w kolejce
- Odbieranie i usuwanie komunikatów z kolejki
- Usuwanie kolejki

Dodatkowe zasoby:

- [Dokumentacja referencyjna interfejsu API](/java/api/overview/azure/storage-queue-readme)
- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Pakiet (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Samples](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw Java Development Kit (JDK) w](/java/azure/jdk/) wersji 8 lub nowszej
- [Apache Maven](https://maven.apache.org/download.cgi)
- Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
- Konto usługi Azure Storage [— tworzenie konta magazynu](../common/storage-account-create.md)

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji ojęliśmy proces przygotowywania projektu do pracy z biblioteką klienta Azure Queue Storage w wersji 12 dla języka Java.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację Java o nazwie `queues-quickstart-v12` .

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj programu Maven, aby utworzyć nową aplikację konsolową o nazwie `queues-quickstart-v12` . Wpisz następujące `mvn` polecenie, aby utworzyć projekt Java "hello world".

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

1. Dane wyjściowe z generowania projektu powinny wyglądać podobnie do tych:

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

### <a name="install-the-package"></a>Instalowanie pakietu

Otwórz plik `pom.xml` w edytorze tekstów. Dodaj następujący element zależności do grupy zależności.

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
1. Otwórz plik `App.java` w edytorze
1. Usuwanie `System.out.println("Hello, world");` instrukcji
1. Dodawanie `import` dyrektyw

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

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia listy prac do asynchronicznego przetwarzania. Queue Storage oferuje trzy typy zasobów:

- Konto magazynu
- Kolejka na koncie magazynu
- Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury usługi Queue Storage](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas języka Java, aby wchodzić w interakcje z tymi zasobami:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): `QueueClientBuilder` klasa konfiguruje obiekt i jego `QueueClient` wystąpienia.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): `QueueServiceClient` umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): Klasa umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz `QueueClient` manipulowanie nimi.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): `QueueMessageItem` klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje za pomocą Azure Queue Storage klienta dla języka Java:

- [Pobieranie parametrów połączenia](#get-the-connection-string)
- [Tworzenie kolejki](#create-a-queue)
- [Dodawanie komunikatów do kolejki](#add-messages-to-a-queue)
- [Wgląd w komunikaty w kolejce](#peek-at-messages-in-a-queue)
- [Aktualizowanie komunikatu w kolejce](#update-a-message-in-a-queue)
- [Odbieranie i usuwanie komunikatów z kolejki](#receive-and-delete-messages-from-a-queue)
- [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera ciąg połączenia dla konta magazynu. W ciągu połączenia jest przechowywana zmienna środowiskowa utworzona w sekcji [Konfigurowanie parametrów połączenia](#configure-your-storage-connection-string) magazynu.

Dodaj ten kod wewnątrz `main` metody :

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

Wybierz nazwę nowej kolejki. Poniższy kod dołącza wartość identyfikatora GUID do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazewnictwa kolejek, zobacz [Naming queues and metadata (Nazywanie kolejek i metadanych).](/rest/api/storageservices/naming-queues-and-metadata)

Utwórz wystąpienie [`QueueClient`](/java/api/com.azure.storage.queue.queueclient) klasy . Następnie wywołaj [`Create`](/java/api/com.azure.storage.queue.queueclient.create) metodę , aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na końcu `main` metody :

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

Poniższy fragment kodu dodaje komunikaty do kolejki przez wywołanie [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) metody . Zapisuje również [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) zwrócony z `sendMessage` wywołania. Wynik jest używany do aktualizowania komunikatu w dalszej części programu.

Dodaj ten kod na końcu `main` metody :

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w komunikaty w kolejce

Wgląd w komunikaty w kolejce przez wywołanie [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) metody . Ta metoda pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

Dodaj ten kod na końcu `main` metody :

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie komunikatu w kolejce

Zaktualizuj zawartość komunikatu, wywołując [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) metodę . Ta metoda może zmienić limit czasu widoczności i zawartość komunikatu. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością komunikatu przekaż identyfikator komunikatu i potwierdzenie pop przy użyciu pliku , który `SendMessageResult` został zapisany wcześniej w kodzie. Identyfikator komunikatu i potwierdzenie pop identyfikują komunikat do zaktualizowania.

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

Pobierz wcześniej dodane komunikaty, wywołując [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) metodę . Przykładowy kod usuwa również komunikaty z kolejki po ich otrzymaniu i przetworzeniu. W takim przypadku przetwarzanie tylko wyświetla komunikat w konsoli.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika przez wywołanie `System.console().readLine();` metody , zanim odbierze i usunie komunikaty. Przed [usunięciem Azure Portal](https://portal.azure.com) w aplikacji sprawdź, czy zasoby zostały utworzone prawidłowo. Wszystkie komunikaty, które nie zostały jawnie usunięte, staną się w końcu ponownie widoczne w kolejce na kolejną szansę ich przetwarzania.

Dodaj ten kod na końcu `main` metody :

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

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) metody .

Dodaj ten kod na końcu `main` metody :

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera je i usuwa przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu aplikacji, a następnie skompilować i uruchomić aplikację.

```console
mvn compile
```

Następnie skompilowanie pakietu.

```console
mvn package
```

Użyj następującego `mvn` polecenia, aby uruchomić aplikację.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

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

Gdy aplikacja zostanie wstrzymana przed odebraniem komunikatów, sprawdź konto magazynu w Azure Portal [.](https://portal.azure.com) Sprawdź, czy komunikaty znajdują się w kolejce.

Naciśnij klawisz `Enter` , aby odbierać i usuwać komunikaty. Po wyświetleniu monitu naciśnij ponownie `Enter` klawisz , aby usunąć kolejkę i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia kolejki i dodawania do niego komunikatów przy użyciu kodu Java. Następnie dowiedzieliśmy się, jak podeglądać, pobierać i usuwać komunikaty. Na koniec dowiedzieliśmy się, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, szybki start i inne dokumenty można znaleźć na stronie:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów rozwiązań w chmurze w języku Java](/azure/developer/java/)

- Aby uzyskać więcej Azure Queue Storage przykładowych aplikacji, zobacz Azure Queue Storage klienta w wersji [12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)dla języka Java — przykłady .
