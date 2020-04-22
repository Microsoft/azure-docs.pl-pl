---
title: Wprowadzenie do bliźniaczych urządzeń usługi Azure IoT Hub (Java) | Dokumenty firmy Microsoft
description: Jak dodać tagi za pomocą bliźniamcych urządzeń usługi Azure IoT Hub, a następnie użyć kwerendy Centrum IoT. Użyj zestawu SDK urządzenia Usługi Azure IoT dla języka Java, aby zaimplementować aplikację urządzenia urządzenia i zestaw SDK usługi Azure IoT dla języka Java w celu zaimplementowania aplikacji usługi, która dodaje tagi i uruchamia kwerendę Centrum IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt
ms.openlocfilehash: 3ea2f0eec12d756a898f1761f6b22fd034c1bc3e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732454"
---
# <a name="get-started-with-device-twins-java"></a>Wprowadzenie do bliźniąt urządzeń (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

W tym samouczku utworzysz dwie aplikacje konsoli Java:

* **add-tags-query**, aplikacja zaplecza Java, która dodaje tagi i zapytania bliźniacze urządzenia.
* **symulowane urządzenie**— aplikacja na urządzenie Java, która łączy się z centrum IoT hub i zgłasza jego stan łączności przy użyciu zgłoszonej właściwości.

