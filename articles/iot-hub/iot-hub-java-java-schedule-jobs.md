---
title: Planowanie zadań przy użyciu usługi Azure IoT Hub (Java) | Microsoft Docs
description: Jak zaplanować zadanie usługi Azure IoT Hub, aby wywołać metodę bezpośrednią i ustawić odpowiednią właściwość na wielu urządzeniach. Zestaw SDK urządzeń Azure IoT dla języka Java służy do implementowania aplikacji symulowanych urządzeń i zestawu SDK usługi Azure IoT dla języka Java w celu zaimplementowania aplikacji usługi w celu uruchomienia zadania.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 3e98cfc2d8c7fb8d40c8565a1c620f123ce171ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217843"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Planowanie i emitowanie zadań (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub umożliwia planowanie i śledzenie zadań, które aktualizują miliony urządzeń. Użyj zadań do:

* Aktualizowanie żądanych właściwości
* Aktualizowanie tagów
* Wywoływanie metod bezpośrednich

Zadanie otacza jedną z tych akcji i śledzi wykonywanie na zestawie urządzeń. Zapytanie o sznurki urządzenia definiuje zbiór urządzeń, względem których wykonywane jest zadanie. Na przykład aplikacja zaplecza może użyć zadania do wywołania metody bezpośredniej na 10 000 urządzeniach, które ponownie uruchamiają urządzenia. Należy określić zestaw urządzeń z zapytaniem o sznurki urządzenia i zaplanować zadanie do uruchomienia w przyszłości. Zadanie śledzi postęp, ponieważ każde z urządzeń odbiera i wykonuje metodę bezpośredniego ponownego uruchomienia.

Aby dowiedzieć się więcej o każdej z tych funkcji, zobacz:

* Sznurki i właściwości urządzenia: Rozpoczynanie [pracy z usługą Device bliźniaczych reprezentacji](iot-hub-java-java-twin-getstarted.md)

