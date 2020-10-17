---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub przy użyciu platformy .NET | Microsoft Docs
description: Jak przekazywać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla platformy .NET. Przekazane pliki są przechowywane w kontenerze obiektów BLOB usługi Azure Storage.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 8d45ad630d09a4909cf00b830df139057cc0fcaf
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142289"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym samouczku przedstawiono kod w oknie [wysyłanie komunikatów z chmury do urządzeń za pomocą](iot-hub-csharp-csharp-c2d.md) samouczka IoT Hub, aby zobaczyć, jak korzystać z funkcji przekazywania plików IoT Hub. Pokazano, jak:

* Bezpieczne zapewnianie urządzenia za pomocą identyfikatora URI obiektu blob platformy Azure na potrzeby przekazywania pliku.

* Użyj powiadomień przekazywania plików IoT Hub, aby wyzwolić przetwarzanie pliku w zapleczu aplikacji.

[Wysyłanie danych telemetrycznych z urządzenia do](quickstart-send-telemetry-dotnet.md) przewodnika Szybki Start dotyczącego usługi IoT Hub i [wysyłanie komunikatów z chmury do urządzeń za pomocą](iot-hub-csharp-csharp-c2d.md) samouczka IoT Hub przedstawia podstawowe funkcje przesyłania komunikatów z urządzenia do chmury i IoT Hub z chmury do urządzenia. Samouczek [Konfigurowanie routingu komunikatów z IoT Hub](tutorial-routing.md) zawiera opis sposobu niezawodnego przechowywania komunikatów przesyłanych z urządzeń do chmury w Microsoft Azure usłudze BLOB Storage. Jednak w niektórych scenariuszach nie można łatwo zmapować danych wysyłanych przez urządzenia do bezwzględnie niewielkich komunikatów z urządzenia do chmury, które IoT Hub akceptowane. Na przykład:

* Duże pliki zawierające obrazy

* Filmy wideo

* Próbkowanie danych drgań z wysoką częstotliwością

* Niektóre formy wstępnie przetworzonych danych

Te pliki są zwykle przetwarzane wsadowo w chmurze przy użyciu narzędzi, takich jak [Azure Data Factory](../data-factory/introduction.md) lub stos [Hadoop](../hdinsight/index.yml) . W przypadku konieczności przekazywania plików z urządzenia można nadal korzystać z zabezpieczeń i niezawodności IoT Hub.

Na końcu tego samouczka uruchomisz dwie aplikacje konsolowe programu .NET:

* **SimulatedDevice**. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI SYGNATURy dostępu współdzielonego dostarczonego przez Centrum IoT. Jest to zmodyfikowana wersja aplikacji utworzonej w ramach [wysyłania komunikatów z chmury do urządzeń za pomocą](iot-hub-csharp-csharp-c2d.md) samouczka IoT Hub.

* **ReadFileUploadNotification**. Ta aplikacja odbiera powiadomienia o przekazywaniu plików z Centrum IoT Hub.

> [!NOTE]
> IoT Hub obsługuje wiele platform i języków urządzeń, w tym C, Java, Python i JavaScript, za pomocą zestawów SDK urządzeń usługi Azure IoT. Zapoznaj się z [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot) , aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z usługą Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Visual Studio

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji zmodyfikujesz aplikację urządzenia utworzoną w temacie [wysyłanie komunikatów z chmury do urządzenia za pomocą IoT Hub](iot-hub-csharp-csharp-c2d.md) , aby odbierać komunikaty z chmury do urządzenia z Centrum IoT Hub.

1. W programie Visual Studio Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **SimulatedDevice** , a następnie wybierz pozycję **Dodaj**  >  **istniejący element**. Znajdź plik obrazu i dołącz go do projektu. W tym samouczku założono, że obraz ma nazwę `image.jpg` .

1. Kliknij prawym przyciskiem myszy obraz, a następnie wybierz polecenie **Właściwości**. Upewnij się, że wartość **Kopiuj do katalogu wyjściowego** jest ustawiona na **zawsze Kopiuj**.

    ![Pokaż miejsce, do którego chcesz zaktualizować właściwość obrazu na potrzeby kopiowania do katalogu wyjściowego](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. W pliku **program.cs** Dodaj następujące instrukcje w górnej części pliku:

    ```csharp
    using System.IO;
    ```

1. Dodaj następującą metodę do klasy **program** :

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

    `UploadToBlobAsync`Metoda przyjmuje nazwę pliku i źródło strumienia pliku, który ma zostać przekazany i obsługuje przekazywanie do magazynu. Aplikacja konsoli wyświetla czas potrzebny na przekazanie pliku.

1. Dodaj następujący wiersz w metodzie **Main** , bezpośrednio przed `Console.ReadLine()` :

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Dla uproszczenia w tym samouczku nie są implementowane żadne zasady ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania, takie jak wycofywania wykładniczy, zgodnie z sugestią w [przejściowej obsłudze błędów](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza w celu odbierania komunikatów powiadomień o przekazywaniu plików z Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-dotnet.md). Aby odbierać komunikaty powiadomień o przekazywaniu plików, usługa musi mieć uprawnienia do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Odbieranie powiadomienia o przekazywaniu plików

W tej sekcji utworzysz aplikację konsolową .NET, która odbiera komunikaty powiadomień o przekazaniu plików z IoT Hub.

1. W bieżącym rozwiązaniu programu Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. W obszarze **Utwórz nowy projekt**wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. Nazwij projekt *ReadFileUploadNotification*. W obszarze **rozwiązanie**wybierz pozycję **Dodaj do rozwiązania**. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

    ![Konfigurowanie projektu ReadFileUploadNotification w programie Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **ReadFileUploadNotification** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**. Wyszukaj i wybierz pozycję **Microsoft. Azure. Devices**, a następnie wybierz pozycję **Zainstaluj**.

    Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do [pakietu NuGet zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) w projekcie **ReadFileUploadNotification** .

1. W pliku **program.cs** dla tego projektu, Dodaj następującą instrukcję na początku pliku:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp `{iot hub connection string}` wartość symbolu zastępczego parametrami połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Dodaj następującą metodę do klasy **program** :

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

    Uwaga Ten wzorzec odbierania jest taki sam, jak w przypadku otrzymywania komunikatów z chmury do urządzenia z aplikacji urządzenia.

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

1. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie wybierz pozycję **Ustaw projekty startowe**.

1. W polu **Common Properties**  >  **projekt startowy**właściwości wspólne wybierz opcję **wiele projektów startowych**, a następnie wybierz akcję **Rozpocznij** dla **ReadFileUploadNotification** i **SimulatedDevice**. Wybierz przycisk **OK**, aby zapisać zmiany.

1. Naciśnij klawisz **F5**. Należy uruchomić obie aplikacje. Powinno zostać wyświetlone zakończono przekazywanie w jednej aplikacji konsolowej, a komunikat powiadomienia o przekazaniu odebrany przez inną aplikację konsolową. Możesz użyć [Azure Portal](https://portal.azure.com/) lub Eksplorator serwera programu Visual Studio, aby sprawdzić obecność przekazanego pliku na koncie usługi Azure Storage.

    ![Zrzut ekranu przedstawiający ekran danych wyjściowych](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz nadal eksplorować IoT Hub funkcje i scenariusze z następującymi artykułami:

* [Programistyczne tworzenie Centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)