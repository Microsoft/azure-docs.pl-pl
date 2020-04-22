---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub za pomocą platformy .NET | Dokumenty firmy Microsoft
description: Jak przekazać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzenia Usługi Azure IoT dla platformy .NET. Przekazane pliki są przechowywane w kontenerze obiektów blob magazynu platformy Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 14472e84d425bf03a3c6a0c2dc558d4b8225caec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733396"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ten samouczek opiera się na kodzie w [wysyłaniu komunikatów z chmury do urządzenia za pomocą centrum IoT Hub,](iot-hub-csharp-csharp-c2d.md) aby pokazać, jak korzystać z możliwości przekazywania plików usługi IoT Hub. To pokazuje, jak:

* Bezpiecznie zapewnij urządzeniu identyfikator URI obiektu blob platformy Azure do przekazywania pliku.

* Powiadomienia o przekazywaniu plików usługi IoT Hub za pomocą wyzwalania przetwarzania pliku w zapleczu aplikacji.

Wysyłanie [danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md) Szybki start i Wysyłanie komunikatów z chmury do urządzenia za pomocą samouczka Centrum [IoT hub](iot-hub-csharp-csharp-c2d.md) pokazują podstawowe funkcje obsługi wiadomości między urządzeniami i chmury do urządzenia usługi IoT Hub. Narzędzie [Konfigurowanie routingu wiadomości za pomocą narzędzia IoT Hub](tutorial-routing.md) opisuje sposób niezawodnego przechowywania wiadomości z urządzenia do chmury w magazynie obiektów Blob platformy Microsoft Azure. Jednak w niektórych scenariuszach nie można łatwo mapować danych wysyłanych przez urządzenia do stosunkowo małych komunikatów z urządzenia do chmury akceptowanych przez centrum IoT Hub. Przykład:

* Duże pliki zawierające obrazy

* Filmy wideo

* Dane dotyczące drgań, z dużą częstotliwością

* Jakaś forma wstępnie przetworzonych danych

Te pliki są zazwyczaj wsadowe przetwarzane w chmurze przy użyciu narzędzi, takich jak [usługa Azure Data Factory](../data-factory/introduction.md) lub [stosu Hadoop.](../hdinsight/index.yml) Gdy musisz przekazać pliki z urządzenia, nadal możesz korzystać z zabezpieczeń i niezawodności usługi IoT Hub.

Na końcu tego samouczka można uruchomić dwie aplikacje konsoli .NET:

* **Symulowaneurządzenia**. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego dostarczonego przez centrum IoT. Jest to zmodyfikowana wersja aplikacji utworzonej w wiadomościach z chmury do urządzenia z samouczka [Centrum IoT.](iot-hub-csharp-csharp-c2d.md)

* **ReadFileUploadNotification**. Ta aplikacja odbiera powiadomienia o przekazywaniu plików z centrum IoT Hub.

> [!NOTE]
> Usługa IoT Hub obsługuje wiele platform i języków urządzeń, w tym C, Java, Python i Javascript, za pośrednictwem zestawów SDK urządzeń IoT platformy Azure. Zapoznaj się z [Centrum deweloperów usługi Azure IoT,](https://azure.microsoft.com/develop/iot) aby uzyskać instrukcje krok po kroku dotyczące podłączania urządzenia do usługi Azure IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

* Visual Studio

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji zmodyfikujesz aplikację urządzenia utworzoną w [aplikacji Wyślij wiadomości z chmury do urządzenia za pomocą usługi IoT Hub](iot-hub-csharp-csharp-c2d.md) w celu odbierania komunikatów z chmury do urządzenia z centrum IoT Hub.

1. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy projekt **Symulowanejwiększość** i wybierz pozycję **Dodaj** > **istniejący element**. Znajdź plik obrazu i dołącz go do projektu. W tym samouczku przyjęto założenie, że obraz ma nazwę `image.jpg`.

1. Kliknij prawym przyciskiem myszy obraz, a następnie wybierz polecenie **Właściwości**. Upewnij się, że **funkcja Kopiowanie do katalogu wyjściowego** jest zawsze ustawiona na **Kopiowanie**.

    ![Pokaż, gdzie zaktualizować właściwość obrazu dla katalogu kopiowania do katalogu wyjściowego](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. W pliku **Program.cs** dodaj następujące instrukcje u góry pliku:

    ```csharp
    using System.IO;
    ```

1. Dodaj następującą metodę do **klasy Program:**

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    Metoda `UploadToBlobAsync` przyjmuje w nazwie pliku i źródła strumienia pliku do przekazania i obsługuje przekazywania do magazynu. Aplikacja konsoli wyświetla czas potrzebny do przekazania pliku.

1. Dodaj następujący wiersz w **Main** metody, tuż przed: `Console.ReadLine()`

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Dla uproszczenia ten samouczek nie implementuje żadnych zasad ponawiania prób. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób, takie jak wykładnicze wycofywanie, zgodnie z sugestią w [przypadku obsługi błędów przejściowych.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę zaplecza do odbierania wiadomości powiadomień o przekazywaniu plików z centrum IoT utworzonego w [obszarze Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-dotnet.md). Aby odbierać komunikaty powiadomień o przekazywaniu plików, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Odbierz powiadomienie o przekazaniu pliku

W tej sekcji napisz aplikację konsoli .NET, która odbiera komunikaty powiadomień o przekazywaniu plików z usługi IoT Hub.

1. W bieżącym rozwiązaniu programu Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu**. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. Nazwij projekt *ReadFileUploadNotification*. W obszarze **Rozwiązanie**wybierz pozycję **Dodaj do rozwiązania**. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

    ![Konfigurowanie projektu ReadFileUploadNotification w programie Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **ReadFileUploadNotification** i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**. Wyszukaj i wybierz pozycję **Microsoft.Azure.Devices**, a następnie wybierz pozycję **Zainstaluj**.

    Ten krok pobiera, instaluje i dodaje odwołanie do [pakietu NuGet usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) w projekcie **ReadFileUploadNotification.**

1. W pliku **Program.cs** dla tego projektu dodaj następującą instrukcję u góry pliku:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu `{iot hub connection string}` zastępczego parametrym połączenia centrum IoT skopiowanym wcześniej w polu Pobierz parametry połączenia centrum [IoT:](#get-the-iot-hub-connection-string)

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Dodaj następującą metodę do **klasy Program:**

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Należy zauważyć, że ten wzorzec odbierania jest taki sam, który jest używany do odbierania wiadomości z chmury do urządzenia z aplikacji urządzenia.

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Ustaw projekty startowe**.

1. W **programie Common Properties** > **Startup Project**wybierz pozycję Wiele **projektów startowych**, a następnie wybierz akcję **Start** dla **ReadFileUploadNotification** i **SimulatedDevice**. Aby zapisać zmiany, wybierz pozycję **OK**.

1. Naciśnij klawisz **F5**. Obie aplikacje powinny zostać uruchomiony. Przesyłanie powinno zostać zakończone w jednej aplikacji konsoli i komunikat powiadomienia o przekazaniu odebrany przez drugą aplikację konsoli. Za pomocą [witryny Azure portal](https://portal.azure.com/) lub Visual Studio Server Explorer można sprawdzić obecność przekazanego pliku na koncie usługi Azure Storage.

    ![Zrzut ekranu przedstawiający ekran wyjściowy](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak korzystać z możliwości przekazywania plików usługi IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz kontynuować eksplorowanie funkcji i scenariuszy usługi IoT Hub, wykonując następujące artykuły:

* [Programowo tworzenie centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do C SDK](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