> [!NOTE]
> Artykuł [Zestaw SDK usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera informacje o zestawach SDK usługi Azure IoT, których można używać do tworzenia aplikacji na urządzeniach i zapleczu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw java se development 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Upewnij się, że w obszarze **Java 8** w obszarze **Długoterminowa pomoc techniczna** umożliwia pobieranie dla JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację Java, która dodaje metadane lokalizacji jako znacznik do bliźniaczej reprezentacji urządzenia w centrum IoT Hub skojarzonej z **myDeviceId**. Aplikacja najpierw wysyła zapytanie do centrum IoT hub dla urządzeń znajdujących się w Stanach Zjednoczonych, a następnie dla urządzeń, które zgłaszają połączenie z siecią komórkową.

1. Na komputerze deweloperskim utwórz pusty folder o nazwie **iot-java-twin-getstarted**.

2. W folderze **iot-java-twin-getstarted** utwórz projekt Maven o nazwie **add-tags-query,** używając następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. W wierszu polecenia przejdź do folderu **zapytań o znaczniki dodane.**

4. Za pomocą edytora tekstu otwórz plik **pom.xml** w folderze **add-tags-query** i dodaj następującą zależność do węzła **zależności.** Ta zależność umożliwia korzystanie z pakietu **iot-service-client** w aplikacji do komunikowania się z centrum IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-service-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Dodaj następujący węzeł **kompilacji** po węźle **zależności.** Ta konfiguracja nakazuje Maven używać java 1.8 do tworzenia aplikacji.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Zapisz i zamknij plik **pom.xml**.

7. Za pomocą edytora tekstu otwórz plik **add-tags-query\src\main\java\com\mycompany\app\App.java.**

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień `{youriothubconnectionstring}` na skopiowany ciąg połączenia koncentratora IoT w pliku [Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Zaktualizuj podpis metody `throws` **głównej,** aby uwzględnić następującą klauzulę:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Zastąp kod w metodzie **głównej** następującym kodem, aby utworzyć obiekty **DeviceTwin** i **DeviceTwinDevice.** **DeviceTwin** obiekt obsługuje komunikacji z centrum IoT hub. **DeviceTwinDevice** obiekt reprezentuje bliźniaczej reprezentacji urządzenia z jego właściwości i tagów:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Dodaj następujący `try/catch` blok do **metody głównej:**

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Aby zaktualizować znaczniki bliźniaczych reprezentacji **urządzeń regionu** i `try` **instalacji** w bliźniaczej reprezentacji urządzenia, dodaj w bloku następujący kod:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Aby zbadać bliźniacze urządzenia w centrum IoT `try` hub, dodaj następujący kod do bloku po kod dodany w poprzednim kroku. Kod uruchamia dwa zapytania. Każda kwerenda zwraca maksymalnie 100 urządzeń.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Zapisz i zamknij plik **add-tags-query\src\main\java\com\mycompany\app\App.java**

16. Tworzenie aplikacji **add-tags-query** i poprawianie błędów. W wierszu polecenia przejdź do folderu **zapytań o znaczniki dodane** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli Java, która ustawia zgłoszoną wartość właściwości, która jest wysyłana do usługi IoT Hub.

1. W folderze **iot-java-twin-getstarted** utwórz projekt Maven o nazwie **symulowane urządzenie,** używając następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do folderu **symulowanego urządzenia.**

3. Za pomocą edytora tekstu otwórz plik **pom.xml** w folderze **symulowanego urządzenia** i dodaj następujące zależności do węzła **zależności.** Ta zależność umożliwia korzystanie z pakietu **iot-device-client** w aplikacji do komunikowania się z centrum IoT hub.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-device-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Dodaj następującą zależność do węzła **zależności.** Ta zależność konfiguruje NOP dla fasady rejestrowania Apache [SLF4J,](https://www.slf4j.org/) która jest używana przez sdk klienta urządzenia do implementacji rejestrowania. Ta konfiguracja jest opcjonalna, ale jeśli ją pominiesz, po uruchomieniu aplikacji może zostać wyświetlone ostrzeżenie. Aby uzyskać więcej informacji na temat rejestrowania w sdk klienta urządzenia, zobacz [Rejestrowanie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) w *przykładach dla pliku SDK urządzenia Usługi Azure IoT dla* pliku readme języka Java.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Dodaj następujący węzeł **kompilacji** po węźle **zależności.** Ta konfiguracja nakazuje Maven używać Java 1.8 do tworzenia aplikacji:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Zapisz i zamknij plik **pom.xml**.

7. Za pomocą edytora tekstu otwórz plik **symulowanego urządzenia\src\main\java\com\mycompany\app\App.java.**

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień `{yourdeviceconnectionstring}` na skopiowany ciąg połączenia urządzenia w [Rejestrze nowe urządzenie w centrum IoT](#register-a-new-device-in-the-iot-hub)hub .

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**.

10. Dodaj następującą metodę do klasy **app,** aby wydrukować informacje o bliźniaczych aktualizacjach:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Zastąp kod w metodzie **głównej** następującym kodem, aby:

    * Utwórz klienta urządzenia do komunikowania się z Centrum IoT.

    * Utwórz **obiekt Device** do przechowywania właściwości bliźniaczej reprezentacji urządzenia.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. Dodaj następujący kod do **metody głównej,** aby utworzyć **właściwość zgłoszenia connectivityType** i wysłać ją do centrum IoT Hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. Dodaj następujący kod na końcu **metody głównej.** Oczekiwanie na **klawisz Enter** umożliwia centrum IoT Hub raportowanie stanu operacji bliźniaczej reprezentacji urządzenia.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Zmodyfikuj podpis metody **main**, aby uwzględnić poniższe wyjątki:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Zapisz i zamknij plik **symulowanego urządzenia\src\main\java\com\mycompany\app\App.java.**

16. Skompiluj aplikację **symulowanego urządzenia** i popraw wszelkie błędy. W wierszu polecenia przejdź do folderu **symulowanego urządzenia** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje konsoli.

1. W wierszu polecenia w folderze **add-tags-query** uruchom następujące polecenie, aby uruchomić aplikację usługi **add-tags-query:**

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja usługi Java IoT Hub do aktualizowania wartości tagów i uruchamiania zapytań o urządzenia](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Możesz zobaczyć etykiety **roślin** i **regionu** dodane do bliźniaczej reprezentacji urządzenia. Pierwsza kwerenda zwraca urządzenie, ale drugie nie.

2. W wierszu polecenia w folderze **symulowanego urządzenia** uruchom następujące polecenie, aby dodać właściwość **connectivityType** reported do bliźniaczej reprezentacji urządzenia:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Klient urządzenia dodaje właściwość **connectivityType** reported](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. W wierszu polecenia w folderze **add-tags-query** uruchom następujące polecenie, aby uruchomić aplikację usługi **add-tags-query** po raz drugi:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja usługi Java IoT Hub do aktualizowania wartości tagów i uruchamiania zapytań o urządzenia](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Teraz, gdy urządzenie wysłało **connectivityType** właściwość do Usługi IoT Hub, drugie zapytanie zwraca urządzenie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodano metadane urządzenia jako znaczniki z aplikacji zaplecza i napisałeś aplikację urządzenia do raportowania informacji o łączności urządzenia w bliźniaczej reprezentacji urządzenia. Dowiedzialiście się również, jak badać informacje o bliźniaczej reprezentacji urządzenia przy użyciu języka zapytań usługi IoT Hub podobnej do języka SQL.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzeń za pomocą samouczka [Wprowadzenie do usługi IoT Hub.](quickstart-send-telemetry-java.md)

* Sterowanie urządzeniami interaktywnie (na przykład włączanie wentylatora z aplikacji kontrolowanej przez użytkownika) za pomocą samouczka [Użyj metod bezpośrednich.](quickstart-control-device-java.md)
