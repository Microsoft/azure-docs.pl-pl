---
title: Wprowadzenie do bliźniaczych urządzeń usługi Azure IoT Hub (.NET/.NET) | Dokumenty firmy Microsoft
description: Jak dodać tagi za pomocą bliźniamcych urządzeń usługi Azure IoT Hub, a następnie użyć kwerendy Centrum IoT. Zestaw SDK urządzenia Usługi Azure IoT dla platformy .NET służy do implementowania symulowanej aplikacji urządzenia i zestawu SDK usługi Azure IoT dla platformy .NET w celu zaimplementowania aplikacji usługi, która dodaje tagi i uruchamia kwerendę Centrum IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733130"
---
# <a name="get-started-with-device-twins-net"></a>Wprowadzenie do bliźniąt urządzeń (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

W tym samouczku utworzysz te aplikacje konsoli .NET:

* **Utwórz Tożsamość urządzeń**. Ta aplikacja tworzy tożsamość urządzenia i skojarzony klucz zabezpieczeń, aby połączyć symulowaną aplikację urządzenia.

* **AddTagsAndQuery**. Ta aplikacja zaplecza dodaje tagi i zapytania bliźniacze urządzenia.

* **ReportConnectivity**. Ta aplikacja urządzenia symuluje urządzenie, które łączy się z centrum IoT hub z tożsamości urządzenia utworzone wcześniej i raportuje jego stan łączności.

