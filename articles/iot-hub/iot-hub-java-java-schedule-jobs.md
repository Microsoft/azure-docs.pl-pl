---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (Java) | Dokumenty firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub, aby wywołać metodę bezpośrednią i ustawić żądaną właściwość na wielu urządzeniach. Użyj zestawu SDK urządzenia Usługi Azure IoT dla języka Java do zaimplementowania symulowanych aplikacji urządzeń i zestawu SDK usługi Azure IoT dla języka Java w celu zaimplementowania aplikacji usługi do uruchomienia zadania.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: 5e3f4f4aedb0bc3fb1f8ea11001b08daa57aafc1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732484"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Planowanie i emisja zadań (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub umożliwia planowanie i śledzenie zadań, które aktualizują miliony urządzeń. Zadania można wykorzystać do:

* Aktualizowanie żądanych właściwości
* Aktualizuj tagi
* Wywoływanie metod bezpośrednich

Zadanie zawija jedną z tych akcji i śledzi wykonanie względem zestawu urządzeń. Zapytanie bliźniaczej reprezentacji urządzenia definiuje zestaw urządzeń wykonywanych przez zadanie. Na przykład aplikacja zaplecza można użyć zadania do wywołania metody bezpośredniej na 10 000 urządzeń, które uruchamiają ponownie urządzenia. Należy określić zestaw urządzeń z zapytaniem bliźniaczej reprezentacji urządzenia i zaplanować zadanie do uruchomienia w przyszłości. Zadanie śledzi postęp, gdy każde z urządzeń odbiera i wykonuje metodę bezpośredniego ponownego uruchomienia.

Aby dowiedzieć się więcej o każdej z tych funkcji, zobacz:

* Bliźniaczy i właściwości urządzenia: [wprowadzenie do bliźniaczych reprezentacji urządzeń](iot-hub-java-java-twin-getstarted.md)

