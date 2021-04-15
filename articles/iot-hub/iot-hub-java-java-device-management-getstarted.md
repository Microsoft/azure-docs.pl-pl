---
title: Wprowadzenie do zarządzania Azure IoT Hub urządzeniami mobilnymi (Java) | Microsoft Docs
description: Jak zainicjować zdalne Azure IoT Hub urządzenia za pomocą zarządzania urządzeniami zdalnymi. Zestaw SDK urządzenia usługi Azure IoT dla języka Java służy do implementowania aplikacji urządzenia symulowanego, która zawiera metodę bezpośrednią, oraz zestawu SDK usługi Azure IoT dla języka Java w celu zaimplementowania aplikacji usługi, która wywołuje metodę bezpośrednią.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 797c795b6c936e2f117a96fed4b4a483876f1c47
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478084"
---
# <a name="get-started-with-device-management-java"></a>Wprowadzenie do zarządzania urządzeniami (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Skorzystaj z Azure Portal, aby utworzyć IoT Hub i utworzyć tożsamość urządzenia w centrum IoT.

* Utwórz aplikację urządzenia symulowanego, która implementuje metodę bezpośrednią w celu ponownego uruchomienia urządzenia. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację, która wywołuje metodę bezpośredniej ponownego uruchomienia w aplikacji urządzenia symulowanego za pośrednictwem centrum IoT. Następnie ta aplikacja monitoruje zgłaszane właściwości z urządzenia, aby zobaczyć, kiedy operacja ponownego uruchomienia została ukończona.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe Java:

**simulated-device.** Ta aplikacja:

* Nawiązuje połączenie z centrum IoT przy użyciu utworzonej wcześniej tożsamości urządzenia.

* Odbiera wywołanie metody bezpośredniej ponownego uruchomienia.

* Symuluje fizyczny ponowny rozruch.

* Raportuje czas ostatniego ponownego uruchomienia za pośrednictwem zgłaszanej właściwości.

**trigger-reboot**. Ta aplikacja:

* Wywołuje metodę bezpośrednią w aplikacji urządzenia symulowanego.

* Wyświetla odpowiedź na wywołanie metody bezpośredniej wysyłane przez urządzenie symulowane.

* Wyświetla zaktualizowane zgłaszane właściwości.

