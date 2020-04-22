---
title: Wprowadzenie do modułu Azure IoT Hub & bliźniaczej reprezentacji modułu (.NET)
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu zestawów SDK IoT dla platformy .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp
ms.openlocfilehash: 919d1e37e6066c78e83d58be4fe4667ec67e45ad
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733392"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Wprowadzenie do tożsamości modułu IoT Hub i bliźniaczej reprezentacji modułów (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Podczas gdy tożsamość urządzenia usługi Azure IoT Hub i bliźniacza reprezentacja urządzenia umożliwiają aplikacji zaplecza konfigurowanie urządzenia i zapewnia wgląd w warunki urządzenia, tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na urządzeniach obsługujących wiele składników, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia oprogramowania układowego, tożsamości modułów i bliźniacze moduły umożliwiają izolowane konfiguracje i warunki dla każdego składnika.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* **CreateIdentities**. Ta aplikacja tworzy tożsamość urządzenia, tożsamość modułu i skojarzony klucz zabezpieczeń, aby połączyć klientów urządzenia i modułu.

* **UpdateModuleTwinReportedProperties**. Ta aplikacja wysyła zaktualizowane bliźniaczej reprezentacji modułu zgłaszane właściwości do centrum IoT hub.

> [!NOTE]
> Artykuł [Azure IoT SDKs](iot-hub-devguide-sdks.md) (Zestawy SDK usługi Azure IoT) zawiera informacje dotyczące zestawów SDK usługi Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

## <a name="create-a-hub"></a>Tworzenie koncentratora

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

W tej sekcji tworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

Przed rozpoczęciem pobierz parametry połączenia modułu. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Przejdź do centrum i wybierz pozycję **Urządzenia IoT**. Znajdź **myFirstDevice**. Wybierz **myFirstDevice,** aby go otworzyć, a następnie wybierz **myFirstModule,** aby go otworzyć. W **module Identity Details**, skopiować parametry połączenia **(klucz podstawowy)** w razie potrzeby w poniższej procedurze.

   ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. W programie Visual Studio dodaj nowy projekt do rozwiązania, wybierając **pozycję Plik** > **nowego** > **projektu**. W obszarze Tworzenie nowego projektu wybierz pozycję **Aplikacja konsoli (.NET Framework)** i wybierz pozycję **Dalej**.

1. Nadaj projektowi nazwę *UpdateModuleTwinReportedProperties*. W przypadku **rozwiązania**wybierz pozycję **Dodaj do rozwiązania**. Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej.

    ![Tworzenie projektu programu Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Wybierz **pozycję Utwórz,** aby utworzyć projekt.

1. W programie Visual Studio otwórz **program Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution**. Wybierz kartę **Przeglądaj**.

1. Wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Client**, a następnie wybierz pozycję **Zainstaluj**.

    ![Instalowanie bieżącej wersji pakietu SDK usługi Azure IoT Hub .NET](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego na parametry połączenia modułu.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Dodaj następującą metodę **OnDesiredPropertyChanged** do klasy **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. Dodaj następujące wiersze do **Main** metody:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Ten przykładowy kod przedstawia sposób pobierania bliźniaczej reprezentacji modułu i aktualizacji zgłoszonych właściwości za pomocą protokołu AMQP. W publicznej wersji zapoznawczej na potrzeby operacji bliźniaczych reprezentacji modułów obsługujemy tylko protokół AMQP.

1. Opcjonalnie można dodać te instrukcje do **Main** metody, aby wysłać zdarzenie do usługi IoT Hub z modułu. Umieść te linie `try catch` poniżej bloku.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje.

1. W programie Visual Studio w **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Ustaw projekty startowe**.

1. W obszarze **Wspólne właściwości**wybierz pozycję **Projekt uruchamiania.**

1. Wybierz **pozycję Wiele projektów startowych**, a następnie wybierz pozycję **Start** jako akcję dla aplikacji i **OK,** aby zaakceptować zmiany.

1. Naciśnij **klawisz F5,** aby uruchomić aplikacje.

## <a name="next-steps"></a>Następne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md)

* [Getting started with IoT Edge](../iot-edge/tutorial-simulate-device-linux.md) (Wprowadzenie do usługi IoT Edge)
