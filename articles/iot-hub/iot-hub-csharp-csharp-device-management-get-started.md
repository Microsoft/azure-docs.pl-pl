---
title: Wprowadzenie do zarządzania urządzeniami usługi Azure IoT Hub (.NET/.NET) | Dokumenty firmy Microsoft
description: Jak za pomocą zarządzania urządzeniami usługi Azure IoT Hub do inicjowania ponownego uruchomienia urządzenia zdalnego. Użyj zestawu SDK urządzenia Usługi Azure IoT dla platformy .NET w celu zaimplementowania aplikacji symulowanego urządzenia, która zawiera metodę bezpośrednią i zestaw SDK usługi Azure IoT dla platformy .NET w celu zaimplementowania aplikacji usługi, która wywołuje metodę bezpośrednią.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 3cc74faa39b21b1ab275149db4f85de8f55fd07e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733469"
---
# <a name="get-started-with-device-management-net"></a>Wprowadzenie do zarządzania urządzeniami (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure Portal, aby utworzyć centrum IoT i utworzyć tożsamość urządzenia w centrum IoT hub.

* Utwórz aplikację symulowanego urządzenia, która zawiera bezpośrednią metodę ponownego uruchomienia tego urządzenia. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację konsoli .NET, która wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia za pośrednictwem centrum IoT hub.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* **SimulateManagedDevice**. Ta aplikacja łączy się z centrum IoT hub z tożsamości urządzenia utworzone wcześniej, odbiera metodę bezpośredniego ponownego uruchomienia, symuluje fizyczny ponowny rozruch i raportuje czas ostatniego ponownego uruchomienia.

* **TriggerReboot**. Ta aplikacja wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia, wyświetla odpowiedź i wyświetla zaktualizowane zgłoszone właściwości.

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia urządzenia przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsoli .NET przy użyciu języka C#, która inicjuje zdalny ponowny rozruch na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań bliźniaczej reprezentacji urządzenia, aby odkryć ostatni czas ponownego uruchomienia dla tego urządzenia.

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.

1. W **obszarze Tworzenie nowego projektu**znajdź i wybierz szablon projektu aplikacji konsoli **(.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfiguruj nowy projekt nazwij**projekt *TriggerReboot*i wybierz opcję .NET Framework w wersji 4.5.1 lub nowszej. Wybierz pozycję **Utwórz**.

    ![Nowy projekt Visual C# Windows Classic Desktop](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **TriggerReboot,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Wybierz **pozycję Przeglądaj**, a następnie wyszukaj i wybierz pozycję **Microsoft.Azure.Devices**. Wybierz **pozycję Zainstaluj,** aby zainstalować pakiet **Microsoft.Azure.Devices.**

    ![Okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet usługi Azure IoT i](https://www.nuget.org/packages/Microsoft.Azure.Devices/) jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu `{iot hub connection string}` zastępczego ciągiem połączenia usługi IoT Hub skopiowanym wcześniej w polu Pobierz ciąg połączenia centrum [IoT](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Dodaj następującą metodę do klasy **Program**.  Ten kod pobiera bliźniaczej reprezentacji urządzenia dla urządzenia ponownego uruchamiania i wyprowadza zgłoszone właściwości.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Dodaj następującą metodę do klasy **Program**.  Ten kod inicjuje ponowne uruchomienie na urządzeniu przy użyciu metody bezpośredniej.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Na koniec dodaj następujące wiersze do metody **Główne**:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Wybierz **opcję Zbuduj** > **rozwiązanie kompilacji**.

> [!NOTE]
> Ten samouczek wykonuje tylko jedną kwerendę dla zgłoszonych właściwości urządzenia. W kodzie produkcyjnym zaleca się sondowanie w celu wykrycia zmian w zgłoszonych właściwościach.

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Utwórz aplikację konsoli platformy .NET, która odpowiada na metodę bezpośrednią wywoływaną przez chmurę.

* Wyzwalanie symulowanego ponownego uruchomienia urządzenia.

* Użyj zgłoszonych właściwości, aby włączyć zapytania bliźniaczej reprezentacji urządzenia do identyfikowania urządzeń i kiedy zostały ostatnio ponownie uruchomione.

Aby utworzyć aplikację symulowanego urządzenia, wykonaj następujące czynności:

1. W programie Visual Studio w już utworzonym rozwiązaniu TriggerReboot wybierz pozycję **Plik** > **nowego** > **projektu**. W **obszarze Tworzenie nowego projektu**znajdź i wybierz szablon projektu aplikacji konsoli **(.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfiguruj nowy projekt nazwij**projekt *SimulateManagedDevice*, a w przypadku **rozwiązania**wybierz pozycję Dodaj **do rozwiązania**. Wybierz pozycję **Utwórz**.

    ![Nadaj nazwę i dodaj projekt do rozwiązania](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy nowy projekt **SimulateManagedDevice,** a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. Wybierz **pozycję Przeglądaj**, a następnie wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Client**. Wybierz pozycję **Zainstaluj**.

    ![Aplikacja klienta okna Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet zestawu NuGet urządzenia Usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) i jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu `{device connection string}` zastępczego ciągiem połączenia urządzenia, który został wcześniej odnotowany w polu Zarejestruj nowe urządzenie w centrum [IoT hub](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Dodaj następujące elementy, aby zaimplementować metodę bezpośrednią na urządzeniu:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Na koniec dodaj następujący kod do **Main** metody, aby otworzyć połączenie z centrum IoT hub i zainicjować odbiornik metody:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Ustaw projekty startowe**.

1. W przypadku**projektu uruchamiania** **wspólnych właściwości** > wybierz **pojedynczy projekt startowy**, a następnie wybierz projekt **SimulateManagedDevice.** Aby zapisać zmiany, wybierz pozycję **OK**.

1. Wybierz **opcję Zbuduj** > **rozwiązanie kompilacji**.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak wykładnicze wycofywanie), zgodnie z sugestią w [przypadku obsługi błędów przejściowych.](/azure/architecture/best-practices/transient-faults)

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje.

1. Aby uruchomić aplikację urządzenia .NET **SimulateManagedDevice**w Eksploratorze rozwiązań, kliknij prawym przyciskiem myszy projekt **SimulateManagedDevice,** wybierz polecenie **Debug**, a następnie wybierz pozycję **Rozpocznij nowe wystąpienie**. Aplikacja powinna rozpocząć nasłuchiwania wywołań metod z centrum IoT Hub.

1. Następnie urządzenie jest połączone i oczekuje na wywołania metody, kliknij prawym przyciskiem myszy projekt **TriggerReboot,** wybierz **debugowanie**, a następnie wybierz pozycję **Rozpocznij nowe wystąpienie**.

   Powinieneś zobaczyć "Ponowne uruchomienie!" zapisane w konsoli **SimulatedManagedDevice** i zgłoszone właściwości urządzenia, które obejmują ostatni czas ponownego uruchomienia, zapisane w konsoli **TriggerReboot.**

    ![Uruchamianie aplikacji usługi i urządzenia](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
