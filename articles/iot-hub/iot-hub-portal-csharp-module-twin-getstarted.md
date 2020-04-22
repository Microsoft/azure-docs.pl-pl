---
title: Tożsamość modułu usługi Azure IoT Hub & bliźniaczej reprezentacji modułu (portal i .NET)
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu portalu i platformy.NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: a3258de2ed7269ab50e6feca3c421d55de5a9d91
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759785"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi IoT Hub przy użyciu portalu i urządzenia platformy .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Podczas gdy tożsamość urządzenia usługi Azure IoT Hub i bliźniacza reprezentacja urządzenia umożliwiają aplikacji zaplecza konfigurowanie urządzenia i zapewnia wgląd w warunki urządzenia, tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na urządzeniach obsługujących wiele składników, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia oprogramowania układowego, tożsamości modułów i bliźniacze moduły umożliwiają izolowane konfiguracje i warunki dla każdego składnika.
>

Z tego samouczka dowiesz się:

* Jak utworzyć tożsamość modułu w portalu.

* Jak zaktualizować moduł bliźniaczej reprezentacji modułu z urządzenia za pomocą pakietu SDK urządzenia .NET.

> [!NOTE]
> Aby uzyskać informacje na temat zestawów SDK usługi Azure IoT, których można używać do tworzenia zarówno aplikacji do uruchamiania na urządzeniach, jak i zaplecza rozwiązania, zobacz [zestawy SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)
>

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

## <a name="create-a-hub"></a>Tworzenie koncentratora

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Zarejestruj nowe urządzenie w centrum

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Tworzenie tożsamości modułu w portalu

W ramach jednej tożsamości urządzenia możesz utworzyć maksymalnie 20 tożsamości modułu. Aby dodać tożsamość, wykonaj następujące czynności:

1. Dla urządzenia utworzonego w poprzedniej sekcji wybierz pozycję **Dodaj tożsamość modułu,** aby utworzyć pierwszą tożsamość modułu.

1. Wprowadź nazwę *myFirstModule*. Zapisz swoją tożsamość modułu.

    ![Dodawanie tożsamości modułu](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Twoja nowa tożsamość modułu pojawi się u dołu ekranu. Wybierz go, aby wyświetlić szczegóły tożsamości modułu.

    ![Zobacz szczegóły tożsamości modułu](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Zapisz **ciąg Połącz — klucz podstawowy**. Używasz go w następnej sekcji, aby skonfigurować moduł na urządzeniu.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

Pomyślnie utworzono tożsamość modułu w centrum IoT Hub. Spróbujmy nawiązać połączenie z chmurą z poziomu urządzenia symulowanego. Po utworzeniu tożsamości modułu w centrum IoT Hub jest niejawnie tworzona bliźniacza reprezentacja modułu. W tej sekcji utworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

### <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Aby utworzyć aplikację, która aktualizuje właściwości zgłaszane bliźniaczej reprezentacji modułu, wykonaj następujące kroki:

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**, a następnie wybierz pozycję **Aplikacja konsoli (.NET Framework)** i wybierz pozycję **Dalej**.

1. W **obszarze Konfigurowanie nowego projektu**wprowadź funkcję *UpdateModuleTwinReportedProperties* jako **nazwę projektu**. Wybierz **pozycję Utwórz,** aby kontynuować.

    ![Konfigurowanie projektu visual studio](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instalowanie najnowszego pakietu SDK urządzenia usługi Azure IoT Hub .NET

Tożsamość modułu i bliźniaczej reprezentacji modułu jest w publicznej wersji zapoznawczej. Jest dostępna tylko w szafach SDK urządzeń wersji wstępnej usługi IoT Hub. Aby go zainstalować, wykonaj następujące kroki:

1. W programie Visual Studio otwórz **program Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution**.

1. Wybierz **pozycję Przeglądaj**, a następnie wybierz pozycję **Dołącz wydanie wstępne**. Wyszukaj *witrynę Microsoft.Azure.Devices.Client*. Wybierz najnowszą wersję i zainstaluj.

    ![Instalowanie pakietu SDK usługi Azure IoT Hub .NET](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Teraz masz dostęp do wszystkich funkcji modułu.

### <a name="get-your-module-connection-string"></a>Pobierz parametry połączenia modułu

Potrzebny jest ciąg połączenia modułu dla aplikacji konsoli. Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do centrum IoT i wybierz pozycję **Urządzenia IoT**. Otwórz **myFirstDevice** i zobaczysz, że **myFirstModule** został pomyślnie utworzony.

1. Wybierz **myFirstModule** w obszarze **Tożsamości modułu**. W **module Identity Details**, skopiuj ciąg połączenia **(klucz podstawowy)**.

    ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Tworzenie aplikacji konsoli UpdateModuleTwinReportedProperties

Aby utworzyć aplikację, wykonaj następujące czynności:

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego na parametry połączenia modułu.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Dodaj następującą metodę **OnDesiredPropertyChanged** do klasy **Program**:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

4. Na koniec zastąp **Main** metody z następującym kodem:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
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
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  Możesz tworzyć i uruchamiać tę aplikację za pomocą **F5**.

Ten przykładowy kod przedstawia sposób pobierania bliźniaczej reprezentacji modułu i aktualizacji zgłoszonych właściwości za pomocą protokołu AMQP. W publicznej wersji zapoznawczej na potrzeby operacji bliźniaczych reprezentacji modułów obsługujemy tylko protokół AMQP.

## <a name="next-steps"></a>Następne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi IoT Hub przy użyciu kopii zapasowej platformy .NET i urządzenia platformy .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Getting started with IoT Edge](../iot-edge/tutorial-simulate-device-linux.md) (Wprowadzenie do usługi IoT Edge)
