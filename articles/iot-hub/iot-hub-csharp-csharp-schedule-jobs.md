---
title: Planowanie zadań przy użyciu usługi Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Jak zaplanować zadanie IoT Hub platformy Azure w celu wywołania metody bezpośredniej na wielu urządzeniach. Użyj zestawu SDK urządzeń Azure IoT dla platformy .NET, aby zaimplementować aplikacje symulowanych urządzeń i aplikację usługi w celu uruchomienia zadania.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 0391afe5562238f488fde7e50d914c46a5c32944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142467"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Planowanie i emitowanie zadań (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub umożliwia planowanie i śledzenie zadań, które aktualizują miliony urządzeń. Użyj zadań do:

* Aktualizowanie żądanych właściwości

* Aktualizowanie tagów

* Wywoływanie metod bezpośrednich

Zadanie otacza jedną z tych akcji i śledzi wykonywanie na zestawie urządzeń, które są zdefiniowane przez zapytanie o pojedyncze urządzenie. Na przykład aplikacja zaplecza może użyć zadania do wywołania metody bezpośredniej na 10 000 urządzeniach, które ponownie uruchamiają urządzenia. Należy określić zestaw urządzeń z zapytaniem o sznurki urządzenia i zaplanować zadanie do uruchomienia w przyszłości. Zadanie śledzi postęp, ponieważ każde z urządzeń odbiera i wykonuje metodę bezpośredniego ponownego uruchomienia.

Aby dowiedzieć się więcej o każdej z tych funkcji, zobacz:

* Sznurki i właściwości urządzenia: [wprowadzenie do bliźniaczych reprezentacjii urządzeń](iot-hub-csharp-csharp-twin-getstarted.md) i [Samouczek: jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [przewodnik dewelopera IoT Hub — bezpośrednie metody](iot-hub-devguide-direct-methods.md) i [Samouczek: Używanie metod bezpośrednich](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację urządzenia, która implementuje metodę bezpośrednią o nazwie **LockDoor**, która może być wywoływana przez aplikację zaplecza.

* Utwórz aplikację zaplecza, która tworzy zadanie wywołujące metodę **LockDoor** Direct na wielu urządzeniach. Inne zadanie wysyła do wielu urządzeń żądane aktualizacje właściwości.

Na końcu tego samouczka masz dwie aplikacje konsolowe platformy .NET (C#):

* **SimulateDeviceMethods**. Ta aplikacja łączy się z Centrum IoT Hub i implementuje metodę **LockDoor** Direct.

* **ScheduleJob**. Ta aplikacja używa zadań do wywołania metody **LockDoor** Direct i zaktualizowania odpowiednich właściwości na wielu urządzeniach.

## <a name="prerequisites"></a>Wymagania wstępne

* Programu Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową .NET, która reaguje na metodę bezpośrednią wywoływaną przez zaplecze rozwiązania.

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**, a następnie wybierz szablon projektu **aplikacja konsoli (.NET Framework)** . Wybierz przycisk **Dalej**, aby kontynuować.

1. W obszarze **Konfigurowanie nowego projektu** Nazwij projekt *SimulateDeviceMethods*, a następnie wybierz pozycję **Utwórz**.

    ![Konfigurowanie projektu SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **SimulateDeviceMethods** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet** wybierz pozycję **Przeglądaj** i Wyszukaj, a następnie wybierz pozycję **Microsoft. Azure. Devices. Client**. Wybierz pozycję **Zainstaluj**.

    ![Aplikacja kliencka Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK urządzenia usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia, które zostały zanotowane w poprzedniej sekcji:

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

1. Dodaj następującą metodę, aby zaimplementować odbiornik bliźniaczych reprezentacji urządzenia na urządzeniu:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Na koniec Dodaj następujący kod do metody **Main** , aby otworzyć połączenie z Centrum IoT Hub i zainicjować odbiornik metody:

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

1. Zapisz swoją służbę i skompiluj swoje rozwiązanie.

> [!NOTE]
> Aby zachować prostotę, ten samouczek nie implementuje żadnych zasad ponawiania. W polu kod produkcyjny należy zaimplementować zasady ponawiania (takie jak próba połączenia), zgodnie z opisem w temacie [przejściowa obsługa błędów](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planowanie zadań związanych z wywoływaniem metody bezpośredniej i wysyłaniem aktualizacji bliźniaczych urządzeń

W tej sekcji utworzysz aplikację konsolową .NET (przy użyciu języka C#), która używa zadań do wywołania metody **LockDoor** Direct i wysyłania wymaganych aktualizacji właściwości na wiele urządzeń.

1. W programie Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nadaj projektowi nazwę *ScheduleJob*. W obszarze **rozwiązanie** wybierz opcję **Dodaj do rozwiązania**, a następnie wybierz pozycję **Utwórz**.

    ![Nazwa i konfiguracja projektu ScheduleJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **ScheduleJob** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet** wybierz pozycję **Przeglądaj**, Wyszukaj i wybierz pozycję **Microsoft. Azure. Devices**, a następnie wybierz pozycję **Zainstaluj**.

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Dodaj następującą `using` instrukcję, jeśli nie jest jeszcze obecna w instrukcjach domyślnych.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp symbole zastępcze IoT Hub parametrami połączenia, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string) i nazwę urządzenia.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Dodaj następującą metodę do klasy **program** :

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

1. Dodaj następującą metodę do klasy **program** :

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

1. Dodaj kolejną metodę do klasy **program** :

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
    > Aby uzyskać więcej informacji na temat składni zapytań, zobacz [IoT Hub języka zapytań](./iot-hub-devguide-query-language.md).
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

1. Zapisz swoją służbę i skompiluj swoje rozwiązanie.

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W Eksplorator rozwiązań Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz polecenie **Ustaw projekty startowe**.

1. Wybierz kolejno pozycje **wspólne właściwości**  >  **projekt startowy**, a następnie wybierz **wiele projektów startowych**.

1. Upewnij `SimulateDeviceMethods` się, że znajduje się w górnej części listy, a następnie `ScheduleJob` . Ustaw obie akcje do **uruchomienia** i wybierz **przycisk OK**.

1. Uruchom projekty, klikając przycisk **Start** lub przejdź do menu **Debuguj** , a następnie kliknij **Rozpocznij debugowanie**.

   Zobaczysz dane wyjściowe z urządzeń i aplikacji zaplecza.

    ![Uruchamianie aplikacji w celu planowania zadań](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania do zaplanowania metody bezpośredniej dla urządzenia i aktualizacji właściwości sznurka urządzenia.

* Aby nadal zacząć korzystać z wzorców IoT Hub i zarządzania urządzeniami, takimi jak zdalne przez aktualizację oprogramowania układowego, Przeczytaj [Samouczek: jak wykonać aktualizację oprogramowania układowego](tutorial-firmware-update.md).

* Aby dowiedzieć się więcej o wdrażaniu AI na urządzeniach brzegowych za pomocą Azure IoT Edge, zobacz [wprowadzenie do IoT Edge](../iot-edge/quickstart-linux.md).