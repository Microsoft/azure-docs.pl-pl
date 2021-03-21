---
title: Jak używać Queue Storage z poziomu języka Java — Azure Storage
description: Dowiedz się, jak tworzyć i usuwać kolejki za pomocą Queue Storage. Dowiedz się, jak wstawiać, zaglądać, pobierać i usuwać wiadomości za pomocą biblioteki klienckiej usługi Azure Storage dla języka Java.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 997a37ac4252813abf1b35877cd34e192ec3e2ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585721"
---
# <a name="how-to-use-queue-storage-from-java"></a>Jak używać Queue Storage języka Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Omówienie

W tym przewodniku pokazano, jak kodować typowe scenariusze za pomocą usługi Azure Queue Storage. Przykłady zostały napisane w języku Java i wymagają użycia [zestawu SDK usługi Azure Storage dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage). Scenariusze obejmują **Wstawianie**, **wgląd**, **pobieranie** i **usuwanie** komunikatów w kolejce. Podano również kod służący do **tworzenia** i **usuwania** kolejek. Aby uzyskać więcej informacji o kolejkach, zobacz sekcję [następne kroki](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java

# <a name="java-v12"></a>[V12 Java](#tab/java)

Najpierw sprawdź, czy Twój system programistyczny spełnia wymagania wstępne wymienione w sekcji [V12 klienta usługi Azure queue storage dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue).

Aby utworzyć aplikację Java o nazwie `queues-how-to-v12` :

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj Maven, aby utworzyć nową aplikację konsolową o nazwie `queues-how-to-v12` . Wpisz następujące `mvn` polecenie, aby utworzyć projekt Java "Hello World".

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

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
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created `queues-howto-v12` directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz `pom.xml` plik w edytorze tekstu. Dodaj następujący element zależności do grupy zależności.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Najpierw sprawdź, czy system programistyczny spełnia wymagania wstępne wymienione w [zestawie SDK usługi Azure Storage dla języka Java V8](https://github.com/azure/azure-storage-java). Postępuj zgodnie z instrukcjami dotyczącymi pobierania i instalowania bibliotek usługi Azure Storage dla języka Java. Następnie możesz utworzyć aplikację Java przy użyciu przykładów w tym artykule.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Skonfiguruj aplikację pod kątem dostępu do Queue Storage

Dodaj następujące instrukcje importu na początku pliku języka Java, w którym chcesz używać interfejsów API usługi Azure Storage w celu uzyskiwania dostępu do kolejek:

# <a name="java-v12"></a>[V12 Java](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage

Klient usługi Azure Storage korzysta z parametrów połączenia magazynu w celu uzyskania dostępu do usług zarządzania danymi. Pobierz nazwę i podstawowy klucz dostępu dla konta magazynu wymienionego w [Azure Portal](https://portal.azure.com). Użyj ich jako `AccountName` wartości i `AccountKey` w parametrach połączenia. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:

# <a name="java-v12"></a>[V12 Java](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Ten ciąg można zapisać w pliku konfiguracji usługi o nazwie `ServiceConfiguration.cscfg` . W przypadku aplikacji uruchomionej w ramach roli Microsoft Azure należy uzyskać dostęp do parametrów połączenia przez wywołanie metody `RoleEnvironment.getConfigurationSettings` . Oto przykład pobierania parametrów połączenia z `Setting` elementu o nazwie `StorageConnectionString` :

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

W poniższych przykładach założono, że masz `String` obiekt zawierający parametry połączenia magazynu.

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki

# <a name="java-v12"></a>[V12 Java](#tab/java)

`QueueClient`Obiekt zawiera operacje umożliwiające współdziałanie z kolejką. Poniższy kod tworzy `QueueClient` obiekt. Użyj `QueueClient` obiektu, aby utworzyć kolejkę, której chcesz użyć.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

`CloudQueueClient`Obiekt umożliwia uzyskanie obiektów referencyjnych dla kolejek. Poniższy kod tworzy `CloudQueueClient` obiekt, który dostarcza odwołanie do kolejki, której chcesz użyć. Możesz utworzyć kolejkę, jeśli nie istnieje.

> [!NOTE]
> Istnieją inne sposoby tworzenia `CloudStorageAccount` obiektów. Aby uzyskać więcej informacji, zobacz `CloudStorageAccount` w [dokumentacji zestawu SDK klienta usługi Azure Storage](https://azure.github.io/azure-sdk-for-java/storage.html).

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>Instrukcje: Dodawanie komunikatu do kolejki

# <a name="java-v12"></a>[V12 Java](#tab/java)

Aby wstawić komunikat do istniejącej kolejki, wywołaj `sendMessage` metodę. Komunikat może być ciągiem (w formacie UTF-8) lub tablicą bajtów. Oto kod, który wysyła komunikat ciągu do kolejki.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nowy `CloudQueueMessage` . Następnie Wywołaj `addMessage` metodę. Element `CloudQueueMessage` można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej. Oto kod, który tworzy kolejkę (jeśli nie istnieje) i wstawia komunikat `Hello, World` .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: wgląd do następnego komunikatu

Możesz wgląd do komunikatu z przodu kolejki bez usuwania go z kolejki przez wywołanie `peekMessage` .

# <a name="java-v12"></a>[V12 Java](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: zmienianie zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie służbowe, można użyć tej funkcji do zaktualizowania stanu. Poniższy kod aktualizuje komunikat kolejki z nową zawartością i ustawia limit czasu widoczności, aby powiększyć 30 sekund. Przedłużenie limitu czasu widoczności powoduje, że klient będzie kontynuował pracę na wiadomości. Możesz również zachować liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, należy go usunąć. Ten scenariusz chroni przed komunikatem, który wyzwala błąd aplikacji przy każdym przetwarzaniu.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Następujący przykładowy kod przeszukuje kolejkę komunikatów, lokalizuje pierwszą zawartość wiadomości, która pasuje do ciągu wyszukiwania, modyfikuje zawartość komunikatu i kończy pracę.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Poniższy przykładowy kod przeszukuje kolejkę komunikatów, lokalizuje pierwszą zawartość wiadomości, która pasuje `Hello, world` , modyfikuje zawartość wiadomości i kończy pracę.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

Poniższy przykład kodu aktualizuje tylko pierwszą widoczną wiadomość w kolejce.

# <a name="java-v12"></a>[V12 Java](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>Instrukcje: pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce.

# <a name="java-v12"></a>[V12 Java](#tab/java)

`getProperties`Metoda zwraca kilka wartości, w tym liczbę komunikatów aktualnie znajdujących się w kolejce. Licznik jest tylko przybliżony, ponieważ komunikaty mogą być dodawane lub usuwane po żądaniu. `getApproximateMessageCount`Metoda zwraca ostatnią wartość pobraną przez wywołanie do `getProperties` , bez wywoływania queue storage.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

`downloadAttributes`Metoda pobiera kilka wartości, w tym liczbę komunikatów aktualnie znajdujących się w kolejce. Licznik jest tylko przybliżony, ponieważ komunikaty mogą być dodawane lub usuwane po żądaniu. `getApproximateMessageCount`Metoda zwraca ostatnią wartość pobraną przez wywołanie do `downloadAttributes` , bez wywoływania queue storage.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: dequeueing Next Message

# <a name="java-v12"></a>[V12 Java](#tab/java)

Kod usuwa komunikat z kolejki w dwóch krokach. Po wywołaniu otrzymujesz `receiveMessage` następną wiadomość w kolejce. Komunikat zwrócony z programu `receiveMessage` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać metodę `deleteMessage` . Jeśli Twój kod nie może przetworzyć komunikatu, ten dwuetapowy proces zapewnia, że można uzyskać ten sam komunikat i spróbować ponownie. Kod wywołuje `deleteMessage` się bezpośrednio po przetworzeniu komunikatu.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Kod usuwa komunikat z kolejki w dwóch krokach. Po wywołaniu otrzymujesz `retrieveMessage` następną wiadomość w kolejce. Komunikat zwrócony z programu `retrieveMessage` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać metodę `deleteMessage` . Jeśli Twój kod nie może przetworzyć komunikatu, ten dwuetapowy proces zapewnia, że można uzyskać ten sam komunikat i spróbować ponownie. Kod wywołuje `deleteMessage` się bezpośrednio po przetworzeniu komunikatu.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje związane z dekolejką komunikatów

Istnieją dwa sposoby dostosowywania pobierania komunikatów z kolejki. Najpierw pobierz partię komunikatów (do 32). Następnie ustaw dłuższy lub krótszy limit czasu niewidoczności, co pozwoli na zwiększenie lub skrócenie czasu w celu pełnego przetworzenia poszczególnych komunikatów.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Poniższy przykład kodu używa metody, `receiveMessages` Aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `for` pętli. Ustawia również limit czasu niewidoczności na pięć minut (300 sekund) dla każdego komunikatu. Limit czasu jest uruchamiany dla wszystkich komunikatów w tym samym czasie. Po upływie pięciu minut od wywołania do `receiveMessages` , wszystkie komunikaty, które nie zostały usunięte, staną się znów widoczne.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Poniższy przykład kodu używa metody, `retrieveMessages` Aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `for` pętli. Ustawia również limit czasu niewidoczności na pięć minut (300 sekund) dla każdego komunikatu. Limit czasu jest uruchamiany dla wszystkich komunikatów w tym samym czasie. Po upływie pięciu minut od wywołania do `retrieveMessages` , wszystkie komunikaty, które nie zostały usunięte, staną się znów widoczne.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>Instrukcje: Wyświetlanie listy kolejek

# <a name="java-v12"></a>[V12 Java](#tab/java)

Aby uzyskać listę bieżących kolejek, wywołaj `QueueServiceClient.listQueues()` metodę, która zwróci kolekcję `QueueItem` obiektów.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Aby uzyskać listę bieżących kolejek, wywołaj `CloudQueueClient.listQueues()` metodę, która zwróci kolekcję `CloudQueue` obiektów.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>Instrukcje: usuwanie kolejki

# <a name="java-v12"></a>[V12 Java](#tab/java)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj `delete` metodę dla `QueueClient` obiektu.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj `deleteIfExists` metodę dla `CloudQueue` obiektu.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Queue Storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

- [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/Azure/Azure-SDK-for-Java)
- [Dokumentacja zestawu SDK klienta usługi Azure Storage](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Interfejs API REST usług Azure Storage](/rest/api/storageservices/)
- [Blog zespołu usługi Azure Storage](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
