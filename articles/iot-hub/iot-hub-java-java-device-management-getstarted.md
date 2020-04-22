---
title: Wprowadzenie do zarządzania urządzeniami usługi Azure IoT Hub (Java) | Dokumenty firmy Microsoft
description: Jak za pomocą zarządzania urządzeniami usługi Azure IoT Hub do inicjowania ponownego uruchomienia urządzenia zdalnego. Użyj zestawu SDK urządzenia Usługi Azure IoT dla języka Java, aby zaimplementować aplikację symulowanego urządzenia, która zawiera metodę bezpośrednią i zestaw SDK usługi Azure IoT dla języka Java w celu zaimplementowania aplikacji usługi, która wywołuje metodę bezpośrednią.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt
ms.openlocfilehash: 75d89b54bae6eb8166d44e08ea020a0da67ad20c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732554"
---
# <a name="get-started-with-device-management-java"></a>Wprowadzenie do zarządzania urządzeniami (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure Portal, aby utworzyć centrum IoT Hub i utworzyć tożsamość urządzenia w centrum IoT hub.

* Utwórz aplikację symulowanego urządzenia, która implementuje bezpośrednią metodę ponownego uruchomienia urządzenia. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację, która wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia za pośrednictwem centrum IoT hub. Ta aplikacja następnie monitoruje zgłaszane właściwości z urządzenia, aby zobaczyć, kiedy operacja ponownego uruchomienia jest zakończona.

Na końcu tego samouczka masz dwie aplikacje konsoli Java:

**symulowanego urządzenia**. Ta aplikacja:

* Łączy się z centrum IoT hub z tożsamości urządzenia utworzone wcześniej.

* Odbiera wywołanie metody bezpośredniej ponownego uruchomienia.

* Symuluje fizyczny ponowny rozruch.

* Raportuje czas ostatniego ponownego uruchomienia za pośrednictwem zgłoszonej właściwości.

**trigger-reboot**. Ta aplikacja:

* Wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia.

* Wyświetla odpowiedź na bezpośrednie wywołanie metody wysyłane przez symulowane urządzenie.

* Wyświetla zaktualizowane zgłoszone właściwości.

