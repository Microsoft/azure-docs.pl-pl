---
title: Komunikaty z chmury do urządzeń za pomocą usługi Azure IoT Hub (.NET) | Microsoft Docs
description: Jak wysyłać komunikaty z chmury do urządzenia z usługi Azure IoT Hub za pomocą zestawów SDK usługi Azure IoT dla platformy .NET. Użytkownik modyfikuje aplikację urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikować aplikację zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: ff6153abb3e930e3268ed7768e4ab44c9b5824cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449560"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Wysyłanie komunikatów z chmury do urządzenia przy użyciu IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md) pokazuje, jak utworzyć Centrum IoT, zainicjować w nim tożsamość urządzenia i kod aplikacji urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek kompiluje na temat [wysyłania danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md). Przedstawiono w nim sposób wykonywania następujących zadań:

* Z zaplecza rozwiązania Wyślij komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem IoT Hub.

* Odbieraj komunikaty z chmury do urządzenia na urządzeniu.

* Z zaplecza rozwiązania Poproś o potwierdzenie dostarczenia (*Opinie*) o komunikatach wysyłanych do urządzenia z IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzeń można znaleźć w temacie [D2C and C2D Messaging with IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchamiasz dwie aplikacje konsolowe programu .NET.

* **SimulatedDevice**. Ta aplikacja nawiązuje połączenie z Centrum IoT Hub i odbiera komunikaty z chmury do urządzenia. Ta aplikacja jest zmodyfikowaną wersją aplikacji utworzonej w wysyłanie danych [telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Ta aplikacja wysyła komunikat z chmury do urządzenia do aplikacji urządzenia za pomocą IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń, w tym C, Java, Python i JavaScript, za pomocą [zestawów SDK urządzeń usługi Azure IoT](iot-hub-devguide-sdks.md). Instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z tym kodem samouczka i ogólnie do usługi Azure IoT Hub można znaleźć w [przewodniku dewelopera IoT Hub](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Wymagania wstępne

* Visual Studio

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Odbieranie wiadomości w aplikacji urządzenia

W tej sekcji zmodyfikuj aplikację urządzenia utworzoną w obszarze Wysyłanie danych [telemetrycznych z urządzenia do usługi IoT Hub](quickstart-send-telemetry-dotnet.md) , aby odbierać komunikaty z chmury do urządzenia z Centrum IoT Hub.

1. W programie Visual Studio w projekcie **SimulatedDevice** Dodaj następującą metodę do klasy **SimulatedDevice** .

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Dodaj następującą metodę w metodzie **Main** , bezpośrednio przed `Console.ReadLine()` wierszem:

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync`Metoda asynchronicznie zwraca odebraną wiadomość w momencie odebrania jej przez urządzenie. Zwraca *wartość null* po określonym okresie limitu czasu. W tym przykładzie użyto domyślnie jednej minuty. Gdy aplikacja otrzymuje *wartość null*, powinna nadal czekać na nowe komunikaty. To wymaganie jest przyczyną dla `if (receivedMessage == null) continue` wiersza.

Wywołanie `CompleteAsync()` powiadomienia powiadamia IoT Hub o pomyślnym przetworzeniu komunikatu oraz o tym, że komunikat można bezpiecznie usunąć z kolejki urządzeń. Urządzenie powinno wywołać tę metodę, gdy przetwarzanie zostało pomyślnie zakończone, niezależnie od używanego protokołu.

W przypadku AMQP i HTTPS, ale nie MQTT, urządzenie może również:

* Porzuć komunikat, co spowoduje IoT Hub zachowywanie komunikatu w kolejce urządzeń w celu użycia w przyszłości.
* Odrzuć komunikat, który trwale usuwa komunikat z kolejki urządzeń.

W przypadku, gdy coś się nie powiedzie, że urządzenie zakończy działanie, porzucanie lub odrzucanie komunikatu, IoT Hub po upływie ustalonego limitu czasu będzie kolejkować komunikat o dostarczeniu ponownie. Z tego powodu logika przetwarzania komunikatów w aplikacji urządzenia musi być *idempotentne*, dzięki czemu ten sam komunikat wielokrotnie daje ten sam wynik.

Aby uzyskać szczegółowe informacje na temat sposobu, w jaki IoT Hub przetwarza komunikatów z chmury do urządzenia, w tym szczegółowe informacje o cyklu życia komunikatów z chmury do urządzenia, zobacz [wysyłanie komunikatów z chmury do urządzeń z Centrum IoT](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> W przypadku korzystania z protokołu HTTPS zamiast MQTT lub AMQP jako transportu `ReceiveAsync` Metoda wraca natychmiast. Obsługiwany wzorzec dla komunikatów z chmury do urządzenia z protokołem HTTPS jest sporadycznie połączonymi urządzeniami, które często sprawdzają obecność komunikatów (co najmniej 25 minut). Wydawanie większej liczby odbieranych przez protokół HTTPS powoduje IoT Hub ograniczanie żądań. Aby uzyskać więcej informacji o różnicach między obsługą MQTT, AMQP i HTTPS, zobacz [wskazówki dotyczące komunikacji z chmury do urządzeń](iot-hub-devguide-c2d-guidance.md) i [Wybierz protokół komunikacyjny](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md). Aby można było wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnień do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatu z chmury do urządzenia

W tej sekcji utworzysz aplikację konsolową .NET, która wysyła komunikaty z chmury do urządzenia do aplikacji symulowanego urządzenia.

1. W bieżącym rozwiązaniu programu Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. W obszarze **Utwórz nowy projekt**wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. Nazwij projekt *SendCloudToDevice*. W obszarze **rozwiązanie**wybierz pozycję **Dodaj do rozwiązania** i zaakceptuj najnowszą wersję .NET Framework. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

   ![Konfigurowanie nowego projektu w programie Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy nowy projekt, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W obszarze **Zarządzaj pakietami NuGet**wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **Microsoft. Azure. Devices**. Wybierz pozycję  **Zainstaluj**.

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do [pakietu NuGet zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Dodaj następującą `using` instrukcję w górnej części pliku **program.cs** .

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Dodaj następujące pola do klasy **Program**: Zastąp `{iot hub connection string}` wartość symbolu zastępczego parametrami połączenia usługi IoT Hub zanotowanymi wcześniej w temacie [pobieranie parametrów połączenia usługi IoT Hub](#get-the-iot-hub-connection-string). Zastąp `{device id}` wartość symbolu zastępczego identyfikatorem urządzenia dodanego w polu [Wyślij telemetrię z urządzenia do](quickstart-send-telemetry-dotnet.md) przewodnika Szybki Start dotyczącego usługi IoT Hub.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. Dodaj następującą metodę do klasy **program** , aby wysłać komunikat do urządzenia.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

1. Na koniec Dodaj następujące wiersze do metody **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie wybierz pozycję **Ustaw projekty startowe**.

1. W polu **Common Properties**  >  **projekt startowy**właściwości wspólne wybierz opcję **wiele projektów startowych**, a następnie wybierz akcję **Rozpocznij** dla **SimulatedDevice** i **SendCloudToDevice**. Wybierz przycisk **OK**, aby zapisać zmiany.

1. Naciśnij klawisz **F5**. Należy uruchomić obie aplikacje. Wybierz okno **SendCloudToDevice** , a następnie naciśnij klawisz **Enter**. Powinien zostać wyświetlony komunikat, który jest odbierany przez aplikację urządzenia.

   ![Komunikat dotyczący odebrania aplikacji urządzenia](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Otrzymywanie opinii o dostawie

Można zażądać potwierdzenia dostarczenia (lub wygaśnięcia) z IoT Hub dla każdej wiadomości z chmury do urządzenia. Ta opcja umożliwia zaplecze rozwiązania łatwe informowanie logiki ponawiania lub kompensacji. Aby uzyskać więcej informacji na temat opinii z chmury do urządzeń, zobacz [D2C i C2D Messaging with IoT Hub](iot-hub-devguide-messaging.md).

W tej sekcji zmodyfikujesz aplikację **SendCloudToDevice** , aby żądać opinii i odbierać ją z Centrum IoT.

1. W programie Visual Studio w projekcie **SendCloudToDevice** Dodaj następującą metodę do klasy **program** .

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Uwaga Ten wzorzec odbierania jest taki sam, jak w przypadku otrzymywania komunikatów z chmury do urządzenia z aplikacji urządzenia.

1. Dodaj następujący wiersz w metodzie **Main** , po prawej stronie `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Aby zażądać opinii o dostarczeniu komunikatu z chmury do urządzenia, należy określić właściwość w metodzie **SendCloudToDeviceMessageAsync** . Dodaj następujący wiersz, bezpośrednio po `var commandMessage = new Message(...);` wierszu.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Uruchom aplikacje, naciskając klawisz **F5**. Powinny pojawić się oba uruchomienia aplikacji. Wybierz okno **SendCloudToDevice** , a następnie naciśnij klawisz **Enter**. Powinien pojawić się komunikat z informacją, że odebrana przez aplikację urządzenia, a po upływie kilku sekund komunikat o opinii jest odbierany przez aplikację **SendCloudToDevice** .

   ![Aplikacja urządzenia, która otrzymuje informację o komunikacie i usłudze](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Dla uproszczenia w tym samouczku nie są implementowane żadne zasady ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania, takie jak wycofywania wykładniczy, zgodnie z sugestią w [przejściowej obsłudze błędów](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Następne kroki

W tym instruktażu pokazano, jak wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zapoznać się z przykładami kompletnych kompleksowych rozwiązań, które używają IoT Hub, zobacz [Akcelerator rozwiązania do monitorowania zdalnego usługi Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide.md).
