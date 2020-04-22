---
title: 'Szybki start: biblioteka magazynu kolejki platformy Azure w wersji 12 — Java'
description: Dowiedz się, jak utworzyć kolejkę i dodać wiadomości do kolejki za pomocą biblioteki Java w wersji 12 usługi Azure Queue. Następnie dowiesz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 013281db2821c99f6942edd1322f4978e100c144
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729853"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Szybki start: biblioteka klienta magazynu usługi Azure Queue w wersji 12 dla środowiska Java

Wprowadzenie do biblioteki klienta usługi Azure Queue storage w wersji 12 dla języka Java. Usługa Azure Queue storage to usługa do przechowywania dużej liczby wiadomości do późniejszego pobierania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta magazynu usługi Azure Queue w wersji 12 dla języka Java, aby:

* Tworzenie kolejki
* Dodawanie wiadomości do kolejki
* Wgląd do wiadomości w kolejce
* Aktualizowanie wiadomości w kolejce
* Odbieranie i usuwanie wiadomości z kolejki
* Usuwanie kolejki

[Dokumentacja](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | referencyjna interfejsu API[Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | (Maven) kod[źródłowy](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples) [(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | 

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) w wersji 8 lub wyższej
* [Apache Maven](https://maven.apache.org/download.cgi)
* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji znajdziesz informacje o przygotowaniu projektu do pracy z biblioteką klienta magazynu usługi Azure Queue w wersji 12 dla języka Java.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację Java o nazwie *queues-quickstart-v12*.

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj maven, aby utworzyć nową aplikację konsoli o nazwie *queues-Quickstart-v12*. Wpisz następujące polecenie **mvn,** aby utworzyć "Hello world!" projektu Java.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Dane wyjściowe z generowania projektu powinny wyglądać mniej więcej tak:

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

1. Przełącz się do nowo utworzonego katalogu *kolejki-szybki start-v12.*

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz plik *pom.xml* w edytorze tekstu. Dodaj następujący element zależności do grupy zależności.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Przejdź do *katalogu /src/main/java/com/queues/quickstart*
1. Otwieranie pliku *App.java* w edytorze
1. Usuwanie `System.out.println("Hello world!");` instrukcji
1. Dodawanie `import` dyrektyw

Oto kod:

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
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

## <a name="object-model"></a>Model obiektu

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat kolejki może mieć rozmiar do 64 KB. Kolejka może zawierać miliony wiadomości, do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości pracy do przetwarzania asynchronicznie. Magazyn kolejek oferuje trzy typy zasobów:

* Konto magazynu
* Kolejka na koncie magazynu
* Wiadomości w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejek](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas Java do interakcji z tymi zasobami:

* [QueueClientBuilder:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html) `QueueClientBuilder` Klasa konfiguruje i `QueueClient` wystąpienia obiektu.
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html): `QueueServiceClient` Umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
* [QueueClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) `QueueClient` Klasa umożliwia zarządzanie i manipulowanie poszczególnych kolejek i jej wiadomości.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): `QueueMessageItem` Klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje za pomocą biblioteki klienta magazynu usługi Azure Queue dla środowiska Java:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kolejki](#create-a-queue)
* [Dodawanie wiadomości do kolejki](#add-messages-to-a-queue)
* [Wgląd do wiadomości w kolejce](#peek-at-messages -in-a-queue)
* [Aktualizowanie wiadomości w kolejce](#update-a-message-in-a-queue)
* [Odbieranie i usuwanie wiadomości z kolejki](#receive-and-delete-messages-from-a-queue)
* [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu. Parametry połączenia są przechowywane zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `main` wewnątrz metody:

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Zdecyduj o nazwie nowej kolejki. Poniższy kod dołącza wartość guid do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się od litery lub liczby. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi również zawierać od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazywania kolejek, zobacz [Nazywanie kolejek i metadanych](/rest/api/storageservices/naming-queues-and-metadata).


Utwórz wystąpienie klasy [QueueClient.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) Następnie należy [wywołać](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) metodę tworzenia, aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na `main` końcu metody:

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

### <a name="add-messages-to-a-queue"></a>Dodawanie wiadomości do kolejki

Poniższy fragment kodu dodaje wiadomości do kolejki, wywołując [sendMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) metody. Zapisuje również [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) zwrócony `sendMessage` z wywołania. Wynik jest używany do aktualizacji wiadomości w dalszej części programu.

Dodaj ten kod na `main` końcu metody:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd do wiadomości w kolejce

Zajrzeć do wiadomości w kolejce, wywołując [metodę peekMessages.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) Metoda `peelkMessages` pobiera jeden lub więcej wiadomości z przodu kolejki, ale nie zmienia widoczności wiadomości.

Dodaj ten kod na `main` końcu metody:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie wiadomości w kolejce

Zaktualizuj zawartość wiadomości, wywołując [metodę updateMessage.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) Metoda `updateMessage` może zmienić limit czasu widoczności wiadomości i zawartość. Zawartość wiadomości musi być ciągiem zakodowanym w uiścić UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością wiadomości, przekazać w identyfikatorze wiadomości `SendMessageResult` i pop potwierdzenia przy użyciu tego, który został zapisany wcześniej w kodzie. Identyfikator wiadomości i pop receipt identyfikują, który komunikat należy zaktualizować.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Odbieranie i usuwanie wiadomości z kolejki

Pobierz wcześniej dodane wiadomości, wywołując metodę [receiveMessages.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) Przykładowy kod usuwa również wiadomości z kolejki po ich odebraniu i przetworzyniu. W takim przypadku przetwarzanie jest po prostu wyświetlanie komunikatu na konsoli.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika, wywołując `System.console().readLine();` przed odebraniem i usunięciem wiadomości. Sprawdź w [witrynie Azure portal,](https://portal.azure.com) czy zasoby zostały utworzone poprawnie, zanim zostaną usunięte. Wszystkie wiadomości, które nie zostały jawnie usunięte, po pewnym czasie staną się ponownie widoczne w kolejce, aby uzyskać kolejną szansę na ich przetworzenie.

Dodaj ten kod na `main` końcu metody:

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

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając kolejkę przy użyciu metody [usuwania.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--)

Dodaj ten kod na `main` końcu metody:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę wiadomości w kolejce, a następnie pobiera je i usuwa, zanim na koniec usunie kolejkę.

W oknie konsoli przejdź do katalogu aplikacji, a następnie skompiluj i uruchom aplikację.

```console
mvn compile
```

Następnie skompiluj pakiet.

```console
mvn package
```

Uruchom następujące `mvn` polecenie, aby wykonać aplikację.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Queues storage v12 - Java quickstart sample

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

Gdy aplikacja wstrzymuje się przed odbierania wiadomości, sprawdź swoje konto magazynu w [witrynie Azure portal](https://portal.azure.com). Sprawdź, czy wiadomości znajdują się w kolejce.

Naciśnij klawisz **Enter,** aby odbierać i usuwać wiadomości. Po wyświetleniu monitu naciśnij ponownie klawisz **Enter,** aby usunąć kolejkę i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć kolejkę i dodać do niej wiadomości za pomocą kodu Java. Następnie nauczyłeś się podglądać, pobierać i usuwać wiadomości. Na koniec dowiedziałeś się, jak usunąć kolejkę wiadomości.

Aby uzyskać samouczki, przykłady, szybkie uruchamianie i inną dokumentację, odwiedź stronę:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów języka Java w chmurze](https://docs.microsoft.com/azure/developer/java/)

* Aby wyświetlić więcej przykładowych aplikacji magazynu usługi Azure Queue, przejdź do [przykładów biblioteki klienta Java magazynu usługi Azure Queue SDK w wersji 12.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)
