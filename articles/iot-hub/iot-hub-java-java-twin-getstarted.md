---
title: Wprowadzenie do usługi Azure IoT Hub Device bliźniaczych reprezentacji (Java) | Microsoft Docs
description: Jak dodać tagi przy użyciu usługi Azure IoT Hub Device bliźniaczych reprezentacji, a następnie użyć kwerendy IoT Hubowej. Zestaw SDK urządzeń Azure IoT dla języka Java służy do implementowania aplikacji urządzenia i zestawu SDK usługi Azure IoT dla języka Java w celu zaimplementowania aplikacji usługi, która dodaje Tagi i uruchamia kwerendę IoT Hubową.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 53a82ba545fa1709d1b79f3ff6b18f1817736bb7
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217860"
---
# <a name="get-started-with-device-twins-java"></a>Rozpoczynanie pracy z usługą Device bliźniaczych reprezentacji (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

W tym samouczku utworzysz dwie aplikacje konsolowe Java:

* **Dodawanie tagów — zapytanie**, aplikacja zaplecza języka Java, która dodaje Tagi i zapytania bliźniaczych reprezentacji urządzenia.
* **symulowane-urządzenie**, aplikacja urządzenia Java, która łączy się z Centrum IoT Hub i raportuje swój stan łączności przy użyciu raportowanej właściwości.

> [!NOTE]
> Artykuł [Azure IoT SDK](iot-hub-devguide-sdks.md) zawiera informacje na temat zestawów SDK usługi Azure IoT, których można użyć do tworzenia aplikacji zarówno dla urządzeń, jak i zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

* [Java SE Development Kit 8](/java/azure/jdk/). Upewnij się, że wybrano opcję **Java 8** w obszarze **Obsługa długoterminowa** , aby pobrać pliki do pobrania dla programu JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację Java, która dodaje metadane lokalizacji jako tag do sznurka urządzenia w IoT Hub skojarzony z **myDeviceId**. Aplikacja najpierw wysyła zapytanie do usługi IoT Hub dla urządzeń znajdujących się w Stanach Zjednoczonych, a następnie dla urządzeń, które zgłaszają połączenie sieci komórkowej.

1. Na komputerze deweloperskim Utwórz pusty folder o nazwie **IoT-Java-bliźniaczy-getstarted**.

2. W folderze **IoT-Java-sznury-getstarted** Utwórz projekt Maven o nazwie **Add-Tags-Query** przy użyciu następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. W wierszu polecenia przejdź do folderu **Dodawanie tagów-Query** .

4. Za pomocą edytora tekstów Otwórz plik **pom.xml** w folderze **Dodaj Tagi-Query** i Dodaj następujący zależność do węzła **zależności** . Ta zależność umożliwia korzystanie z pakietu **IoT-Service-Client** w aplikacji w celu komunikowania się z Centrum IoT:

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

5. Dodaj następujący węzeł **kompilacji** po węźle **zależności** . Ta konfiguracja nakazuje Maven do kompilowania aplikacji przy użyciu języka Java 1,8.

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

7. Za pomocą edytora tekstów Otwórz plik **Add-Tags-query\src\main\java\com\mycompany\app\App.Java** .

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień na `{youriothubconnectionstring}` Parametry połączenia usługi IoT Hub skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Zaktualizuj podpis metody **Main** , aby uwzględnić następującą `throws` klauzulę:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Zastąp kod w metodzie **Main** następującym kodem, aby utworzyć obiekty **DeviceTwin** i **DeviceTwinDevice** . Obiekt **DeviceTwin** obsługuje komunikację z Centrum IoT Hub. Obiekt **DeviceTwinDevice** reprezentuje splot urządzenia z jego właściwościami i tagami:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Dodaj następujący `try/catch` blok do metody **Main** :

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. W celu zaktualizowania tagów sznurka **urządzenia i urządzeń** **roślinnych** w ramach sznurka urządzenia Dodaj następujący kod do `try` bloku:

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

14. Aby zbadać bliźniaczych reprezentacji urządzenia w usłudze IoT Hub, Dodaj następujący kod do `try` bloku po kodzie dodanym w poprzednim kroku. Kod uruchamia dwa zapytania. Każde zapytanie zwraca maksymalnie 100 urządzeń.

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

15. Zapisz i zamknij plik **Add-Tags-query\src\main\java\com\mycompany\app\App.Java**

16. Kompiluj aplikację **Dodaj Tagi-Query** i popraw wszelkie błędy. W wierszu polecenia przejdź do folderu **Dodawanie tagów-Query** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsolową Java, która ustawia raportowaną wartość właściwości, która jest wysyłana do IoT Hub.

