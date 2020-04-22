---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub za pomocą oprogramowania Java | Dokumenty firmy Microsoft
description: Jak przekazać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzenia Usługi Azure IoT dla języka Java. Przekazane pliki są przechowywane w kontenerze obiektów blob magazynu platformy Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f0753827fe5f7f2b866726683d4cb1f205da4599
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732467"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Przesyłanie plików z urządzenia do chmury za pomocą usługi IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ten samouczek opiera się na kodzie w [wysyłaniu wiadomości z chmury do urządzenia za pomocą centrum IoT Hub,](iot-hub-java-java-c2d.md) aby pokazać, jak korzystać z [możliwości przekazywania plików usługi IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania pliku do [magazynu obiektów blob platformy Azure](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpiecznie zapewnij urządzeniu identyfikator URI obiektu blob platformy Azure do przekazywania pliku.

* Powiadomienia o przekazywaniu plików usługi IoT Hub za pomocą wyzwalania przetwarzania pliku w zapleczu aplikacji.

Wysyłanie [danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-java.md) Szybki start i Wysyłanie komunikatów z chmury do urządzenia za pomocą samouczka Centrum [IoT hub](iot-hub-java-java-c2d.md) pokazują podstawowe funkcje obsługi wiadomości między urządzeniami i chmury do urządzenia usługi IoT Hub. Przewodnik [Konfigurowanie routingu wiadomości za pomocą narzędzia IoT Hub](tutorial-routing.md) opisuje sposób niezawodnego przechowywania wiadomości z urządzenia do chmury w magazynie obiektów blob platformy Azure. Jednak w niektórych scenariuszach nie można łatwo mapować danych wysyłanych przez urządzenia do komunikatów stosunkowo małych urządzeń do chmury akceptowanych przez usługę IoT Hub. Przykład:

* Duże pliki zawierające obrazy
* Filmy wideo
* Dane dotyczące drgań, z dużą częstotliwością
* Jakaś forma wstępnie przetworzonych danych.

Te pliki są zazwyczaj wsadowe przetwarzane w chmurze przy użyciu narzędzi, takich jak [usługa Azure Data Factory](../data-factory/introduction.md) lub [stosu Hadoop.](../hdinsight/index.yml) Gdy potrzebujesz plików wyżynnych z urządzenia, nadal możesz korzystać z zabezpieczeń i niezawodności usługi IoT Hub.

Na końcu tego samouczka można uruchomić dwie aplikacje konsoli Java:

* **symulowane urządzenie**, zmodyfikowana wersja aplikacji utworzona w samouczku [Wysyłanie komunikatów z chmury do urządzenia za pomocą usługi IoT Hub]. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego dostarczonego przez centrum IoT.

* **read-file-upload-notification**, które odbiera powiadomienia o przekazywaniu plików z centrum IoT Hub.

> [!NOTE]
> Usługa IoT Hub obsługuje wiele platform i języków urządzeń (w tym C, .NET i Javascript) za pośrednictwem zestawów SDK urządzeń IoT platformy Azure. Zapoznaj się z [Centrum deweloperów usługi Azure IoT,](https://azure.microsoft.com/develop/iot) aby uzyskać instrukcje krok po kroku dotyczące podłączania urządzenia do usługi Azure IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw java se development 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Upewnij się, że w obszarze **Java 8** w obszarze **Długoterminowa pomoc techniczna** umożliwia pobieranie dla JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji zmodyfikujesz aplikację urządzenia utworzoną w [aplikacji Wyślij wiadomości z chmury do urządzenia za pomocą usługi IoT Hub,](iot-hub-java-java-c2d.md) aby przekazać plik do centrum IoT Hub.

1. Skopiuj `simulated-device` plik obrazu do `myimage.png`folderu i zmień jego nazwę .

2. Za pomocą edytora `simulated-device\src\main\java\com\mycompany\app\App.java` tekstu otwórz plik.

3. Dodaj deklarację zmiennej do klasy **App:**

    ```java
    private static String fileName = "myimage.png";
    ```

4. Aby przetworzyć komunikaty o stanie przekazywania plików, dodaj następującą klasę zagnieżdżoną do klasy **app:**

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Aby przekazać obrazy do Centrum IoT, dodaj następującą metodę do klasy **aplikacji,** aby przekazać obrazy do Centrum IoT Hub:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Zmodyfikuj **metodę główną,** aby wywołać metodę **uploadFile,** jak pokazano w poniższym urywek:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Użyj następującego polecenia, aby utworzyć aplikację **symulowanego urządzenia** i sprawdzić, czy nie ma błędów:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych do odbierania wiadomości powiadomień o przekazywaniu plików z centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-java.md). Aby odbierać komunikaty powiadomień o przekazywaniu plików, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Odbierz powiadomienie o przekazaniu pliku

W tej sekcji utworzysz aplikację konsoli Java, która odbiera komunikaty powiadomień o przekazywaniu plików z Usługi IoT Hub.

1. Utwórz projekt Maven o nazwie **odczyt-plik-upload-notification** za pomocą następującego polecenia w wierszu polecenia. Należy zauważyć, że to polecenie jest pojedyncze, długie polecenie:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź `read-file-upload-notification` do nowego folderu.

3. Za pomocą edytora `pom.xml` tekstu otwórz `read-file-upload-notification` plik w folderze i dodaj następującą zależność do węzła **zależności.** Dodawanie zależności umożliwia korzystanie z pakietu **iothub-java-service-client** w aplikacji do komunikowania się z usługą centrum IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-service-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Zapisz i `pom.xml` zamknij plik.

5. Za pomocą edytora `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` tekstu otwórz plik.

6. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp wartość symbolu `{Your IoT Hub connection string}` zastępczego parametrym połączenia centrum IoT skopiowanym wcześniej w polu Pobierz parametry połączenia centrum [IoT:](#get-the-iot-hub-connection-string)

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Aby wydrukować informacje o przekazywaniu pliku do konsoli, dodaj następującą klasę zagnieżdżoną do klasy **App:**

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Aby uruchomić wątek, który nasłuchuje powiadomień o przekazywaniu plików, dodaj następujący kod do **metody głównej:**

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Zapisz i `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` zamknij plik.

11. Użyj następującego polecenia, aby utworzyć aplikację **do odczytu-plik-przekazywanie powiadomień** i sprawdzić, czy nie ma błędów:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia `read-file-upload-notification` w folderze uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

W wierszu polecenia `simulated-device` w folderze uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **symulowanego urządzenia:**

![Dane wyjściowe z aplikacji symulowanego urządzenia](media/iot-hub-java-java-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **do odczytu-upload-notification:**

![Dane wyjściowe z aplikacji do odczytu-upload-upload-notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Za pomocą portalu można wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

![Przesłany plik](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak korzystać z możliwości przekazywania plików usługi IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz kontynuować eksplorowanie funkcji i scenariuszy centrum IoT za pomocą następujących artykułów:

* [Programowo tworzenie centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do C SDK](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Symulowanie urządzenia za pomocą przeglądarce IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
