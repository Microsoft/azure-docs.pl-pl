---
title: Samouczek dotyczący zabezpieczeń X.509 w usłudze Azure IoT Hub | Dokumenty firmy Microsoft
description: Rozpocznij pracę nad zabezpieczeniami opartymi na x.509 w centrum Azure IoT hub w symulowanym środowisku.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: ac45cf42ed174d3e9423b4ea39cadf16b84897ef
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759637"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Konfigurowanie zabezpieczeń X.509 w usłudze Azure IoT Hub

W tym samouczku przedstawiono kroki potrzebne do zabezpieczenia centrum Usługi Azure IoT przy użyciu *uwierzytelniania certyfikatów X.509.* Na potrzeby ilustracji używamy narzędzia open source OpenSSL do tworzenia certyfikatów lokalnie na komputerze z systemem Windows. Zaleca się użycie tego samouczka tylko do celów testowych. W środowisku produkcyjnym należy zakupić certyfikaty od *głównego urzędu certyfikacji*.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga, aby były gotowe następujące zasoby:

* Utworzono centrum IoT hub z subskrypcją platformy Azure. Zobacz [Tworzenie centrum IoT za pośrednictwem portalu, aby](iot-hub-create-through-portal.md) uzyskać szczegółowe kroki.

* Masz zainstalowany [program Visual Studio 2017 lub Visual Studio 2019.](https://www.visualstudio.com/vs/)

## <a name="get-x509-ca-certificates"></a>Uzyskaj certyfikaty X.509 urzędu certyfikacji

Zabezpieczenia oparte na certyfikatach X.509 w Centrum IoT wymagają rozpoczęcia [od łańcucha certyfikatów X.509,](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)który zawiera certyfikat główny, a także wszelkie certyfikaty pośrednie aż do certyfikatu typu liść.

Możesz wybrać dowolny z następujących sposobów uzyskania certyfikatów:

* Kup certyfikaty X.509 od *głównego urzędu certyfikacji .* Ta metoda jest zalecana w środowiskach produkcyjnych.

* Utwórz własne certyfikaty X.509 za pomocą narzędzia innej firmy, takiego jak [OpenSSL.](https://www.openssl.org/) Ta technika jest w porządku do celów testowych i rozwojowych. Aby uzyskać informacje na temat generowania testowych certyfikatów urzędu certyfikacji przy użyciu programu PowerShell lub Bash, zobacz [Zarządzanie testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) Pozostała część tego samouczka używa certyfikatów urzędu certyfikacji testowych wygenerowanych przez przestrzeganie instrukcji [w temacie Zarządzanie testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)

* Wygeneruj [certyfikat pośredniego urzędu certyfikacji X.509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) podpisany przez istniejący certyfikat głównego urzędu certyfikacji i przekaż go do koncentratora. Po przesłaniu i zweryfikowaniu certyfikatu pośredniego, zgodnie z poniższą instrukcją, może być używany w miejscu certyfikatu głównego urzędu certyfikacji wymienionego poniżej. Narzędzia takie jak OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) i [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) mogą być używane do generowania i podpisywania pośredniego certyfikatu urzędu certyfikacji.

> [!NOTE]
> Nie przesyłaj katalogu głównego innej firmy, jeśli nie jest on unikatowy dla Użytkownika, ponieważ umożliwiłoby to innym klientom innej firmy łączenie ich urządzeń z centrum IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Rejestrowanie certyfikatów urzędu certyfikacji X.509 w centrum IoT

Te kroki pokazują, jak dodać nowy urząd certyfikacji do centrum IoT za pośrednictwem portalu.

1. W witrynie Azure portal przejdź do centrum IoT hub i wybierz pozycję**Certyfikaty** **ustawień** > dla centrum.

1. Wybierz **pozycję Dodaj,** aby dodać nowy certyfikat.

1. W **polu Nazwa certyfikatu**wprowadź przyjazną nazwę wyświetlaną i wybierz plik certyfikatu utworzony w poprzedniej sekcji z komputera.

1. Po otrzymaniu powiadomienia o pomyślnym przesłaniu certyfikatu wybierz pozycję **Zapisz**.

    ![Przekazywanie certyfikatu](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Certyfikat pojawi się na liście certyfikatów o stanie **Niezweryfikowany**.

1. Wybierz certyfikat, który właśnie został dodany, aby wyświetlić **szczegóły certyfikatu,** a następnie wybierz pozycję **Generuj kod weryfikacyjny**.

   ![Weryfikowanie certyfikatu](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Skopiuj **kod weryfikacyjny** do schowka. Służy do sprawdzania poprawności własności certyfikatu.

1. Postępuj zgodnie z krokiem 3 w [zarządzaniu certyfikatami certyfikatów urzędu certyfikacji testowych dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Ten proces podpisuje kod weryfikacyjny za pomocą skojarzenia klucza prywatnego z certyfikatem urzędu certyfikacji X.509, który generuje podpis. Dostępne są narzędzia do wykonywania tego procesu podpisywania, na przykład OpenSSL. Proces ten jest znany jako [dowód posiadania](https://tools.ietf.org/html/rfc5280#section-3.1).

1. W **obszarze Szczegóły certyfikatu**, w obszarze **Certyfikat weryfikacji .pem lub .cer file**, znajdź i otwórz plik podpisu. Następnie wybierz pozycję **Sprawdź**.

   Stan certyfikatu zmienia się na **Zweryfikowany**. Wybierz **opcję Odśwież,** jeśli certyfikat nie zostanie zaktualizowany automatycznie.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Tworzenie urządzenia X.509 dla centrum IoT

1. W witrynie Azure portal przejdź do centrum IoT Hub, a następnie wybierz pozycję **Explorers** > **IoT devices**.

1. Wybierz **pozycję Nowy,** aby dodać nowe urządzenie.

1. W **pliku Device ID**wprowadź przyjazną nazwę wyświetlaną. W przypadku **typu Uwierzytelnianie**wybierz pozycję **Podpisany urząd certyfikacji X.509,** a następnie wybierz pozycję **Zapisz**.

   ![Tworzenie urządzenia X.509 w portalu](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Uwierzytelnij urządzenie X.509 za pomocą certyfikatów X.509

Aby uwierzytelnić urządzenie X.509, należy najpierw podpisać urządzenie za pomocą certyfikatu urzędu certyfikacji. Podpisywanie urządzeń liściowych odbywa się zwykle w zakładzie produkcyjnym, gdzie narzędzia produkcyjne zostały odpowiednio włączone. W miarę jak urządzenie przechodzi od jednego producenta do drugiego, akcja podpisywania każdego producenta jest przechwytywany jako certyfikat pośredni w łańcuchu. Wynikiem jest łańcuch certyfikatów od certyfikatu urzędu certyfikacji do certyfikatu typu typu liść urządzenia. Krok 4 [w zarządzaniu testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generuje certyfikat urządzenia.

Następnie pokażemy Ci, jak utworzyć aplikację języka C#, aby symulować urządzenie X.509 zarejestrowane dla centrum IoT hub. Wyślemy wartości temperatury i wilgotności z symulowanego urządzenia do twojego koncentratora. W tym samouczku utworzymy tylko aplikację urządzenia. Pozostaje jako ćwiczenie dla czytelników, aby utworzyć aplikację usługi Usługi Usługi IoT Hub, która wyśle odpowiedź na zdarzenia wysyłane przez to symulowane urządzenie. Aplikacja języka C# zakłada, że postępujesz zgodnie z [instrukcjami w zarządzaniu certyfikatami testu urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Otwórz program Visual Studio, wybierz pozycję **Utwórz nowy projekt**, a następnie wybierz szablon projektu **aplikacji konsoli (NET Framework).** Wybierz opcję **Dalej**.

1. W **obszarze Konfiguruj nowy projekt**nazwij projekt *SimulateX509Device*, a następnie wybierz pozycję **Utwórz**.

   ![Tworzenie projektu urządzenia X.509 w programie Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **SimulateX509Device,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj** i wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Client**. Wybierz pozycję **Zainstaluj**.

   ![Dodawanie pakietu NuGet zestawu SDK urządzenia w programie Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Ten krok pobiera, instaluje i dodaje odwołanie do pakietu NuGet zestawu NuGet urządzenia Usługi Azure IoT i jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Dodaj następujące pola do klasy **Program:**

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Użyj przyjaznej nazwy urządzenia użytej w poprzedniej sekcji zamiast _<your_device_id>_.

1. Dodaj następującą funkcję, aby utworzyć losowe liczby temperatury i wilgotności i wysłać te wartości do piasty:

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Na koniec dodaj następujące wiersze kodu do funkcji **Main,** zastępując symbole zastępcze _device-id_, _your-iot-hub-name_i _absolute-path-to-your-device-pfx-file_ zgodnie z wymaganiami konfiguracji.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Ten kod łączy się z centrum IoT hub, tworząc parametry połączenia dla urządzenia X.509. Po pomyślnym podłączeniu wysyła do koncentratora zdarzenia temperatury i wilgotności i czeka na jego odpowiedź.

1. Uruchom aplikację. Ponieważ ta aplikacja uzyskuje dostęp do pliku *.pfx,* może być konieczne uruchomienie tej aplikacji jako administrator.

   1. Tworzenie rozwiązania programu Visual Studio.

   1. Otwórz nowe okno wiersza polecenia przy użyciu **polecenia Uruchom jako administrator**.  

   1. Przejdź do folderu zawierającego rozwiązanie, a następnie przejdź do ścieżki *bin/debugowania* w folderze rozwiązania.

   1. Uruchom aplikację **SimulateX509Device.exe** z wiersza polecenia.

   Urządzenie powinno pomyślnie połączyć się z koncentratorem i wysłać zdarzenia.

   ![Uruchamianie aplikacji urządzenia](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zabezpieczaniu rozwiązania IoT, zobacz:

* [Najważniejsze wskazówki dotyczące zabezpieczeń IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Architektura zabezpieczeń IoT](../iot-fundamentals/iot-security-architecture.md)

* [Zabezpieczanie wdrożenia IoT](../iot-fundamentals/iot-security-deployment.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
