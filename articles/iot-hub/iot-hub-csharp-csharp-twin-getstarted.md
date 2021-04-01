---
title: Wprowadzenie do usługi Azure IoT Hub Device bliźniaczych reprezentacji (.NET/.NET) | Microsoft Docs
description: Jak dodać tagi przy użyciu usługi Azure IoT Hub Device bliźniaczych reprezentacji, a następnie użyć kwerendy IoT Hubowej. Zestaw SDK urządzeń Azure IoT dla platformy .NET służy do implementowania aplikacji symulowanego urządzenia i zestawu SDK usługi Azure IoT dla platformy .NET w celu zaimplementowania aplikacji usługi, która dodaje Tagi i uruchamia kwerendę IoT Hubową.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 267a69486dc91ef95c0de704346eeb1d1780ef48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89013762"
---
# <a name="get-started-with-device-twins-net"></a>Wprowadzenie do bliźniaczych reprezentacji urządzeń (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

W tym samouczku utworzysz następujące aplikacje konsolowe platformy .NET:

* **AddTagsAndQuery**. Ta aplikacja zaplecza dodaje Tagi i kwerendy bliźniaczych reprezentacji urządzeń.

* **ReportConnectivity**. Ta aplikacja urządzenia symuluje urządzenie, które nawiązuje połączenie z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia i zgłasza warunek łączności.

> [!NOTE]
> Artykuł [Azure IoT SDK](iot-hub-devguide-sdks.md) zawiera informacje na temat zestawów SDK usługi Azure IoT, których można użyć do tworzenia aplikacji zarówno dla urządzeń, jak i zaplecza.
>

## <a name="prerequisites"></a>Wymagania wstępne

