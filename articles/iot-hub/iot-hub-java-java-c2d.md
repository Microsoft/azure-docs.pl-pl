---
title: Komunikaty między chmurami a urządzeniem za pomocą usługi Azure IoT Hub (Java) | Dokumenty firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia do urządzenia z centrum Usługi Azure IoT przy użyciu zestawów SDK usługi Azure IoT dla środowiska Java. Zmodyfikowana aplikacja urządzenia jest modyfikowana w celu odbierania komunikatów z chmury do urządzenia i modyfikowania aplikacji zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
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
ms.openlocfilehash: e16d0ed264f32746c11d89e88ea1e67f9383b773
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732513"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Wysyłanie wiadomości z chmury do urządzenia za pomocą usługi IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która pomaga umożliwić niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania. Wyślij [dane telemetryczne z urządzenia do centrum IoT Hub](quickstart-send-telemetry-java.md) Szybki start pokazuje, jak utworzyć centrum IoT hub, aprowizować tożsamość urządzenia w nim i kod symulowanej aplikacji urządzenia, która wysyła komunikaty urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się na [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-java.md)hub . Pokazuje, jak wykonać następujące czynności:

* Z zaplecza rozwiązania wysyłaj komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem usługi IoT Hub.

* Odbieranie komunikatów z chmury do urządzenia na urządzeniu.

* W rozwiązaniu zaplecza, żądanie potwierdzenia dostarczania *(opinie)* dla wiadomości wysyłanych do urządzenia z Usługi IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzenia można znaleźć [w przewodniku dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchomisz dwie aplikacje konsoli Java:

* **symulowane urządzenie**, zmodyfikowana wersja aplikacji utworzonej w [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-java.md)hub , który łączy się z centrum IoT hub i odbiera komunikaty z chmury do urządzenia.

* **send-c2d-messages**, który wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pośrednictwem usługi IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym języka C, Java, Python i Javascript) za pośrednictwem zestawów SDK urządzeń IoT platformy Azure. Aby uzyskać instrukcje krok po kroku dotyczące podłączania urządzenia do kodu tego samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów usługi Azure IoT.](https://azure.microsoft.com/develop/iot)

## <a name="prerequisites"></a>Wymagania wstępne

* Pełna wersja robocza [danych telemetrycznych wysyłania z urządzenia do szybkiego startu centrum IoT](quickstart-send-telemetry-java.md) lub [routingu komunikatów Konfigurowanie za pomocą samouczka Centrum IoT.](tutorial-routing.md)

* [Zestaw java se development 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Upewnij się, że w obszarze **Java 8** w obszarze **Długoterminowa pomoc techniczna** umożliwia pobieranie dla JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia

W tej sekcji zmodyfikujesz aplikację symulowanego urządzenia utworzoną w [sekcji Wyślij dane telemetryczne z urządzenia do centrum IoT hub,](quickstart-send-telemetry-java.md) aby odbierać komunikaty z chmury do urządzenia z centrum IoT Hub.

1. Za pomocą edytora tekstów otwórz plik simulated-device\src\main\java\com\mycompany\app\App.java.

2. Dodaj następującą klasę **MessageCallback** jako klasę zagnieżdżoną wewnątrz klasy **aplikacji.** Metoda **execute** jest wywoływana, gdy urządzenie odbiera komunikat z usługi IoT Hub. W tym przykładzie urządzenie zawsze powiadamia centrum IoT hub, że zakończył komunikat:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Zmodyfikuj **metodę główną,** aby utworzyć **wystąpienie AppMessageCallback** i wywołaj metodę **setMessageCallback,** zanim otworzy klienta w następujący sposób:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Jeśli używasz protokołu HTTPS zamiast MQTT lub AMQP jako transportu, **DeviceClient wystąpienie** sprawdza komunikaty z Usługi IoT Hub rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji na temat różnic między obsługą MQTT, AMQP i HTTPS oraz ograniczaniem przepustowości centrum IoT, zobacz [sekcję obsługi wiadomości w przewodniku dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

4. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych do wysyłania komunikatów z chmury do urządzenia za pośrednictwem centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-java.md). Aby wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie wiadomości z chmury do urządzenia

W tej sekcji utworzysz aplikację konsoli Java, która wysyła komunikaty z chmury do urządzenia do symulowanej aplikacji urządzenia. Potrzebny jest identyfikator urządzenia dodanego w danych [telemetrycznych wysyłania z urządzenia do szybkiego startu centrum IoT Hub.](quickstart-send-telemetry-java.md) Potrzebny jest również ciąg połączenia centrum IoT skopiowany wcześniej w [pliku Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

1. Utwórz projekt Maven o nazwie **send-c2d-messages** za pomocą następującego polecenia w wierszu polecenia. Należy zauważyć, że to polecenie jest pojedyncze, długie polecenie:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do nowego folderu wiadomości send-c2d.

3. Za pomocą edytora tekstu otwórz plik pom.xml w folderze send-c2d-messages i dodaj następującą zależność do węzła **zależności.** Dodawanie zależności umożliwia korzystanie z pakietu **iothub-java-service-client** w aplikacji do komunikowania się z usługą centrum IoT hub:

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

5. Za pomocą edytora tekstu otwórz plik send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Dodaj następujące zmienne na poziomie klasy do klasy **aplikacji,** zastępując **{yourhubconnectionstring}** i **{yourdeviceid}** wartościami, które zostały wcześniej wymienione:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Zastąp metodę **główną** następującym kodem. Ten kod łączy się z centrum IoT hub, wysyła wiadomość do urządzenia, a następnie czeka na potwierdzenie, że urządzenie odebrało i przetworzyło wiadomość:

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
    > Dla uproszczenia ten samouczek nie implementuje żadnych zasad ponawiania prób. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak wykładnicze wycofywanie), zgodnie z sugestią w artykule Obsługa [błędów przejściowych.](/azure/architecture/best-practices/transient-faults)

9. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze symulowanego urządzenia uruchom następujące polecenie, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT hub i nasłuchiwać komunikatów z chmury do urządzenia wysyłanych z koncentratora:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-java-java-c2d/receivec2d.png)

2. W wierszu polecenia w folderze send-c2d-messages uruchom następujące polecenie, aby wysłać wiadomość z chmury do urządzenia i poczekać na potwierdzenie opinii:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Uruchom polecenie, aby wysłać komunikat z chmury do urządzenia](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zobaczyć przykłady kompletnych kompleksowych rozwiązań korzystających z usługi IoT Hub, zobacz [Akceleratory rozwiązań Usługi Azure IoT.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)
