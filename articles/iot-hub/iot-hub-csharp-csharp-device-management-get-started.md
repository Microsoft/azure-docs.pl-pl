---
title: Rozpoczynanie pracy z usługą Azure IoT Hub Device Management (.NET/.NET) | Microsoft Docs
description: Jak zainicjować zdalne ponowne uruchomienie urządzenia za pomocą usługi Azure IoT Hub Device Management. Zestaw SDK urządzeń Azure IoT dla platformy .NET służy do implementowania aplikacji symulowanego urządzenia, która obejmuje metodę bezpośrednią i zestaw SDK usługi Azure IoT dla platformy .NET, aby zaimplementować aplikację usługi, która wywołuje metodę bezpośrednią.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89018403"
---
# <a name="get-started-with-device-management-net"></a>Wprowadzenie do zarządzania urządzeniami (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj Azure Portal, aby utworzyć Centrum IoT Hub i utworzyć tożsamość urządzenia w centrum IoT.

* Utwórz aplikację symulowanego urządzenia, która zawiera metodę bezpośrednią, która uruchamia ponownie to urządzenie. Metody bezpośrednie są wywoływane z chmury.

* Tworzenie aplikacji konsolowej .NET, która wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia za pośrednictwem Centrum IoT.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* **SimulateManagedDevice**. Ta aplikacja łączy się z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia, odbiera metodę bezpośredniego ponownego uruchomienia, symuluje rozruch fizyczny i raportuje godzinę ostatniego ponownego uruchomienia.

* **TriggerReboot**. Ta aplikacja wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia, wyświetla odpowiedź i wyświetla zaktualizowane raportowane właściwości.

## <a name="prerequisites"></a>Wymagania wstępne

* Programu Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsolową .NET przy użyciu języka C#, która inicjuje zdalne ponowne uruchomienie na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań o bliźniaczych urządzeniach do wykrywania czasu ostatniego rozruchu dla tego urządzenia.

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt** Znajdź i wybierz szablon projektu **aplikacja konsoli (.NET Framework)** , a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nadaj projektowi nazwę *TriggerReboot* i wybierz pozycję .NET Framework w wersji 4.5.1 lub nowszej. Wybierz przycisk **Utwórz**.

    ![Nowy projekt Visual C# Windows Classic Desktop](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **TriggerReboot** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. Wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **Microsoft. Azure. Devices**. Wybierz pozycję **Zainstaluj** , aby zainstalować pakiet **Microsoft. Azure. Devices** .

    ![Okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Dodaj następujące pola do klasy **Program**: Zastąp `{iot hub connection string}` wartość symbolu zastępczego parametrami połączenia IoT Hub skopiowanymi wcześniej w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Dodaj następującą metodę do klasy **Program**.  Ten kod pobiera dwuosiową urządzenie do ponownego rozruchu i wyprowadza raportowane właściwości.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Dodaj następującą metodę do klasy **Program**.  Ten kod inicjuje ponowny rozruch urządzenia przy użyciu metody bezpośredniej.

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

1. Wybierz pozycję **kompilacja** Kompiluj  >  **rozwiązanie**.

> [!NOTE]
> W tym samouczku wykonywane jest tylko jedno zapytanie dotyczące raportowanych właściwości urządzenia. W kodzie produkcyjnym zalecamy sondowanie w celu wykrywania zmian raportowanych właściwości.

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Utwórz aplikację konsolową platformy .NET, która odpowiada na bezpośrednią metodę wywoływaną przez chmurę.

* Wyzwalanie ponownego uruchomienia symulowanego urządzenia.

* Korzystając z raportowanych właściwości, można włączyć funkcję sznurów odosiowych urządzeń w celu zidentyfikowania urządzeń i czasu ostatniego ponownego uruchomienia.

Aby utworzyć aplikację symulowanego urządzenia, wykonaj następujące kroki:

1. W programie Visual Studio w już utworzonym rozwiązaniu TriggerReboot wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. W obszarze **Utwórz nowy projekt** Znajdź i wybierz szablon projektu **aplikacja konsoli (.NET Framework)** , a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nazwij projekt *SimulateManagedDevice*, a w polu **rozwiązanie** wybierz pozycję **Dodaj do rozwiązania**. Wybierz przycisk **Utwórz**.

    ![Nazwa i Dodawanie projektu do rozwiązania](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy nowy projekt **SimulateManagedDevice** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. Wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **Microsoft. Azure. Devices. Client**. Wybierz pozycję **Zainstaluj**.

    ![Aplikacja kliencka Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK urządzenia usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp `{device connection string}` wartość symbolu zastępczego parametrami połączenia urządzenia zanotowanymi wcześniej w temacie [Rejestrowanie nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Dodaj następujące polecenie, aby zaimplementować metodę bezpośrednią na urządzeniu:

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

1. Na koniec Dodaj następujący kod do metody **Main** , aby otworzyć połączenie z Centrum IoT Hub i zainicjować odbiornik metody:

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

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Ustaw projekty startowe**.

1. W polu  >  **projekt startowy** typowych właściwości wybierz pozycję **pojedynczy projekt startowy**, a następnie wybierz projekt **SimulateManagedDevice** . Wybierz przycisk **OK**, aby zapisać zmiany.

1. Wybierz pozycję **kompilacja** Kompiluj  >  **rozwiązanie**.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z sugestią w [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchamiania aplikacji.

1. Aby uruchomić aplikację urządzenia .NET **SimulateManagedDevice**, w Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **SimulateManagedDevice** , wybierz polecenie **Debuguj**, a następnie wybierz polecenie **Uruchom nowe wystąpienie**. Aplikacja powinna rozpocząć nasłuchiwanie wywołań metod z Centrum IoT Hub.

1. Gdy urządzenie jest połączone i oczekuje na wywołania metody, kliknij prawym przyciskiem myszy projekt **TriggerReboot** , wybierz polecenie **Debuguj**, a następnie wybierz polecenie **Uruchom nowe wystąpienie**.

   Powinien pojawić się "ponowne uruchomienie!" zapisany w konsoli programu **SimulatedManagedDevice** i raportowane właściwości urządzenia, w tym czas ostatniego ponownego uruchomienia, zapisany w konsoli **TriggerReboot** .

    ![Uruchom aplikację usługi i urządzenia](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