> [!NOTE]
> Aby uzyskać informacje na temat zestawów SDK, których można używać do tworzenia aplikacji do uruchamiania na urządzeniach i zapleczu rozwiązania, zobacz [zestawy SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw java se development 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Upewnij się, że w obszarze **Java 8** w obszarze **Długoterminowa pomoc techniczna** umożliwia pobieranie dla JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia urządzenia przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsoli Java, która:

1. Wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia.

2. Wyświetla odpowiedź.

3. Sonduje zgłoszone właściwości wysyłane z urządzenia, aby określić, kiedy ponowne uruchomienie zostanie zakończone.

Ta aplikacja konsoli łączy się z Centrum IoT, aby wywołać metodę bezpośrednią i odczytać zgłoszone właściwości.

1. Utwórz pusty folder o nazwie **dm-get-started**.

2. W folderze **dm-get-started** utwórz projekt Maven o nazwie **trigger-reboot,** używając następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. W wierszu polecenia przejdź do folderu **wyzwalania i ponownego uruchamiania.**

4. Za pomocą edytora tekstu otwórz plik **pom.xml** w folderze **trigger-reboot** i dodaj następującą zależność do węzła **zależności.** Ta zależność umożliwia korzystanie z pakietu iot-service-client w aplikacji do komunikowania się z centrum IoT hub:

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

7. Za pomocą edytora tekstu otwórz plik **źródłowy trigger-reboot\src\main\java\com\mycompany\app\App.java.**

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień `{youriothubconnectionstring}` na parametry połączenia Usługi IoT Hub skopiowane wcześniej w [pobierz ciąg połączenia centrum IoT:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Aby zaimplementować wątek, który odczytuje zgłaszane właściwości z bliźniaczej reprezentacji urządzenia co 10 sekund, dodaj następującą klasę zagnieżdżoną do klasy **App:**

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Zmodyfikuj podpis **metody głównej,** aby zgłosić następujący wyjątek:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Aby wywołać metodę bezpośredniego ponownego uruchomienia na symulowanym urządzeniu, zastąp kod w metodzie **głównej** następującym kodem:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Aby uruchomić wątek, aby sondować zgłoszone właściwości z symulowanego urządzenia, dodaj następujący kod do **metody głównej:**

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Aby umożliwić zatrzymanie aplikacji, dodaj następujący kod do **metody głównej:**

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Zapisz i zamknij plik **trigger-reboot\src\main\java\com\mycompany\app\App.java.**

16. Tworzenie **aplikacji zaplecza wyzwalacza i** poprawianie błędów. W wierszu polecenia przejdź do folderu **wyzwalania i ponownego uruchamiania** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli Java, która symuluje urządzenie. Aplikacja nasłuchuje wywołania metody bezpośredniej ponownego uruchomienia z centrum IoT hub i natychmiast odpowiada na to wywołanie. Aplikacja następnie przechodzi w tryb uśpienia przez chwilę, aby symulować proces ponownego uruchamiania, zanim użyje zgłoszonej właściwości, aby powiadomić aplikację **zaplecza wyzwalacza o** zakończeniu ponownego uruchomienia.

1. W folderze **dm-get-started** utwórz projekt Maven o nazwie **symulowane urządzenie** za pomocą następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do folderu **symulowanego urządzenia.**

3. Za pomocą edytora tekstu otwórz plik **pom.xml** w folderze **symulowanego urządzenia** i dodaj następującą zależność do węzła **zależności.** Ta zależność umożliwia korzystanie z pakietu iot-service-client w aplikacji do komunikowania się z centrum IoT hub:

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

7. Za pomocą edytora tekstu otwórz plik **źródłowy symulowane urządzenie\src\main\java\com\mycompany\app\App.java.**

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień `{yourdeviceconnectionstring}` na ciąg połączenia urządzenia odnotowany w sekcji Zarejestruj nowe urządzenie w sekcji [IoT hub:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Aby zaimplementować program obsługi wywołania zwrotnego dla zdarzeń stanu metody bezpośredniej, dodaj następującą klasę zagnieżdżoną do klasy **app:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Aby zaimplementować program obsługi wywołania zwrotnego dla zdarzeń stanu bliźniaczej reprezentacji urządzenia, dodaj następującą klasę zagnieżdżoną do klasy **aplikacji:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Aby zaimplementować program obsługi wywołania zwrotnego dla zdarzeń właściwości, dodaj następującą klasę zagnieżdżoną do klasy **app:**

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Aby zaimplementować wątek do symulacji ponownego uruchomienia urządzenia, dodaj następującą klasę zagnieżdżoną do klasy **aplikacji.** Wątek przesiewnie przez pięć sekund, a następnie ustawia **właściwość raportowana przez ostatniaReboot:**

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Aby zaimplementować metodę bezpośrednią na urządzeniu, dodaj następującą klasę zagnieżdżoną do klasy **App.** Gdy symulowana aplikacja odbiera wywołanie metody bezpośredniej **ponownego uruchomienia,** zwraca potwierdzenie do wywołującego, a następnie uruchamia wątek do przetwarzania ponownego uruchomienia:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Zmodyfikuj podpis **metody głównej,** aby zgłosić następujące wyjątki:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Aby utworzyć wystąpienie **deviceclient**, zastąp kod w metodzie **głównej** następującym kodem:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Aby rozpocząć nasłuchiwanie wywołań metody bezpośredniej, dodaj następujący kod do **metody głównej:**

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Aby wyłączyć symulator urządzenia, dodaj następujący kod do **metody głównej:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Zapisz i zamknij plik symulowanego urządzenia\src\main\java\com\mycompany\app\App.java.

20. Skompiluj aplikację **symulowanego urządzenia** i popraw wszelkie błędy. W wierszu polecenia przejdź do folderu **symulowanego urządzenia** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje.

1. W wierszu polecenia w folderze **symulowanego urządzenia** uruchom następujące polecenie, aby rozpocząć nasłuchiwanie wywołań metody ponownego uruchamiania z centrum IoT hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja symulowanego urządzenia Java IoT Hub do nasłuchiwać wywołań bezpośrednich metod ponownego uruchomienia](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. W wierszu polecenia w folderze **wyzwalania-ponownego uruchamiania** uruchom następujące polecenie, aby wywołać metodę ponownego uruchamiania na symulowanym urządzeniu z centrum IoT hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja usługi Java IoT Hub do wywołania metody bezpośredniego ponownego uruchomienia](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Symulowane urządzenie odpowiada na wywołanie metody bezpośredniej ponownego uruchomienia:

    ![Aplikacja urządzenia symulowanego usługi Java IoT Hub odpowiada na bezpośrednie wywołanie metody](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
