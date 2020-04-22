---
title: Komunikaty między chmurami a urządzeniem za pomocą usługi Azure IoT Hub (.NET) | Dokumenty firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia do urządzenia z centrum Usługi Azure IoT przy użyciu zestawów SDK usługi Azure IoT dla platformy .NET. Zmodyfikuj aplikację urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikuj aplikację zaplecza, aby wysyłać wiadomości z chmury do urządzenia.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 41c29e55f04f9edf06ba375ad4539e5fb3f82c18
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733416"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Wysyłanie wiadomości z chmury do urządzenia za pomocą usługi IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która pomaga umożliwić niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania. Wyślij [dane telemetryczne z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md) Szybki start pokazuje, jak utworzyć centrum IoT hub, aprowizować tożsamość urządzenia w nim i kod aplikacji urządzenia, który wysyła komunikaty urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się na [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-dotnet.md)hub . Pokazuje, jak wykonać następujące zadania:

* Z zaplecza rozwiązania wysyłaj komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem usługi IoT Hub.

* Odbieranie komunikatów z chmury do urządzenia na urządzeniu.

* W rozwiązaniu zaplecza, żądanie potwierdzenia dostarczania *(opinie)* dla wiadomości wysyłanych do urządzenia z Usługi IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzenia można znaleźć w aplikacjach [D2C i C2D Messaging z centrum IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchomisz dwie aplikacje konsoli .NET.

* **Symulowaneurządzenia**. Ta aplikacja łączy się z centrum IoT hub i odbiera komunikaty z chmury do urządzenia. Ta aplikacja jest zmodyfikowaną wersją aplikacji utworzonej w [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-dotnet.md)hub .

* **SendCloudToDevice**. Ta aplikacja wysyła komunikat z chmury do urządzenia do aplikacji urządzenia za pośrednictwem usługi IoT Hub, a następnie odbiera potwierdzenie dostarczenia.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń, w tym języka C, Java, Python i Javascript, za pośrednictwem [zestawów SDK urządzeń IoT platformy Azure.](iot-hub-devguide-sdks.md) Aby uzyskać instrukcje krok po kroku dotyczące podłączania urządzenia do kodu tego samouczka i ogólnie do usługi Azure IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)
>

## <a name="prerequisites"></a>Wymagania wstępne

* Visual Studio

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Odbieranie wiadomości w aplikacji urządzenia

W tej sekcji zmodyfikuj aplikację urządzenia utworzoną w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub,](quickstart-send-telemetry-dotnet.md) aby odbierać komunikaty z chmury do urządzenia z centrum IoT Hub.

1. W programie Visual Studio w projekcie **Symulowanejdęczyć** dodać następującą metodę do **klasy Program.**

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

1. Dodaj następującą metodę w **Main** metody, tuż przed wierszem: `Console.ReadLine()`

   ```csharp
   ReceiveC2dAsync();
   ```

Metoda `ReceiveAsync` asynchronicznie zwraca odebraną wiadomość w momencie, gdy jest odbierana przez urządzenie. Zwraca *wartość null* po upływie określonego limitu czasu. W tym przykładzie jest używana wartość domyślna jednej minuty. Gdy aplikacja otrzyma *wartość null*, należy nadal czekać na nowe wiadomości. Ten wymóg jest przyczyną `if (receivedMessage == null) continue` linii.

Wywołanie `CompleteAsync()` powiadamia Centrum IoT, że wiadomość została pomyślnie przetworzona. Wiadomość można bezpiecznie usunąć z kolejki urządzeń. Jeśli stało się coś, co uniemożliwiło aplikacji urządzenia ukończenie przetwarzania wiadomości, Usługa IoT Hub dostarcza go ponownie. Logika przetwarzania wiadomości w aplikacji urządzenia musi być *idempotentna*, tak aby odbieranie tego samego komunikatu wiele razy daje ten sam wynik.