1. W folderze **IoT-Java-sznury-getstarted** Utwórz projekt Maven o nazwie **symulowane-Device** przy użyciu następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do folderu **symulowane urządzenia** .

3. Za pomocą edytora tekstów Otwórz plik **pom.xml** w folderze **symulowane urządzenia** i Dodaj następujące zależności do węzła **zależności** . Ta zależność umożliwia korzystanie z pakietu **IoT-Device-Client** w aplikacji w celu komunikowania się z Centrum IoT.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-device-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Dodaj następującą zależność do węzła **zależności** . Ta zależność konfiguruje NOP dla rejestrowania Apache [SLF4J](https://www.slf4j.org/) , który jest używany przez zestaw SDK klienta urządzenia do implementowania rejestrowania. Ta konfiguracja jest opcjonalna, ale jeśli zostanie pominięta, podczas uruchamiania aplikacji może pojawić się ostrzeżenie w konsoli programu. Aby uzyskać więcej informacji na temat rejestrowania w zestawie SDK klienta urządzenia, zobacz artykuł [Rejestrowanie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) w *przykładach dla pliku Readme zestawu SDK urządzeń Azure IoT dla języka Java* .

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Dodaj następujący węzeł **kompilacji** po węźle **zależności** . Ta konfiguracja nakazuje Maven do kompilowania aplikacji przy użyciu języka Java 1,8:

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

7. Za pomocą edytora tekstów Otwórz plik **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień na `{yourdeviceconnectionstring}` Parametry połączenia urządzenia, które zostały skopiowane podczas [rejestrowania nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**.

10. Dodaj następującą metodę do klasy **App** , aby wydrukować informacje o aktualizacjach bliźniaczych:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Zastąp kod w metodzie **Main** następującym kodem, aby:

    * Utwórz klienta urządzenia, aby komunikować się z IoT Hub.

    * Utwórz obiekt **urządzenia** do przechowywania właściwości sznurka urządzenia.

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

12. Dodaj następujący kod do metody **Main** , aby utworzyć właściwość " **connectivitytype** " i wysłać ją do IoT Hub:

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

13. Dodaj następujący kod na końcu metody **Main** . Oczekiwanie na **wpisanie** klawisza Enter pozwala IoT Hub na raportowanie stanu operacji przędzy za pomocą urządzenia.

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

15. Zapisz i zamknij plik **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

16. Kompiluj aplikację **symulowaną przez urządzenie** i popraw wszelkie błędy. W wierszu polecenia przejdź do folderu **symulowane urządzenia** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchamiania aplikacji konsolowych.

1. W wierszu polecenia w folderze **dodawania tagów-Query** Uruchom następujące polecenie, aby uruchomić aplikację **Dodaj Tagi-Query** Service:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Zrzut ekranu pokazujący dane wyjściowe polecenia, aby uruchomić aplikację Dodaj Tagi zapytanie usługi.](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Można zobaczyć znaczniki **zakładek** i **regionów** dodane do sznurka urządzenia. Pierwsze zapytanie zwraca urządzenie, ale drugi nie.

2. W wierszu polecenia w folderze **symulowane urządzenia** Uruchom następujące polecenie, aby dodać do sznurka urządzenia wartość raportowaną przez właściwość **connectivitytype** :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Klient urządzenia dodaje właściwość zgłoszonego typu łączności](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. W wierszu polecenia w folderze **dodawania tagów-Query** Uruchom następujące polecenie, aby uruchomić aplikację usługi **Add-Tags-Query** Service po raz drugi:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja usługi Java IoT Hub do aktualizowania wartości tagów i uruchamiania zapytań dotyczących urządzeń](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Teraz, gdy urządzenie przesłało Właściwość **connectivitytype** do IoT Hub, drugie zapytanie zwróci urządzenie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Metadane urządzenia zostały dodane jako Tagi z aplikacji zaplecza i zapisały aplikację urządzenia do raportowania informacji o łączności urządzenia w ramach sznurka urządzenia. Dowiesz się również, jak badać informacje o bliźniaczym urządzeniu przy użyciu języka zapytań IoT Hub, takich jak SQL.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wyślij dane telemetryczne z urządzeń za pomocą samouczka Wprowadzenie [do IoT Hub](quickstart-send-telemetry-java.md) .

* Interakcyjne sterowanie urządzeniami (na przykład Włączanie wentylatorów z poziomu aplikacji sterowanej przez użytkownika) przy użyciu samouczka [Korzystanie z metod bezpośrednich](quickstart-control-device-java.md) .