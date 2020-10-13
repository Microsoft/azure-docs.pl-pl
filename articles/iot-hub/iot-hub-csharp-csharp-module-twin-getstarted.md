---
title: Wprowadzenie do usługi Azure IoT Hub Identity & module (.NET)
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu zestawów SDK IoT dla platformy .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 3d683ada6401c4ba4f2e232948e9712643339fd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533472"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Rozpoczynanie pracy z modułem IoT Hub Identity and module (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. W czasie, gdy usługa Azure IoT Hub tożsamość urządzenia i sznurki urządzenia umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewnienie widoczności warunków urządzenia, tożsamość modułu i sznurki modułowe zapewniają te możliwości dla poszczególnych składników urządzenia. Na urządzeniach z obsługą wielu składników, takich jak urządzenia z systemem operacyjnym lub urządzenia oprogramowania układowego, tożsamości modułów i moduł bliźniaczych reprezentacji umożliwiają wyizolowaną konfigurację i warunki dla każdego składnika.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* Moje **tożsamości**. Ta aplikacja tworzy tożsamość urządzenia, tożsamość modułu i skojarzony klucz zabezpieczeń w celu połączenia klientów z urządzeniem i modułem.

* **UpdateModuleTwinReportedProperties**. Ta aplikacja wysyła zaktualizowane właściwości dotyczące sznurka modułu do centrum IoT Hub.

> [!NOTE]
> Artykuł [Azure IoT SDKs](iot-hub-devguide-sdks.md) (Zestawy SDK usługi Azure IoT) zawiera informacje dotyczące zestawów SDK usługi Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.

## <a name="prerequisites"></a>Wymagania wstępne

* Programu Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

## <a name="create-a-hub"></a>Tworzenie koncentratora

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

W tej sekcji tworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

Przed rozpoczęciem Pobierz parametry połączenia modułu. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Przejdź do centrum i wybierz pozycję **urządzenia IoT**. Znajdź **myFirstDevice**. Wybierz pozycję **myFirstDevice** , aby ją otworzyć, a następnie wybierz pozycję **myFirstModule** , aby ją otworzyć. W obszarze **szczegóły tożsamości modułu**Skopiuj **Parametry połączenia (klucz podstawowy)** w razie konieczności w poniższej procedurze.

   ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. W programie Visual Studio Dodaj nowy projekt do rozwiązania, wybierając pozycję **plik**  >  **Nowy**  >  **projekt**. W obszarze Utwórz nowy projekt wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz pozycję **dalej**.

1. Nadaj projektowi nazwę *UpdateModuleTwinReportedProperties*. W obszarze **rozwiązanie**wybierz pozycję **Dodaj do rozwiązania**. Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej.

    ![Tworzenie projektu programu Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć projekt.

1. W programie Visual Studio Otwórz pozycję **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **Zarządzanie pakietami NuGet dla rozwiązania**. Wybierz kartę **Przeglądaj**.

1. Wyszukaj i wybierz pozycję **Microsoft. Azure. Devices. Client**, a następnie wybierz pozycję **Zainstaluj**.

    ![Zrzut ekranu pokazujący wybraną wartość "Microsoft. Azure. Devices. Client" i wyróżniony przycisk "Zainstaluj".](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Dodaj następujące `using` instrukcje w górnej części pliku **program.cs** :

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

1. Dodaj następujące wiersze do metody **Main** :

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

1. Opcjonalnie można dodać te instrukcje do metody **Main** , aby wysłać zdarzenie do IoT Hub z modułu. Umieść te wiersze poniżej `try catch` bloku.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchamiać aplikacje.

1. W programie Visual Studio w **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz polecenie **Ustaw projekty startowe**.

1. W obszarze **wspólne właściwości**wybierz pozycję **projekt startowy.**

1. Wybierz opcję **wiele projektów startowych**, a następnie wybierz pozycję **Uruchom** jako akcję dla aplikacji, a następnie kliknij **przycisk OK** , aby zaakceptować zmiany.

1. Naciśnij klawisz **F5** , aby uruchomić aplikacje.

## <a name="next-steps"></a>Następne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md)

* [Getting started with IoT Edge](../iot-edge/tutorial-simulate-device-linux.md) (Wprowadzenie do usługi IoT Edge)