Aplikacja może również tymczasowo porzucić wiadomość, co powoduje, że centrum IoT zachowuje komunikat w kolejce do przyszłego użycia. Lub aplikacja może odrzucić wiadomość, która trwale usuwa wiadomość z kolejki. Aby uzyskać więcej informacji na temat cyklu życia komunikatów z chmury do urządzenia, zobacz [Wiadomości D2C i C2D z Centrum IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > W przypadku korzystania z protokołu HTTPS zamiast MQTT `ReceiveAsync` lub AMQP jako transportu, metoda zwraca natychmiast. Obsługiwany wzorzec dla wiadomości z chmury do urządzenia z HTTPS jest sporadycznie podłączonych urządzeń, które sprawdzają wiadomości rzadko (mniej niż co 25 minut). Wydawanie więcej https odbiera wyniki w Centrum IoT ograniczania żądań. Aby uzyskać więcej informacji na temat różnic między obsługą MQTT, AMQP i HTTPS oraz ograniczaniem przepustowości centrum IoT, zobacz [Wiadomości D2C i C2D z Centrum IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-dotnet.md). Aby wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie wiadomości z chmury do urządzenia

Teraz piszesz aplikację konsoli .NET, która wysyła komunikaty z chmury do urządzenia do aplikacji urządzenia.

1. W bieżącym rozwiązaniu programu Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu**. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. Nazwij projekt *SendCloudToDevice*. W obszarze **Rozwiązanie**wybierz pozycję **Dodaj do rozwiązania** i zaakceptuj najnowszą wersję programu .NET Framework. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

   ![Konfigurowanie nowego projektu w programie Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy nowe rozwiązanie, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W **obszarze Zarządzanie pakietami NuGet**wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **Microsoft.Azure.Devices**. Wybierz **pozycję Zainstaluj**.

   Ten krok pobiera, instaluje i dodaje odwołanie do [pakietu NuGet usługi Azure IoT.](https://www.nuget.org/packages/Microsoft.Azure.Devices/)

1. Dodaj następującą `using` instrukcję w górnej części pliku **Program.cs.**

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametryem połączenia centrum IoT skopiowanym wcześniej w polu [Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Dodaj następującą metodę do klasy **Program**. Ustaw nazwę urządzenia na używaną podczas definiowania urządzenia w [funkcji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Ta metoda wysyła nowy komunikat chmury do urządzenia do urządzenia `myFirstDevice`o identyfikatorze. Zmień ten parametr tylko wtedy, gdy zmodyfikowano go z używanego w [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT hub](quickstart-send-telemetry-dotnet.md).

1. Na koniec dodaj następujące wiersze do **Main** metody.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Ustaw projekty startowe**.

1. W **programie Common Properties** > **Startup Project**wybierz pozycję Wiele projektów **startowych**, a następnie wybierz pozycję **Uruchom** akcję **Dla readdeviceToCloudMessages**, **SimulatedDevice**i **SendCloudToDevice**. Aby zapisać zmiany, wybierz pozycję **OK**.

1. Naciśnij klawisz **F5**. Wszystkie trzy aplikacje powinny zostać uruchomiony. Wybierz okna **SendCloudToDevice** i naciśnij klawisz **Enter**. Powinien zostać wyświetlony komunikat odbierany przez aplikację urządzenia.

   ![Komunikat o otrzymaniu aplikacji](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Otrzymaj opinię o dostarczeniu

Istnieje możliwość żądania potwierdzenia dostarczania (lub wygaśnięcia) z Usługi IoT Hub dla każdego komunikatu chmury do urządzenia. Ta opcja umożliwia zapleczu rozwiązania łatwo poinformować ponawiania lub logiki kompensacji. Aby uzyskać więcej informacji na temat opinii z chmury do urządzenia, zobacz [D2C i C2D Messaging z Centrum IoT Hub](iot-hub-devguide-messaging.md).

W tej sekcji zmodyfikujesz aplikację **SendCloudToDevice,** aby zażądać opinii i odbierzesz ją z centrum IoT Hub.

1. W programie Visual Studio w projekcie **SendCloudToDevice** dodaj następującą metodę do klasy **Program.**

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

    Należy zauważyć, że ten wzorzec odbierania jest taki sam, który jest używany do odbierania wiadomości z chmury do urządzenia z aplikacji urządzenia.

1. Dodaj następujący wiersz w **Main** metody, zaraz po `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Aby poprosić o opinię o dostarczenie komunikatu chmury do urządzenia, należy określić właściwość w **SendCloudToDeviceMessageAsync** metody. Dodaj następujący wiersz tuż `var commandMessage = new Message(...);` za wierszem.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Uruchom aplikacje, naciskając klawisz **F5**. Powinny być widoczne wszystkie trzy aplikacje start. Wybierz okna **SendCloudToDevice** i naciśnij klawisz **Enter**. Powinien zostać wyświetlony komunikat odbierany przez aplikację urządzenia i po kilku sekundach wiadomość zwrotna odbierana przez aplikację **SendCloudToDevice.**

   ![Komunikat o otrzymaniu aplikacji](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Dla uproszczenia ten samouczek nie implementuje żadnych zasad ponawiania prób. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób, takie jak wykładnicze wycofywanie, zgodnie z sugestią w [przypadku obsługi błędów przejściowych.](/azure/architecture/best-practices/transient-faults)
>

## <a name="next-steps"></a>Następne kroki

W tym instrukcje, można dowiedzieć się, jak wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zobaczyć przykłady kompletnych kompleksowych rozwiązań korzystających z usługi IoT Hub, zobacz [akcelerator rozwiązań zdalnego monitorowania usługi Azure IoT.](https://docs.microsoft.com/azure/iot-suite/)

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)
