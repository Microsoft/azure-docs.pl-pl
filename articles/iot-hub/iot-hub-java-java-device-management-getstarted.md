---
title: Rozpoczynanie pracy z usługą Azure IoT Hub Device Management (Java) | Microsoft Docs
description: Jak zainicjować zdalne ponowne uruchomienie urządzenia za pomocą usługi Azure IoT Hub Device Management. Zestaw SDK urządzeń Azure IoT dla języka Java służy do implementowania aplikacji symulowanego urządzenia, która obejmuje metodę bezpośrednią i zestaw SDK usługi Azure IoT dla języka Java w celu zaimplementowania aplikacji usługi, która wywołuje metodę bezpośrednią.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: f05e1a458bc83fe4042c4b6cf35d9aa2095868ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217962"
---
# <a name="get-started-with-device-management-java"></a>Wprowadzenie do zarządzania urządzeniami (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj Azure Portal, aby utworzyć IoT Hub i utworzyć tożsamość urządzenia w centrum IoT.

* Tworzenie aplikacji symulowanego urządzenia, która implementuje metodę bezpośrednią w celu ponownego uruchomienia urządzenia. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację, która wywołuje metodę ponownego rozruchu w aplikacji symulowanego urządzenia za pośrednictwem Centrum IoT. Ta aplikacja monitoruje następnie zgłoszone właściwości z urządzenia, aby sprawdzić, kiedy operacja ponownego uruchamiania zostanie zakończona.

Na końcu tego samouczka masz dwie aplikacje konsolowe Java:

**symulowane — urządzenie**. Ta aplikacja:

* Nawiązuje połączenie z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia.

* Odbiera wywołanie metody bezpośredniego ponownego uruchomienia.

* Symuluje fizyczne ponowne uruchomienie.

* Zgłasza czas ostatniego ponownego uruchomienia za pomocą raportowanej właściwości.

**wyzwalacz — ponowny rozruch**. Ta aplikacja:

* Wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia.

* Wyświetla odpowiedź na wywołanie metody bezpośredniej wysyłane przez symulowane urządzenie.

* Wyświetla zaktualizowane raportowane właściwości.

> [!NOTE]
> Aby uzyskać informacje o zestawach SDK, których można użyć do kompilowania aplikacji do uruchamiania na urządzeniach i zaplecza rozwiązania, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Java SE Development Kit 8](/java/azure/jdk/). Upewnij się, że wybrano opcję **Java 8** w obszarze **Obsługa długoterminowa** , aby pobrać pliki do pobrania dla programu JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsolową Java, która:

1. Wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia.

2. Wyświetla odpowiedź.

3. Sonduje raportowane właściwości wysyłane z urządzenia w celu określenia, kiedy ponowne uruchomienie zostało zakończone.

Ta Aplikacja konsolowa nawiązuje połączenie z IoT Hub, aby wywołać metodę bezpośrednią i odczytać raportowane właściwości.

1. Utwórz pusty folder o nazwie **DM-Get-Started**.

2. W folderze **DM-Get-Started** Utwórz projekt Maven o nazwie **wyzwalacz-ponowny rozruch** przy użyciu następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. W wierszu polecenia przejdź do folderu **wyzwalacz-ponowne uruchomienie** .

4. Za pomocą edytora tekstów Otwórz plik **pom.xml** w folderze **wyzwalacz-ponowny rozruch** i Dodaj następujący zależność do węzła **zależności** . Ta zależność umożliwia korzystanie z pakietu IoT-Service-Client w aplikacji w celu komunikowania się z Centrum IoT:

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

7. Za pomocą edytora tekstów Otwórz plik źródłowy **Trigger-reboot\src\main\java\com\mycompany\app\App.Java** .

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

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień na `{youriothubconnectionstring}` IoT Hub parametry połączenia, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Aby zaimplementować wątek, który odczytuje raportowane właściwości z sznurów urządzeń co 10 sekund, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

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

11. Zmodyfikuj podpis metody **Main** , aby zgłosić następujący wyjątek:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Aby wywołać metodę bezpośredniego ponownego rozruchu na symulowanym urządzeniu, Zastąp kod w metodzie **Main** następującym kodem:

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