* Programu Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsolową .NET przy użyciu języka C#, która dodaje metadane lokalizacji do sznurka urządzenia skojarzonego z **myDeviceId**. Następnie wysyła zapytanie do bliźniaczych reprezentacji urządzenia przechowywanego w usłudze IoT Hub, wybierając urządzenia znajdujące się w Stanach Zjednoczonych, a następnie te, które zgłosiły połączenie komórkowe.

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nadaj projektowi nazwę **AddTagsAndQuery**.

    ![Konfigurowanie projektu AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **AddTagsAndQuery** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. Wybierz pozycję **Przeglądaj** i Wyszukaj, a następnie wybierz pozycję **Microsoft. Azure. Devices**. Wybierz pozycję **Zainstaluj**.

    ![Okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Dodaj następujące pola do klasy **Program**: Zamień na `{iot hub connection string}` IoT Hub parametry połączenia, które zostały skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Dodaj następującą metodę do klasy **program** :

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    Klasa **registrymanager** udostępnia wszystkie metody wymagane do współpracy z bliźniaczych reprezentacji urządzeń z usługi. Poprzedni kod najpierw inicjuje obiekt **registrymanager** , a następnie pobiera sznurki urządzenia dla **myDeviceId**, a wreszcie aktualizuje Tagi o informacje o żądanych lokalizacjach.

    Po aktualizacji wykonuje dwa zapytania: pierwszy wybiera tylko urządzenie bliźniaczych reprezentacji urządzeń znajdujące się w zakładzie **Redmond43** , a drugi zawęża zapytanie w celu wybrania tylko tych urządzeń, które są również połączone przez sieć komórkową.

    Poprzedni kod, gdy tworzy obiekt **zapytania** , określa maksymalną liczbę zwracanych dokumentów. Obiekt **zapytania** zawiera właściwość logiczną **HasMoreResults** , która umożliwia wywoływanie metod **GetNextAsTwinAsync** wiele razy, aby pobrać wszystkie wyniki. Metoda o nazwie **GetNextAsJson** jest dostępna dla wyników, które nie są bliźniaczych reprezentacji urządzenia, na przykład wyników zapytań agregacji.

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Uruchom tę aplikację, klikając prawym przyciskiem myszy projekt **AddTagsAndQuery** i wybierając pozycję **Debuguj**, a następnie polecenie **Uruchom nowe wystąpienie**. Powinno zostać wyświetlone jedno urządzenie w wynikach zapytania z pytaniem o wszystkie urządzenia znajdujące się w **Redmond43** i brak dla zapytania, które ogranicza wyniki do urządzeń korzystających z sieci komórkowej.

    ![Wyniki zapytania w oknie](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

W następnej sekcji utworzysz aplikację urządzenia, która zgłosi informacje o łączności i zmieni wynik zapytania w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsolową platformy .NET, która łączy się z centrum jako **myDeviceId**, a następnie aktualizuje raportowane właściwości w taki sposób, aby zawierała informacje, które są połączone z siecią komórkową.

1. W programie Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nadaj projektowi nazwę **ReportConnectivity**. W obszarze **rozwiązanie** wybierz opcję **Dodaj do rozwiązania**, a następnie wybierz pozycję **Utwórz**.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **ReportConnectivity** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. Wybierz pozycję **Przeglądaj** i Wyszukaj, a następnie wybierz pozycję **Microsoft. Azure. Devices. Client**. Wybierz pozycję **Zainstaluj**.

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK urządzenia usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Dodaj następujące pola do klasy **Program**: Zamień na `{device connection string}` Parametry połączenia urządzenia zanotowane w [zarejestrowaniu nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Dodaj następującą metodę do klasy **program** :

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Obiekt **Client** uwidacznia wszystkie metody wymagane do współpracy z urządzeniem bliźniaczych reprezentacji z urządzenia. Kod pokazany powyżej inicjuje obiekt **klienta** , a następnie pobiera sznurki urządzenia dla **myDeviceId**.

1. Dodaj następującą metodę do klasy **program** :

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Powyższy kod aktualizuje raportowaną Właściwość  **myDeviceId** z informacjami o łączności.

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Ustaw projekty startowe**.

1. W polu  >  **projekt uruchomieniowy** typowych właściwości wybierz pozycję **wiele projektów startowych**. Dla **ReportConnectivity** wybierz pozycję **Rozpocznij** jako **akcję**. Wybierz przycisk **OK**, aby zapisać zmiany.  

1. Uruchom tę aplikację, klikając prawym przyciskiem myszy projekt **ReportConnectivity** i wybierając polecenie **Debuguj**, a następnie **Rozpocznij nowe wystąpienie**. Powinna zostać wyświetlona aplikacja, która pobiera informacje o bliźniaczych, a następnie wysyła łączność jako **_raportowaną Właściwość_**.

    ![Uruchom aplikację urządzenia, aby zgłosić łączność](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Po zgłoszeniu informacji o łączności przez urządzenie powinno ono pojawić się w obu zapytaniach.

1. Kliknij prawym przyciskiem myszy projekt **AddTagsAndQuery** i wybierz polecenie **Debuguj**  >  **Uruchom nowe wystąpienie** , aby ponownie uruchomić zapytania. Tym razem **myDeviceId** powinien pojawić się w obu wynikach zapytania.

    ![Pomyślnie zgłoszono łączność urządzenia](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Metadane urządzenia zostały dodane jako Tagi z aplikacji zaplecza i zapisały symulowaną aplikację urządzenia do raportowania informacji o łączności urządzenia w ramach sznurka urządzenia. Dowiesz się również, jak wykonywać zapytania dotyczące tych informacji przy użyciu języka zapytań IoT Hub w programie SQL Server.

Więcej informacji można znaleźć w następujących zasobach:

* Aby dowiedzieć się, jak wysyłać dane telemetryczne z urządzeń, zapoznaj się z samouczkiem [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md) .

* Aby dowiedzieć się, jak skonfigurować urządzenia za pomocą odpowiednich właściwości z sznurka urządzenia, zobacz Samouczek dotyczący [konfigurowania urządzeń](tutorial-device-twins.md) .

* Aby dowiedzieć się, jak sterować urządzeniami interaktywnie, na przykład włączania wentylatorów z poziomu aplikacji sterowanej przez użytkownika, Skorzystaj z samouczka [Korzystanie z metod bezpośrednich](quickstart-control-device-dotnet.md) .