> [!NOTE]
> Aby uzyskać informacje o zestawach SDK, których można użyć do tworzenia aplikacji do uruchamiania na urządzeniach i w zabudówce rozwiązania, zobacz [Zestawy SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Java SE Development Kit 8.](/java/azure/jdk/) Upewnij się, że wybierasz **pozycję Java 8** w **obszarze Długoterminowa obsługa,** aby uzyskać dostęp do plików do pobrania dla JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [](https://azure.microsoft.com/pricing/free-trial/) bezpłatne konto w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym artykule używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Uzyskiwanie parametrów połączenia centrum IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia urządzenia przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsolą Java, która:

1. Wywołuje metodę bezpośrednią ponownego rozruchu w aplikacji urządzenia symulowanego.

2. Wyświetla odpowiedź.

3. Sonduje zgłaszane właściwości wysyłane z urządzenia, aby określić, kiedy ponowne uruchomienie zostało ukończone.

Ta aplikacja konsolowa łączy się z IoT Hub w celu wywołania metody bezpośredniej i odczytania zgłoszonych właściwości.

1. Utwórz pusty folder o nazwie **dm-get-started.**

2. W **folderze dm-get-started** utwórz projekt Maven o nazwie **trigger-reboot** przy użyciu następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. W wierszu polecenia przejdź do folderu **trigger-reboot.**

4. Za pomocą edytora tekstów otwórz **plikpom.xml** w folderze **trigger-reboot** i dodaj następującą zależność **do węzła zależności.** Ta zależność umożliwia używanie pakietu iot-service-client w aplikacji do komunikowania się z centrum IoT:

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

5. Dodaj następujący **węzeł kompilacji** po **węźle zależności.** Ta konfiguracja instruuje program Maven, aby skompilował aplikację przy użyciu języka Java 1.8:

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

7. Za pomocą edytora tekstów otwórz plik źródłowy **trigger-reboot\src\main\java\com\mycompany\app\App.java.**

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

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp ciąg IoT Hub parametrów połączenia skopiowanych wcześniej w teście Get the IoT hub connection string (Uzyskiwanie parametrów połączenia `{youriothubconnectionstring}` centrum [IoT):](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Aby zaimplementować wątek, który co 10 sekund odczytuje zgłaszane właściwości z bliźniaczej reprezentacji urządzenia, dodaj następującą zagnieżdżoną klasę do **klasy App:**

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

12. Aby wywołać metodę bezpośrednią ponownego uruchomienia na urządzeniu symulowanym, zastąp kod w metodzie **main** następującym kodem:

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

13. Aby uruchomić wątek w celu sondowania zgłoszonych właściwości z urządzenia symulowanego, dodaj następujący kod do **metody** głównej:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Aby umożliwić zatrzymanie aplikacji, dodaj następujący kod do **metody main:**

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Zapisz i zamknij plik **trigger-reboot\src\main\java\com\mycompany\app\App.java.**

16. Skompilowanie **aplikacji back-end wyzwalacza** i ponownego uruchomienia oraz poprawienie wszelkich błędów. W wierszu polecenia przejdź do folderu **trigger-reboot** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli Java, która symuluje urządzenie. Aplikacja nasłuchuje wywołania metody bezpośredniej ponownego uruchomienia z centrum IoT i natychmiast odpowiada na to wywołanie. Następnie aplikacja jest uśpięna przez jakiś czas, aby zasymulować proces ponownego uruchamiania, zanim użyje zgłaszanej właściwości w celu powiadomienia aplikacji **back-reboot** wyzwalacza o zakończeniu ponownego uruchamiania.

1. W **folderze dm-get-started** utwórz projekt narzędzia Maven o nazwie **simulated-device** przy użyciu następującego polecenia w wierszu polecenia:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do **folderu simulated-device.**

3. Za pomocą edytora tekstów otwórz **pom.xml** w folderze **simulated-device** i dodaj następującą zależność do **węzła zależności.** Ta zależność umożliwia używanie pakietu iot-service-client w aplikacji do komunikowania się z centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-device-client** za pomocą [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Dodaj następującą zależność do **węzła zależności.** Ta zależność konfiguruje nop dla fasady rejestrowania Apache [SLF4J,](https://www.slf4j.org/) która jest używana przez zestaw SDK klienta urządzenia do implementowania rejestrowania. Ta konfiguracja jest opcjonalna, ale jeśli ją pominiesz, po uruchomieniu aplikacji w konsoli może zostać wyświetlony komunikat ostrzegawczy. Aby uzyskać więcej informacji na temat rejestrowania w zestawie SDK klienta urządzenia, zobacz Rejestrowanie w przykładach dla pliku readme zestawu *SDK urządzenia usługi Azure IoT* dla języka Java. [](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Dodaj następujący węzeł **kompilacji** po **węźle zależności.** Ta konfiguracja instruuje program Maven, aby skompilował aplikację przy użyciu języka Java 1.8:

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

7. Za pomocą edytora tekstów otwórz plik źródłowy **simulated-device\src\main\java\com\mycompany\app\App.java.**

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

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp ciąg ciągami połączenia urządzenia zanotymi w sekcji Rejestrowanie nowego urządzenia `{yourdeviceconnectionstring}` [w centrum IoT:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Aby zaimplementować obsługę wywołania zwrotnego dla zdarzeń stanu metody bezpośredniej, dodaj następującą zagnieżdżone klasę do **klasy App:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Aby zaimplementować obsługę wywołania zwrotnego dla zdarzeń stanu bliźniaczej reprezentacji urządzenia, dodaj następującą zagnieżdżone klasy do **klasy App:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Aby zaimplementować obsługę wywołania zwrotnego dla zdarzeń właściwości, dodaj następującą zagnieżdżone klasy do **klasy App:**

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

13. Aby zaimplementować wątek symulujący ponowne uruchomienie urządzenia, dodaj następującą zagnieżdżone klasy do **klasy App.** Wątek jest uśpiany przez pięć sekund, a następnie ustawia zgłaszaną właściwość **lastReboot:**

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

14. Aby zaimplementować metodę bezpośrednią na urządzeniu, dodaj następującą zagnieżdżone klasy do **klasy App.** Gdy symulowana aplikacja odbiera wywołanie  metody bezpośredniej ponownego uruchomienia, zwraca potwierdzenie do wywołującego, a następnie uruchamia wątek w celu przetwarzania ponownego uruchomienia:

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

16. Aby utworzyć wystąpienia **deviceClient,** zastąp kod w **metodzie main** następującym kodem:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Aby rozpocząć nasłuchiwanie wywołań metody bezpośredniej, dodaj następujący kod do **metody main:**

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

18. Aby zamknąć symulator urządzenia, dodaj następujący kod do **metody main:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Zapisz i zamknij plik simulated-device\src\main\java\com\mycompany\app\App.java.

20. **Skompilowanie aplikacji simulated-device** i poprawienie wszelkich błędów. W wierszu polecenia przejdź do folderu **simulated-device** i uruchom następujące polecenie:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje.

1. W wierszu polecenia w folderze **simulated-device** uruchom następujące polecenie, aby rozpocząć nasłuchiwanie wywołań metody ponownego uruchamiania z centrum IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja symulowanego IoT Hub Java do nasłuchiwać wywołań metody bezpośredniej ponownego uruchomienia](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. W wierszu polecenia w folderze **trigger-reboot** uruchom następujące polecenie, aby wywołać metodę ponownego uruchamiania na urządzeniu symulowanym z centrum IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikacja IoT Hub Java do wywołania metody bezpośredniej ponownego uruchomienia](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Urządzenie symulowane reaguje na wywołanie metody bezpośredniej ponownego uruchomienia:

    ![Aplikacja IoT Hub Java odpowiada na wywołanie metody bezpośredniej](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
