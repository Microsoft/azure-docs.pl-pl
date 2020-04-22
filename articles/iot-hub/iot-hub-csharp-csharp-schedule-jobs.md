---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (.NET/.NET) | Dokumenty firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub, aby wywołać metodę bezpośrednią na wielu urządzeniach. Użyj zestawu SDK urządzenia Usługi Azure IoT dla platformy .NET, aby zaimplementować symulowane aplikacje urządzeń i aplikację usługi do uruchomienia zadania.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 4c71a108d1967027465d127db50737119af3e2c1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733366"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Planowanie i emisja zadań (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub umożliwia planowanie i śledzenie zadań, które aktualizują miliony urządzeń. Zadania można wykorzystać do:

* Aktualizowanie żądanych właściwości

* Aktualizuj tagi

* Wywoływanie metod bezpośrednich

Zadanie zawija jedną z tych akcji i śledzi wykonanie względem zestawu urządzeń, który jest zdefiniowany przez zapytanie bliźniaczej reprezentacji urządzenia. Na przykład aplikacja zaplecza można użyć zadania do wywołania metody bezpośredniej na 10 000 urządzeń, które uruchamiają ponownie urządzenia. Należy określić zestaw urządzeń z zapytaniem bliźniaczej reprezentacji urządzenia i zaplanować zadanie do uruchomienia w przyszłości. Zadanie śledzi postęp, gdy każde z urządzeń odbiera i wykonuje metodę bezpośredniego ponownego uruchomienia.

Aby dowiedzieć się więcej o każdej z tych funkcji, zobacz:

* Bliźniaczy bliźniaczej reprezentacji urządzenia i właściwości: [Wprowadzenie do bliźniaczych reprezentacji urządzeń](iot-hub-csharp-csharp-twin-getstarted.md) i [samouczek: Jak używać właściwości bliźniaczej reprezentacji urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [Przewodnik dla deweloperów IoT Hub - metody bezpośrednie](iot-hub-devguide-direct-methods.md) i [samouczek: użyj metod bezpośrednich](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację urządzenia, która implementuje metodę bezpośrednią o nazwie **LockDoor**, która może być wywoływana przez aplikację zaplecza.

* Utwórz aplikację zaplecza, która tworzy zadanie do wywołania **LockDoor** bezpośredniej metody na wielu urządzeniach. Inne zadanie wysyła żądane aktualizacje właściwości do wielu urządzeń.

Na końcu tego samouczka masz dwie aplikacje konsoli .NET (C#):

* **SimulateDeviceMetody**. Ta aplikacja łączy się z centrum IoT hub i implementuje **LockDoor** bezpośredniej metody.

* **ScheduleJob**. Ta aplikacja używa zadań do wywołania **LockDoor** bezpośredniej metody i zaktualizować bliźniaczej reprezentacji urządzenia żądane właściwości na wielu urządzeniach.

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli .NET, która odpowiada na metodę bezpośrednią wywoływaną przez zaplecze rozwiązania.

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**, a następnie wybierz szablon projektu **aplikacji konsoli (NET Framework).** Kliknij przycisk **Dalej**, aby kontynuować.

1. W **obszarze Konfiguruj nowy projekt**nazwij projekt *SimulateDeviceMetody*, a następnie wybierz pozycję **Utwórz**.

    ![Konfigurowanie projektu SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **SimulateDeviceMethods,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj** i wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Client**. Wybierz pozycję **Zainstaluj**.

    ![Aplikacja klienta okna Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet zestawu NuGet urządzenia Usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) i jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego ciągiem połączenia urządzenia odnotowanym w poprzedniej sekcji:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Dodaj następujący kod, aby zaimplementować metodę bezpośrednią na urządzeniu:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Dodaj następującą metodę, aby zaimplementować detektor bliźniaczych reprezentacji urządzenia na urządzeniu:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Na koniec dodaj następujący kod do **Main** metody, aby otworzyć połączenie z centrum IoT hub i zainicjować odbiornik metody:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Zapisz swoją pracę i zbuduj swoje rozwiązanie.

> [!NOTE]
> Aby zachować rzeczy proste, ten samouczek nie implementuje żadnych zasad ponawiania prób. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak ponawianie połączenia), zgodnie z sugestią w [obszarze Obsługa błędów przejściowych.](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planowanie zadań wywoływania metody bezpośredniej i wysyłania bliźniaczych aktualizacji urządzenia

W tej sekcji utworzysz aplikację konsoli .NET (przy użyciu języka C#), która używa zadań do wywoływania metody direct **LockDoor** i wysyłania aktualizacji żądanej właściwości do wielu urządzeń.

1. W programie Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu**. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfigurowanie nowego projektu**nazwij projekt *ScheduleJob*. W obszarze **Rozwiązanie**wybierz pozycję **Dodaj do rozwiązania**, a następnie wybierz pozycję **Utwórz**.

    ![Nazwij i konfiguruj projekt ScheduleJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Harmonogramujob,** a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**, wyszukaj i wybierz pozycję **Microsoft.Azure.Devices**, a następnie wybierz pozycję **Zainstaluj**.

   Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet usługi Azure IoT i](https://www.nuget.org/packages/Microsoft.Azure.Devices/) jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Dodaj następującą `using` instrukcję, jeśli nie jest jeszcze obecny w instrukcji domyślnej.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp symbole zastępcze ciągiem połączenia usługi IoT Hub skopiowanym wcześniej w aplikacji [Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string) i nazwę urządzenia.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Dodaj następującą metodę do **klasy Program:**

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Dodaj następującą metodę do **klasy Program:**

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Dodaj inną metodę do **klasy Program:**

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat składni kwerendy, zobacz [Język kwerend Centrum IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    >

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Zapisz swoją pracę i zbuduj swoje rozwiązanie.

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Ustaw projekty startowe**.

1. Wybierz pozycję**Projekt uruchamiania** **wspólnych właściwości,** > a następnie wybierz pozycję **Wiele projektów startowych**.

1. Upewnij `SimulateDeviceMethods` się, że znajduje się `ScheduleJob`na początku listy, po której następuje . Ustaw obie ich akcje na **Początek** i wybierz **przycisk OK**.

1. Uruchom projekty, klikając przycisk **Start** lub przejdź do menu **Debugowanie** i kliknij przycisk **Rozpocznij debugowanie**.

   Dane wyjściowe są widoczne zarówno z aplikacji urządzenia, jak i zaplecza.

    ![Uruchamianie aplikacji w celu planowania zadań](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania, aby zaplanować metodę bezpośrednią do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

* Aby kontynuować pracę z Usługą IoT Hub i wzorcami zarządzania urządzeniami, takimi jak zdalna aktualizacja oprogramowania układowego, przeczytaj [samouczek: Jak wykonać aktualizację oprogramowania układowego](tutorial-firmware-update.md).

* Aby dowiedzieć się więcej o wdrażaniu sztucznej inteligencji na urządzeniach brzegowych za pomocą usługi Azure IoT Edge, zobacz [Wprowadzenie do usługi IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