> [!NOTE]
> Artykuł [Zestaw SDK usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera informacje o zestawach SDK usługi Azure IoT, których można używać do tworzenia aplikacji na urządzeniach i zapleczu.
>

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsoli .NET przy użyciu języka C#, która dodaje metadane lokalizacji do bliźniaczej reprezentacji urządzenia skojarzonej z **identyfikatorem myDeviceId**. Następnie wysyła zapytanie do bliźniąt urządzeń przechowywanych w centrum IoT, wybierając urządzenia znajdujące się w Stanach Zjednoczonych, a następnie te, które zgłosiły połączenie komórkowe.

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfiguruj nowy projekt**nazwij projekt **AddTagsAndQuery**.

    ![Konfigurowanie projektu AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **AddTagsAndQuery,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Wybierz **pozycję Przeglądaj** i wyszukaj i wybierz pozycję **Microsoft.Azure.Devices**. Wybierz pozycję **Zainstaluj**.

    ![Okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet usługi Azure IoT i](https://www.nuget.org/packages/Microsoft.Azure.Devices/) jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Dodaj następujące pola do klasy **Program**: Zamień `{iot hub connection string}` na skopiowany ciąg połączenia usługi IoT Hub w pliku [Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Dodaj następującą metodę do **klasy Program:**

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

    **RegistryManager** Klasa udostępnia wszystkie metody wymagane do interakcji z bliźniaczych urządzeń z usługi. Poprzedni kod najpierw inicjuje obiekt **registryManager,** a następnie pobiera bliźniaczątkę urządzenia dla **myDeviceId**i na koniec aktualizuje swoje tagi z żądanymi informacjami o lokalizacji.

    Po aktualizacji wykonuje dwa zapytania: pierwszy wybiera tylko bliźniacze urządzenia urządzeń znajdujących się w zakładzie **Redmond43,** a drugi udoskonala kwerendę, aby wybrać tylko urządzenia, które są również połączone za pośrednictwem sieci komórkowej.

    Poprzedni kod, podczas tworzenia obiektu **kwerendy,** określa maksymalną liczbę zwróconych dokumentów. Obiekt **kwerendy** zawiera **HasMoreResults** właściwości logiczne, które można użyć do wywołania **GetNextAsTwinAsync** metody wiele razy, aby pobrać wszystkie wyniki. Metoda o nazwie **GetNextAsJson** jest dostępna dla wyników, które nie są bliźniaczymi reprezentacjami urządzeń, na przykład wyniki zapytań agregacji.

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Uruchom tę aplikację, klikając prawym przyciskiem myszy projekt **AddTagsAndQuery** i wybierając **debug**, a następnie **uruchom nowe wystąpienie**. W wynikach kwerendy powinna zostać wyświetlone jedno urządzenie z prośbą o wszystkie urządzenia znajdujące się w **redmond43,** a w przypadku kwerendy, która ogranicza wyniki, do urządzeń korzystających z sieci komórkowej.

    ![Wyniki kwerendy w oknie](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

W następnej sekcji utworzysz aplikację urządzenia, która raportuje informacje o łączności i zmienia wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli .NET, która łączy się z koncentratorem jako **myDeviceId**, a następnie aktualizuje jej zgłoszone właściwości, aby zawierały informacje, że jest ona połączona za pomocą sieci komórkowej.

1. W programie Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu**. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfigurowanie nowego projektu**nazwij projekt **ReportConnectivity**. W obszarze **Rozwiązanie**wybierz pozycję **Dodaj do rozwiązania**, a następnie wybierz pozycję **Utwórz**.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **ReportConnectivity,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Wybierz **pozycję Przeglądaj** i wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Client**. Wybierz pozycję **Zainstaluj**.

   Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet zestawu NuGet urządzenia Usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) i jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Dodaj następujące pola do klasy **Program**: Zamień `{device connection string}` na ciąg połączenia urządzenia, który został odnotowany w [zarejestruj nowe urządzenie w centrum IoT hub](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Dodaj następującą metodę do **klasy Program:**

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

    **Client** Obiekt udostępnia wszystkie metody, które są wymagane do interakcji z bliźniacze urządzenia z urządzenia. Kod pokazany powyżej inicjuje **client** obiektu, a następnie pobiera bliźniaczej reprezentacji urządzenia dla **myDeviceId**.

1. Dodaj następującą metodę do **klasy Program:**

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

   Powyższy kod aktualizuje zgłoszoną właściwość **myDeviceId** z informacjami o łączności.

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

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Ustaw projekty startowe**.

1. W **programie Common Properties** > **Startup Project**wybierz pozycję Wiele **projektów startowych**. W obszarze **ReportConnectivity**wybierz pozycję **Rozpocznij** jako **akcję**. Aby zapisać zmiany, wybierz pozycję **OK**.  

1. Uruchom tę aplikację, klikając prawym przyciskiem myszy projekt **ReportConnectivity** i wybierając **debugowanie**, a następnie **uruchom nowe wystąpienie**. Powinien zostać wyświetlony, że aplikacja otrzymuje bliźniacze informacje, a następnie wysyła łączność jako ***zgłoszoną właściwość.***

    ![Uruchamianie aplikacji urządzenia w celu raportowania łączności](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Po zgłosić informacje o łączności, powinny pojawić się w obu kwerendach.

1. Kliknij prawym przyciskiem myszy projekt **AddTagsAndQuery** i wybierz polecenie **Rozpocznij debugowanie** > **nowego wystąpienia,** aby ponownie uruchomić kwerendy. Tym razem **myDeviceId** powinien pojawić się w obu wynikach kwerendy.

    ![Łączność urządzenia została pomyślnie zgłoszona](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodano metadane urządzenia jako znaczniki z aplikacji zaplecza i napisałeś symulowaną aplikację urządzenia, aby zgłosić informacje o łączności urządzenia w bliźniaczej reprezentacji urządzenia. Dowiedzialiście się również, jak zbadać te informacje przy użyciu języka zapytań usługi IoT Hub podobnej do języka SQL.

Możesz dowiedzieć się więcej z następujących zasobów:

* Aby dowiedzieć się, jak wysyłać dane telemetryczne z urządzeń, zobacz [Wysyłanie danych telemetrycznych z urządzenia do samouczka centrum IoT](quickstart-send-telemetry-dotnet.md) hub.

* Aby dowiedzieć się, jak skonfigurować urządzenia przy użyciu żądanych właściwości bliźniaczej reprezentacji urządzenia, zobacz [Użyj żądanych właściwości, aby skonfigurować urządzenia](tutorial-device-twins.md) samouczek.

* Aby dowiedzieć się, jak interaktywnie sterować urządzeniami, na przykład włączanie wentylatora z aplikacji kontrolowanej przez użytkownika, zobacz Samouczek [Używanie metod bezpośrednich.](quickstart-control-device-dotnet.md)