* Metody bezpośrednie: [przewodnik dewelopera IoT Hub — bezpośrednie metody](iot-hub-devguide-direct-methods.md) i [Samouczek: Używanie metod bezpośrednich](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację urządzenia, która implementuje metodę bezpośrednią o nazwie **lockDoor**. Aplikacja urządzenia odbiera również wymagane zmiany właściwości z aplikacji zaplecza.

* Utwórz aplikację zaplecza, która tworzy zadanie wywołujące metodę **lockDoor** Direct na wielu urządzeniach. Inne zadanie wysyła do wielu urządzeń żądane aktualizacje właściwości.

Na końcu tego samouczka masz aplikację urządzenia konsolowego Java i aplikację zaplecza konsoli języka Java:

**symulowane — urządzenie** , które nawiązuje połączenie z usługą IoT Hub, implementuje metodę **lockDoor** Direct i obsługuje zmiany żądanych właściwości.

**Zaplanuj zadania** , które używają zadań do wywołania metody **lockDoor** Direct i aktualizacji odpowiednich właściwości na wielu urządzeniach.

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

Możesz również użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby dodać urządzenie do centrum IoT.

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsolową Java, która używa zadań do:

* Wywołaj metodę **lockDoor** Direct na wielu urządzeniach.

* Wyślij żądane właściwości do wielu urządzeń.

Aby utworzyć aplikację:

1. Na komputerze deweloperskim Utwórz pusty folder o nazwie **IoT-Java-Schedule-Jobs**.

2. W folderze **IoT-Java-Schedule-Jobs** Utwórz projekt Maven o nazwie **Schedule-Jobs** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. W wierszu polecenia przejdź do folderu **Schedule-Jobs** .

4. Za pomocą edytora tekstów Otwórz plik **pom.xml** w folderze **Schedule-Jobs** i Dodaj następujący zależność do węzła **zależności** . Ta zależność umożliwia korzystanie z pakietu **IoT-Service-Client** w aplikacji w celu komunikowania się z Centrum IoT:

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

7. Za pomocą edytora tekstów Otwórz plik **Schedule-jobs\src\main\java\com\mycompany\app\App.Java** .

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

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień na `{youriothubconnectionstring}` Parametry połączenia usługi IoT Hub skopiowane wcześniej w polu [Pobierz parametry połączenia Centrum IoT Hub](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Dodaj następującą metodę do klasy **App** , aby zaplanować zadanie aktualizowania pożądanych właściwości **budynku** i **podłogi** w postaci sznurka urządzenia:

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

11. Aby zaplanować zadanie wywoływania metody **lockDoor** , Dodaj następującą metodę do klasy **aplikacji** :

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

12. Aby monitorować zadanie, Dodaj następującą metodę do klasy **aplikacji** :

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

13. Aby wykonać zapytanie o szczegóły uruchomionych zadań, Dodaj następującą metodę:

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

14. Zaktualizuj podpis metody **Main** , aby uwzględnić następującą `throws` klauzulę:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Aby uruchomić i monitorować dwa zadania sekwencyjnie, Zastąp kod w metodzie **Main** następującym kodem:

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

16. Zapisz i zamknij plik **Schedule-jobs\src\main\java\com\mycompany\app\App.Java**

17. Kompiluj aplikację **Schedule-Jobs** i popraw błędy. W wierszu polecenia przejdź do folderu **Schedule-Jobs** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsolową Java, która obsługuje żądane właściwości wysyłane z IoT Hub i implementuje wywołanie metody bezpośredniej.

1. W folderze **IoT-Java-Schedule-Jobs** Utwórz projekt Maven o nazwie **symulowane-Device** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. W wierszu polecenia przejdź do folderu **symulowane urządzenia** .

3. Za pomocą edytora tekstów Otwórz plik **pom.xml** w folderze **symulowane urządzenia** i Dodaj następujące zależności do węzła **zależności** . Ta zależność umożliwia korzystanie z pakietu **IoT-Device-Client** w aplikacji w celu komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-device-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Dodaj następującą zależność do węzła **zależności** . Ta zależność konfiguruje NOP dla rejestrowania Apache [SLF4J](https://www.slf4j.org/) , który jest używany przez zestaw SDK klienta urządzenia do implementowania rejestrowania. Ta konfiguracja jest opcjonalna, ale jeśli zostanie pominięta, podczas uruchamiania aplikacji może pojawić się ostrzeżenie w konsoli programu. Aby uzyskać więcej informacji na temat rejestrowania w zestawie SDK klienta urządzenia, zobacz artykuł [Rejestrowanie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)w *przykładach dla pliku Readme zestawu SDK urządzeń Azure IoT dla języka Java* .

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

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień na `{yourdeviceconnectionstring}` Parametry połączenia urządzenia skopiowane wcześniej w sekcji [Rejestrowanie nowego urządzenia w centrum IoT Hub](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**.

10. Aby wydrukować powiadomienia o bliźniaczych urządzeniach do konsoli programu, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Aby wydrukować powiadomienia metody bezpośredniej do konsoli programu, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Aby obsłużyć bezpośrednie wywołania metod z IoT Hub, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

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

13. Zaktualizuj podpis metody **Main** , aby uwzględnić następującą `throws` klauzulę:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Zastąp kod w metodzie **Main** następującym kodem, aby:
    * Utwórz klienta urządzenia, aby komunikować się z IoT Hub.
    * Utwórz obiekt **urządzenia** do przechowywania właściwości sznurka urządzenia.

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

15. Aby uruchomić usługi klienta urządzenia, Dodaj następujący kod do metody **Main** :

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

16. Aby poczekać, aż użytkownik naciśnie klawisz **Enter** przed zamknięciem, Dodaj następujący kod na końcu metody **Main** :

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Zapisz i zamknij plik **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

18. Kompiluj aplikację **symulowaną przez urządzenie** i popraw wszelkie błędy. W wierszu polecenia przejdź do folderu **symulowane urządzenia** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchamiania aplikacji konsolowych.

1. W wierszu polecenia w folderze **symulowane urządzenia** Uruchom następujące polecenie, aby uruchomić aplikację urządzenia nasłuchiwanie dla żądanych zmian właściwości i wywołań metod bezpośrednich:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Klient urządzenia zostanie uruchomiony](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. W wierszu polecenia w `schedule-jobs` folderze Uruchom następujące polecenie, aby uruchomić aplikację **harmonogram-zadania** w celu uruchomienia dwóch zadań. Pierwszy ustawia żądane wartości właściwości, drugi wywołuje metodę bezpośrednią:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Aplikacja usługi IoT Hub Java tworzy dwa zadania](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Aplikacja urządzenia obsługuje żądaną zmianę właściwości i wywołanie metody bezpośredniej:

   ![Klient urządzenia odpowiada na zmiany](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania do zaplanowania metody bezpośredniej dla urządzenia i aktualizacji właściwości sznurka urządzenia.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wyślij dane telemetryczne z urządzeń za pomocą samouczka Wprowadzenie [do IoT Hub](quickstart-send-telemetry-java.md) .

* Interaktywne sterowanie urządzeniami (na przykład Włączanie wentylatorów z poziomu aplikacji sterowanej przez użytkownika) za pomocą samouczka [use Direct Methods](quickstart-control-device-java.md) . s