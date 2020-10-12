---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub przy użyciu języka Java | Microsoft Docs
description: Jak przekazywać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla języka Java. Przekazane pliki są przechowywane w kontenerze obiektów BLOB usługi Azure Storage.
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
- devx-track-java
ms.openlocfilehash: e6006444e933dd93467dde01affd29c5d0c4a146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90019550"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym samouczku przedstawiono kod w oknie [wysyłanie komunikatów z chmury do urządzeń za pomocą](iot-hub-java-java-c2d.md) samouczka IoT Hub, aby dowiesz się, jak używać [funkcji przekazywania plików IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania plików do [usługi Azure Blob Storage](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpieczne zapewnianie urządzenia za pomocą identyfikatora URI obiektu blob platformy Azure na potrzeby przekazywania pliku.

* Użyj powiadomień przekazywania plików IoT Hub, aby wyzwolić przetwarzanie pliku w zapleczu aplikacji.

[Wysyłanie danych telemetrycznych z urządzenia do](quickstart-send-telemetry-java.md) przewodnika Szybki Start dotyczącego usługi IoT Hub i [wysyłanie komunikatów z chmury do urządzeń za pomocą](iot-hub-java-java-c2d.md) samouczka IoT Hub przedstawia podstawowe funkcje przesyłania komunikatów z urządzenia do chmury i IoT Hub z chmury do urządzenia. Samouczek [Konfigurowanie routingu komunikatów z IoT Hub](tutorial-routing.md) zawiera opis sposobu niezawodnego przechowywania komunikatów przesyłanych z urządzenia do chmury w usłudze Azure Blob Storage. Jednak w niektórych scenariuszach nie można łatwo zmapować danych wysyłanych przez urządzenia do bezwzględnie niewielkich komunikatów z urządzenia do chmury, które IoT Hub akceptowane. Na przykład:

* Duże pliki zawierające obrazy
* Wideo
* Próbkowanie danych drgań z wysoką częstotliwością
* Niektóre formy wstępnie przetworzonych danych.

Te pliki są zwykle przetwarzane wsadowo w chmurze przy użyciu narzędzi, takich jak [Azure Data Factory](../data-factory/introduction.md) lub stos [Hadoop](../hdinsight/index.yml) . W przypadku konieczności przewożenia plików z urządzenia można nadal korzystać z zabezpieczeń i niezawodności IoT Hub.

Na końcu tego samouczka uruchomisz dwie aplikacje konsolowe Java:

* **symulowane — urządzenie**, zmodyfikowana wersja aplikacji utworzona w samouczku [wysyłanie komunikatów z chmury do urządzenia z IoT Hub]. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI SYGNATURy dostępu współdzielonego dostarczonego przez Centrum IoT.

* **plik Read-File-Upload-Notification**, który odbiera powiadomienia o przekazywaniu plików z Centrum IoT Hub.

> [!NOTE]
> IoT Hub obsługuje wiele platform i języków urządzeń (w tym C, .NET i JavaScript) za pomocą zestawów SDK urządzeń usługi Azure IoT. Zapoznaj się z [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot) , aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z usługą Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Upewnij się, że wybrano opcję **Java 8** w obszarze **Obsługa długoterminowa** , aby pobrać pliki do pobrania dla programu JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji zmodyfikujesz aplikację urządzenia utworzoną w temacie [wysyłanie komunikatów z chmury do urządzeń za pomocą IoT Hub](iot-hub-java-java-c2d.md) w celu przekazania pliku do usługi IoT Hub.

1. Skopiuj plik obrazu do `simulated-device` folderu i zmień jego nazwę `myimage.png` .

2. Za pomocą edytora tekstów Otwórz `simulated-device\src\main\java\com\mycompany\app\App.java` plik.

3. Dodaj deklarację zmiennej do klasy **aplikacji** :

    ```java
    private static String fileName = "myimage.png";
    ```

4. Aby przetwarzać komunikaty zwrotne stanu przekazywania plików, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Aby przekazać obrazy do IoT Hub, Dodaj następującą metodę do klasy **App** , aby przekazać obrazy do IoT Hub:

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

6. Zmodyfikuj metodę **Main** , aby wywołać metodę **uploadFile** , jak pokazano w poniższym fragmencie kodu:

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

7. Użyj następującego polecenia, aby skompilować aplikację **symulowaną przez urządzenie** i sprawdzić pod kątem błędów:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do odbierania komunikatów powiadomień o przekazywaniu plików z Centrum IoT Hub utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-java.md). Aby odbierać komunikaty powiadomień o przekazywaniu plików, usługa musi mieć uprawnienia do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Odbieranie powiadomienia o przekazywaniu plików

W tej sekcji utworzysz aplikację konsolową Java, która odbiera komunikaty powiadomień o przekazaniu plików z IoT Hub.

1. Utwórz projekt Maven o nazwie **Read-File-Upload-Notification** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że to polecenie jest pojedynczym, długim poleceniem:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do nowego `read-file-upload-notification` folderu.

3. Za pomocą edytora tekstów Otwórz `pom.xml` plik w `read-file-upload-notification` folderze i Dodaj następujący zależność do węzła **zależności** . Dodanie zależności umożliwia korzystanie z pakietu **iothub-Java-Service-Client** w aplikacji w celu komunikowania się z usługą IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-service-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Zapisz i Zamknij `pom.xml` plik.

5. Za pomocą edytora tekstów Otwórz `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` plik.

6. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{Your IoT Hub connection string}` wartość symbolu zastępczego parametrami połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string):

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Aby wydrukować informacje o przekazywaniu pliku do konsoli programu, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

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

9. Aby rozpocząć wątek, który nasłuchuje powiadomień przekazywania plików, Dodaj następujący kod do metody **Main** :

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

10. Zapisz i Zamknij `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` plik.

11. Użyj następującego polecenia, aby skompilować aplikację **Read-File-Upload-Notification** i wyszukać błędy:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia w `read-file-upload-notification` folderze Uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

W wierszu polecenia w `simulated-device` folderze Uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **symulowanego urządzenia** :

![Wyjście z aplikacji symulowanej — urządzenie](media/iot-hub-java-java-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **Read-File-Upload-Notification** :

![Dane wyjściowe z aplikacji Read-File-Upload-Notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Możesz użyć portalu, aby wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

![Przekazany plik](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz w dalszym ciągu eksplorować funkcje i scenariusze dotyczące programu IoT Hub z następującymi artykułami:

* [Programistyczne tworzenie Centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Symulowanie urządzenia przy użyciu IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