* Metody bezpośrednie: [Przewodnik dla deweloperów IoT Hub - metody bezpośrednie](iot-hub-devguide-direct-methods.md) i [samouczek: użyj metod bezpośrednich](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację urządzenia, która implementuje metodę bezpośrednią o nazwie **lockDoor**. Aplikacja urządzenia odbiera również żądane zmiany właściwości z aplikacji zaplecza.

* Utwórz aplikację zaplecza, która tworzy zadanie do wywołania **lockDoor** bezpośredniej metody na wielu urządzeniach. Inne zadanie wysyła żądane aktualizacje właściwości do wielu urządzeń.

Na końcu tego samouczka masz aplikację urządzenia konsoli Java i aplikację zaplecza konsoli Java:

**symulowane urządzenie,** które łączy się z centrum IoT hub, implementuje **lockDoor** direct metody i obsługuje żądane zmiany właściwości.

**schedule-zadania,** które używają zadań do wywołania **lockDoor** direct metody i zaktualizować bliźniaczej reprezentacji urządzenia żądane właściwości na wielu urządzeniach.

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

Można również użyć [rozszerzenia IoT dla narzędzia interfejsu wiersza polecenia platformy Azure,](https://github.com/Azure/azure-iot-cli-extension) aby dodać urządzenie do centrum IoT hub.

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsoli Java, która używa zadań do:

* Wywołanie **lockDoor** bezpośredniej metody na wielu urządzeniach.

* Wysyłanie żądanych właściwości do wielu urządzeń.

Aby utworzyć aplikację:

1. Na komputerze deweloperskim utwórz pusty folder o nazwie **iot-java-schedule-jobs**.

2. W folderze **iot-java-schedule-jobs** utwórz projekt Maven o nazwie **schedule-jobs,** używając następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. W wierszu polecenia przejdź do folderu **zadania harmonogramu.**

4. Za pomocą edytora tekstu otwórz plik **pom.xml** w folderze **zadania harmonogramu** i dodaj następującą zależność do węzła **zależności.** Ta zależność umożliwia korzystanie z pakietu **iot-service-client** w aplikacji do komunikowania się z centrum IoT hub:

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

7. Za pomocą edytora tekstu otwórz plik **schedule-jobs\src\main\java\com\mycompany\app\App.java.**

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień `{youriothubconnectionstring}` na parametry połączenia koncentratora IoT skopiowany wcześniej w aplikacji [Pobierz parametry połączenia centrum IoT:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Dodaj następującą metodę do **klasy aplikacji,** aby zaplanować zadanie, aby zaktualizować **właściwości żądane budynki** i **piętro** w bliźniaczej reprezentacji urządzenia:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Aby zaplanować zadanie wywoływania **lockDoor** metody, dodaj następującą metodę do **klasy App:**

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Aby monitorować zadanie, dodaj następującą metodę do klasy **App:**

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Aby zbadać szczegóły uruchomionych zadań, dodaj następującą metodę:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Zaktualizuj podpis metody `throws` **głównej,** aby uwzględnić następującą klauzulę:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Aby uruchamiać i monitorować dwa zadania sekwencyjnie, zastąp kod w metodzie **głównej** następującym kodem:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Zapisz i zamknij plik **harmonogram-jobs\src\main\java\com\mycompany\app\App.java**

17. Tworzenie aplikacji **schedule-jobs** i poprawianie błędów. W wierszu polecenia przejdź do folderu **zadania harmonogramu** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli Java, która obsługuje żądane właściwości wysyłane z Usługi IoT Hub i implementuje bezpośrednie wywołanie metody.

1. W folderze **iot-java-schedule-jobs** utwórz projekt Maven o nazwie **symulowane urządzenie** za pomocą następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. W wierszu polecenia przejdź do folderu **symulowanego urządzenia.**

3. Za pomocą edytora tekstu otwórz plik **pom.xml** w folderze **symulowanego urządzenia** i dodaj następujące zależności do węzła **zależności.** Ta zależność umożliwia korzystanie z pakietu **iot-device-client** w aplikacji do komunikowania się z centrum IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-device-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Dodaj następującą zależność do węzła **zależności.** Ta zależność konfiguruje NOP dla fasady rejestrowania Apache [SLF4J,](https://www.slf4j.org/) która jest używana przez sdk klienta urządzenia do implementacji rejestrowania. Ta konfiguracja jest opcjonalna, ale jeśli ją pominiesz, po uruchomieniu aplikacji może zostać wyświetlone ostrzeżenie. Aby uzyskać więcej informacji na temat rejestrowania w sdk klienta urządzenia, zobacz [Rejestrowanie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)w *przykładach dla pliku SDK urządzenia Usługi Azure IoT dla* pliku readme języka Java.

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

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień `{yourdeviceconnectionstring}` na skopiowany wcześniej ciąg połączenia urządzenia w sekcji Zarejestruj nowe urządzenie w sekcji [IoT hub:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**.

10. Aby wydrukować podwójne powiadomienia urządzenia do konsoli, dodaj następującą klasę zagnieżdżoną do klasy **app:**

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Aby wydrukować bezpośrednie powiadomienia o metodach do konsoli, dodaj następującą klasę zagnieżdżoną do klasy **App:**

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Aby obsłużyć bezpośrednie wywołania metody z Usługi IoT Hub, dodaj następującą klasę zagnieżdżoną do klasy **aplikacji:**

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. Zaktualizuj podpis metody `throws` **głównej,** aby uwzględnić następującą klauzulę:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Zastąp kod w metodzie **głównej** następującym kodem, aby:
    * Utwórz klienta urządzenia do komunikowania się z Centrum IoT.
    * Utwórz **obiekt Device** do przechowywania właściwości bliźniaczej reprezentacji urządzenia.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Aby uruchomić usługi klienta urządzenia, dodaj następujący kod do **metody głównej:**

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Aby poczekać, aż użytkownik naciśnie klawisz **Enter** przed zamknięciem, dodaj następujący kod na końcu **metody głównej:**

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Zapisz i zamknij plik **symulowanego urządzenia\src\main\java\com\mycompany\app\App.java.**

18. Skompiluj aplikację **symulowanego urządzenia** i popraw wszelkie błędy. W wierszu polecenia przejdź do folderu **symulowanego urządzenia** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje konsoli.

1. W wierszu polecenia w folderze **symulowanego urządzenia** uruchom następujące polecenie, aby uruchomić aplikację urządzenia nasłuchiwanie żądanych zmian właściwości i bezpośrednie wywołanie metody:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Klient urządzenia uruchamia](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. W wierszu polecenia `schedule-jobs` w folderze uruchom następujące polecenie, aby uruchomić aplikację usługi **schedule-jobs,** aby uruchomić dwa zadania. Pierwszy ustawia żądane wartości właściwości, drugi wywołuje metodę bezpośrednią:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Aplikacja usługi Java IoT Hub tworzy dwa zadania](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Aplikacja urządzenia obsługuje żądaną zmianę właściwości i bezpośrednie wywołanie metody:

   ![Klient urządzenia reaguje na zmiany](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania, aby zaplanować metodę bezpośrednią do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzeń za pomocą samouczka [Wprowadzenie do usługi IoT Hub.](quickstart-send-telemetry-java.md)

* Sterowanie urządzeniami interaktywnie (np. włączanie wentylatora z aplikacji kontrolowanej przez użytkownika) za pomocą samouczka [Użyj metod bezpośrednich.s](quickstart-control-device-java.md)