13. Aby rozpocząć wątek w celu sondowania raportowanych właściwości z symulowanego urządzenia, Dodaj następujący kod do metody **Main** :

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Aby można było zatrzymać aplikację, Dodaj następujący kod do metody **Main** :

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Zapisz i zamknij plik **Trigger-reboot\src\main\java\com\mycompany\app\App.Java** .

16. Utwórz **wyzwalacz-Uruchom** ponownie aplikację zaplecza i popraw błędy. W wierszu polecenia przejdź do folderu **wyzwalacz-ponowny rozruch** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową Java, która symuluje urządzenie. Aplikacja nasłuchuje wywołań metody bezpośredniego ponownego rozruchu z Centrum IoT Hub i natychmiast odpowiada na to wywołanie. Aplikacja przestanie być w stanie uśpienia przez pewien czas, aby symulować proces ponownego uruchamiania, zanim użyje raportowanej właściwości do powiadomienia **wyzwalacz-ponowne uruchomienie** aplikacji zaplecza.

1. W folderze **DM-Get-Started** Utwórz projekt Maven o nazwie **symulowane-Device** przy użyciu następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do folderu **symulowane urządzenia** .

3. Za pomocą edytora tekstów Otwórz plik **pom.xml** w folderze **symulowane urządzenia** i Dodaj następujący zależność do węzła **zależności** . Ta zależność umożliwia korzystanie z pakietu IoT-Service-Client w aplikacji w celu komunikowania się z Centrum IoT:

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

7. Za pomocą edytora tekstów Otwórz plik źródłowy **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

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

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zamień na `{yourdeviceconnectionstring}` Parametry połączenia urządzenia zanotowane w sekcji [Rejestrowanie nowego urządzenia w centrum IoT](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Aby zaimplementować procedurę obsługi wywołania zwrotnego dla zdarzeń ze stanem metody bezpośredniej, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Aby zaimplementować procedurę obsługi wywołania zwrotnego dla zdarzeń stanu przędzy urządzenia, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Aby zaimplementować procedurę obsługi wywołania zwrotnego dla zdarzeń właściwości, Dodaj następującą klasę zagnieżdżoną do klasy **App** :

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

13. Aby zaimplementować wątek w celu symulowania ponownego uruchomienia urządzenia, Dodaj następującą klasę zagnieżdżoną do klasy **App** . Wątek jest uśpiony przez pięć sekund, a następnie ustawia właściwość **lastReboot** raportowaną:

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

14. Aby zaimplementować metodę bezpośrednią na urządzeniu, Dodaj następującą klasę zagnieżdżoną do klasy **App** . Gdy symulowana aplikacja odbiera wywołanie metody bezpośredniego **ponownego uruchomienia** , zwraca potwierdzenie do obiektu wywołującego, a następnie uruchamia wątek w celu przetworzenia ponownego uruchomienia:

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

15. Zmodyfikuj podpis metody **Main** , aby zgłosić następujące wyjątki:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Aby utworzyć wystąpienie elementu **DeviceClient**, Zastąp kod w metodzie **Main** następującym kodem:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Aby rozpocząć nasłuchiwanie wywołań metody bezpośredniej, Dodaj następujący kod do metody **Main** :

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

18. Aby zamknąć symulatora urządzeń, Dodaj następujący kod do metody **Main** :

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Zapisz i zamknij plik simulated-device\src\main\java\com\mycompany\app\App.java.

20. Kompiluj aplikację **symulowaną przez urządzenie** i popraw wszelkie błędy. W wierszu polecenia przejdź do folderu **symulowane urządzenia** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchamiania aplikacji.

1. W wierszu polecenia w folderze **symulowane urządzenia** Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie wywołań metod ponownego rozruchu z Centrum IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Środowisko Java IoT Hub symulowanej aplikacji urządzenia do nasłuchiwania wywołań metody bezpośredniego ponownego rozruchu](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. W wierszu polecenia w folderze **wyzwalacza ponownego** uruchomienia Uruchom następujące polecenie, aby wywołać metodę ponownego uruchamiania urządzenia symulowanego z Centrum IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja usługi Java IoT Hub do wywoływania metody bezpośredniego ponownego uruchomienia](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Symulowane urządzenie odpowiada na wywołanie metody bezpośredniego ponownego uruchomienia:

    ![Symulowana aplikacja urządzenia IoT Hub Java reaguje na wywołanie metody bezpośredniej](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]