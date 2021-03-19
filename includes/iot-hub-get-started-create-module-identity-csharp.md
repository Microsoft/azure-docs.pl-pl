---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68883836"
---
## <a name="create-a-module-identity"></a>Tworzenie tożsamości modułu

W tej sekcji utworzysz aplikację konsolową platformy .NET, która tworzy tożsamość urządzenia i tożsamość modułu w rejestrze tożsamości w centrum. Urządzenie lub moduł nie może połączyć się z centrum, chyba że ma wpis w rejestrze tożsamości. Więcej informacji znajduje się w sekcji [Identity registry (Rejestr tożsamości) artykułu IoT Hub developer guide](../articles/iot-hub/iot-hub-devguide-identity-registry.md) (Usługa IoT Hub — przewodnik dewelopera).

Uruchomienie tej aplikacji konsolowej powoduje wygenerowanie unikatowego identyfikatora i klucza zarówno dla urządzenia, jak i modułu. Twoje urządzenie i moduł używają tych wartości, aby identyfikować je podczas wysyłania komunikatów z urządzenia do chmury do IoT Hub. W identyfikatorach jest uwzględniana wielkość liter.

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)**.

1. Wybierz pozycję **dalej** , aby otworzyć okno **Konfigurowanie nowego projektu**. Nadaj projektowi nazwę *CreateIdentities*, a rozwiązaniu *IoTHubGetStarted*. Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej.

    ![Podaj nazwę i strukturę rozwiązania programu Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. W programie Visual Studio Otwórz pozycję **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **Zarządzanie pakietami NuGet dla rozwiązania**. Wybierz kartę **Przeglądaj**.

1. Wyszukaj ciąg **Microsoft. Azure. Devices**. Zaznacz go, a następnie wybierz pozycję **Zainstaluj**.

    ![Zainstaluj bieżącą wersję zestawu SDK usługi .NET IoT Hub platformy Azure](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia usługi IoT Hub dla centrum IoT utworzonego w poprzedniej sekcji.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Dodaj następujący kod do klasy **Main** .

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Dodaj następujące metody do klasy **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    `AddDeviceAsync`Metoda tworzy tożsamość urządzenia o identyfikatorze **myFirstDevice**. Jeśli ten identyfikator urządzenia już istnieje w rejestrze tożsamości, kod po prostu pobiera istniejące informacje o urządzeniu. Aplikacja następnie wyświetla klucz podstawowy dla tej tożsamości. Ten klucz jest używany w aplikacji symulowanego urządzenia do łączenia się z centrum.

    `AddModuleAsync`Metoda tworzy tożsamość modułu o identyfikatorze **myFirstModule** w obszarze Device **myFirstDevice**. Jeśli ten identyfikator modułu już istnieje w rejestrze tożsamości, kod po prostu pobiera informacje o istniejących modułach. Aplikacja następnie wyświetla klucz podstawowy dla tej tożsamości. Ten klucz jest używany w aplikacji symulowanego modułu do łączenia się z centrum.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Uruchom tę aplikację i zanotuj klucz urządzenia i klucz modułu.

> [!NOTE]
> W rejestrze tożsamości IoT Hub są przechowywane tylko tożsamości urządzeń i modułów w celu zapewnienia bezpiecznego dostępu do centrum. W rejestrze tożsamości są przechowywane identyfikatory urządzeń i klucze służące jako poświadczenia zabezpieczeń. W rejestrze tożsamości są także przechowywane flagi włączenia/wyłączenia dla każdego urządzenia, za pomocą których można wyłączyć dostęp do danego urządzenia. Jeśli aplikacja wymaga przechowywania innych metadanych specyficznych dla urządzenia, należy użyć magazynu specyficznego dla aplikacji. Nie istnieje flaga włączenia/wyłączenia tożsamości modułów. Więcej informacji znajduje się w temacie [IoT Hub Developer Guide](../articles/iot-hub/iot-hub-devguide-identity-registry.md) (Usługa IoT Hub — przewodnik dewelopera).
