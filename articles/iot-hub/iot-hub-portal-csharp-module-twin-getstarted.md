---
title: Tożsamość modułu IoT Hub platformy Azure &a sznurki modułowe (Portal i .NET)
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu portalu i platformy.NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 0d16d7a6dbf903ae790015c12e3d34e4a2a553d3
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92139351"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi IoT Hub przy użyciu portalu i urządzenia platformy .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. W czasie, gdy usługa Azure IoT Hub tożsamość urządzenia i sznurki urządzenia umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewnienie widoczności warunków urządzenia, tożsamość modułu i sznurki modułowe zapewniają te możliwości dla poszczególnych składników urządzenia. Na urządzeniach z obsługą wielu składników, takich jak urządzenia z systemem operacyjnym lub urządzenia oprogramowania układowego, tożsamości modułów i moduł bliźniaczych reprezentacji umożliwiają wyizolowaną konfigurację i warunki dla każdego składnika.
>

Z tego samouczka dowiesz się:

* Jak utworzyć tożsamość modułu w portalu.

* Używanie zestawu SDK urządzenia .NET do aktualizowania sznurka modułu z urządzenia.

> [!NOTE]
> Aby uzyskać informacje na temat zestawów SDK usługi Azure IoT, których można użyć do tworzenia aplikacji do uruchamiania na urządzeniach i zaplecza rozwiązania, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Wymagania wstępne

* Programu Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

## <a name="create-a-hub"></a>Tworzenie koncentratora

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Rejestrowanie nowego urządzenia w centrum

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Tworzenie tożsamości modułu w portalu

W ramach jednej tożsamości urządzenia możesz utworzyć maksymalnie 20 tożsamości modułu. Aby dodać tożsamość, wykonaj następujące kroki:

1. W przypadku urządzenia utworzonego w poprzedniej sekcji Wybierz pozycję **Dodaj tożsamość modułu** , aby utworzyć pierwszą tożsamość modułu.

1. Wprowadź nazwę *myFirstModule*. Zapisz tożsamość modułu.

    ![Dodaj tożsamość modułu](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Nowa tożsamość modułu pojawia się u dołu ekranu. Wybierz go, aby wyświetlić szczegóły tożsamości modułu.

    ![Zobacz szczegóły tożsamości modułu](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Zapisz **ciąg połączenia — klucz podstawowy**. Użyjesz go w następnej sekcji, aby skonfigurować moduł na urządzeniu.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

Pomyślnie utworzono tożsamość modułu w centrum IoT Hub. Spróbujmy nawiązać połączenie z chmurą z poziomu urządzenia symulowanego. Po utworzeniu tożsamości modułu w centrum IoT Hub jest niejawnie tworzona bliźniacza reprezentacja modułu. W tej sekcji utworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

### <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Aby utworzyć aplikację, która aktualizuje raportowane właściwości w module, wykonaj następujące czynności:

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**, a następnie wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu**wprowadź *UpdateModuleTwinReportedProperties* jako **nazwę projektu**. Wybierz pozycję **Utwórz** , aby kontynuować.

    ![Konfigurowanie projektu programu Visual Studio](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Zainstaluj najnowszą wersję zestawu SDK urządzeń usługi Azure IoT Hub .NET

Tożsamość modułu i sznurki modułu są w publicznej wersji zapoznawczej. Jest on dostępny tylko w zestawach SDK urządzeń IoT Hub w wersji wstępnej. Aby go zainstalować, wykonaj następujące kroki:

1. W programie Visual Studio Otwórz pozycję **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **Zarządzanie pakietami NuGet dla rozwiązania**.

1. Wybierz pozycję **Przeglądaj**, a następnie wybierz pozycję **Uwzględnij wersję wstępną**. Wyszukaj ciąg *Microsoft. Azure. Devices. Client*. Wybierz najnowszą wersję i zainstaluj.

    ![Instalowanie zestawu SDK usługi Azure IoT Hub .NET w wersji zapoznawczej](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Teraz masz dostęp do wszystkich funkcji modułu.

### <a name="get-your-module-connection-string"></a>Pobierz parametry połączenia modułu

Potrzebujesz parametrów połączenia modułu dla aplikacji konsolowej. Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do centrum IoT Hub i wybierz pozycję **urządzenia IoT**. Otwórz **myFirstDevice** i zobaczysz, że **myFirstModule** został pomyślnie utworzony.

1. Wybierz pozycję **myFirstModule** w obszarze **tożsamości modułów**. W obszarze **szczegóły tożsamości modułu**Skopiuj **Parametry połączenia (klucz podstawowy)**.

    ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Tworzenie aplikacji konsolowej UpdateModuleTwinReportedProperties

Aby utworzyć aplikację, wykonaj następujące kroki:

1. Dodaj następujące `using` instrukcje w górnej części pliku **program.cs** :

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

4. Na koniec Zastąp metodę **Main** następującym kodem:

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
  
  Możesz skompilować i uruchomić tę aplikację za pomocą klawisza **F5**.

Ten przykładowy kod przedstawia sposób pobierania bliźniaczej reprezentacji modułu i aktualizacji zgłoszonych właściwości za pomocą protokołu AMQP. W publicznej wersji zapoznawczej na potrzeby operacji bliźniaczych reprezentacji modułów obsługujemy tylko protokół AMQP.

## <a name="next-steps"></a>Następne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi IoT Hub przy użyciu kopii zapasowej platformy .NET i urządzenia platformy .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Getting started with IoT Edge](../iot-edge/quickstart-linux.md) (Wprowadzenie do usługi IoT Edge)