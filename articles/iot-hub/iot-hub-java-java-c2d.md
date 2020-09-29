---
title: Komunikaty z chmury do urządzeń za pomocą usługi Azure IoT Hub (Java) | Microsoft Docs
description: Jak wysyłać komunikaty z chmury do urządzenia z usługi Azure IoT Hub za pomocą zestawów SDK usługi Azure IoT dla języka Java. Zmodyfikuj aplikację symulowanego urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikować aplikację zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
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
ms.openlocfilehash: f4e5880a39d6ad299fd6e7f29bd0e3aefadc3bcd
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446892"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Wysyłanie komunikatów z chmury do urządzeń za pomocą IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-java.md) pokazuje, jak utworzyć Centrum IoT, zainicjować w nim tożsamość urządzenia i kod aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek kompiluje na temat [wysyłania danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-java.md). Przedstawiono w nim sposób wykonywania następujących czynności:

* Z zaplecza rozwiązania Wyślij komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem IoT Hub.

* Odbieraj komunikaty z chmury do urządzenia na urządzeniu.

* Z zaplecza rozwiązania Poproś o potwierdzenie dostarczenia (*Opinie*) o komunikatach wysyłanych do urządzenia z IoT Hub.

Więcej informacji na temat [komunikatów z chmury do urządzeń można znaleźć w przewodniku dewelopera IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchamiasz dwie aplikacje konsolowe Java:

* **symulowane — urządzenie**, zmodyfikowana wersja aplikacji utworzona w wysyłanie danych [telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-java.md), który łączy się z Centrum IoT i odbiera komunikaty z chmury do urządzenia.

* **send-C2D-messages**, które wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pomocą IoT Hub, a następnie otrzymuje potwierdzenie dostawy.

> [!NOTE]
> IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym C, Java, Python i JavaScript) za pomocą zestawów SDK urządzeń usługi Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z tym kodem samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Wymagania wstępne

* Kompletna wersja robocza [wysyłania danych telemetrycznych z urządzenia do](quickstart-send-telemetry-java.md) przewodnika Szybki Start usługi IoT Hub lub [Konfiguracja routingu komunikatów za pomocą IoT Hub](tutorial-routing.md) .

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Upewnij się, że wybrano opcję **Java 8** w obszarze **Obsługa długoterminowa** , aby pobrać pliki do pobrania dla programu JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieraj komunikaty w aplikacji symulowanego urządzenia

W tej sekcji zmodyfikujesz aplikację symulowanego urządzenia utworzoną w artykule wysyłanie danych [telemetrycznych z urządzenia do usługi IoT Hub](quickstart-send-telemetry-java.md) w celu odbierania komunikatów z chmury do urządzenia z Centrum IoT Hub.

1. Za pomocą edytora tekstów otwórz plik simulated-device\src\main\java\com\mycompany\app\App.java.

2. Dodaj następującą klasę **MessageCallback** jako klasę zagnieżdżoną wewnątrz klasy **App** . Metoda **Execute** jest wywoływana, gdy urządzenie odbiera komunikat z IoT Hub. W tym przykładzie urządzenie zawsze powiadamia Centrum IoT o ukończeniu komunikatu:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Zmodyfikuj metodę **Main** , aby utworzyć wystąpienie **AppMessageCallback** i wywołać metodę **setMessageCallback** przed otwarciem klienta w następujący sposób:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

`execute`Metoda w `AppMessageCallback` klasie zwraca `IotHubMessageResult.COMPLETE` . Spowoduje to powiadomienie IoT Hub, że komunikat został pomyślnie przetworzony i że można bezpiecznie usunąć ten komunikat z kolejki urządzeń. Urządzenie powinno zwrócić tę wartość, gdy przetwarzanie zostało pomyślnie zakończone, niezależnie od używanego protokołu.

W przypadku AMQP i HTTPS, ale nie MQTT, urządzenie może również:

* Porzuć komunikat, co spowoduje IoT Hub zachowywanie komunikatu w kolejce urządzeń w celu użycia w przyszłości.
* Odrzuć komunikat, który trwale usuwa komunikat z kolejki urządzeń.

W przypadku, gdy coś się nie powiedzie, że urządzenie zakończy działanie, porzucanie lub odrzucanie komunikatu, IoT Hub po upływie ustalonego limitu czasu będzie kolejkować komunikat o dostarczeniu ponownie. Z tego powodu logika przetwarzania komunikatów w aplikacji urządzenia musi być *idempotentne*, dzięki czemu ten sam komunikat wielokrotnie daje ten sam wynik.

Aby uzyskać szczegółowe informacje na temat sposobu, w jaki IoT Hub przetwarza komunikatów z chmury do urządzenia, w tym szczegółowe informacje o cyklu życia komunikatów z chmury do urządzenia, zobacz [wysyłanie komunikatów z chmury do urządzeń z Centrum IoT](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Jeśli używasz protokołu HTTPS zamiast MQTT lub AMQP jako transportu, wystąpienie **DeviceClient** sprawdza komunikaty IoT Hub rzadko (co najmniej 25 minut). Aby uzyskać więcej informacji o różnicach między obsługą MQTT, AMQP i HTTPS, zobacz [wskazówki dotyczące komunikacji z chmury do urządzeń](iot-hub-devguide-c2d-guidance.md) i [Wybierz protokół komunikacyjny](iot-hub-devguide-protocols.md).

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-java.md). Aby można było wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnień do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatu z chmury do urządzenia

W tej sekcji utworzysz aplikację konsolową Java, która wysyła komunikaty z chmury do urządzenia do aplikacji symulowanego urządzenia. Potrzebujesz identyfikatora urządzenia dodanego w polu [Wyślij telemetrię z urządzenia do](quickstart-send-telemetry-java.md) przewodnika Szybki Start dotyczącego usługi IoT Hub. Potrzebne są również parametry połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

1. Utwórz projekt Maven o nazwie **send-C2D-messages** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że to polecenie jest pojedynczym, długim poleceniem:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do nowego folderu Send-C2D-messages.

3. Za pomocą edytora tekstów Otwórz plik pom.xml w folderze Send-C2D-messages i Dodaj następujący zależność do węzła **zależności** . Dodanie zależności umożliwia korzystanie z pakietu **iothub-Java-Service-Client** w aplikacji w celu komunikowania się z usługą IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-service-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Zapisz i zamknij plik pom.xml.

5. Za pomocą edytora tekstów Otwórz plik send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Dodaj następujące zmienne na poziomie klasy do klasy **App** , zastępując wartości **{yourhubconnectionstring}** i **{yourdeviceid}** wartościami zanotowanymi wcześniej:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Zastąp metodę **Main** poniższym kodem. Ten kod nawiązuje połączenie z usługą IoT Hub, wysyła komunikat do urządzenia, a następnie czeka na potwierdzenie, że urządzenie odebrało i przetworzy komunikat:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Dla uproszczenia w tym samouczku nie są implementowane żadne zasady ponawiania. W polu kod produkcyjny należy zaimplementować zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).

9. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze symulowane urządzenia Uruchom następujące polecenie, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT Hub i nasłuchiwanie komunikatów z chmury do urządzeń wysyłanych z centrum:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-java-java-c2d/receivec2d.png)

2. W wierszu polecenia w folderze Send-C2D-messages Uruchom następujące polecenie, aby wysłać komunikat z chmury do urządzenia i poczekać na potwierdzenie opinii:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Uruchom polecenie, aby wysłać komunikat z chmury do urządzenia](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zapoznać się z przykładami kompletnych kompleksowych rozwiązań, które używają IoT Hub, zobacz [Akceleratory rozwiązań usługi Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide.md).
